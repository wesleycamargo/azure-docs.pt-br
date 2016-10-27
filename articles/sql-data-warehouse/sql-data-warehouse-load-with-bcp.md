<properties
   pageTitle="Usar o bcp para carregar dados no SQL Data Warehouse | Microsoft Azure"
   description="Saiba o que é o bcp e como usá-lo em cenários de data warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/10/2016"
   ms.author="mausher;barbkess;sonyama"/>



# <a name="load-data-with-bcp"></a>Carregar dados com o bcp

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)  
- [Fábrica de dados](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
- [BCP](sql-data-warehouse-load-with-bcp.md)


**[bcp][]** é um utilitário de carregamento em massa de linha de comando que permite copiar dados entre o SQL Server, arquivos de dados e o SQL Data Warehouse. Use o bcp para importar grandes quantidades de linhas nas tabelas do SQL Data Warehouse ou para exportar dados das tabelas do SQL Server em arquivos de dados. Exceto quando usado com a opção queryout, o bcp não exige conhecimento em Transact-SQL.

O bcp é uma maneira rápida e fácil de mover conjuntos de dados menores para dentro e fora de um banco de dados do SQL Data Warehouse. O volume exato de dados que é recomendado para carregamento/extração por meio do bcp dependerá da sua conexão de rede com o datacenter do Azure.  Geralmente, as tabelas de dimensão podem ser carregadas e extraídas prontamente com bcp, no entanto, não recomendamos bcp para carregar ou extrair grandes volumes de dados.  Polybase é a ferramenta recomendada para carregar e extrair grandes volumes de dados, pois aproveita melhor a arquitetura de processamento extremamente paralelo do SQL Data Warehouse.

Com o bcp, você pode:

- Usar um utilitário de linha de comando simples para carregar dados no SQL Data Warehouse.
- Usar um utilitário de linha de comando simples para extrair dados do SQL Data Warehouse.

Este tutorial mostrará como:

- Importar dados em uma tabela usando o comando bcp in
- Exportar dados de uma tabela usando o comando bcp out

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar este tutorial, você precisará:

- Um banco de dados do SQL Data Warehouse.
- O utilitário de linha de comando bcp instalado
- O utilitário de linha de comando SQLCMD instalado

>[AZURE.NOTE] Você pode baixar os utilitários bcp e sqlcmd do [Centro de Download da Microsoft][].

## <a name="import-data-into-sql-data-warehouse"></a>Importar dados no SQL Data Warehouse

Neste tutorial, você criará uma tabela no SQL Data Warehouse e importará dados na tabela.

### <a name="step-1:-create-a-table-in-azure-sql-data-warehouse"></a>Etapa 1: Criar uma tabela no SQL Data Warehouse do Azure

Em um prompt de comando, use sqlcmd para executar a consulta a seguir e criar uma tabela em sua instância:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

>[AZURE.NOTE] Consulte [Visão Geral da Tabela][] ou a [sintaxe CREATE TABLE][] para obter mais informações sobre como criar uma tabela no SQL Data Warehouse e ver as opções disponíveis na cláusula WITH.

### <a name="step-2:-create-a-source-data-file"></a>Etapa 2: Criar um arquivo de dados de origem

Abra o Bloco de Notas e copie as seguintes linhas de dados em um novo arquivo de texto. Em seguida, salve esse arquivo em seu diretório temporário local, c:\Temp\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [AZURE.NOTE] É importante lembrar que o bcp.exe não oferece suporte a codificação do arquivo UTF-8. Use arquivos ASCII ou arquivos codificados em UTF-16 ao usar o bcp.exe.

### <a name="step-3:-connect-and-import-the-data"></a>Etapa 3: Conectar e importar os dados
Usando o bcp, você pode conectar e importar os dados usando o comando a seguir, substituindo os valores conforme apropriado:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Você pode verificar se os dados foram carregados executando a consulta a seguir usando o sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Isso deve retornar os seguintes resultados:

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

### <a name="step-4:-create-statistics-on-your-newly-loaded-data"></a>Etapa 4: criar estatísticas sobre os dados recém-carregados

O SQL Data Warehouse do Azure ainda não dá suporte a estatísticas de criação ou atualização automática. Para obter o melhor desempenho de suas consultas, é importante que as estatísticas sejam criadas em todas as colunas de todas as tabelas após o primeiro carregamento ou após uma alteração significativa nos dados. Para obter uma explicação detalhada das estatísticas, confira o tópico [Estatísticas][] no grupo de tópicos Desenvolver. Veja abaixo um exemplo de como criar estatísticas na tabela carregada neste exemplo

Execute as seguintes instruções CREATE STATISTICS em um prompt de sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Exportar dados do SQL Data Warehouse
Neste tutorial, você criará um arquivo de dados de uma tabela no SQL Data Warehouse. Exportaremos os dados que criamos acima para um novo arquivo de dados chamado DimDate2_export.txt.

### <a name="step-1:-export-the-data"></a>Etapa 1: Exportar os dados

Usando o utilitário bcp, você pode conectar e exportar dados usando o comando a seguir, substituindo os valores conforme apropriado:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Você pode verificar se os dados foram exportados corretamente abrindo o novo arquivo. Os dados no arquivo devem corresponder ao texto abaixo:

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

>[AZURE.NOTE] Devido à natureza dos sistemas distribuídos, a ordem dos dados pode não ser a mesma entre os bancos de dados do SQL Data Warehouse. Outra opção é usar a função **queryout** do bcp para escrever uma extração de consulta, em vez de exportar a tabela inteira.

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral do carregamento, consulte [Carregar dados no SQL Data Warehouse][].
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento do SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->

[Carregar dados no SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[Visão geral de desenvolvimento do SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Visão Geral da Tabela]: ./sql-data-warehouse-tables-overview.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[Sintaxe CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Centro de Download da Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433



<!--HONumber=Oct16_HO2-->


