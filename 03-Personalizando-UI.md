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

### 3.2 Demonstrate an ability to create UI customizations using a combination of a block and template 

**How do you assign a template to a block?**
**How do you assign a different template to a native block?**

### 3.3 Identify the uses of different types of blocks 
**When would you use non-template block types?**

### 3.4 Describe the elements of the Magento layout XML schema, including the major XML directives 
**How do you use layout XML directives in your customizations?**
**How do you register a new layout file?**

### 3.5 Create and add code and markup to a given page 
**How do you add new content to existing pages using layout XML?**
