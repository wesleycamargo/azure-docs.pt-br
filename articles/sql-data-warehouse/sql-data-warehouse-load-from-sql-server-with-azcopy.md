---
title: Carregar dados do SQL Server no Azure SQL Data Warehouse (PolyBase) | Microsoft Docs
description: Usa o bcp para exportar dados do SQL Server para arquivos simples, o AZCopy para importar dados no Armazenamento de Blobs do Azure e o PolyBase para incluir os dados no Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4d42786a-fb28-43c9-9c3b-72d19c0ecc11
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: cakarst;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f3a4ad30d1aa0ec273b6b875b0d2d037005ac159


---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>Carregar dados do SQL Server no Azure SQL Data Warehouse (AZCopy)
Use os utilitários de linha de comando bcp e AZCopy para carregar dados do SQL Server no Armazenamento de Blobs do Azure. Depois, use o PolyBase ou o Azure Data Factory para carregar os dados Azure SQL Data Warehouse. 

## <a name="prerequisites"></a>Pré-requisitos
Para acompanhar este tutorial, você precisará:

* Um banco de dados do SQL Data Warehouse.
* O utilitário de linha de comando bcp instalado
* O utilitário de linha de comando SQLCMD instalado

> [!NOTE]
> Você pode baixar os utilitários bcp e sqlcmd do [Centro de Download da Microsoft][Centro de Download da Microsoft].
> 
> 

## <a name="import-data-into-sql-data-warehouse"></a>Importar dados no SQL Data Warehouse
Neste tutorial, você criará uma tabela no SQL Data Warehouse e importará dados na tabela.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Etapa 1: Criar uma tabela no SQL Data Warehouse do Azure
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

> [!NOTE]
> Consulte [Visão Geral da Tabela][Visão Geral da Tabela] ou [sintaxe CREATE TABLE][sintaxe CREATE TABLE] para obter mais informações sobre como criar uma tabela no SQL Data Warehouse e ver as opções disponíveis na cláusula WITH.
> 
> 

### <a name="step-2-create-a-source-data-file"></a>Etapa 2: Criar um arquivo de dados de origem
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

> [!NOTE]
> É importante lembrar que o bcp.exe não oferece suporte a codificação do arquivo UTF-8. Use arquivos ASCII ou arquivos codificados em UTF-16 ao usar o bcp.exe.
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>Etapa 3: Conectar e importar os dados
Usando o bcp, você pode conectar e importar os dados usando o comando a seguir, substituindo os valores conforme apropriado:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Você pode verificar se os dados foram carregados executando a consulta a seguir usando o sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Isso deve retornar os seguintes resultados:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Etapa 4: criar estatísticas sobre os dados recém-carregados
O SQL Data Warehouse do Azure ainda não dá suporte a estatísticas de criação ou atualização automática. Para obter o melhor desempenho de suas consultas, é importante que as estatísticas sejam criadas em todas as colunas de todas as tabelas após o primeiro carregamento ou após uma alteração significativa nos dados. Para obter uma explicação detalhada das estatísticas, confira o tópico [Estatísticas][Estatísticas] no grupo de tópicos Desenvolver. Veja abaixo um exemplo de como criar estatísticas na tabela carregada neste exemplo

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

### <a name="step-1-export-the-data"></a>Etapa 1: Exportar os dados
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

> [!NOTE]
> Devido à natureza dos sistemas distribuídos, a ordem dos dados pode não ser a mesma entre os bancos de dados do SQL Data Warehouse. Outra opção é usar a função **queryout** do bcp para escrever uma extração de consulta, em vez de exportar a tabela inteira.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral do carregamento, consulte [Carregar dados no SQL Data Warehouse][Carregar dados no SQL Data Warehouse].
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento do SQL Data Warehouse][Visão geral de desenvolvimento do SQL Data Warehouse].

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



<!--HONumber=Nov16_HO2-->


