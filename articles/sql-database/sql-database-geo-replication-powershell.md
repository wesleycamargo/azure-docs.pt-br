<properties 
    pageTitle="Configurar a replicação geográfica para o Banco de Dados SQL do Azure usando o PowerShell | Microsoft Azure" 
    description="Replicação Geográfica para o Banco de Dados SQL do Azure usando o PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="11/10/2015"
    ms.author="sstein"/>

# Configurar a replicação geográfica para o Banco de Dados SQL do Azure com o PowerShell



> [AZURE.SELECTOR]
- [Azure preview portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


Este artigo mostra como configurar a replicação geográfica para o Banco de Dados SQL do Azure com o PowerShell.

A replicação geográfica permite criar até quatro bancos de dados de réplica (secundários) em diferentes locais do datacenter (regiões). Os bancos de dados secundários estão disponíveis no caso de uma paralisação do data center ou da incapacidade de conectar ao banco de dados primário.

A replicação geográfica só está disponível para os bancos de dados Standard e Premium.

Os bancos de dados Standard podem ter um secundário não legível e devem usar a região recomendada. Os bancos de dados Premium podem ter até quatro secundários legíveis em qualquer uma das regiões disponíveis.

Para configurar a replicação geográfica, você precisará do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um Banco de Dados SQL do Azure - o banco de dados primário que você deseja replicar para uma região geográfica diferente.
- Azure PowerShell 1.0 Preview. Você pode baixar e instalar os módulos do Azure PowerShell seguindo [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md).

> [AZURE.IMPORTANT]A partir da liberação da Visualização do Azure PowerShell 1.0, o cmdlet Switch-AzureMode não está mais disponível, e os cmdlets contidos no módulo do Gerenciador de Recursos do Azure foram renomeados. Os exemplos neste artigo usam a nova convenção de nomenclatura do PowerShell 1.0 Preview. Para obter informações detalhadas, veja [Substituição de Switch-AzureMode no Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).





## Configurar suas credenciais e selecionar sua assinatura

Em primeiro lugar, você deve estabelecer o acesso à sua conta do Azure e, depois, iniciar o PowerShell e executar o cmdlet a seguir. Na tela de logon, insira o mesmo email e senha que você usa para entrar no portal do Azure.


	Login-AzureRmAccount

Depois de se conectar com êxito, você verá algumas informações na tela, incluindo a ID usada para entrar e as assinaturas do Azure as quais você tem acesso.


### Selecionar sua assinatura do Azure

Para selecionar a assinatura, você precisa da ID da assinatura. É possível copiar a Id da assinatura nas informações exibidas na etapa anterior ou, se tiver várias assinaturas e precisar de mais detalhes, você pode executar o cmdlet **Get-AzureRmSubscription** e copiar as informações da assinatura desejada do resultset. O cmdlet a seguir usa a Id de assinatura para definir a assinatura atual:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Depois de executar **Select-AzureRmSubscription** com êxito, você retornará ao prompt do PowerShell.



## Adicionar banco de dados secundário


As etapas a seguir criam um novo banco de dados secundário em uma parceria de replicação geográfica.
  
Para habilitar um secundário, você deverá ser o proprietário ou o co-proprietário da assinatura.

Você pode usar o cmdlet **AzureRmSqlDatabaseSecondary novo** para adicionar um banco de dados secundário rm um servidor parceiro a um banco de dados local no servidor ao qual você está conectado (o banco de dados primário).

Esse cmdlet substitui **Start-AzureSqlDatabaseCopy** pelo parâmetro **–IsContinuous**. Ele terá como saída um objeto **AzureRmSqlDatabaseSecondary**, que pode ser usado por outros cmdlets para identificar claramente um link de replicação específico. Esse cmdlet retornará quando o banco de dados secundário for criado e totalmente propagado. Dependendo do tamanho do banco de dados, ele poderá levar de minutos a horas.

O banco de dados replicado no servidor secundário terá o mesmo nome do banco de dados do servidor primário e, por padrão, terá o mesmo nível de serviço. O banco de dados secundário pode ser legível ou não legível, e pode ser um único banco de dados ou um banco de dados elástico. Para saber mais, consulte [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) e [Camadas de serviço](sql-database-service-tiers.md). Depois do banco de dados secundário ser criado e propagado, os dados começarão a ser replicados desde o banco de dados primário até o novo banco de dados secundário. As etapas a seguir descrevem como realizar essa tarefa usando o PowerShell para criar secundários não legível e legíveis, com um banco de dados individual ou com um banco de dados elástico.

O comando falhará se o banco de dados parceiro já existir (como, por exemplo, como resultado do encerramento de um relacionamento de replicação geográfica anterior).



### Adicionar um secundário não legível (banco de dados individual)

O comando a seguir cria um secundário não legível do banco de dados "mydb" do servidor "srv2" no grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "None"



### Adicionar um secundário legível (banco de dados individual)

O comando a seguir cria um secundário legível do banco de dados "mydb" do servidor "srv2" no grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### Adicionar um secundário não legível (banco de dados elástico)

O comando a seguir cria um secundário não legível do banco de dados "mydb" do servidor “srv2” no pool de bancos de dados elásticos chamado "ElasticPool1" no grupo de recursos "srv2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "None"


### Adicionar um secundário legível (banco de dados elástico)

O comando a seguir cria um secundário legível do banco de dados "mydb" do servidor “srv2” no pool de bancos de dados elásticos chamado "ElasticPool1" no grupo de recursos "srv2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## Remover banco de dados secundário

Use o cmdlet **Remove-AzureRmSqlDatabaseSecondary** para encerrar permanentemente a parceria de replicação entre um banco de dados secundário e seu primário. Após o encerramento da relação, o banco de dados secundário se torna um banco de dados de leitura/gravação. Se a conectividade com o banco de dados secundário for interrompida, o comando terá êxito, mas o secundário se tornará de leitura/gravação após a conectividade ser restaurada. Para saber mais, consulte [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) e [Camadas de serviço](https://azure.microsoft.com/documentation/articles/sql-database-service-tiers/).

Esse cmdlet substitui Stop-AzureSqlDatabaseCopy para a replicação.

Essa remoção é equivalente a um encerramento forçado que remove o link de replicação e deixa o secundário anterior como um banco de dados autônomo que não é totalmente replicado antes do encerramento. Todos os dados de link serão limpos no antigo primário e no antigo secundário, se ou quando eles estiverem disponíveis. Esse cmdlet retornará quando o link de replicação for removido.


Para remover secundária, os usuários devem ter acesso de gravação aos bancos de dados primários e secundários de acordo com o RBAC. Consulte Controle de acesso baseado em função para obter detalhes.

O item a seguir remove o link de replicação do banco de dados chamado "mydb" para o servidor "srv2" do grupo de recursos "rg2".

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 




## Iniciar um failover planejado

Você pode usar o cmdlet **Set-AzureRmSqlDatabaseSecondary** com o parâmetro **-Failover** para promover um banco de dados secundário para que ele se torne o novo banco de dados primário de maneira planejada, rebaixando o primário existente para se tornar um secundário. Essa funcionalidade é designada para um failover planejado, como durante os exercícios de recuperação de desastres, e requer que o banco de dados primário esteja disponível.

O comando executa o seguinte fluxo de trabalho:

1. Alterne temporariamente a replicação para o modo síncrono. Isso fará com que todas as transações pendentes sejam liberadas para o secundário.

2. Alterne as funções dos dois bancos de dados na parceria de replicação geográfica.

Essa sequência garante que não ocorrerá nenhuma perda de dados. Há um breve período durante o qual os bancos de dados não estão disponíveis (na ordem de 0 a 25 segundos) enquanto as funções são alternadas. A operação inteira deve levar menos de um minuto para ser concluída em circunstâncias normais. Para saber mais, consulte [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).


> [AZURE.NOTE]Se o banco de dados primário não estiver disponível quando o comando for emitido, ele falhará com uma mensagem de erro indicando que o servidor primário não está disponível. Em situações raras, é possível que a operação não seja concluída e pareça paralisada. Nesse caso, o usuário poderá executar o comando de failover à força e aceitar a perda de dados.



Esse cmdlet retornará quando o processo de alternar o banco de dados secundário para primário for concluído.

O comando a seguir alterna as funções do banco de dados chamado "mydb" no servidor "srv2" sob o grupo de recursos "rg2" para o primário. O primário original ao qual "db2" foi conectado será alternado para o secundário depois que os dois bancos de dados forem completamente sincronizados.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover



## Iniciar um failover não planejado do banco de dados primário para o banco de dados secundário


Você pode usar o cmdlet **Set-AzureRmSqlDatabaseSecondary** com os parâmetros **–Failover** e **-AllowDataLoss** para promover um banco de dados secundário para se tornar o novo banco de dados primário de maneira planejada, rebaixando o primário existente para se tornar um secundário quando o banco de dados primário não estiver mais disponível.

Essa funcionalidade foi designada para a recuperação de desastres quando a restauração da disponibilidade do banco de dados é fundamental e a perda de dados é aceitável. Quando o failover forçado é chamado, o banco de dados secundário especificado imediatamente se torna o banco de dados primário e começa a aceitar as transações de gravação. Assim que o banco de dados primário original for capaz de se reconectar com o novo banco de dados primário após a operação de failover forçado, um backup incremental será realizado no banco de dados primário original e o antigo banco de dados primário será transformado em um banco de dados secundário para o novo banco de dados primário; em seguida, será simplesmente uma réplica do novo primário.

Mas como a Restauração Pontual não tem suporte em bancos de dados secundários, se você quiser confirmar os dados de recuperação no banco de dados primário antigo que não tenham sido replicados para o novo banco de dados primário, deverá contar com o CSS para restaurar um banco de dados para o backup de log conhecido.

> [AZURE.NOTE]Se o comando for emitido quando o primário e o secundário estiverem online, o antigo primário se tornará o novo secundário, mas não haverá uma tentativa de sincronização dos dados e, portanto, poderá ocorrer a perda de alguns dados.


Se o banco de dados primário tiver vários secundários, o comando será parcialmente bem-sucedido. O secundário no qual o comando foi executado se tornará o primário. O primário antigo, entretanto, permanecerá como primário, isto é, os dois primários terminarão em um estado inconsistente e conectados por um link de replicação suspensa. O usuário terá de reparar manualmente essa configuração usando uma API para "remover secundário" em qualquer um desses bancos de dados primários.


O comando a seguir alternará as funções de banco de dados chamado "mydb" para primário quando o primário não estiver disponível. O primário original ao qual “mydb” estava conectado será alternado para secundário quando voltar a ficar online. Nesse ponto, a sincronização poderá resultar em perda de dados.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss



## Monitorar a configuração e a integridade da replicação geográfica

Monitorar as tarefas inclui o monitoramento da configuração de replicação geográfica e da integridade da replicação dos dados.

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) pode ser usado para recuperar as informações sobre os links de replicação direta visíveis no modo de exibição de catálogo de sys.geo\_replication\_links.

O comando a seguir recupera o status do link de replicação entre o banco de dados "mydb" primário e secundário no servidor "srv2" do grupo de recursos "rg2".

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb”
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”



   

## Próximas etapas

- [Executar análise de recuperação de desastres](sql-database-disaster-recovery-drills.md)




## Recursos adicionais

- [Destacar os novos recursos de replicação geográfica](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication)
- [Projetando aplicativos de nuvem para a continuidade de negócios usando a replicação geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Nov15_HO3-->