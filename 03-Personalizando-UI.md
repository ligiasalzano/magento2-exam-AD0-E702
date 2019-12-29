## 3. Personalizando a Interface do Usuário (UI)

### 3.1 Demonstrar a capacidade de personalizar a UI da Magento usando temas

#### Quando você deve criar um novo tema?

Os temas englobam um conjunto de modificações na interface dos usuários. Eles podem extender e personalizar um tema disponível ou serem totalmente independentes.
Criar um tema envolve copiar e modificar alguns templates, criar arquivos XML para ajustar layouts e a criação de estilos para adequar a loja ao projeto definido.
Lojistas podem usar um tema comprado ou desenvolvido especialmente para eles, dependendo das suas necessidades.
Os temas `Magento/blank` e `Magento/luma` acompanham a instalação do Magento e podem ser encontrados na pasta vendor.
Os temas customizados são localizados em `app/design/frontend/` ou, se for um tema para o admin, em `app/design/adminhtml/`.
Dentro desses diretórios, encontramos a pasta do pacote do tema e, dentro dela, a pasta do tema propriamente dito.
Os únicos arquivos obrigatórios para criar um tema são o `registration.php` e o `theme.xml`.

#### Como você define a hierarquia do tema em um projeto?

O tema pai é definido no nó `<parent>` dentro do arquivo `theme.xml`.

### 3.2 Demonstrar habilidade para criar personalizações na interface do usuário usando uma combinação de um bloco e template

#### Como você atribui um template a um bloco? 

Um template é atribuído à um bloco através de um arquivo de layout XML.
```xml
<block class="Namespace\Of\Your\Class"
       name="blockName"
       template="Module_Name::path/to/your/template.phtml"
/>
```
> Observação: o caminho para o template phtml é descrito a partir do diretório `templates/`.


#### Como você atribui um template diferente a um bloco nativo?

A substituição de um arquivo de template de um bloco já existente também é feita em um arquivo de layout XML:

```xml
<referenceBlock name="blockName">
    <action method="setTemplate">
        <argument name="template" xsi:type="string">
            Module_Name::path/to/your/template.phtml
        <argument>
    </action>
</referenceBlock>
```

### 3.3  Identifique os usos dos diferentes tipos de blocos

#### Quando você usaria os tipos de bloco "sem template"?

Quando usamos renderizadores simples. Quando o conteúdo do bloco é gerado dinamicamente ou armazenado em um banco de dados ou em contêineres.

- **Text**: imprime uma _string_. Caminho: `vendor/magento/framework/View/Element/Text.php`.
- **ListText**: seria como um contêiner que retorna cada um dos blocos filhos. Caminho: `vendor/magento/framework/View/Element/Text/ListText.php`.
- **Templates**: Renderiza um HTML para o usuário. Caminho: `vendor/magento/framework/View/Element/Template.php`.

> Com os contêineres de layout do Magento, os casos de uso de blocos "sem template" são muito poucos. 

### 3.4  Descrever os elementos do esquema de layout XML da Magento, incluindo as principais diretivas XML

#### Como você usa as diretivas do layout XML em suas customizações? 

#### Como você registra um novo arquivo de layout?

- Dentro do diretório do seu módulo (`app/code/MyCompany/MyModule/`), crie o diretório `view/frontend/layout`.
- Crie o arquivo xml para o _handle_ que você quer modificar. Ex.: `cms_index_index.xml`


**How do you use layout XML directives in your customizations?**
**How do you register a new layout file?**

### 3.5 Create and add code and markup to a given page


**How do you add new content to existing pages using layout XML?**
