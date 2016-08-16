<properties
   pageTitle="Criar um SQL Data Warehouse no Portal do Azure | Microsoft Azure"
   description="Saiba como criar um Azure SQL Data Warehouse no Portal do Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/23/2016"
   ms.author="barbkess;lodipalm;sonyama"/>

# Criar um Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Este tutorial usa o Portal do Azure para criar um SQL Data Warehouse que contém um exemplo de banco de dados AdventureWorksDW.


## Pré-requisitos

Para começar, você precisa do seguinte:

- **Conta do Azure**: visite [Avaliação Gratuita do Azure][] ou [Créditos do Azure no MSDN][] para criar uma conta.
- **servidor SQL do Azure**: veja [Criar um servidor lógico do Banco de Dados SQL com o Portal do Azure][] para obter mais detalhes.

> [AZURE.NOTE] A criação de um SQL Data Warehouse pode resultar em um novo serviço faturável. Confira [Preços do SQL Data Warehouse][] para obter mais detalhes.

## Criar um SQL Data Warehouse

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Clique em **+ Novo** > **Dados + Armazenamento** > **SQL Data Warehouse**.

    ![Criar](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. Na folha **SQL Data Warehouse**, preencha as informações necessárias e, em seguida, pressione ‘Criar’ para criar.

    ![Criar banco de dados](./media/sql-data-warehouse-get-started-provision/create-database.png)

	- **Servidor**: recomendamos que você selecione seu servidor primeiro.

	- **Nome do banco de dados**: o nome usado para referenciar o SQL Data Warehouse. Esse nome tem que ser exclusivo do servidor.
	
    - **Desempenho**: é recomendável começar com 400 [DWUs][DWU]. Você pode mover o controle deslizante para a esquerda ou direita para ajustar o desempenho de seu data warehouse, ou ampliar ou reduzir após a criação. Para saber mais sobre DWUs, veja nossa documentação sobre [dimensionamento](./sql-data-warehouse-manage-compute-overview.md) ou nossa [página de preços][SQL Data Warehouse pricing].

    - **Assinatura**: selecione a [assinatura] na qual esse SQL Data Warehouse será cobrado.

    - **Grupo de recursos**: os [grupos de recursos][Resource group] são contêineres projetados para ajudar você a gerenciar uma coleção de recursos do Azure. Saiba mais sobre [grupos de recursos](../resource-group-overview.md).

    - **Selecionar fonte**: clique em **Selecionar fonte** > **Exemplo**. O Azure preenche automaticamente a opção **Selecionar exemplo** com AdventureWorksDW.

4. Clique em **Criar** para criar seu SQL Data Warehouse.

5. Aguarde alguns minutos. Quando o data warehouse estiver pronto, retorne para o [Portal do Azure](https://portal.azure.com). Você pode encontrar o SQL Data Warehouse em seu painel, relacionado sob seus Bancos de Dados SQL ou no grupo de recursos que você usou para criá-lo.

    ![exibição de portal](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [Criar servidor do Banco de Dados SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Próximas etapas

Agora que você criou um SQL Data Warehouse, está pronto para [Conectar](./sql-data-warehouse-connect-overview.md) e começar a consultar.

Para carregar dados no SQL Data Warehouse, veja a [visão geral de carregamento](./sql-data-warehouse-overview-load.md).

Se você estiver tentando migrar um banco de dados existente para o SQL Data Warehouse, confira a [Visão geral de migração](./sql-data-warehouse-overview-migrate.md) ou use o [Utilitário de migração](./sql-data-warehouse-migrate-migration-utility.md).

As regras de firewall também podem ser configuradas usando o Transact-SQL. Para saber mais, veja [sp\_set\_firewall\_rule][] e [sp\_set\_database\_firewall\_rule][].

Também é uma ótima ideia dar uma olhada nas [práticas recomendadas][].

<!--Article references-->
[Criar um servidor lógico do Banco de Dados SQL com o Portal do Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resource groups]: ../resource-group-template-deploy-portal.md
[práticas recomendadas]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md#data-warehouse-units
[assinatura]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group

<!--MSDN references-->
[sp\_set\_firewall\_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp\_set\_database\_firewall\_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Preços do SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Avaliação Gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Créditos do Azure no MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0810_2016-->