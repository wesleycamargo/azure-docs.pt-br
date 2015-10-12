<properties 
    pageTitle="Alterar a camada de serviço e o nível de desempenho de um banco de dados SQL do Azure usando PowerShell" 
    description="Alterar a camada de serviço e o nível de desempenho de um banco de dados SQL do Azure mostra como escalar verticalmente seu banco de dados SQL com o PowerShell. Alterando o tipo de preço de um banco de dados SQL do Azure com o PowerShell." 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/10/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Alterar a camada de serviços e o nível de desempenho (tipo de preço) de um banco de dados SQL com o PowerShell

**Banco de dados individual**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


Este artigo mostra como alterar a camada de serviço e o nível de desempenho do banco de dados SQL com o PowerShell.

Use as informações em [Atualizar banco de dados Web/Business do Banco de Dados SQL para novas camadas de serviço](sql-database-upgrade-new-service-tiers.md) e [Camadas de serviço e níveis de desempenho do Banco de Dados SQL do Azure](sql-database-service-tiers.md) para determinar o nível de desempenho e a camada de serviço apropriados para o Banco de Dados SQL do Azure.

> [AZURE.IMPORTANT]A alteração do nível de desempenho e da camada de serviço de um banco de dados SQL é uma operação online. Isso significa que seu banco de dados permanecerá online e disponível durante toda a operação, sem qualquer tempo de inatividade.

- Para fazer downgrade de um banco de dados, este deve ter um tamanho menor do que o máximo permitido para a camada de serviço de destino. 
- Ao atualizar um banco de dados com [Replicação geográfica padrão](https://msdn.microsoft.com/library/azure/dn758204.aspx) ou [Replicação geográfica](https://msdn.microsoft.com/library/azure/dn741339.aspx) habilitada, é necessário atualizar primeiro seus bancos de dados secundários para o nível de desempenho desejado antes de atualizar o banco de dados primário.
- Ao fazer downgrade de uma camada de serviço Premium, primeiro, você deve encerrar todos os relacionamentos de Replicação Geográfica. Você pode seguir as etapas descritas no tópico [Finalizar uma relação de cópia contínuo](https://msdn.microsoft.com/library/azure/dn741323.aspx) para interromper o processo de replicação entre os bancos de dados primários e secundários ativos.
- As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se estiver fazendo downgrade, talvez você perca a capacidade de fazer uma restauração pontual ou tenha um período menor de retenção do backup. Para saber mais, confira [Backup e restauração do Banco de dados SQL do Azure](https://msdn.microsoft.com/library/azure/jj650016.aspx).
- Você pode fazer até quatro alterações individuais de banco de dados (camada de serviço ou níveis de desempenho) em um período de 24 horas.
- As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.



**Para concluir este artigo, você precisa do seguinte:**

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um banco de dados SQL do Azure. Se você não tiver um banco de dados SQL, crie um executando as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).
- PowerShell do Azure. Você pode baixar e instalar o módulo PowerShell no Azure executando o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](powershell-install-configure.md).

Os cmdlets para alteração da camada de serviços do Bancos de Dados SQL do Azure estão localizados no módulo do Gerenciador de Recursos do Azure. Quando você inicia o PowerShell do Azure, os cmdlets no módulo do Azure são importados por padrão. Para alternar para o módulo do Gerenciador de Recursos do Azure, use o cmdlet Switch-AzureMode.

	Switch-AzureMode -Name AzureResourceManager

Se você executar o **Switch-AzureMode** e o aviso: O *cmdlet Switch-AzureMode foi substituído e será removido em uma versão futura* for exibido, não tem problema; basta passar para a próxima etapa e configurar suas credenciais.

Para obter informações detalhadas, confira [Usando o Windows PowerShell com o Gerenciador de Recursos](powershell-azure-resource-manager.md).

## Configurar suas credenciais e selecionar sua assinatura

Em primeiro lugar, você deve estabelecer o acesso à sua conta do Azure e, depois, iniciar o PowerShell e executar o cmdlet a seguir. Na tela de logon, insira o mesmo email e senha que você usa para entrar no portal do Azure.

	Add-AzureAccount

Depois de se conectar com êxito, você verá algumas informações na tela, incluindo a ID usada para entrar e as assinaturas do Azure as quais você tem acesso.


### Selecionar sua assinatura do Azure

Para selecionar a assinatura, é necessário ter a ID ou o nome da assinatura (**-SubscriptionName**). É possível copiar a ID da assinatura nas informações exibidas na etapa anterior ou, se tiver várias assinaturas e precisar de mais detalhes, você pode executar o cmdlet **Get-AzureSubscription** e copiar as informações da assinatura desejada do resultset. Quando tiver sua assinatura, execute o seguinte cmdlet:

	$SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureSubscription -SubscriptionId $SubscriptionId

Depois de executar **Select-AzureSubscription** com êxito, você retornará ao prompt do PowerShell. Se tiver mais de uma assinatura, você poderá executar **Get-AzureSubscription** e verificar se a assinatura que deseja usar mostra **IsCurrent: True**.


 


## Alterar a camada de serviço e o nível de desempenho do banco de dados SQL

Execute o cmdlet **Set-AzureSqlDatabase** e defina o **-RequestedServiceObjectiveName** com o nível de desempenho do tipo de preço desejado, por exemplo, *S0*, *S1*, *S2*, *S3*, *P1*, *P2*, ...

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"

    $NewEdition = "Standard"
    $NewPricingTier = "S2"

    $ScaleRequest = Set-AzureSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier


  

   


## Amostra de script do PowerShell para alterar a camada de serviços e o nível de desempenho do banco de dados SQL

    
	Switch-AzureMode -Name AzureResourceManager
    
    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest
    
        


## Próximas etapas

- [Escalar horizontalmente](sql-database-elastic-scale-get-started.md)
- [Conectar-se a e consultar um banco de dados SQL com SSMS](sql-database-connect-query-ssms.md)
- [Exportar um banco de dados SQL do Azure](sql-database-export-powershell.md)

## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Oct15_HO1-->