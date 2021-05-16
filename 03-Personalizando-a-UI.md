---
title: Personalizando a Interface do Usuário (UI)
description: Personalizando a Interface do Usuário (UI) no Magento 2
permalink: /personalizando-a-ui
---

1. Demonstrar a habilidade de personalizar a UI da Magento usando temas
2. Demonstrar habilidade para criar personalizações na interface do usuário usando uma combinação de um bloco e template
3. Identifique os usos dos diferentes tipos de blocos
4. Descrever os elementos do esquema de layout XML da Magento, incluindo as principais diretivas XML
5. Criar e adicionar código e marcação em uma determinada página
{:toc}

## Demonstrar a capacidade de personalizar a UI da Magento usando temas

**Quando você deve criar um novo tema?**

Os temas englobam um conjunto de modificações na interface dos usuários. Eles podem extender e personalizar um tema disponível ou serem totalmente independentes.
Criar um tema envolve copiar e modificar alguns templates, criar arquivos XML para ajustar layouts e a criação de estilos para adequar a loja ao projeto definido.
Lojistas podem usar um tema comprado ou desenvolvido especialmente para eles, dependendo das suas necessidades.
Os temas `Magento/blank` e `Magento/luma` acompanham a instalação do Magento e podem ser encontrados na pasta vendor.
Os temas customizados são localizados em `app/design/frontend/` ou, se for um tema para o admin, em `app/design/adminhtml/`.
Dentro desses diretórios, encontramos a pasta do pacote do tema e, dentro dela, a pasta do tema propriamente dito.
Os únicos arquivos obrigatórios para criar um tema são o `registration.php` e o `theme.xml`.

**Como você define a hierarquia do tema em um projeto?**

O tema pai é definido no nó `<parent>` dentro do arquivo `theme.xml`.

## Demonstrar habilidade para criar personalizações na interface do usuário usando uma combinação de um bloco e template

**Como você atribui um template a um bloco?**

Um template é atribuído à um bloco através de um arquivo de layout XML.
```xml
<block class="Namespace\Of\Your\Class"
       name="blockName"
       template="Module_Name::path/to/your/template.phtml"
/>
```
> Observação: o caminho para o template phtml é descrito a partir do diretório `templates/`.


**Como você atribui um template diferente a um bloco nativo?**

A substituição de um arquivo de template de um bloco já existente também é feita em um arquivo de layout XML:

```xml
<referenceBlock name="block_to_change" template="Vendor_Module::/path/to/template.phtml" />
```

ou

```xml
<referenceBlock name="block_to_change">
    <arguments>
        <argument name="template" xsi:type="string">[Vendor]_[Module]::/path/to/template.phtml</argument>
    </arguments>
</referenceBlock>
```

## Identifique os usos dos diferentes tipos de blocos

**Quando você usaria os tipos de bloco "sem template"?**

Quando usamos renderizadores simples. Quando o conteúdo do bloco é gerado dinamicamente ou armazenado em um banco de dados ou em contêineres.

- **Text**: imprime uma _string_. Caminho: `vendor/magento/framework/View/Element/Text.php`.
- **ListText**: seria como um contêiner que retorna cada um dos blocos filhos. Caminho: `vendor/magento/framework/View/Element/Text/ListText.php`.
- **Templates**: Renderiza um HTML para o usuário. Caminho: `vendor/magento/framework/View/Element/Template.php`.

> Com os contêineres de layout do Magento, os casos de uso de blocos "sem template" são muito poucos. 

## Descrever os elementos do esquema de layout XML da Magento, incluindo as principais diretivas XML

**Como você usa as diretivas do layout XML em suas customizações?**

As diretivas do layout XML da Magento contém instruções para:
- Adicionar e excluir recursos estáticos (JavaScript, CSS, fontes) na _head_ da página;
  - Isso pode ser feito no arquivo `<theme_dir>/Magento_Theme/layout/default_head_blocks.xml`
- Criar e modificar contêineres;
  - Para criar, usa-se o nó _container_:
    ```xml
      <container name="you.container" as="youContainer" label="My Container" htmlTag="div" htmlClass="my-container" />
    ```
  - Para modificar, usamos o nó _referenceContainer_: 
     ```xml
       <referenceContainer name="header.panel">
           <block class="YouVendor\YouModule\Block\YouBlock" name="new.block" />
       </referenceContainer>
       ```
- Criar e modificar blocos;
  - Para criar, com o nó _block_: 
     ```xml
     <block class="Magento\Catalog\Block\Product\View\Description" name="product.info.sku" template="product/view/attribute.phtml" after="product.info.type" />
     ```
  - E para modificar, aplicamos o _referenceBlock_:
       ```xml
       <!-- Bloco original -->
       <block class="Namespace_Module_Block_Type" name="block.example">
           <arguments>
               <argument name="label" xsi:type="string">Block Label</argument>
           </arguments>
       </block>

      <!-- Modificando o bloco -->
       <referenceBlock name="block.example">
           <arguments>
               <!-- modificando um argumento preexistente -->
               <argument name="label" xsi:type="string">New Block Label</argument>
               <!-- Novo argumento -->
               <argument name="custom_label" xsi:type="string">Custom Block Label</argument>
           </arguments>
       </referenceBlock>
       ```
