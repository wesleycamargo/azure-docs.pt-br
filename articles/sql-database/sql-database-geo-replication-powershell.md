<properties 
    pageTitle="Configurar a Replicação Geográfica Ativa para o Banco de Dados SQL do Azure usando o PowerShell | Microsoft Azure" 
    description="Configurar a Replicação Geográfica Ativa para o Banco de Dados SQL do Azure usando o PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
   ms.workload="NA"
    ms.date="07/14/2016"
    ms.author="sstein"/>

# Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure com o PowerShell

> [AZURE.SELECTOR]
- [Visão geral](sql-database-geo-replication-overview.md)
- [Portal do Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Este artigo mostra como configurar a Replicação Geográfica Ativa para o Banco de Dados SQL do Azure com o PowerShell.

Para iniciar o failover usando o PowerShell, veja [Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o PowerShell](sql-database-geo-replication-failover-powershell.md).

>[AZURE.NOTE] Replicação Geográfica Ativa (secundários legíveis) agora está disponível para todos os bancos de dados em todas as camadas de serviço. Em abril de 2017 o tipo de secundário não legível será descontinuado e bancos de dados não legíveis existentes serão automaticamente atualizados para secundários legíveis.



Para configurar a replicação geográfica ativa usando o PowerShell, você precisará do seguinte:

- Uma assinatura do Azure.
- Um Banco de Dados SQL do Azure - o banco de dados primário que você deseja replicar.
- Azure PowerShell 1.0 ou posterior. Você pode baixar e instalar os módulos do Azure PowerShell seguindo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


## Configurar suas credenciais e selecionar sua assinatura

Em primeiro lugar, você deve estabelecer o acesso à sua conta do Azure e, depois, iniciar o PowerShell e executar o cmdlet a seguir. Na tela de logon, insira o mesmo email e senha que você usa para entrar no portal do Azure.


	Login-AzureRmAccount

Depois de se conectar com êxito, você verá algumas informações na tela, incluindo a ID usada para entrar e as assinaturas do Azure as quais você tem acesso.


### Selecionar sua assinatura do Azure

Para selecionar a assinatura, você precisa da ID da assinatura. É possível copiar a ID da assinatura nas informações exibidas na etapa anterior ou, se tiver várias assinaturas e precisar de mais detalhes, você pode executar o cmdlet **Get-AzureRmSubscription** e copiar as informações da assinatura desejada do conjunto de resultados. O cmdlet a seguir usa a Id de assinatura para definir a assinatura atual:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Depois de executar **Select-AzureRmSubscription** com êxito, você retornará ao prompt do PowerShell.


## Adicionar banco de dados secundário


As etapas a seguir criam um novo banco de dados secundário em uma parceria de Replicação Geográfica.
  
Para habilitar um secundário, você deverá ser o proprietário ou o coproprietário da assinatura.

Você pode usar o cmdlet **AzureRmSqlDatabaseSecondary novo** para adicionar um banco de dados secundário rm um servidor parceiro a um banco de dados local no servidor ao qual você está conectado (o banco de dados primário).

Esse cmdlet substitui **Start-AzureSqlDatabaseCopy** pelo parâmetro **–IsContinuous**. Ele terá como saída um objeto **AzureRmSqlDatabaseSecondary**, que pode ser usado por outros cmdlets para identificar claramente um link de replicação específico. Esse cmdlet retornará quando o banco de dados secundário for criado e totalmente propagado. Dependendo do tamanho do banco de dados, ele poderá levar de minutos a horas.

O banco de dados replicado no servidor secundário terá o mesmo nome do banco de dados do servidor primário e, por padrão, terá o mesmo nível de serviço. O banco de dados secundário pode ser legível ou não legível, e pode ser um único banco de dados ou um banco de dados elástico. Para saber mais, consulte [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) e [Camadas de serviço](sql-database-service-tiers.md). Depois do banco de dados secundário ser criado e propagado, os dados começarão a ser replicados desde o banco de dados primário até o novo banco de dados secundário. As etapas a seguir descrevem como realizar essa tarefa usando o PowerShell para criar secundários não legível e legíveis, com um banco de dados individual ou com um banco de dados elástico.

O comando falhará se o banco de dados parceiro já existir (por exemplo, como resultado do encerramento de um relacionamento de Replicação Geográfica anterior).



### Adicionar um secundário não legível (banco de dados individual)

O comando a seguir cria um secundário não legível do banco de dados "mydb" do servidor "srv2" no grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### Adicionar um secundário legível (banco de dados individual)

O comando a seguir cria um secundário legível do banco de dados "mydb" do servidor "srv2" no grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### Adicionar um secundário não legível (banco de dados elástico)

O comando a seguir cria um secundário não legível do banco de dados "mydb" do servidor “srv2” no pool de bancos de dados elásticos chamado "ElasticPool1" no grupo de recursos "srv2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### Adicionar um secundário legível (banco de dados elástico)

O comando a seguir cria um secundário legível do banco de dados "mydb" do servidor “srv2” no pool de bancos de dados elásticos chamado "ElasticPool1" no grupo de recursos "srv2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## Remover banco de dados secundário

Use o cmdlet **Remove-AzureRmSqlDatabaseSecondary** para encerrar permanentemente a parceria de replicação entre um banco de dados secundário e seu primário. Após o encerramento da relação, o banco de dados secundário se torna um banco de dados de leitura/gravação. Se a conectividade com o banco de dados secundário for interrompida, o comando terá êxito, mas o secundário se tornará de leitura/gravação após a conectividade ser restaurada. Para saber mais, consulte [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) e [Camadas de serviço](sql-database-service-tiers.md).

Esse cmdlet substitui Stop-AzureSqlDatabaseCopy para a replicação.

Essa remoção é equivalente a um encerramento forçado que remove o link de replicação e deixa o secundário anterior como um banco de dados autônomo que não é totalmente replicado antes do encerramento. Todos os dados de link serão limpos no antigo primário e no antigo secundário, se ou quando eles estiverem disponíveis. Esse cmdlet retornará quando o link de replicação for removido.


Para remover secundária, os usuários devem ter acesso de gravação aos bancos de dados primários e secundários de acordo com o RBAC. Consulte Controle de acesso baseado em função para obter detalhes.

O item a seguir remove o link de replicação do banco de dados chamado "mydb" para o servidor "srv2" do grupo de recursos "rg2".

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## Monitorar a configuração e a integridade da replicação geográfica

Monitorar as tarefas inclui o monitoramento da configuração de replicação geográfica e da integridade da replicação dos dados.

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) pode ser usado para recuperar as informações sobre os links de replicação direta visíveis no modo de exibição de catálogo de sys.geo\_replication\_links.

O comando a seguir recupera o status do link de replicação entre o banco de dados "mydb" primário e secundário no servidor "srv2" do grupo de recursos "rg2".

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


## Próximas etapas

- Para saber mais sobre a Replicação Geográfica Ativa, veja [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)

<!---HONumber=AcomDC_0727_2016-->