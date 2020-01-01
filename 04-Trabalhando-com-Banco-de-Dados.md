## 4. Trabalhando com Banco de Dados

### 4.1 Descrever os conceitos básicos de models, resource models e collections

#### Quais são as responsabilidades de cada um dos tipos de objeto ORM (Object Relational Mapping ou Mapeamento Objeto Relacional)? Como eles se relacionam uns com os outros?

Os objetos ORM são construídos em torno de _models_, _resource models_ e _resource collections_.
Temos quatro elementos no Magento:
- **_Models_**
  - Definem os dados e comportamento de entidades.
  - São uma camada de abstração de dados em forma de objeto (classe)
  - Models não tem acesso direto ao banco de dados, apenas os resource models.
  
- **_Resource Models_**
  - Mapeadores de dados para estruturas de armazenamento.
  - _Resource Models_ são responsáveis por:
    - Executar todas operações CRUD (Criar, Ler, Atualizar, Apagar registros)
    - Lógica de negócios. Um Resource Model pode fazer validações dos dados, iniciar processos antes ou depois que um dado é salvo, ou realizar outras operações no banco.
- **_Collections_**: Armazena conjuntos de modelos e funcionalidades relacionadas, incluindo filtragem, classificação e paginação.
- **_Resources_**: Conecta com o banco de dados através de adaptadores.




**What are the responsibilities of each of the ORM object types?**
**How do they relate to one another?**

### 4.2 Describe how entity load and save occurs 
**How do you use the native Magento save/load process in the development process?**

### 4.3 Describe how to filter, sort, and specify the selected values for collections and repositories 
**How do you select a subset of records from the database?**

### 4.4 Demonstrate an ability to use declarative schema 
**How do you add a column using declarative schema?**
**How do you modify a table added by another module?**
**How do you delete a column?**
**How do you add an index or foreign key using declarative schema?**
**How do you manipulate data using data patches?**
**What is the purpose of schema patches?**
