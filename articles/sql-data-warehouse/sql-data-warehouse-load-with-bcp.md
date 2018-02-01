---
title: Usar o bcp para carregar dados no SQL Data Warehouse | Microsoft Docs
description: "Saiba o que é o bcp e como usá-lo em cenários de data warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/22/2018
ms.author: cakarst;barbkess
ms.openlocfilehash: 55211e29149cd334421bd8723d47278a19afbfbb
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="load-data-with-bcp"></a>Carregar dados com o bcp

**[bcp](/sql/tools/bcp-utility.md)** é um utilitário de carregamento em massa de linha de comando que permite copiar dados entre o SQL Server, arquivos de dados e o SQL Data Warehouse. Use o bcp para importar grandes quantidades de linhas nas tabelas do SQL Data Warehouse ou para exportar dados das tabelas do SQL Server em arquivos de dados. Exceto quando usado com a opção queryout, o bcp não exige conhecimento em Transact-SQL.

O bcp é uma maneira rápida e fácil de mover conjuntos de dados menores para dentro e fora de um banco de dados do SQL Data Warehouse. O volume exato de dados que é recomendado para carregamento/extração por meio do bcp depende da sua conexão de rede com o Azure.  Tabelas de dimensões pequenas podem ser carregadas e extraídas prontamente com o bcp. No entanto, o Polybase, não o bcp, é a ferramenta recomendada para carregar e extrair grandes volumes de dados. O PolyBase foi projetado para a arquitetura de processamento paralelo em massa do SQL Data Warehouse.

Com o bcp, você pode:

* Usar um utilitário de linha de comando para carregar dados no SQL Data Warehouse.
* Usar um utilitário de linha de comando para extrair dados do SQL Data Warehouse.

Este tutorial:

* Importar dados em uma tabela usando o comando bcp in
* Exportar dados de uma tabela usando o comando bcp out

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>pré-requisitos
Para acompanhar este tutorial, você precisará:

* Um banco de dados do SQL Data Warehouse.
* utilitários de linha de comando bcp e sqlcmd. Você pode baixá-los no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=36433). 

### <a name="data-in-ascii-or-utf-16-format"></a>Dados em formato ASCII ou UTF-16
Se você estiver experimentando este tutorial com seus próprios dados, eles precisarão usar a codificação ASCII ou UTF-16, já que bcp não oferece suporte a UTF-8. 

O PolyBase oferece suporte a UTF-8, mas ainda não oferece suporte a UTF-16. Para usar o bcp para exportação de dados e, em seguida, o PolyBase para carregamento de dados, você precisa transformar os dados para UTF-8 depois que eles são exportados do SQL Server. 

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

Para saber mais informações sobre como criar uma tabela no SQL Data Warehouse, consulte [Visão geral da tabela](sql-data-warehouse-tables-overview.md) ou a sintaxe [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse.md).
 

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

A consulta deve retornar os seguintes resultados:

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
Após o carregamento de dados, uma última etapa é criar ou atualizar estatísticas. Isso ajuda a melhorar o desempenho da consulta. Para obter mais informações, consulte [Estatísticas](sql-data-warehouse-tables-statistics.md). O exemplo de sqlcmd a seguir cria estatísticas em uma tabela que contém os dados carregados recentemente.


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Exportar dados do SQL Data Warehouse
Neste tutorial, você cria um arquivo de dados a partir de uma tabela no SQL Data Warehouse. Ele exporta os dados importados na seção anterior. Os resultados vão para um arquivo chamado DimDate2_export.txt.

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
Para criar o processo de carregamento, consulte a [Visão geral de carregamento] (sql-data-warehouse-design-elt-data-loading].  



<!--MSDN references-->



<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
