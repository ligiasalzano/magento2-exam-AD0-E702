# 1. Arquitetura Magento e Técnicas de Customização 

## 1.1 Descrever a arquitetura baseada em módulos do Magento

### Descrever a arquitetura do módulo.

Um módulo é um **grupo lógico** – ou seja, um deiratório contendo _blocks, controllers, helpers e models_ – que é relacionado à um **recurso específico** do negócio. Cada módulo pode adicionar um novo recurso implementando uma nova funcionalidade ou estendendo a funcionalidade de outro módulo. Contudo, o Magento possui foco na modularidade, por isso, um módulo deve trazer apenas um recurso e depender minimamente de outros módulos.

Dessa forma, um módulo deverá ser o mais independente e específico possível. Sendo que a inclusão ou exclusão de um módulo não deve afetar a funcionalidade de outros. [Magento DevDocs - Module overview](https://devdocs.magento.com/guides/v2.3/architecture/archi_perspectives/components/modules/mod_intro.html)

#### Tipos de componentes
No Magento existem 3 tipos de componentes:
- Módulos (adicionam ou estendem uma funcionalidade)
- Temas (personalizam a aparência - que podem ser para o frontend ou para a área adminhtml)
- Pacotes de tradução (adiciona traduções de idioma)

#### As áreas do Magento

Há 7 áreas no Magento:

- `adminhtml` - A área adminhtml inclui o código necessário para o gerenciamento da loja. Refere-se ao painel administrativo (admin ou backend) do Magento. 
- `frontend` - Refere-se à parte da loja na qual haverá a interação com o cliente (usuário final)
- `global` (ou `base`) - Engloba todas as áreas e é usado como _fallback_ para arquivos ausentes nas áreas `frontend` e `adminhtml`
- `crontab` - É carregada quando as _crons_ são executadas. A classe `\Magento\Framework\App\Cron` sempre carrega a área `crontab`.
- `webapi_rest` - Esta área responde pelas chamadas _REST_ da _API_ do Magneto 2.
- `webapi_soap` – Refere-se às chamadas _SOAP_.
- `graphql` - Responde pelas chamadas GRAPHQL (essa área foi inserida a partir do Magento 2.3)

Nem todas as áreas estão disponíveis todo o tempo. Por exemplo, a `crontab` é usada apenas ao executar tarefas cron.

###  Quais são os principais passos para adicionar um novo módulo?

Os arquivos necessários para inicializar um módulo são: `registration.php`, `etc/module.xml` e `composer.json`. Sendo que são obrigatórios o `registration.php` e o `etc/module.xml`.

#### 1. Declare o componente e suas dependências no composer.json.

Nesse arquivo, informamos o nome, descrição, autor e versão do módulo, suas dependências e outras informações.
No `composer.json`, o **_autoload_** especifica as informações necessarias para serem carregadas, como o arquivo _registration.php_.
```json
{
 "name": "Acme-vendor/bar-component",
 "autoload": {
    "psr-4": { "AcmeVendor\\BarComponent\\": "" },
    "files": [ "registration.php" ]
 }
}
```

#### 2. Registre o componente com o arquivo registration.php (obrigatório).

Este arquivo é incluído pelo _Composer autoloader_ (`app/etc/NonComposerComponentRegistration.php`).
Isto adiciona o módulo à lista de componentes em `\Magento\Framework\Component\ComponetRegistrar`.

Após a leitura deste arquivo, o Magento vai procurar o `etc/module.xml`.

##### Registrando alguns tipos de componentes
- **Módulos:** `ComponentRegistrar::register(ComponentRegistrar::MODULE, '<VendorName_ModuleName>', __DIR__);`
- **Temas:** `ComponentRegistrar::register(ComponentRegistrar::THEME, '<area>/<vendor>/<theme name>', __DIR__);`
- **Pacotes de tradução:** `ComponentRegistrar::register(ComponentRegistrar::LANGUAGE, '<VendorName>_<packageName>', __DIR__);`
- **Bibliotecas:** `ComponentRegistrar::register(ComponentRegistrar::LIBRARY, '<vendor>/<library_name>', __DIR__);`


#### 3. Nomeie, declare e defina as dependências no arquivo module.xml (obrigatório).

Cada módulo deve ser nomeado e declarado em um arquivo xml específico do componente. 
- module.xml (modules), theme.xml (themes) and language.xml (for language packages).

O arquivo module.xml é localizado no diretório `<vendor>/<module>/etc`. Neste arquivo também são declaradas a versão do módulo (que será utilizada pelas classes de Setup do módulo) e as suas dependências.
As dependências são carregadas no elemento `<sequence>`:
```xml
<sequence>
  <module name="Vendor_Module"/>
</sequence>
```
O nó `<sequence>` reporta ao Magento em qual ordem carregar os módulos. Esse ajuste acontece quando o módulo é instalado. A lista de módulos é armazenada no arquivo `app/etc/config.php`. Esta sequência é montada em `vendor/magento/framework/Module/ModuleList/Loader.php`. Esta organização é muito útil para _events_, _plugins_, _preferences_ e _layouts_.

#### Dependencias do módulo

No Magento 2 os módulos possuem dois tipos de dependências: _hard (forte)_ e _soft (fraca)_. Módulos com dependência forte não funcionam sem o módulo do qual dependem. Já os de depêndencia fraca funcionam independentemente de outro módulo.

**_Hard dependencies_** são definidas na seção `require` do arquivo `app/code/<Vendor>/<Module>/composer.json`.

**_Soft dependencies_** são definidas na seção `suggest`do arquivo `app/code/<Vendor>/<Module>/composer.json`.
Elas também estão no nó `<sequence>` do `app/code/<Vendor>/<Module>/etc/module.xml`.

Dentro dos tipos de depências, um módulo pode ter vários tipos de relações com outros módulos. Como:
> - **uses:** módulo A usa o módulo B;
> - **reacts to:** o módulo A reage ao módulo B se o seu comportamento for acionado por um evento que ocorre no módulo B (isso acontece sem o "conhecimento" do módulo B);
> - **customizes:** o módulo A personaliza o módulo B;
> - **implements:** o módulo A implementa o módulo B;
> - **replaces:** o módulo A substitui o módulo B;
> ([Referência](https://amasty.com/blog/magento-2-certification-module-based-architecture/))

Um dos princípios do Magento é a **minimização de dependências**, ou seja, um módulo deve ter o mínimo possível de dependências, bem como, não depender de vários módulos. Isso tem relação com o **princípio de responsabilidade única** em que o módulo deve ter apenas uma função.

###  Quais são os diferentes tipos de pacotes do Composer? 

Os tipos de pacotes específicos do Magneto são: _magento2-module_ para módulos, _magento2-theme_ para temas, _magento2-language_ para pacotes de tradução e _magento2-component_ para extensões que não se enquadram nos outros tipos.

Use o seguinte formato quando for nomear o seu pacote: `<vendor-name>/<package-name>`

`vendor-name`: Todas as letras devem ser minúsculas. Por exemplo, o nome _vendor_ para extensões desenvolvidas pela Magento Inc é _magento_.

`package-name`: Todas as letras devem ser minúsculas. A convenção para o nome dos pacotes da Magento é: `magento/<type-prefix>-<suffix>`

`<type-prefix>` é qualquer tipo de extenção do Mangento. Pode ser: _module-_ (para módulos), _theme-_ (para temas), _language-_ (para pacotes de tradução), _product-_ (para **_metapackages_** como o _Magento Open Source_ ou _Magento Commerce_.


###  Quando você deve colocar um módulo no app/code versus outros locais?

Os módulos estão localizados nos diretórios **vendor** e **app/code**. 
- No diretório **vendor** são colocados os módulos instalados via Composer. Ali também estão os módulos principais do Magento (caso ele tenha sido instalado via Composer). Os arquivos que estão nesta pasta, são considerados "intocáveis", ou melhor, não podem ser editados diretamente. Os módulos deste diretório seguem a seguinte síntaxe: `vendor/<vendor>/<type>-<module-name>`, onde `<type>` pode ser _module_, _theme_ ou _language_.
- O diretório **app/code** é recomendado para o desenvolvimento de módulos (ou instalações de módulos sem ser pelo Composer). Aqui, o diretório do módulo fica assim: `app/code/<vendor>/<module-name>`. 


## 1.2 Descrever a estrutura de diretórios do Magento

### Descrever a estrutura de diretórios do Magento

Os arquivos JavaScript são encontrados na pasta `/view/<area>/web/`. Os HTML (com a extenção .phtml), na pasta `/view/<area>/templates`. E os arquivos PHP podem ser encontrados em qualquer pasta, com excessão da `/view/<area>/web/`.

#### Estrutura de arquivos do Magento

##### ./app/

Contém módulos, temas, traduções e configurações globais.
- `./app/code/`: módulos
- `./app/design/`: temas
- `./app/etc/`: configurações globais
- `./app/i18n/`: traduções

##### ./bin/

O script executável da CLI do Magento. Daqui vem o comando `bin/magento`.

##### ./dev/

Ferramentas para desenvolvedores. Armazena testes funcionais automatizados que foram executados pelo Magento Test Framework.

##### ./generated/

Aqui é armazenado os códigos gerados automaticamente pelo Magento, como _Factories_, _Proxies_, _Interceptors_ etc. Na configuração padrão, se a classe for injetada em um construtor, o código será gerado pelo Magento para criar _non-existent factory classes_. 

##### ./lib/

Este diretório contém todos os arquivos de bibliotecas do Magento e do `vendor`. Ele também inclui todo o código Magento não baseado em módulo.

##### ./phpserver/

Aqui fica o arquivo `Router.php`, o qual é usado para implementar o servidor _built-in_ do PHP. No entanto, por questões de segurança, não é recomendável trabalhar com ele. Serve para casos específicos.

##### ./pub/

Inclui os arquivos públicos do Magento, como arquivos estáticos (css, imagens, js etc) e páginas de erro. Quando em produção, o Magento deve apontar para esta pasta. Isto tráz segurando à aplicação por proteger a raiz da instalação. 
- `pub/errors` - erros
- `pub/media` - imagens
- `pub/opt`
- `pub/static` - arquivos gerados pelo Magento
- `pub/cron.php`
- `pub/index.php`

##### ./setup/

Aqui tem os arquivos do instalador do Magento. 

##### ./var/

Arquivos temporários/gerados como classes, seções, cachê, _backups_ de banco de dados e erros.
- `var/log`: arquivos de log do sistema, como `exception.log` e `system.log`.
- `var/cache`: contém todo o cachê do Magneto. Esta pasta é limpa quando rodamos o `bin/magento cache:clean`
- `var/di`: gerado pelo `bin/magento setup:di:compile`

##### ./vendor/

Diretório nativo do _Composer_. Contém o _framework core_. Tudo o que for instalado pelo _Composer_ vem para este diretório.
Aqui encontram-se todos os módulos do Magento.


#### Estrutura de arquivos dos temas

##### /etc

Arquivos de configuração como o view.xml

##### /i18n

Dicionários de tradução do tema

##### /media

Imagens de pré-visualização do tema.

##### /web
Arquivos estáticos.

- `web/css/source`: arquivos de configuração _LESS_ do tema
- `web/css/source/lib`: Inclui arquivos de exibição que substituem os arquivos de biblioteca da UI armazenados em `lib/web/css/source/lib`
- `web/fonts`: arquivos de fonte
- `web/images`: imagens
- `web/js`: arquivos JavaScript

#### Estrutura de arquivos dos módulos

##### /Api: Service Contracts - Contratos de serviço

Aqui ficam todas as intefáceis responsáveis pelos contratos de serviço (_services contracts_). Contém as classes que serão expostas na api do Magento. Exemplo `\Magento\Catalog\Api\CategoryListInterface`.

##### /Api/Data: Data Service Contracts - Dados dos contratos de serviço

Esta pasta contém interfaces que representam dados. Exemplos: _Product interface, Category interface_ e _Customer interface_.

##### /Block: View Models

Este diretório faz parte da camada View do MVC. Contém os _View Models_ para os templates do Magento. Os _blocks_ são as classes responsáveis por fazer a inteface entre o template e os _resource models_ do Magento para obter dados, trabalhá-los e passá-los para o template. Eles fornecem a lógica de negócio para os templates, que devem usar o mínimo de PHP (separação de responsábilidades).

##### /Console: Console Commands

Abriga os códigos para os comandos do `bin/magento`. Cada comando que aparece na listagem do CLI é referente à uma classe desse diretório.

##### /Controller: Web Request Handlers

Todos os _controllers_ do módulo ficam aqui. Cada _controller_ deve ter uma única responsábilidade (uma _Action_). Quando uma página é requisitada, o caminho é construído com parâmetros do arquivo `routes.xml` e dos _controllers_ do diretório `/Controller`.

##### /Controller/Adminhtml: Admin controllers

Aqui ficam os _controllers_ da área adminhtml.

##### /Cron: Cron Job Classes

Possui as crontabs que serão executadas no Magento (tarefas agendadas). 

##### /etc: Configuration files

Este diretório engloba todos os arquivos `.xml` de configuração do módulo. Aqui, as configurações podem ser globais (`etc/`) ou por área (`etc/frontend` ou `etc/adminhtml`).
Alguns arquivos precisam estar dentro de uma área (`routes.xml` e `sections.xml`), outros devem ser globais (`acl.xml`) e outros podem ser globlais ou específicos de área (`di.xml`).

##### /Helper: Occasionally useful for small, reusable code 

Classes que contém métodos auxiliares (métodos estáticos), que não devem depender de outras classes.

##### /i18n: Translation CSV Files

Aqui estão todos os arquivos CSV de tradução. Esses CSVs possuem duas colunas: _de_ e _para_.

##### /Model: Data Handling and Structures

Nesta pasta ficam os _Models_, classes que lidam diretamente com o banco de dados. 

##### /Model/ResourceModel: Database Interactions

Aqui são as classes que definem como os dados devem ser recuperados e salvos no banco de dados. Qualquer interação direta com o bando de dados deve acontecer nesses arquivos.

##### /Observer: Event Listeners

Todos os _Observers_ são incluídos neste diretório. Quando o Magento dispara um evento, as classes que estão observando esse evento são chamadas. Os _event listeners_ devem implementar o `ObserverInteface` (`\Magento\
Framework\Event\ObserverInterface`). A classe PHP deve serguir o padrão _TitleCase_ enquanto o evento deve seguir o _snake_case_. 
Deve-se evitar colocar lógica de negócio em um observer. O correto seria injetá-la no _Observer_. 

##### /Plugin: Function Modification

O conceito de _Plugin_ foi introduzido no Magento 2. Os _Plugins_ permitem modificar funcionalidades da maioria das classes e interfaces. Plugins funcionam apenas em objetos que foram instanciados pelo _ObjectManager_. Por convenção, os plugins são colocados no diretório `/Plugin`.

##### /Setup: Database Modification

Os arquivos que modificam o banco de dados durante a instalação ou atualização de um módulo são colocados neste diretório. 
Os arquivos utilizados aqui são:

- `InstallSchema.php`: configura o esquema de tabelas e/ou colunas na ocasião da instalação do módulo.
- `UpgradeSchema.php`: modifica as tabelas e colunas quando a versão do módulo é atualizada.
- `Recurring.php`: É executado depois de todas as instalações ou atualizações.
- `InstallData.php`: Configura dados quando o módulo é instalado.
- `UpgradeData.php`: Modifica dados quando o módulo é atualizado.
- `RecurringData.php`: aplica-se aos dados após cada instalação ou atualização.

##### /Test:

O Magento 2 adotou o TDD (Test Driven Development). Todos os testes ficam neste diretório.

##### /Ui: UI Component Data Providers

Aqui ficam todos os _Models_ utilizados pelos componentes UI.

##### /view/[area]/templates: Block Templates

Enquanto a lógica de negócio é representada nos `Blocks`, a forma como essa lógica é apresentada ao usuário é definida nos arquivos de template. Esses arquivos devem conter o mínimo possível de PHP. 
Observação: note que o nome desta pasta é escrito no plural: templates.

##### /view/adminhtml/ui_component: UI Components

Este diretório contêm os arquivos XML de configuração dos _UI Components_. Eles flexibilizam a renderização da interface do usuário. _Grids_ e _Forms_ do admin, bem como o _checkout_ são feitos com _UI Components_.

##### /view/[area]/web: Web Assets

Este é o lugar onde os recursos da Web são armazenados. Isso inclui JS, CSS (ou LESS / SCSS) e imagens.

##### /view/[area]/web/template: JS Templates

Os modelos HTML que podem ser solicitados de forma assíncrona com Javascript são colocados aqui.
Observação: cuidado para não confundir. O nome desta pasta é no singular, template.

##### /view/[area]/requirejs-config.js

Aqui ficam as configurações RequireJS do módulo. Essa configuração é usada para controlar as dependências do módulo Javascript, criar aliases e declarar mixins

### Quais são as convenções de nome e como os _namespaces_ são estabelecidos? Como identificar os arquivos responsáveis por uma certa funcionalidade?

> ###### Preciso melhorar essa parte. Desculpe, ainda não pude fazer isso.

Os _namespaces_ são utilizados para evitar conflitos de nomes no código. Eles ajudam a deixar as coisas organizadas. No PHP, um _namespace_ determina onde o arquivo PHP está localizado na hierarquia do _namespace_. Assim, o nome da classe pode ser reutilizado em _namespaces_ diferentes.

> Para referenciar uma classe, usamos a palavra-chave `::class`. Ex.: `$this->get(ClassName::class);` ou `$this->get(\Magento\Path\To\Class::class);`.

#### Semântica

1. Para nomes de atributos e valores, deve-se utilizar palavras escritas em minúsculo, não abreviadas, com caracteres do Latin e concatenadas com hífen (-)
Exemplo:
```html
<section id="information-dialog-tree">
   <p> ... </p>
   <p> ... </p>
</section>
<a href="#information-dialog-tree">Scroll to text</a></a>
```

Alguns diretórios já estão definidos, por convenção, para serem responsáves por certas funcionalidades. Eles foram descritos nesta seção. Contudo, um módulo pode conter um diretório específico para alguma funcionalidade pouco comum. Nestes casos, é necessário inspecionar o diretório para averiguar a funcionalidade pela qual ele é responsável.

O _namespace_ e o nome da classe auxiliam para identificar o arquivo. Por exemplo, o caminho do arquivo da classe PHP `TestCommand` do namespace `Magenteiro\PrimeiroModulo\Console\Command` é o `app/code/Magenteiro/PrimeiroModulo/Console/Command/TestCommand.php`.

## 1.3 Utilizar configuração e escopo de variáveis de configuração

### Determinar como usar os arquivos de configuração na Magento. Quais arquivos de configuração são importantes no ciclo de desenvolvimento?

O Magento 2 dividiu as configurações em vários arquivos XML, isso evita que tenhamos apenas um arquivo muito longo. Adicionalmente, podemos separar os arquivos em áreas, restringindo determinada configuração à área a qual ela afeta.
Logo abaixo, estão relacionados os principais arquivos de configuração do Magento. Eles estão localizados no diretório `/etc` do módulo.

#### `module.xml`

É o único arquivo de configuração obrigatório. Nele são especificados a versão do módulo e sua ordem de carregamento.

#### `acl.xml`

_Access Control List Rules (ACL)_. Aqui são definidas as permissões de acesso dos usuários à recursos. 

#### `config.xml`

Carregado na configuração padrão (`Stores > Configuration`). 

#### `crontab.xml`

Para os trabalhos agendados.

#### `di.xml`

É um dos arquivos mais utilizados quando estamos personalizando o Magento. Aqui são configuradas as injeções de dependência: plugins são definidos, classes são substituídas, classes concretas são especificadas, dentre outros.

#### `email_templates.xml`

Determina os templates de e-mail que serão usados no Magento.

#### `events.xml`

Esse arquivo frequentemente é criado dentro de uma área específica. Nele são registrados os _event listeners_.

#### `indexer.xml`

Configura os indexadores do Magento.

#### `adminhtml/menu.xml`

Configura um menu na área adminhtml.

#### `adminhtml/system.xml`

Aqui são definidas as _tabs_, _sections_, _groups_ e _fields_ que existem na configuração da loja (`Stores > Configuration`).

#### `mview.xml`

Usado frequentemente para o processo de indexação, aqui é configurado o disparo de um evento quando uma coluna do banco de dados é alterada.

#### `<area>/routes.xml`

Diz ao Magento que a área aceita _web requests_. O nó da rota configura a primeira parte do _layout handle_ (ID da rota) e o _frontname_ (primeira parte da URL após o domínio).

#### `view.xml`

Especifica valores padrão para configurações relacionadas ao design. É semelhante ao `config.xml`.

#### `webapi.xml`

Configura a web API. 

#### `widget.xml`

Configura os widgets para serem usados com páginas ou blocos CMS e produtos.

#### Mais arquivos de configuração: [DevDocs - Module configuration files](https://devdocs.magento.com/guides/v2.2/config-guide/config/config-files.html#config-files-classes-objects)


### Descrever o desenvolvimento no contexto dos escopos website e store. Como você pode identificar o escopo de configuração para uma certa variável? Como os escopos nativos da Magento (por exemplo, preço ou estoque) podem afetar o desenvolvimento e o processo de tomada de decisão?

### Demonstrar capacidade de adicionar valores diferentes para diferentes escopos. Como você pode buscar o valor de uma configuração do sistema por meio de programação? Como você pode substituir os valores de uma configuração do sistema para uma determinada loja usando a configuração XML?

## 1.4 Demonstrate how to use dependency injection (DI) 
Demonstrate the ability to use the dependency injection concept in Magento development. How are objects realized in code? Why is it important to have a centralized object creation process?
Identify how to use DI configuration files for customizing Magento. How can you override a native class, inject your class into another object, and use other techniques available in di.xml (for example, virtualTypes)? 
Given a scenario, determine how to obtain an object using the ObjectManager object. How would you obtain a class instance from different places in the code? 

## 1.5 Demonstrate ability to use plugins 
Demonstrate an understanding of plugins. How are plugins used in core code? How can they be used for customizations? 

## 1.6 Configure event observers and scheduled jobs 
Demonstrate how to create a customization using an event observer. How are observers registered? How are they scoped for frontend or backend? How are automatic events created, and how should they be used? How are scheduled jobs configured?

## 1.7 Utilize the CLI 
Describe the usage of bin/magento commands in the development cycle. Which commands are available? How are commands used in the development cycle? 

## 1.8 Describe how extensions are installed and configured 
How would you install and verify an extension by a customer’s request?

