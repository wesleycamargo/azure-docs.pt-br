<properties
	pageTitle="Replicar máquinas virtuais Hyper-V em nuvens do VMM usando o Azure Site Recovery e PowerShell (Resource Manager) | Microsoft Azure"
	description="Replicar máquinas virtuais de Hyper-V em nuvens do VMM usando o Azure Site Recovery e PowerShell"
	services="site-recovery"
	documentationCenter=""
	authors="Rajani-Janaki-Ram"
	manager="rochakm"
	editor="raynew"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="rajanaki"/>

# Replicar máquinas virtuais Hyper-V em nuvens VMM para Azure usando o PowerShell e o Azure Resource Manager

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - ARM](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portal clássico](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - clássico](site-recovery-deploy-with-powershell.md)



## Visão geral

O Azure Site Recovery colabora com sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação. Para obter uma lista completa de cenários de implantação, consulte a [Visão geral do Azure Site Recovery](site-recovery-overview.md).

Este artigo mostra como usar o PowerShell para automatizar tarefas comuns que você precisa executar quando você configura o Azure Site Recovery para replicar máquinas virtuais Hyper-V em nuvens do System Center VMM para o armazenamento do Azure.

O artigo inclui pré-requisitos para o cenário e mostra a você como:

- Configurar um cofre dos Serviços de Recuperação
- Instalar o Provedor do Azure Site Recovery no servidor VMM de origem
- Registrar o servidor no cofre e adicionar uma conta de armazenamento do Azure
- Instalar o agente dos Serviços de Recuperação do Azure nos servidores host do Hyper-V
- Configurar as definições de proteção para nuvens VMM, que serão aplicadas a todas as máquinas virtuais protegidas
- Habilitar a proteção para essas máquina virtuais
- Testar o failover para garantir que tudo esteja funcionando como esperado.

Se você enfrentar problemas ao configurar esse cenário, publique suas perguntas no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação do Gerenciador de Recursos.

## Antes de começar

Verifique se estes pré-requisitos estão em vigor:

### Pré-requisitos do Azure

