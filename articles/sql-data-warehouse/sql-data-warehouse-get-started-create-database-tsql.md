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
   ms.date="04/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Criar um banco de dados do SQL Data Warehouse usando TSQL (Transact-SQL)

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Este artigo mostra como criar um banco de dados do SQL Data Warehouse usando Transact-SQL (TSQL).

## Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um servidor SQL lógico V12. Será necessário um SQL Server V12 para criar um SQL Data Warehouse. Se você não tiver um servidor SQL V12 lógico, confira **Configurar e criar um servidor** no artigo [Como criar um SQL Data Warehouse do Portal do Azure][].
- Visual Studio. Para obter uma cópia gratuita do Visual Studio, consulte a página [Downloads do Visual Studio][].


> [AZURE.NOTE] A criação de um novo SQL Data Warehouse pode resultar em um novo serviço faturável. Confira [Preços do SQL Data Warehouse][] para obter mais detalhes sobre preços.

## Criar um banco de dados com o Visual Studio

Se você for novo no Visual Studio, confira o artigo [Conectando-se ao SQL Data Warehouse com o Visual Studio][]. Para começar, abra o Pesquisador de Objetos do SQL Server no Visual Studio e conecte-se ao servidor que hospedará seu banco de dados do SQL Data Warehouse. Quando estiver conectado, você poderá criar um SQL Data Warehouse executando o seguinte comando SQL no banco de dados **mestre**. Este comando criará o banco de dados MySqlDwDb com um Objetivo de Serviço de DW400 e permitirá que o banco de dados aumente até um tamanho máximo de 10 TB.

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Criar um banco de dados com sqlcmd

Como alternativa, você pode executar o mesmo comando com sqlcmd executando o seguinte em um prompt de comando.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Os parâmetros **MAXSIZE** e **SERVICE\_OBJECTIVE** especificam o espaço máximo que o banco de dados pode usar em disco, e os recursos de computação alocados para sua instância do Data Warehouse. O objetivo do serviço é essencialmente uma alocação de CPU e memória que acompanha linearmente o tamanho do DWU.

O MAXSIZE pode ser entre 250 GB e 60 TB. O Objetivo do Serviço pode ser entre DW100 e DW2000. Para obter uma lista completa de todos os valores válidos para MAXSIZE e SERVICE\_OBJECTIVE, consulte a documentação do MSDN sobre [CREATE DATABASE][]. O MAXSIZE e SERVICE\_OBJECTIVE também podem ser alterados com um comando T-SQL [ALTER DATABASE][]. É necessário ter cuidado ao alterar o SERVICE\_OBJECTIVE, pois isso causa uma reinicialização de serviços que cancelará todas as consultas em trânsito. A alteração de MAXSIZE não exige esse cuidado, pois é apenas uma operação de metadados.

## Próximas etapas
Após o provisionamento do SQL Data Warehouse, você pode [carregar dados de amostra][] ou conferir como [desenvolver][], [carregar][] ou [migrar][].

<!--Article references-->
[Como criar um SQL Data Warehouse do Portal do Azure]: sql-data-warehouse-get-started-provision.md
[Conectando-se ao SQL Data Warehouse com o Visual Studio]: sql-data-warehouse-get-started-connect.md
[migrar]: sql-data-warehouse-overview-migrate.md
[desenvolver]: sql-data-warehouse-overview-develop.md
[carregar]: sql-data-warehouse-overview-load.md
[carregar dados de amostra]: sql-data-warehouse-get-started-manually-load-samples.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Preços do SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Downloads do Visual Studio]: https://www.visualstudio.com/downloads/download-visual-studio-vs

<!---HONumber=AcomDC_0427_2016-->