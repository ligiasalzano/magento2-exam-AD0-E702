---
title: Trabalhando com Banco de Dados
description: Trabalhando com Banco de Dados no Magento 2
permalink: /trabalhando-com-banco-de-dados
---

## Descrever os conceitos básicos de models, resource models e collections

### Quais são as responsabilidades de cada um dos tipos de objeto ORM (Object Relational Mapping ou Mapeamento Objeto Relacional)? Como eles se relacionam uns com os outros?

Os objetos ORM são construídos em torno de _models_, _resource models_ e _resource collections_.
Temos quatro elementos no Magento:
- **_Models_**
  - Definem os dados e comportamento de entidades.
  - São uma camada de abstração de dados em forma de objeto (classe)
  - Models não tem acesso direto ao banco de dados, apenas os resource models.
- **_Resources_**
  - Conecta com o banco de dados através de adaptadores.
- **_Resource Models_**
  - Mapeadores de dados para estruturas de armazenamento.
  - _Resource Models_ são responsáveis por:
    - Executar todas operações CRUD (Criar, Ler, Atualizar, Apagar registros)
    - Lógica de negócios. Um Resource Model pode fazer validações dos dados, iniciar processos antes ou depois que um dado é salvo, ou realizar outras operações no banco.
- **_Collections_**
  - Armazena conjuntos de modelos e funcionalidades relacionadas, incluindo filtragem, classificação e paginação.
  - classe que carrega vários models e devolve (geralmente) um objeto como um array ou algo parecido.

## Descrever como ocorre a leitura e gravação de uma entidade

### Como você utiliza o método nativo de gravação/leitura no processo de desenvolvimento? 

#### Processo de carregamento (_load process_)
- Primeiro o método `beforeLoad` é chamado (esse ponto é ótimo para usar um plugin). 
- Cria conexão com o banco de dados e carrega uma consulta de seleção usando parâmetros para buscar a linha e definir os dados.
- O método `afterLoad` é chamado (bom para plugins)
- O objetos é retornado.

#### Processo de salvamento (_save process_)

- Uma transação é iniciada
- Se nada foi modificado na entidade, a transação é confirmada e o método retornado
- `beforeSave` é chamado (este é um bom ponto para plugins)
- Se o salvamento for permitido, a representação do banco de dados é atualizada ou criada
- O método `afterSave` é chamado. Quaisquer exceções lançadas na execução deste método cancelarão a transação.

## Descrever como filtrar, ordenar e especificar os valores selecionados para _collections_ e _repositories_

### Como você seleciona um subconjunto de registros do banco de dados?

Describe how to filter, sort, and specify the selected values for collections and repositories 
**How do you select a subset of records from the database?**

## Demonstrar habilidade para usar o esquema declarativo (_declarative schema_)

Ler: https://devdocs.magento.com/guides/v2.3/extension-dev-guide/declarative-schema/


### Como você adiciona uma coluna usando o declarative schema? 

O nó `column` adiciona uma coluna. Conforme no exemplo abaixo, que está adicionando a coluna _date_closed_:
```xml
<schema xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                 xsi:noNamespaceSchemaLocation="urn:magento:framework:Setup/Declaration/Schema/etc/schema.xsd">
    <table name="declarative_table">
        <column xsi:type="int" name="id_column" padding="10" unsigned="true" nullable="false" comment="Entity Id"/>
        <column xsi:type="int" name="severity" padding="10" unsigned="true" nullable="false" comment="Severity code"/>
        <column xsi:type="varchar" name="title" nullable="false" length="255" comment="Title"/>
        <column xsi:type="timestamp" name="time_occurred" padding="10" comment="Time of event"/>
+       <column xsi:type="timestamp" name="date_closed" padding="10" comment="Time of event"/>
        <constraint xsi:type="primary" referenceId="PRIMARY">
            <column name="id_column"/>
        </constraint>
    </table>
</schema>

```


### Como você modifica uma tabela adicionada por outro módulo? 
### Como você exclui uma coluna? 
### Como você adiciona um índice ou chave estrangeira usando o esquema declarativo?
### Como você manipula dados usando patches de dados? 
### Qual é o propósito dos patches de esquema?

**How do you add a column using declarative schema?**
**How do you modify a table added by another module?**
**How do you delete a column?**
**How do you add an index or foreign key using declarative schema?**
**How do you manipulate data using data patches?**
**What is the purpose of schema patches?**
