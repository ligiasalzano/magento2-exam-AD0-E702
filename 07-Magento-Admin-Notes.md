## UNIT 1

Dois menus importantes: **Store** e **System**

- **Data Grids**:
  - Fixed Data Grids. Ex.: Cart Rules
  - Customizable Grids. Ex.: Products
    - Podemos add colunas para algum atributo, de acordo com o escopo.
    
#### Aplication Architeture

- **Aplication scopes**
  - Global scope
    - configurações de todo o aplicativo
  - Websites
    - Website scope: configurações amplas para grandes unidades de negócios
    - métodos de entrega e pagamentos exclusivos para cada website
    - opções para base de preços e URLs únicos, também
  - Stores
    - Lojas permitem catálogos únicos para cada loja, mas são gerenciadas pelo mesmo Admin (exemplo: um catálogo para adultos e outro para crianças)
    - Aqui é definida a "root category"
  - Store Views
    - o nível mais baixo para configurações
    - permite exibir o catálogo em diferentes idiomas e moedas, por exemplo

###### Criando websites, stores and store view: Stores > Settings > All Stores

###### Limpar o cache: System > Tools > Cache Management

###### Indexamento: System > Tools > Index Management

#### Users
  - System > All users and Add new user
  - System > User roles and Add new role
    - Role Resources: define ao que o usuário terá acesso
  - **Role Scopes** é exclusivo do Magento Commerce
    - serve para restringir os acessos de diferentes equipes (ex. uma loja que atende dois países e tem duas equipes)
    - você pode impedir o acesso de uma equipe à uma store view. Ou deixar uma visão acessível apenas para um grupo.
    - você também pode restringir o acesso à recursos da loja
    
#### Set Up for Business
- Locale settings
  - Idioma, fuso horário...
  - Stores > Configuration > General > General > Locale settings
- Store Information
  - Stores > Configuration > General > General > Store Information
  - Nome da loja, endereços
- Secure & Unsecure URLs
  - Stores > Configuration > General > Web > Base URLs
- Website title
  - Content > Design > Configuration > HTML Head
- Website Logo
  - Content > Design > Configuration > Header
- Website Copyright
  - Content > Design > Configuration > Footer
  - O tema tem que estar configurado para usar esse campo
- Store Communication Email Logos
  - Content > Design > Configuration > Emails
- Store Email Addresses
  - Stores > Configuration > General > Store Email Addresses
- Invoice, Shipment & Credit Memo Logos
  - Stores > Configuration > SALES > Sales > Invoice and Packing Slip Design
- Currency Options
  - Stores > Configuration > General > Currency Setup > Currency Options
- Currency Rates
  - Stores > Currency > Currency Rates 

## UNIT 2

- Categorias
  - Categorias são gerenciadas em **Catalog > Categories**
  - Há dois tipos de categorias: root category e subcategory
  - Categorias podem exibir somente produtos, somente material promocional ou uma combinação de ambos
  - subcategorias ficam aninhadas dentro de uma root category ou de outras subcategorias
  - O mesmo produto pode ser incluído em mais de uma categoria
  
- Category Landing Page Appearance
  - Page view: grid ou list view
  - Configurações de aparência da categoria são feitas em:
    - Store > Settings > Configuration > CATALOG > Catalog > Storefront
  - Os usuários podem ver os produtos numa categoria como lista ou como grid
  - Você pode controlar os valores de paginação 
  - Você pode escolher se o usuário poderá ver todos os produtos de uma categoria
  - Categorias podem ser ordenadas por atributos definidos pelo administrador

## UNIT 3

- Product Types
  - Simple products
    - o tipo mais básico
    - Exemplo: Uma bolsa
    - **Shopping Cart**: é visto em uma linha simples sem outras informações 
  - Grouped products
    - uma conveniência para o cliente
    - Ele mostra um grupo fixo de produtos simples em uma única página
    - Na página do produto só é possível especificar a quantidade de cada produto simples que você quer add ao carrinho
    - Produtos agrupados permitem que o usuário adicione vários produtos no carrinho em uma única vez, sem precisar ir em cada produto e adicioná-lo individualmente
    - **Shopping Cart**: Cada produto simples do grupo é adicionado em uma linha separada
  - Configurable products
    - Consiste em uma variação de produtos simples
    - o produto configurável não possui estoque. A quantidade em estoque e o status é definido pelos produtos simples que o compõe
    - **Shopping Cart**: O produto configurável é apresentado em uma linha onde é apresentada a variação escolhida (por trás dessa variação existe um produto simples).
  - Bundle products
    - Pacote de produtos são grupos de produtos em que o usuário tem liberdade para escolher quais gostaria de comprar dentro do pacote (de acordo com o que o admin configurou).
    - Os itens do pacote podem ser produtos simples ou virtuais sem opções personalizadas.
    - Os clientes podem "criar seus próprios" pacotes de produtos.
    - A tela de preço pode ser configurada para uma faixa de preço ou __As low as__
    - SKU e peso podem ser fixos ou dinâmicos ( o preço é dinâmico)
    - Os itens do pacote podem ser enviados juntos ou separadamente.
    - **No pedido**, o bundle product é apresentado com o SKU dos produtos simples concatenados
    - **Shopping Cart**: Uma linha linha simples com as informações da composição do pacote
  - Downloadable products
    - Permite comprar produtos digitais
    - pode exibir samples no topo da página do produto
    - **Shopping Cart**: uma linha simples com "downloads" abaixo do nome do produto
  - Virtual products
    - produtos que não são físicos e nem digitais. Exemplo: garantia.
    - **Shopping Cart**: é exibido como o produto simples
  - **Only Commerce**
    - [Gift Card](https://docs.magento.com/m2/ee/user_guide/catalog/product-gift-card-create.html)
- Product custom options
  - todos os produtos podem tem custom options. Exemplo: um campo de texto para digitar um nome que será gravado
