<properties
	pageTitle="Proteger servidores no Azure usando o Azure PowerShell com o Gerenciador de Recursos do Azure | Microsoft Azure"
	description="Automatize a proteção de servidores no Azure com o Azure Site Recovery usando o PowerShell e o Gerenciador de Recursos do Azure."
	services="site-recovery"
	documentationCenter=""
	authors="bsiva"
	manager="abhiag"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="12/09/2015"
	ms.author="bsiva"/>

# Azure Site Recovery usando o PowerShell e o Gerenciador de Recursos do Azure.


## Visão geral

O Azure Site Recovery colabora com sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação. Para obter uma lista completa de cenários de implantação, consulte a [Visão geral do Azure Site Recovery](site-recovery-overview.md).

O PowerShell no Azure é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell. Ele pode trabalhar com dois tipos de modulo — o módulo de Perfil do Azure ou o módulo ARM (Gerenciador de Recursos do Azure).

Os cmdlets do PowerShell do ASR (Azure Site Recovery) disponíveis com o Azure PowerShell para ARM permitem que você proteja e recupere seus servidores no Azure.

Este artigo descreve como usar o Windows PowerShell® juntamente com o ARM na implantação do Azure Site Recovery para configurar e orquestrar a proteção de servidor no Azure com a ajuda de um exemplo. O exemplo usado neste artigo mostra como proteger, executar failover e recuperar máquinas virtuais em um host Hyper-V para o Azure usando o Azure PowerShell com ARM.

> [AZURE.NOTE]Atualmente, os cmdlets do PowerShell para o Azure Site Recovery permitem que você configure o site do VMM para site do VMM, o site do VMM para o Azure e o site do Hyper-V para cenários do Azure. O suporte para outros cenários de ASR será adicionado em breve.

Não é preciso ser um especialista no PowerShell para usar este artigo, mas é pressuposto que você entenda os conceitos básicos, como módulos, cmdlets e sessões. Para obter mais informações sobre o Windows PowerShell, veja [Introdução ao Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx). Saiba mais sobre como [Usar o Azure PowerShell com o Gerenciador de Recursos do Azure](powershell-azure-resource-manager.md).


## Principais recursos

- Proteger os servidores no Azure, executar failover e recuperá-los em IaaS do Azure (ARM ou clássico)
- Os parceiros da Microsoft que fazem parte do programa CSP (Provedor de Solução de Nuvem) podem configurar e gerenciar a proteção dos servidores de seus clientes na assinatura de CSP do cliente (assinatura de locatário)

## Antes de começar

Verifique se estes pré-requisitos estão em vigor:

