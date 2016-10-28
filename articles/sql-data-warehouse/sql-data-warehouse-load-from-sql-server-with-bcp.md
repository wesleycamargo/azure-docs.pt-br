<properties
   pageTitle="Carregar dados do SQL Server no Azure SQL Data Warehouse (bcp) | Microsoft Azure"
   description="Para um tamanho de dados pequeno, use bcp para exportar dados do SQL Server para arquivos simples e importar os dados diretamente no Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# Carregar dados do SQL Server no Azure SQL Data Warehouse (arquivos simples)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)

Para conjuntos de dados pequenos, você pode usar o utilitário de linha de comando bcp para exportar dados do SQL Server e, depois, carregá-los diretamente no Azure SQL Data Warehouse.

Neste tutorial, você usará o bcp para:

- Exportar uma tabela do SQL Server usando o comando bcp out (ou criar um arquivo de exemplo simples)
- Importar a tabela de um arquivo simples para o SQL Data Warehouse.
- Crie estatísticas sobre os dados carregados.

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## Antes de começar

### Pré-requisitos

Para acompanhar este tutorial, você precisará:

- Um banco de dados do SQL Data Warehouse.
- O utilitário de linha de comando bcp instalado
- O utilitário de linha de comando sqlcmd instalado

Você pode baixar os utilitários bcp e sqlcmd do [Centro de Download da Microsoft][].

### Dados em formato ASCII ou UTF-16

Se você estiver experimentando este tutorial com seus próprios dados, eles precisarão usar a codificação ASCII ou UTF-16, já que bcp não oferece suporte a UTF-8.

O PolyBase oferece suporte a UTF-8, mas ainda não oferece suporte a UTF-16. Observe que se você quiser combinar bcp com o PolyBase, será necessário transformar os dados em UTF-8 após a exportação do SQL Server.


## 1\. Criar uma tabela de destino

Defina uma tabela no SQL Data Warehouse que será a tabela de destino para a carga. As colunas na tabela devem corresponder aos dados em cada linha do arquivo de dados.

Para criar uma tabela, abra um prompt de comando e use sqlcmd.exe para executar o comando a seguir:


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


## 2\. Criar um arquivo de dados de origem

Abra o Bloco de Notas e copie as seguintes linhas de dados em um novo arquivo de texto. Em seguida, salve esse arquivo em seu diretório temporário local, c:\\Temp\\DimDate2.txt. Esses dados estão no formato ASCII.

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

(Opcional) Para exportar seus próprios dados de um banco de dados do SQL Server, abra um prompt de comando e execute o comando a seguir. Substitua TableName, ServerName, DatabaseName, Username e Password por suas próprias informações.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## 3\. Carregar os dados
Para carregar os dados, abra um prompt de comando e execute o comando a seguir, substituindo os valores de Nome do Servidor, Nome do Banco de Dados, Nome de Usuário e Senha por suas próprias informações.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Use este comando para verificar se os dados foram carregados corretamente

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

O resultado deve parecer com o seguinte:

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

## 4\. Criar estatísticas

O SQL Data Warehouse ainda não dá suporte à criação automática ou atualização automática de estatísticas. Para obter o melhor desempenho de suas consultas, é importante que as estatísticas sejam criadas em todas as colunas de todas as tabelas após o primeiro carregamento, ou após uma alteração significativa nos dados. Para obter uma explicação detalhada das estatísticas, confira [Estatísticas][].

Execute o seguinte comando para criar estatísticas em sua tabela recém-carregada.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## 5\. Exportar dados do SQL Data Warehouse
Por diversão, você pode exportar os dados recém-carregados para fora do SQL Data Warehouse. O comando para exportar é exatamente o mesmo usado para a exportação do SQL Server.

No entanto, há uma diferença nos resultados. Como os dados são armazenados em locais distribuídos no SQL Data Warehouse, quando você exporta os dados, cada nó de Computação grava seus dados no arquivo de saída. A ordem dos dados no arquivo de saída provavelmente será diferente da ordem dos dados no arquivo de entrada.

### Exportar uma tabela e comparar os resultados exportados

Para ver os dados exportados, abra um prompt de comando e execute este comando usando seus próprios parâmetros. ServerName é o nome do SQL Server lógico do Azure.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Você pode verificar se os dados foram exportados corretamente abrindo o novo arquivo. Os dados no arquivo devem corresponder ao texto abaixo, mas provavelmente serão classificados em uma ordem diferente:

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

### Exportar os resultados de uma consulta

Você pode usar a função **queryout** do bcp para exportar os resultados de uma consulta em vez de exportar toda a tabela.

## Próximas etapas
Para obter uma visão geral do carregamento, consulte [Carregar dados no SQL Data Warehouse][]. Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento do SQL Data Warehouse][]. Confira [Visão geral da tabela][] ou [Sintaxe CREATE TABLE][] para saber mais sobre como criar uma tabela no SQL Data Warehouse.

<!--Image references-->

<!--Article references-->

[Carregar dados no SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[Visão geral de desenvolvimento do SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Visão geral da tabela]: ./sql-data-warehouse-tables-overview.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[Sintaxe CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Centro de Download da Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=AcomDC_0706_2016-->