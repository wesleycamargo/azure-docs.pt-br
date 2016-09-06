<properties
   pageTitle="Criar um SQL Data Warehouse com o TSQL | Microsoft Azure"
   description="Saiba como criar um SQL Data Warehouse do Azure com o TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Criar um banco de dados do SQL Data Warehouse usando TSQL (Transact-SQL)

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Este artigo mostra como criar um SQL Data Warehouse usando o T-SQL.

## Pré-requisitos

Para começar, você precisa do seguinte:

- **Conta do Azure**: visite [Avaliação Gratuita do Azure][] ou [Créditos do Azure no MSDN][] para criar uma conta.
- **Servidor SQL do Azure**: veja [Criar um servidor lógico do Banco de Dados SQL do Azure com o Portal do Azure][] ou [Criar um servidor lógico do Banco de Dados SQL do Azure com o PowerShell][] para obter mais detalhes.
- **Grupo de recursos**: use o mesmo grupo de recursos do servidor SQL do Azure ou veja [como criar um grupo de recursos][].
- **Ambiente para executar o T-SQL**: você pode usar o [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][] ou [SSMS][] para executar o T-SQL.

> [AZURE.NOTE] A criação de um SQL Data Warehouse pode resultar em um novo serviço faturável. Confira [Preços do SQL Data Warehouse][] para obter mais detalhes sobre preços.

## Criar um banco de dados com o Visual Studio

Se você for novo no Visual Studio, confira o artigo [Consultar o Azure SQL Data Warehouse (Visual Studio)][]. Para começar, abra o Pesquisador de Objetos do SQL Server no Visual Studio e conecte-se ao servidor que hospedará seu banco de dados do SQL Data Warehouse. Quando estiver conectado, você poderá criar um SQL Data Warehouse executando o seguinte comando SQL no banco de dados **mestre**. Este comando cria o banco de dados MySqlDwDb com um Objetivo de Serviço de DW400 e permite que o banco de dados aumente até um tamanho máximo de 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Criar um banco de dados com sqlcmd

Como alternativa, você pode executar o mesmo comando com sqlcmd executando o seguinte em um prompt de comando.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

O agrupamento padrão quando não especificado é COLLATE SQL\_Latin1\_General\_CP1\_CI\_AS. O `MAXSIZE` pode ter entre 250 GB e 240 TB. O `SERVICE_OBJECTIVE` pode estar entre DW100 e DW2000 [DWU][]. Para obter uma lista de todos os valores válidos, confira a documentação do MSDN para [CREATE DATABASE][]. MAXSIZE e SERVICE\_OBJECTIVE podem ser alterados com um comando T-SQL [ALTER DATABASE][]. O agrupamento de um banco de dados não pode ser alterado após a criação. É necessário ter cuidado ao alterar o SERVICE\_OBJECTIVE, pois alterar um DWU causa uma reinicialização dos serviços, cancelando todas as consultas em trânsito. A alteração de MAXSIZE não reinicia os serviços, pois é apenas uma operação de metadados.

## Próximas etapas

Após o provisionamento do SQL Data Warehouse, você pode [carregar dados de amostra][] ou conferir como [desenvolver][], [carregar][] ou [migrar][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Consultar o Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrar]: sql-data-warehouse-overview-migrate.md
[desenvolver]: sql-data-warehouse-overview-develop.md
[carregar]: sql-data-warehouse-overview-load.md
[carregar dados de amostra]: sql-data-warehouse-load-sample-databases.md
[Criar um servidor lógico do Banco de Dados SQL do Azure com o Portal do Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Criar um servidor lógico do Banco de Dados SQL do Azure com o PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[como criar um grupo de recursos]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[Preços do SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Avaliação Gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Créditos do Azure no MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0831_2016-->