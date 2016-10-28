<properties
   pageTitle="Carregar dados de exemplo no SQL Data Warehouse | Microsoft Azure"
   description="Carregar dados de amostra no SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#Carregar dados de amostra no SQL Data Warehouse

Siga estas etapas simples para carregar e consultar o banco de dados de exemplo da Adventure Works. Esses scripts usam sqlcmd para executar SQL, criando tabelas e exibições. Depois que as tabelas tiverem sido criadas, os scripts usarão bcp para carregar os dados. Se você ainda não tiver o sqlcmd e o bcp instalados, siga estes links para [instalar o bcp][] e [instalar o sqlcmd][].

##Carregar dados de exemplo

1. Baixe o arquivo zip de [Scripts de exemplo da Adventure Works para o SQL Data Warehouse][].

2. Extraia os arquivos do zip baixado em um diretório no computador local.

3. Edite o arquivo aw\_create.bat extraído e defina as seguintes variáveis na parte superior do arquivo. Não deixe nenhum espaço em branco entre "=" e o parâmetro. A seguir estão exemplos de como poderão ficar suas edições.

    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```

4. Em um prompt de comando do Windows, execute o aw\_create.bat editado. Verifique se você está no diretório onde salvou a versão editada do aw\_create.bat. Este script...
	* Descartará todas as tabelas da Adventure Works ou exibições que já existem no banco de dados
	* Criará as tabelas e exibições da Adventure Works
	* Carregará cada tabela da Adventure Works usando o bcp
	* Validará as contagens de linhas para cada tabela da Adventure Works
	* Coletará estatísticas sobre cada coluna para cada tabela da Adventure Works


##Consultar dados de exemplo

Agora que você carregou alguns dados de exemplo no SQL Data Warehouse, poderá executar rapidamente algumas consultas. Para executar uma consulta, conecte-se ao banco de dados da Adventure Works recém-criado no SQL DW do Azure usando o Visual Studio e o SSDT, conforme descrito no documento sobre a [consulta com Visual Studio][].

Exemplo de instrução select simples para obter todas as informações dos funcionários:

```sql
SELECT * FROM DimEmployee;
```

Exemplo de uma consulta mais complexa usando construções, como GROUP BY, para ver a quantidade total de todas as vendas em cada dia:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Exemplo de SELECT com uma cláusula WHERE para filtrar os pedidos anteriores a uma determinada data:

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

O SQL Data Warehouse oferece suporte a quase todas as construções T-SQL para as quais o SQL Server oferece suporte. Quaisquer diferenças estão documentadas em nossa documentação para [migrar o código][].

## Próximas etapas
Agora que você teve a oportunidade de experimentar algumas consultas com dados de exemplo, confira como [desenvolver][], [carregar][] ou [migrar][] para o SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[migrar]: sql-data-warehouse-overview-migrate.md
[desenvolver]: sql-data-warehouse-overview-develop.md
[carregar]: sql-data-warehouse-overview-load.md
[consulta com Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migrar o código]: sql-data-warehouse-migrate-code.md
[instalar o bcp]: sql-data-warehouse-load-with-bcp.md
[instalar o sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Scripts de exemplo da Adventure Works para o SQL Data Warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip

<!---HONumber=AcomDC_0817_2016-->