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
   ms.date="07/01/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Criar um banco de dados do SQL Data Warehouse usando TSQL (Transact-SQL)

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Este artigo mostra como criar um banco de dados do SQL Data Warehouse usando Transact-SQL (T-SQL).

## Pré-requisitos
Antes de começar, verifique se os seguintes pré-requisitos foram cumpridos:

- **Conta do Azure**: consulte [Avaliação Gratuita do Azure][] ou [Créditos do Azure no MSDN][] para criar uma conta.
- **Azure SQL Server V12**: consulte [Criar um servidor lógico do Banco de Dados SQL do Azure com o Portal do Azure][] ou [Criar um servidor lógico do Banco de Dados SQL do Azure com o PowerShell][].
- **Nome do grupo de recursos**: use o mesmo Grupo de Recursos que o Azure SQL Server V12, ou confira [grupos de recursos][] para criar um novo grupo de recursos.
- **Visual Studio com SQL Server Data Tools**: para obter instruções de instalação, confira [Instalação do Visual Studio e do SSDT][].

> [AZURE.NOTE] A criação de um novo SQL Data Warehouse pode resultar em um novo serviço faturável. Confira [Preços do SQL Data Warehouse][] para obter mais detalhes sobre preços.

## Criar um banco de dados com o Visual Studio

Se você for novo no Visual Studio, confira o artigo [Conectar-se ao SQL Data Warehouse com o Visual Studio][]. Para começar, abra o Pesquisador de Objetos do SQL Server no Visual Studio e conecte-se ao servidor que hospedará seu banco de dados do SQL Data Warehouse. Quando estiver conectado, você poderá criar um SQL Data Warehouse executando o seguinte comando SQL no banco de dados **mestre**. Este comando criará o banco de dados MySqlDwDb com um Objetivo de Serviço de DW400 e permitirá que o banco de dados aumente até um tamanho máximo de 10 TB.

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Criar um banco de dados com sqlcmd

Como alternativa, você pode executar o mesmo comando com sqlcmd executando o seguinte em um prompt de comando.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

O `MAXSIZE` pode ser entre 250 GB e 240 TB. O `SERVICE_OBJECTIVE` pode estar entre DW100 e DW2000 [DWU][]. Para obter uma lista de todos os valores válidos, confira a documentação do MSDN para [CREATE DATABASE][]. O MAXSIZE e SERVICE\_OBJECTIVE também podem ser alterados com um comando T-SQL [ALTER DATABASE][]. É necessário ter cuidado ao alterar o SERVICE\_OBJECTIVE, pois isso causa uma reinicialização de serviços que cancelará todas as consultas em trânsito. A alteração de MAXSIZE não reinicia os serviços, pois é apenas uma operação de metadados.

## Próximas etapas
Após o provisionamento do SQL Data Warehouse, você pode [carregar dados de amostra][] ou conferir como [desenvolver][], [carregar][] ou [migrar][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: ./sql-data-warehouse-get-started-provision.md
[Conectar-se ao SQL Data Warehouse com o Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desenvolver]: ./sql-data-warehouse-overview-develop.md
[carregar]: ./sql-data-warehouse-overview-load.md
[carregar dados de amostra]: ./sql-data-warehouse-get-started-load-sample-databases.md
[Criar um servidor lógico do Banco de Dados SQL do Azure com o Portal do Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Criar um servidor lógico do Banco de Dados SQL do Azure com o PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[grupos de recursos]: ../azure-portal/resource-group-portal.md
[Instalação do Visual Studio e do SSDT]: ./sql-data-warehouse-install-visual-studio.md


<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Preços do SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Avaliação Gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Créditos do Azure no MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0706_2016-->