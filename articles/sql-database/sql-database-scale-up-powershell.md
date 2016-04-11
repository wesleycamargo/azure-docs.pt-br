<properties 
    pageTitle="Alterar a camada de serviço e o nível de desempenho de um banco de dados SQL do Azure usando PowerShell" 
    description="Alterar a camada de serviço e o nível de desempenho de um banco de dados SQL do Azure mostra como escalar verticalmente seu banco de dados SQL com o PowerShell. Alterando o tipo de preço de um banco de dados SQL do Azure com o PowerShell." 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/29/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Alterar a camada de serviços e o nível de desempenho (tipo de preço) de um banco de dados SQL com o PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


As camadas de serviço e níveis de desempenho descrevem as características e recursos disponíveis para o banco de dados SQL e podem ser atualizados conforme as necessidades do seu aplicativo mudam. Para obter detalhes, consulte [Camadas de serviço](sql-database-service-tiers.md).

Observe que a alteração da camada de serviço e/ou nível de desempenho de um banco de dados cria uma réplica do banco de dados original com o novo nível de desempenho e então faz a transição das conexões para réplica. Nenhum dado é perdido durante esse processo, mas durante o breve momento em que realizamos a transição para a réplica, conexões com o banco de dados são desabilitadas, então algumas transações em andamento podem ser revertidas. Essa janela varia, mas é em média inferior a quatro segundos e, em mais de 99% dos casos, de menos de 30 segundos. Muito raramente, especialmente se houver grandes números de transações em andamento no momento em que as conexões estiverem desabilitadas, esta janela pode ser maior.

A duração de todo o processo de expansão depende a camada tamanho e de serviço do banco de dados antes e após a alteração. Por exemplo, um banco de dados de 250 GB que está mudando para, de ou dentro de uma camada de serviço Standard deverá ser concluída dentro de 6 horas. Um banco de dados do mesmo tamanho cujos níveis de desempenho estão mudando dentro da camada de serviço Premium deverá ser concluído dentro de 3 horas.


- Para fazer downgrade de um banco de dados, este deve ter um tamanho menor do que o máximo permitido para a camada de serviço de destino. 
- Ao atualizar um banco de dados com [Replicação geográfica](sql-database-geo-replication-portal) habilitada, é necessário primeiro atualizar seus bancos de dados secundários para o nível de desempenho desejado antes de atualizar o banco de dados primário.
- Ao fazer downgrade de uma camada de serviço Premium, primeiro, você deve encerrar todos os relacionamentos de Replicação Geográfica. Você pode seguir as etapas descritas no tópico [Recuperação de uma interrupção](sql-database-disaster-recovery.md) para interromper o processo de replicação entre os bancos de dados primários e secundários ativos.
- As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se estiver fazendo downgrade, talvez você perca a capacidade de fazer uma restauração pontual ou tenha um período menor de retenção do backup. Para saber mais, confira [Backup e restauração do Banco de dados SQL do Azure](sql-database-business-continuity.md).
- As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.



**Para concluir este artigo, você precisa do seguinte:**

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **CONTA GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um banco de dados SQL do Azure. Se você não tiver um banco de dados SQL, crie um executando as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).
- PowerShell do Azure.


Para executar os cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).



## Configurar suas credenciais e selecionar sua assinatura

Em primeiro lugar, você deve estabelecer o acesso à sua conta do Azure e, depois, iniciar o PowerShell e executar o cmdlet a seguir. Na tela de logon, insira o mesmo email e senha que você usa para entrar no portal do Azure.

	Login-AzureRmAccount

Depois de se conectar com êxito, você verá algumas informações na tela, incluindo a ID usada para entrar e as assinaturas do Azure as quais você tem acesso.


### Selecionar sua assinatura do Azure

Para selecionar a assinatura, é necessário ter a ID ou o nome da assinatura (**-SubscriptionName**). É possível copiar a ID da assinatura nas informações exibidas na etapa anterior ou, se tiver várias assinaturas e precisar de mais detalhes, você pode executar o cmdlet **Get-AzureSubscription** e copiar as informações da assinatura desejada do resultset. Quando tiver sua assinatura, execute o seguinte cmdlet:

	$SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId




## Alterar a camada de serviço e o nível de desempenho do banco de dados SQL

Execute o cmdlet **Set-AzureRmSqlDatabase** e defina o **-RequestedServiceObjectiveName** com o nível de desempenho do tipo de preço desejado, por exemplo, *S0*, *S1*, *S2*, *S3*, *P1*, *P2*, etc.

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"

    $NewEdition = "Standard"
    $NewPricingTier = "S2"

    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier


  

   


## Amostra de script do PowerShell para alterar a camada de serviços e o nível de desempenho do banco de dados SQL

    

    
    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest
    
        


## Próximas etapas

- [Escalar horizontalmente](sql-database-elastic-scale-get-started.md)
- [Conectar-se a e consultar um banco de dados SQL com SSMS](sql-database-connect-query-ssms.md)
- [Exportar um banco de dados SQL do Azure](sql-database-export-powershell.md)

## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do Banco de Dados SQL](http://azure.microsoft.com/documentation/services/sql-database/)
- [Cmdlets do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/mt574084.aspx)

<!---HONumber=AcomDC_0330_2016-->