- Definir _templates_ para blocos;
  - Método 1 - usando o atributo _template_ (mais forte): `<referenceBlock name="new.template" template="Your_Module::new_template.phtml"/>`
  - Método 2 - usando um argumento com _name_ _template_:
  ```xml
       <referenceBlock name="new.template">
            <arguments>
                <argument name="template" xsi:type="string">Your_Module::new_template.phtml</argument>
            </arguments>
        </referenceBlock>
       ```
- Passar argumentos em blocos; 
- Alterar a localização e a ordem dos elementos (blocos e contêineres):
  ```xml
     <move element="product.info.review" destination="product.info.main" before="product.info.price"/>
  ```
- Excluir elementos (blocos e contêineres).
  ```xml
     <referenceBlock name="catalog.compare.sidebar" remove="true" />
  ```

> Observação: lembrando que o caminho para o arquivo template.phtml é `<module_dir>/view/<area>/templates` ou `<theme_dir>/<Vendor_Module>/templates`

**[Instruções gerais de layout](https://devdocs.magento.com/guides/v2.4/frontend-dev-guide/layouts/xml-instructions.html#fedg_layout_xml-instruc_ex)**
- `<block>`: Criação de blocos. A classe padrão é a `Magento\Framework\View\Element\Template`.
- `<container>`: Um agrupamento de blocos (e outros contêiners). Nele pode-se especificar a tag html que englobará os blocos. Se não houver blocos dentro dele, ele não será mostrado.
- Atributos _before_ e _after_: Ajuda a definir o posicionamento dos elementos em relação à outros
- `<arguments>` e `<argument>`: `<argument>` é usado para passar um argumento. Ele fica encapsulado em `<arguments>`. O argumento define um valor para o _array_ de dados do bloco.
- `<referenceBlock>` e `<referenceContainer>`: Referencia um bloco ou contêiner existente para modificá-los.
- `<move>`: Define que um elemento declarado como filho de outro elemento na ordem especificada.
- `<remove>`: Usado para remover recursos estáticos do _head_ (e para remover um bloco ou contêiner, é usado o atributo _remove_ no `<referenceBlock>` ou `<referenceContainer>`).
- `<update>`: adiciona um arquivo de layout.

**Como você registra um novo arquivo de layout?**
Um novo _layout_ pode ser registrado seguindo estes 2 passos:
1. Crie um novo arquivo de layout de página em um tema personalizado: `app/design/frontend/<VendorName>/<ThemeName>/Magento_Theme/page_layout/new_layout_file.xml`:

```xml
<?xml version="1.0"?>
<layout xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_layout.xsd">
    <update handle="3columns"/>
    <referenceContainer name="page.wrapper">
        <container name="footer-bottom" as="footer-bottom" after="footer" label="Footer Bottom" htmlTag="footer" htmlClass="page-footer-bottom">
            <container name="footer-bottom-content" as="footer-bottom-content" htmlTag="div" htmlClass="footer content">
                <block class="Magento\Framework\View\Element\Template" name="report.bugs.bottom" template="Magento_Theme::html/bugreport.phtml"/>
            </container>
        </container>
    </referenceContainer>
</layout>
```

2. Registre o novo layout no arquivo `layouts.xml`, em `app/design/frontend/<VendorName>/<ThemeName>/Magento_Theme/layouts.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<page_layouts xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/PageLayout/etc/layouts.xsd">
    <layout id="3-columns-double-footer">
        <label translate="true">3 Columns Double Footer</label>
    </layout>
</page_layouts>
```

Observe que o valor do novo atributo layout id deve corresponder ao nome do arquivo XML de layout de página recém-criado.

Veja com mais detalhes [aqui na documentação](https://devdocs.magento.com/guides/v2.4/frontend-dev-guide/layouts/layout-create.html).

Além disso, pode-se registrar um novo _handle_:
- em um arquivo de layout, usando a tag `<update>`
- após do registro de uma nova rota, onde o _handle_ é criado seguindo o padrão: `routeId_controller_action.xml`


## Criar e adicionar código e marcação em uma determinada página

**Como você adiciona um novo conteúdo em uma página existente usando o layout XML?**

Pode-se criar um novo arquivo de layout XML no tema ou módulo para modificar blocos e containers. O nome do arquivo será o nome do handle da página à ser atualizada.
Então pode-se usar as instruções descritas acima (e na [documentação](https://devdocs.magento.com/guides/v2.4/frontend-dev-guide/layouts/xml-instructions.html)) para realizar as modificações desejadas.

Você pode criar um novo arquivo de layout em `<module_dir>/view/<area>/layout/<layout-handle>.xml` ou em `<theme_dir>/<Vendor>_<Module>/layout/<layout-handle>.xml`. Nesse arquivo, nomeado com o _handle_ que você definiu, deve conter a declaração do XML e o nó `<page>` como raiz do arquivo:

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    ...
</page>
```