- Você precisará de uma conta do [Microsoft Azure](http://azure.microsoft.com/). Você precisará de uma conta do [Microsoft Azure](http://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](pricing/free-trial/). Além disso, leia sobre os [preços do Gerenciador do Azure Site Recovery](http://azure.microsoft.com/pricing/details/site-recovery/).
- Você precisará do Azure PowerShell 1.0. Para obter informações sobre essa versão e como instalá-la, confira [Azure PowerShell 1.0](http://azure.microsoft.com/).
- Você precisará ter os módulos [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) e [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) instalados. É possível obter as versões mais recentes desses módulos na [Galeria do PowerShell](https://www.powershellgallery.com/)

Este artigo ilustra como usar o Azure PowerShell com ARM para configurar e gerenciar a proteção dos seus servidores com a ajuda de um exemplo. O exemplo usado neste artigo mostra como proteger uma máquina virtual em execução em um host Hyper-V para o Azure; os pré-requisitos que se seguem são específicos a esse exemplo. Para obter um conjunto mais abrangente dos requisitos para os vários cenários de ASR, veja a documentação referente a um cenário específico.

- Você precisará de um host Hyper-V que executa o Windows Server 2012 R2 contendo uma ou mais máquinas virtuais.
- Os servidores Hyper-V devem estar conectados à Internet, diretamente ou por meio de um proxy.
- As máquinas virtuais que você deseja proteger devem estar em conformidade com os [Pré-requisitos para Máquinas Virtuais](site-recovery-best-practices.md#virtual-machines).
	

## Etapa 1: Fazer logon em sua conta do Azure


1. Abra um console do PowerShell e execute este comando para fazer logon em sua conta do Azure. O cmdlet abre uma página da Web que solicitará suas credenciais de conta.

    	Login-AzureRmAccount

	Como alternativa, você também poderá fornecer suas credenciais de conta como um parâmetro para o cmdlet `Login-AzureRmAccount` usando o parâmetro `-Credential`.
	
	Se você é um parceiro CSP trabalhando em nome de um locatário, você precisará especificar o cliente como um locatário usando seu tenantID ou nome de domínio primário de locatário

		Login-AzureRmAccount -Tenant "fabrikam.com"

2. Uma conta pode ter várias assinaturas, de modo que você precisará associar à conta a assinatura que deseja usar.

    	Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  Verifique se sua assinatura está registrada para usar os Provedores do Azure para os Serviços de Recuperação e a Recuperação de Site usando os seguintes comandos:-

	- `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
	-  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

	Se o RegistrationState estiver definido como Registrado na saída dos dois comandos acima, você pode seguir para a Etapa 2. Caso contrário, você precisará registrar o provedor ausente em sua assinatura.


	Para registrar o provedor do Azure para a Recuperação de Site, faça o seguinte:

    	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
	
	Da mesma forma, se estiver usando os cmdlets dos Serviços de Recuperação pela primeira vez em sua assinatura, você precisará registrar o provedor do Azure para os Serviços de Recuperação. Antes de poder fazer isso, você precisará primeiro habilitar o acesso ao provedor dos Serviços de Recuperação em sua assinatura executando o seguinte comando:

		Register-AzureRmProviderFeature -FeatureName betaAccess -ProviderNamespace Microsoft.RecoveryServices

	>[AZURE.TIP]Pode levar até uma hora para habilitar o acesso ao provedor dos Serviços de Recuperação em sua assinatura após a conclusão bem-sucedida do comando acima. Tentativas de registrar o provedor dos Serviços de Recuperação em sua assinatura usando o comando `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices` poderão falhar durante o processo. Se isso acontecer, aguarde uma hora e tente novamente.

	Depois de habilitar o acesso ao provedor dos Serviços de Recuperação em sua assinatura, registre o provedor em sua assinatura executando o seguinte comando

		Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

	Verifique se os provedores forem registrados com êxito usando os comandos `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` e `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

	

## Etapa 2: configurar o cofre dos Serviços de Recuperação

1. Crie um grupo de recursos do ARM no qual você criará o cofre ou usar um grupo de recursos existente. Você pode criar um novo grupo de recursos do ARM usando o seguinte comando:

		New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

	em que a variável $ResourceGroupName contém o nome do grupo de recursos do ARM que você deseja criar e a variável $Geo contém a região do Azure na qual o grupo de recursos será criado (por ex., Sul do Brasil)

	Você pode obter uma lista de grupos de recursos do ARM em sua assinatura usando o cmdlet `Get-AzureRmResourceGroup`.

2. Crie um novo cofre dos Serviços de Recuperação do Azure da seguinte maneira:-

		$vault = New-AzureRmRecoveryServicesVault -Name <string> -ResouceGroupName <string> -Location <string>

	É possível recuperar uma lista de cofres existentes usando o cmdlet `Get-AzureRmRecoveryServicesVault`.

> [AZURE.NOTE]Se você quiser executar operações nos cofres do ASR criados usando o portal clássico ou o módulo do Azure PowerShell do Gerenciamento de Serviço do Azure, é possível recuperar uma lista de cofres desse tipo usando o cmdlet `Get-AzureRmSiteRecoveryVault`. É recomendável que para todas as novas operações, você crie um novo cofre dos Serviços de Recuperação. Os cofres da Recuperação de Site criados anteriormente continuarão tendo suporte, mas não terão os recursos mais recentes.

## Etapa 3: gerar uma chave de registro do cofre

1. Gere e baixe uma chave de registro do cofre para o seu cofre.

    	Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -Path $path
2. Importar o arquivo de configurações do cofre baixado para definir o contexto de cofre
 
		Import-AzureRmSiteRecoveryVaultSettingsFile -Path $path 

## Etapa 4: criar um Site do Hyper-V e gerar uma nova chave de registro do cofre para o site.

1. Crie um novo site do Hyper-V da seguinte maneira:
		
		$sitename = "MySite"                #Specify site friendly name
		New-AzureRmSiteRecoverySite -Name $sitename

	Este cmdlet inicia um Trabalho do ASR para criar o site e retorna um objeto do Trabalho do ASR. Aguarde a conclusão do Trabalho e verifique se ele foi concluído com êxito.

	É possível recuperar o objeto do Trabalho do ASR e verificar o status atual do trabalho usando o cmdlet Get-AzureRmSiteRecoveryJob 
2. Gere e baixe uma chave de registro para o site:-

		$SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
		Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path
	
	Copie a chave baixada no host Hyper-V. Você precisará da chave para registrar o host Hyper-V no site

	
## Etapa 5: instalar o provedor do Azure Site Recovery e o Agente dos Serviços de Recuperação do Azure no host Hyper-V

1. Baixe [aqui](https://aka.ms/downloaddra) o instalador para a versão mais recente do Provedor
2. Execute o instalador no host Hyper-V e, no final da instalação, continue para a etapa de Registro
3. Forneça a chave de registro de site baixada quando solicitado e conclua o registro do host Hyper-V no site
4. Verifique se o host Hyper-V foi registrado no site usando o seguinte:

		$server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname 

## Etapa 6: criar uma Política de Replicação e associá-la ao Contêiner de Proteção

1. Criar uma Política de Replicação:-

		$ReplicationFrequencyInSeconds = "300";    	#options are 30,300,900
		$PolicyName = “replicapolicy”
		$Recoverypoints = 6            		#specify the number of recovery points
		$storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id 

		$PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

	Verifique o trabalho retornado para garantir que a criação da política de replicação foi bem-sucedida.

	>[AZURE.IMPORTANT]A Conta de Armazenamento especificada deve estar na mesma região do Azure que o cofre de serviços de recuperação e deve ter a replicação geográfica habilitada.
	>
	> - Se a conta de armazenamento de Recuperação especificada for do tipo Armazenamento (Clássico) do Azure, o failover dos computadores protegidos recuperará o computador para o Azure IaaS (Clássico)
	> - Se a conta de armazenamento de Recuperação especificada for do tipo Armazenamento (ARM) do Azure, o failover dos computadores protegidos recuperará o computador para o Azure IaaS (ARM)
	
 
2. Obtenha o contêiner de proteção corresponde ao site:-
		
		$protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.	Inicie a associação do contêiner de proteção à política de replicação:

		$Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
		$associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

	Aguarde a conclusão do trabalho de associação e certifique-se de que ele foi concluído com êxito

##Etapa 7: habilitar a proteção para máquinas virtuais

1. Obtenha a Entidade de Proteção correspondente à VM que deseja proteger:-
		
		$VMFriendlyName = "Fabrikam-app"                    #Name of the VM 
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. Inicie a proteção da máquina virtual:
		
		$Ostype = "Windows"                                 # "Windows" or "Linux"
		$DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

	>[AZURE.IMPORTANT]A Conta de Armazenamento especificada deve estar na mesma região do Azure que o cofre de serviços de recuperação e deve ter a replicação geográfica habilitada.
	>
	> - Se a conta de armazenamento de Recuperação especificada for do tipo Armazenamento (Clássico) do Azure, o failover dos computadores protegidos recuperará o computador para o Azure IaaS (Clássico)
	> - Se a conta de armazenamento de Recuperação especificada for do tipo Armazenamento (ARM) do Azure, o failover dos computadores protegidos recuperará o computador para o Azure IaaS (ARM)

	> Se a VM que você estiver protegendo tiver mais de um disco anexado a ela, será necessário especificar o disco do Sistema Operacional usando o parâmetro OSDiskName.

3. Aguarde até que as máquinas virtuais atinjam uma replicação pós-inicial de estado protegido. Isso levará algum tempo dependendo de fatores como a quantidade de dados a serem replicados e a largura de banda upstream disponível para o Azure. O State e StateDescription do trabalho serão atualizados da seguinte forma depois que a VM atingir um estado protegido.

		
		PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

		PS C:\> $DRjob | Select-Object -ExpandProperty State
		Succeeded

		PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
		Completed

4. Atualize propriedades de recuperação, como do tamanho da função VM e a rede do Azure à qual a NIC da VM será anexada após o failover.

		PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

		PS C:\> $VMFriendlyName = "Fabrikam-App"

		PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

		PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

		PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

		PS C:\> $UpdateJob


		Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
		ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
		                   s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
		Type             : Microsoft.RecoveryServices/vaults/replicationJobs
		JobType          : UpdateVmProperties
		DisplayName      : Update the virtual machine
		ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
		State            : Succeeded
		StateDescription : Completed
		StartTime        : 10-12-2015 17:55:53 +00:00
		EndTime          : 10-12-2015 17:55:54 +00:00
		TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
		TargetObjectType : ProtectionEntity
		TargetObjectName : Fabrikam-App
		AllowedActions   : {Restart}
		Tasks            : {UpdateVmPropertiesTask}
		Errors           : {}



## Etapa 8: executar um failover de teste

1. Execute um teste de failover:
		
		$nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group
		
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

		$TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. Verifique se a VM de Teste é criada no Azure (o trabalho de Failover de Teste está suspenso após a criação da VM de teste no Azure). O trabalho será concluído limpando os artefatos criados ao retomar o trabalho, conforme ilustrado na próxima etapa)

3. Concluir o Failover de teste

    	$TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

<!---HONumber=AcomDC_1217_2015-->