- Você precisará de uma conta do [Microsoft Azure](https://azure.microsoft.com/). Se não tiver uma, comece com uma [conta gratuita](https://azure.microsoft.com/free). Além disso, você pode ler sobre os [preços do Gerenciador do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
- Você precisará de uma assinatura CSP se estiver tentando a replicação em um cenário de assinatura CSP. Saiba mais sobre o programa CSP em [como se registrar no programa CSP](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
- Você precisará de uma conta de armazenamento do Azure V2 (ARM) para armazenar os dados replicados no Azure. A conta precisa estar com a replicação geográfica habilitada. Ela deve estar localizada na mesma região que o serviço Azure Site Recovery e estar associada à mesma assinatura ou assinatura CSP. Para aprender mais sobre como configurar o armazenamento do Azure, confira [Introdução ao Armazenamento do Microsoft Azure](../storage/storage-introduction.md) para obter uma referência.
- Você precisará verificar se as máquinas virtuais que deseja proteger atendem aos [requisitos de máquina virtual do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

> [AZURE.NOTE] Atualmente, apenas operações no nível de VM são possíveis por meio do Powershell. Em breve, será disponibilizado o suporte para operações no nível de plano de recuperação. Por enquanto, você está limitado a executar failovers somente em granularidade de 'VM protegida', e não em um nível de Plano de Recuperação.

### Pré-requisitos do VMM
- Você precisará do servidor VMM em execução no System Center 2012 R2.
- Qualquer servidor VMM que contenha máquinas virtuais que você deseje proteger deverá estar executando o Provedor do Azure Site Recovery. Ele é instalado durante a implantação do Azure Site Recovery.
- Você precisará de pelo menos uma nuvem no servidor VMM que deseja proteger. A nuvem deve conter:
	- Um ou mais grupos de hosts do VMM
	- Um ou mais servidores de host do Hyper-V ou clusters em cada grupo de host.
	- Uma ou mais máquinas virtuais no servidor Hyper-V de origem.
- Saiba mais sobre como configurar nuvens VMM:
	- Leia mais sobre nuvens VMM privadas em [Novidades da nuvem privada com o System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) e em [VMM 2012 e as nuvens](http://go.microsoft.com/fwlink/?LinkId=324956).
	- Saiba mais em [Configurando a malha de nuvem VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- Depois que os elementos de malha de nuvem estiverem em vigor, veja como criar nuvens privadas em [Criando uma nuvem privada no VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) e [Passo a passo: criando de nuvens privadas com o System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).


### Pré-requisitos do Hyper-V

- Os servidores host Hyper-V devem estar executando pelo menos o Windows Server 2012 com a função Hyper-V e ter as últimas atualizações instaladas.
- Se você estiver executando o Hyper-V em um cluster, observe que o agente de cluster não será criado automaticamente se você tiver um cluster de baseados em endereços IP estáticos. Você precisará configurar o agente de cluster manualmente. Por
- Para obter instruções, confira [Como Configurar o Agente de Réplica do Hyper-V](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx).
- Qualquer cluster ou servidor host Hyper-V para o qual você desejar gerenciar a proteção deverá ser incluído em uma nuvem VMM.

### Pré-requisitos de mapeamento de rede
Quando você protege máquinas virtuais no Azure, o mapeamento de rede mapeia as redes de máquina virtual no servidor VMM de origem e visa as redes do Azure para habilitar o seguinte:

- Todas as máquinas que passarem por failover na mesma rede poderão se conectar entre si, independentemente do plano de recuperação em que estão.
- Além disso, se um gateway de rede for configurado na rede Azure de destino, as máquinas virtuais poderão se conectar a outras máquinas virtuais locais.
- Se você não configurar o mapeamento de rede, somente as máquinas virtuais que fazem failover no mesmo plano de recuperação poderão se conectar entre si após failover no Azure.

Se desejar implantar o mapeamento de rede, você precisará do seguinte:

- As máquinas virtuais que você deseja proteger no servidor VMM de origem devem estar conectadas a uma rede VM. Essa rede deve ser vinculada a uma rede lógica que esteja associada à nuvem.
- Uma rede do Azure à qual as máquinas virtuais replicadas possam se conectar após o failover. Você selecionará essa rede no momento do failover. A rede deve estar na mesma região de sua assinatura do Azure Site Recovery.

Saiba mais sobre mapeamento de rede em

- [Como configurar redes lógicas no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Como configurar redes e gateways de VMs no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
- [Como configurar e monitorar redes virtuais no Azure](https://azure.microsoft.com/documentation/services/virtual-network/)


###Pré-requisitos do PowerShell
Verifique se você tem o PowerShell do Azure pronto para uso. Se você já estiver usando o PowerShell, precisará atualizar para a versão 0.8.10 ou posterior. Para obter mais informações sobre como configurar o PowerShell, confira o [Guia de instalação e de configuração do Azure PowerShell](../powershell-install-configure.md). Depois de instalar e configurar o PowerShell, você poderá exibir todos os cmdlets disponíveis para o serviço [aqui](https://msdn.microsoft.com/library/dn850420.aspx).

Para obter dicas que podem ajudar você a usar os cmdlets, como valores de parâmetro, entradas e saídas, que normalmente são tratados no Azure PowerShell, confira [Introdução aos cmdlets do Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## Etapa 1: definir a assinatura 

1. No Azure PowerShell, faça logon na conta do Azure usando os seguintes cmdlets:
 
		$UserName = "<user@live.com>"
		$Password = "<password>"
		$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
		$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
		Login-AzureRmAccount #-Credential $Cred 
	

2. Obtenha uma lista das suas assinaturas. Essa lista também incluirá as IDs de assinatura de cada uma das assinaturas. Anote a ID da assinatura na qual você quer criar o cofre dos serviços de recuperação

		Get-AzureRmSubscription 

3. Defina a assinatura na qual o cofre dos serviços de recuperação deverá ser criado mencionando a ID da assinatura

		Set-AzureRmContext –SubscriptionID <subscriptionId>


## Etapa 2: Criar um cofre dos Serviços de Recuperação 

1. Crie um grupo de recursos do ARM, caso ainda não tenha um

		New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Crie um novo cofre de Serviços de Recuperação e salve o objeto de cofre ASR criado em uma variável (será usado posteriormente). Você também pode recuperar o objeto de cofre ASR pós-criação usando o cmdlet Get-AzureRMRecoveryServicesVault:-

		$vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location 

## Etapa 3: Configurar o contexto do Cofre dos Serviços de Recuperação

1.  Defina o contexto de cofre, executando o comando abaixo.

		Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## Etapa 4: instalar o Provedor do Azure Site Recovery

1.	Na máquina da VMM, crie um diretório executando o seguinte comando:
	
		New-Item c:\ASR -type directory
		
2.	Extraia os arquivos usando o provedor baixado, executando o seguinte comando
	
		pushd C:\ASR\
		.\AzureSiteRecoveryProvider.exe /x:. /q

	
3.	Instale o provedor usando os comandos a seguir:
	
		.\SetupDr.exe /i
		$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
		do
		{
		                $isNotInstalled = $true;
		                if(Test-Path $installationRegPath)
		                {
		                                $isNotInstalled = $false;
		                }
		}While($isNotInstalled)

    Aguarde a conclusão da instalação.
	
4.	Registre o servidor no cofre usando o seguinte comando:
	
		$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
		pushd $BinPath
		$encryptionFilePath = "C:\temp".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

	
## Etapa 5: criar uma conta de armazenamento do Azure

1. Se você não tiver uma conta de armazenamento do Azure, crie uma conta habilitada para replicação geográfica na mesma região que o cofre executando o seguinte comando:

		$StorageAccountName = "teststorageacc1"	#StorageAccountname
		$StorageAccountGeo  = "Southeast Asia" 	
		$ResourceGroupName =  “myRG” 			#ResourceGroupName 
		$RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Observe que a conta de armazenamento precisa estar na mesma região que o serviço Azure Site Recovery e associada à mesma assinatura.

## Etapa 6: instalar o agente dos Serviços de Recuperação do Azure

1. Baixe o agente dos Serviços de Recuperação do Azure em [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) e instale-o em cada servidor host Hyper-V localizado nas nuvens VMM que quer proteger.

2. Execute o comando a seguir em todos os hosts VMM:

	marsagentinstaller.exe /q /nu

## Etapa 7: definir as configurações da proteção de nuvem

1.	Crie uma política de replicação no Azure executando o seguinte comando:

	
		$ReplicationFrequencyInSeconds = "300";    	#options are 30,300,900
		$PolicyName = “replicapolicy”
		$Recoverypoints = 6            		#specify the number of recovery points 

		$policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

2.	Obtenha um contêiner de proteção executando os seguintes comandos:
	
		$PrimaryCloud = "testcloud"
		$protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  
  
3.	Obtenha os detalhes da política para uma variável usando o trabalho que foi criado e mencionando o nome amigável da política:

		$policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.	Inicie a associação do contêiner de proteção à política de replicação:

		$associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  

5.	Após a conclusão do trabalho, execute o seguinte comando:
   
		$job = Get-AzureRmSiteRecoveryJob -Job $associationJob
   		if($job -eq $null -or $job.StateDescription -ne "Completed")
   		 {
        $isJobLeftForProcessing = $true;
    	}

6.	Após a conclusão do processamento do trabalho, execute o seguinte comando:

		if($isJobLeftForProcessing)
    	{
    	Start-Sleep -Seconds 60
    	}
        }While($isJobLeftForProcessing)

Para verificar a conclusão da operação, execute as etapas em [Monitorar a Atividade](#monitor).

## Etapa 8: configurar o mapeamento de rede

Antes de começar o mapeamento de rede, verifique se as máquinas virtuais no servidor VMM de origem estão conectadas a uma rede VM. Além disso, crie uma ou mais redes virtuais do Azure.

Saiba mais sobre como criar uma rede virtual usando o Azure Resource Manager e o PowerShell, em [Criar uma rede virtual com uma conexão VPN site a site usando o Azure Resource Manager e o PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

Observe que várias redes de máquina virtual podem ser mapeadas para uma única rede do Azure. Se a rede de destino tiver várias sub-redes e uma dessas sub-redes tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, a máquina virtual de réplica será conectada à sub-rede de destino após o failover. Se não houver uma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.

1. O primeiro comando obtém servidores para o cofre atual do Azure Site Recovery. O comando armazena os servidores do Microsoft Azure Site Recovery na variável de matriz $Servers.

		$Servers = Get-AzureRmSiteRecoveryServer

2. O segundo comando obtém a rede de recuperação de site para o primeiro servidor na matriz $Servers. O comando armazena as redes na variável $Networks.


    	$Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

3. O terceiro comando obtém redes virtuais do Azure e, em seguida, esse valor na variável $AzureVmNetworks.
   
		$AzureVmNetworks =  Get-AzureRmVirtualNetwork

4. O cmdlet final cria um mapeamento entre a rede principal e a rede de máquina virtual do Azure. O cmdlet especifica a rede principal como o primeiro elemento de $Networks. O cmdlet especifica uma rede de máquina virtual como o primeiro elemento de $AzureVmNetworks.

		New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]


## Etapa 9: habilitar a proteção para máquinas virtuais

Depois que os servidores, nuvens e redes estiverem configurados corretamente, você poderá habilitar a proteção para máquinas virtuais na nuvem.

 Observe o seguinte:

 - As máquinas virtuais devem cumprir os requisitos do Azure. Verifique os [pré-requisitos e suporte](site-recovery-best-practices.md) no guia de planejamento.

 - Para habilitar a proteção, o sistema operacional e as propriedades do disco do sistema operacional devem estar definidos para as máquinas virtuais. Ao criar uma máquina virtual no VMM usando um modelo de máquina virtual, é possível definir a propriedade. Você também pode definir essas propriedades para máquinas virtuais existentes nas guias **Geral** e **Configuração de Hardware** das propriedades da máquina virtual. Se você não definir essas propriedades no VMM, poderá configurá-las no portal de Recuperação de Site do Azure.


  1. Para habilitar a proteção, execute o seguinte comando para obter o contêiner de proteção:
	
			$ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName
	
  2. Obtenha a entidade de proteção (VM) executando o seguinte comando:
	
	 		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer
		
  3. Habilite o DR para a VM executando o seguinte comando:

			$jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1



## Testar a implantação

Para testar sua implantação, você pode executar um failover de teste em uma única máquina virtual ou criar um plano de recuperação com várias máquinas virtuais e executar um failover de teste para o plano. O failover de teste simula o mecanismo de failover e recuperação em uma rede isolada. Observe que:

- Se você quiser se conectar à máquina virtual no Azure usando a Área de Trabalho Remota após o failover, habilite a Conexão de Área de Trabalho Remota na máquina virtual antes de executar o failover de teste.
- Após o failover, você usará um endereço IP público para se conectar à máquina virtual no Azure usando a Área de Trabalho Remota. Se você quiser fazer isso, verifique se não tem nenhuma política de domínio que o impeça de se conectar a uma máquina virtual usando um endereço público.

Para verificar a conclusão da operação, execute as etapas em [Monitorar a Atividade](#monitor).


### Execute um teste de failover

1.	Inicie o teste de failover executando o seguinte comando:
	
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

		$jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### Executar um failover planejado

1. Inicie o failover planejado executando o seguinte comando:
	
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

		$jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### Executar um failover não planejado

1. Inicie o failover não planejado executando o seguinte comando:
		
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

		$jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

	
## <a name=monitor></a> Monitorar a atividade

Use os seguintes comandos para monitorar a atividade. Observe que é necessário aguardar a conclusão do processamento entre os trabalhos.

	Do
	{
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        	$isJobLeftForProcessing = $true;
        }

	if($isJobLeftForProcessing)
        {
        	Start-Sleep -Seconds 60
        }
	}While($isJobLeftForProcessing)



## Próximas etapas

[Leia mais](https://msdn.microsoft.com/library/azure/mt637930.aspx) sobre o Azure Site Recovery com cmdlets do PowerShell do Azure Resource Manager.

<!---HONumber=AcomDC_0921_2016-->