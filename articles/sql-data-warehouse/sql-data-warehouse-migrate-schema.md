<properties
   pageTitle="Migrar seu esquema para o SQL Data Warehouse | Microsoft Azure"
   description="Dicas para migrar seu esquema para o SQL Data Warehouse do Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Migrar seu esquema para o SQL Data Warehouse#

Os resumos a seguir ajudarão você a entender as diferenças entre o SQL Server e o SQL Data Warehouse para ajudar na migração do banco de dados.

### Recursos de tabela
O SQL Data Warehouse não usa nem oferece suporte a estes recursos:

- Chaves primárias
- Chaves estrangeiras
- Restrições de verificação
- Restrições exclusivas
- Índices exclusivos
- Colunas computadas
- Colunas esparsas
- Tipos definidos pelo usuário
- Exibições indexadas
- Identidades
- Sequências
- Gatilhos
- Sinônimos


### Diferenças de tipo de dados
O SQL Data Warehouse oferece suporte a tipos comuns de dados corporativos:

- bigint
- binário
- bit
- char
- data
- datetime
- datetime2
- datetimeoffset
- decimal
- flutuante
- int
- money
- nchar
- nvarchar
- numérico
- real
- smalldatetime
- smallint
- smallmoney
- sysname
- tempo real
- tinyint
- varbinary
- varchar
- uniqueidentifier

Você pode usar essa consulta para identificar colunas em seu data warehouse que contenham tipos incompatíveis:

```sql
SELECT  t.[name]
,       c.[name]
,       c.[system_type_id]
,       c.[user_type_id]
,       y.[is_user_defined]
,       y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN
                (   'geography'
                ,   'geometry'
                ,   'hierarchyid'
                ,   'image'
                ,   'ntext'
                ,   'sql_variant'
                ,   'text'
                ,   'timestamp'
                ,   'xml'
                )
AND  y.[is_user_defined] = 1
;

```

A consulta inclui todos os tipos de dados definidos pelo usuário que também não têm suporte.

Caso você tenha tipos sem suporte no seu banco de dados, não se preocupe. Veja abaixo algumas alternativas propostas.

Em vez de:

- **geometry**, use um tipo varbinary
- **geography**, use um tipo varbinary
- **hierarchyid**, esse tipo de CLR não tem suporte
- **image**, **text**, **ntext**, use varchar/nvarchar (quanto menor, melhor)
- **sql\_variant**, divida a coluna em várias colunas fortemente tipadas
- **table**, converta em tabelas temporárias
- **timestamp**, retrabalhe o código para usar datetime2 e a função `CURRENT_TIMESTAMP`. Observe que você não pode ter current\_timestamp como uma restrição padrão e o valor não será atualizado automaticamente. Se precisar migrar valores de versão de linha de uma coluna tipada timestamp, use binary(8) ou varbinary(8) para valores de versão de linha NOT NULL ou NULL.
- **tipos definidos pelo usuário**, converta de volta aos tipos nativos sempre que possível
- **xml**, use um varchar(max) ou menor para melhor desempenho. Divida entre colunas, se necessário.

Para obter melhor desempenho, em vez de:

- nvarchar(max), use nvarchar(4000) ou menor para um melhor desempenho
- varchar(max), use varchar(8000) ou menor para melhor desempenho

Suporte parcial:

- As restrições padrão oferecem suporte apenas a literais e constantes. Não há suporte para expressões ou funções não determinísticas como `GETDATE()` ou `CURRENT_TIMESTAMP`.

> [AZURE.NOTE] Se estiver usando o Polybase para carregar suas tabelas, defina as tabelas para que o tamanho máximo possível da linha, incluindo o comprimento total das colunas de tamanho variável, não exceda 32.767 bytes. Enquanto é possível definir uma linha com dados de tamanho variável que possa exceder essa figura e carregar linhas com BCP, ainda não será possível usar Polybase para carregar dados. O suporte a Polybase para linhas amplas será adicionado em breve. Além disso, tente limitar o tamanho de suas colunas de tamanho variável para uma taxa de transferência ainda melhor na execução de consultas.

## Próximas etapas
Depois de migrar com êxito o esquema do seu banco de dados para o SQLDW, você poderá passar para um dos artigos a seguir:

- [Migrar seus dados][]
- [Migrar seu código][]

Para obter mais dicas de desenvolvimento, consulte a [visão geral de desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Migrar seu código]: sql-data-warehouse-migrate-code.md
[Migrar seus dados]: sql-data-warehouse-migrate-data.md
[visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0810_2016-->