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
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="lodipalm"/>

# Criar um banco de dados do SQL Data Warehouse usando TSQL (Transact-SQL)

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Este artigo mostra como criar um banco de dados do SQL Data Warehouse usando TSQL (Transact-SQL).

## Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Visual Studio. Para obter uma cópia gratuita do Visual Studio, consulte a página [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).
- Um servidor SQL lógico V12. Será necessário um SQL Server V12 para criar um SQL Data Warehouse. Se você não tiver um servidor SQL V12 lógico, o [tutorial do Portal do Azure][] mostrará como criar um.

## Criar um banco de dados com o Visual Studio

Este artigo não abordará como configurar e conectar-se corretamente usando o Visual Studio. Para obter uma descrição completa de como fazer isso, consulte a documentação sobre [conexão e consulta][]. Para começar, abra o Pesquisador de Objetos do SQL Server no Visual Studio e conecte-se ao servidor que você usará para criar seu banco de dados do SQL Data Warehouse. Após fazer isso, você poderá criar um SQL Data Warehouse executando o seguinte comando em relação ao banco de dados Mestre:

        CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>);

## Criar um banco de dados com sqlcmd

Você também pode criar um SQL Data Warehouse abrindo a linha de comando e executando o seguinte:

        sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>)"

Ao executar as Instruções TSQL acima, observe os parâmetros MAXSIZE e SERVICE\_OBJECTIVE. Eles determinarão o tamanho inicial do armazenamento e a computação alocada para a instância do Data Warehouse. MAXSIZE aceitará os seguintes tamanhos, e sugerimos que você escolha um tamanho grande para deixar espaço para crescimento: 250 GB, 500 GB, 750 GB, 1024 GB, 5120 GB, 10240 GB, 20480 GB, 30720 GB, 40960 GB, 51200 GB.

SERVICE\_OBJECTIVE indicará o número de DWUs com os quais sua instância será iniciada e aceita os seguintes valores: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000. Para saber mais sobre o impacto de cobrança desses parâmetros, consulte nossa [página de preços][].

## Próximas etapas
Após o provisionamento do SQL Data Warehouse, você poderá [carregar dados de exemplo][] ou conferir como [desenvolver][], [carregar][] ou [migrar][].

[tutorial do Portal do Azure]: ./sql-data-warehouse-get-started-provision.md
[conexão e consulta]: ./sql-data-warehouse-get-started-connect.md
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desenvolver]: ./sql-data-warehouse-overview-develop.md
[carregar]: ./sql-data-warehouse-overview-load.md
[carregar dados de exemplo]: ./sql-data-warehouse-get-started-manually-load-samples.md
[página de preços]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/

<!---HONumber=AcomDC_1203_2015-->