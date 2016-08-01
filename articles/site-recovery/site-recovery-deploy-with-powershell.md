<properties
	pageTitle="Replicar máquinas virtuais Hyper-V em nuvens do VMM usando o Azure Site Recovery e PowerShell | Microsoft Azure"
	description="Saiba como automatizar a replicação de máquinas virtuais Hyper-V em nuvens do VMM usando o Site Recovery e o PowerShell."
	services="site-recovery"
	documentationCenter=""
	authors="bsiva"
	manager="abhiag"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="bsiva"/>

# Replicar máquinas virtuais do Hyper-V em nuvens VMM no Azure usando o PowerShell - Clássico

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-azure.md)
- [PowerShell – Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portal clássico](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - clássico](site-recovery-deploy-with-powershell.md)


## Visão geral

O Azure Site Recovery colabora com sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação. Para obter uma lista completa de cenários de implantação, consulte a [Visão geral do Azure Site Recovery](site-recovery-overview.md).

Este artigo mostra como usar o PowerShell para automatizar tarefas comuns que você precisa executar quando você configura o Azure Site Recovery para replicar máquinas virtuais Hyper-V em nuvens do System Center VMM para o armazenamento do Azure.

O artigo inclui os pré-requisitos para o cenário e mostra como configurar um cofre de Recuperação de Site, instalar o Provedor do Azure Site Recovery no servidor VMM de origem, registrar o servidor no cofre, adicionar uma conta de armazenamento do Azure, instalar o agente dos Serviços de Recuperação do Azure nos servidores de host Hyper-V, definir configurações de proteção para nuvens VMM que serão aplicadas a todas as máquinas virtuais protegidas e habilitar a proteção para essas máquinas virtuais. Conclua testando o failover para verificar se tudo está funcionando conforme o esperado.

Se você enfrentar problemas ao configurar esse cenário, publique suas perguntas no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


> [AZURE.NOTE] O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica.



## Antes de começar

Verifique se estes pré-requisitos estão em vigor:

### Pré-requisitos do Azure

- Você precisará de uma conta do [Microsoft Azure](https://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados no Azure. A conta precisa estar com a replicação geográfica habilitada. Ela deve estar localizada na mesma região que o cofre do Azure Site Recovery e ser associada à mesma assinatura. [Saiba mais sobre o Armazenamento do Azure](../storage/storage-introduction.md).
- Você precisará verificar se as máquinas virtuais que deseja proteger atendem aos [requisitos de máquina virtual do Azure](site-recovery-best-practices.md#virtual-machines).

### Pré-requisitos do VMM
- Você precisará do servidor VMM em execução no System Center 2012 R2.
- Você precisará de pelo menos uma nuvem no servidor VMM que deseja proteger. A nuvem deve conter:
	- Um ou mais grupos de hosts do VMM
	- Um ou mais servidores host Hyper-V ou clusters em cada grupo de hosts.
	- Uma ou mais máquinas virtuais no servidor Hyper-V de origem.

### Pré-requisitos do Hyper-V

- Os servidores host Hyper-V devem estar executando pelo menos o Windows Server 2012 com a função Hyper-V e ter as últimas atualizações instaladas.
- Se você estiver executando o Hyper-V em um cluster, observe que o agente de cluster não será criado automaticamente se você tiver um cluster de baseados em endereços IP estáticos. Você precisará configurar o agente de cluster manualmente. Para fazer isso, no Gerenciador do Servidor > Gerenciador de Cluster de Failover, conecte-se ao cluster, clique em **Configurar função** e selecione **Agente de Réplica do Hyper-V** na tela **Selecionar função** do Assistente para alta disponibilidade.
- Qualquer cluster ou servidor host Hyper-V para o qual você desejar gerenciar a proteção deverá ser incluído em uma nuvem VMM.

### Pré-requisitos de mapeamento de rede
Quando você proteger máquinas virtuais na rede do Azure mapeando mapas entre redes VM no servidor VMM de origem e nas redes do Azure destino para habilitar o seguinte:

- Todas as máquinas que passarem por failover na mesma rede poderão se conectar entre si, independentemente do plano de recuperação em que estão.
- Além disso, se um gateway de rede for configurado na rede Azure de destino, as máquinas virtuais poderão se conectar a outras máquinas virtuais locais.
- Se você não configurar o mapeamento de rede, somente máquinas virtuais com failover no mesmo plano de recuperação poderão se conectar entre si após o failover no Azure.

Se desejar implantar o mapeamento de rede, você precisará do seguinte:

- As máquinas virtuais que você deseja proteger no servidor VMM de origem devem estar conectadas a uma rede VM. Essa rede deve ser vinculada a uma rede lógica que esteja associada à nuvem.
- Uma rede do Azure à qual as máquinas virtuais replicadas possam se conectar após o failover. Você selecionará esta rede no momento do failover. A rede deve estar na mesma região que sua assinatura do Azure Site Recovery.
- [Saiba mais](site-recovery-network-mapping.md) sobre o mapeamento de rede:

###Pré-requisitos do PowerShell
Verifique se você tem o PowerShell do Azure pronto para uso. Se você já estiver usando o PowerShell, precisará atualizar para a versão 0.8.10 ou posterior. Para saber mais sobre como configurar o PowerShell, confira [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md). Depois de instalar e configurar o PowerShell, você poderá exibir todos os cmdlets disponíveis para o serviço [aqui](https://msdn.microsoft.com/library/dn850420.aspx).

Para obter dicas que podem ajudar você a usar os cmdlets, por exemplo, como os valores de parâmetro, as entradas e saídas são tratadas normalmente no Azure PowerShell, confira [Introdução aos cmdlets do Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## Etapa 1: definir sua assinatura 

No PowerShell, execute estes cmdlets:

```
$UserName = "<user@live.com>"
$Password = "<password>"
$AzureSubscriptionName = "prod_sub1"

$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
Add-AzureAccount -Credential $Cred;
$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

```

Substitua os elementos dentro de "< >" por suas informações específicas.

## Etapa 2: criar um cofre da Recuperação de Site

No PowerShell, substitua os elementos dentro de "< >" por informações específicas, e execute estes comandos:

```

$VaultName = "<testvault123>"
$VaultGeo  = "<Southeast Asia>"
$OutputPathForSettingsFile = "<c:>"

```


```
New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## Etapa 3: gerar uma chave de registro do cofre

Gere uma chave de registro no cofre. Após baixar o Provedor do Azure Site Recovery e instalá-lo no servidor VMM, você usará essa chave para registrar o servidor VMM no cofre.

1.	Obtenha o arquivo de configuração do cofre e defina o contexto:
	
	```
	
	$VaultName = "<testvault123>"
	$VaultGeo  = "<Southeast Asia>"
	$OutputPathForSettingsFile = "<c:>"
	
	$VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;
	
	```
	
2.	Defina o contexto de cofre, executando os seguintes comandos:
	
	```
	
	$VaultSettingFilePath = $vaultSetingsFile.FilePath 
	$VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop
	
	```

## Etapa 4: instalar o Provedor do Azure Site Recovery

1.	Na máquina da VMM, crie um diretório executando o seguinte comando:
	
	```
	
	pushd C:\ASR\
	
	```
	
2.	Extraia os arquivos usando o provedor baixado, executando o seguinte comando
	
	```
	
	AzureSiteRecoveryProvider.exe /x:. /q
	
	```
	
3.	Instale o provedor usando os comandos a seguir:
	
	```
	
	.\SetupDr.exe /i
	
	```
	
	```
	
	$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
		$isNotInstalled = $true;
		if(Test-Path $installationRegPath)
		{
			$isNotInstalled = $false;
		}
	}While($isNotInstalled)
	
	```
	
	Aguarde a conclusão da instalação.
	
4.	Registre o servidor no cofre usando o seguinte comando:
	
	```
	
	$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
	
	```
	
## Etapa 5: criar uma conta de armazenamento do Azure

Se você não tiver uma conta de armazenamento do Azure, crie uma conta com replicação geográfica habilitada executando o seguinte comando:

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

Observe que a conta de armazenamento precisa estar na mesma região que o serviço do Azure Site Recovery e ser associada à mesma assinatura.


## Etapa 6: instalar o agente dos Serviços de Recuperação do Azure

No Portal do Azure, instale o agente dos Serviços de Recuperação do Azure em cada servidor de host Hyper-V localizado nas nuvens VMM que você deseja proteger.

Execute o comando a seguir em todos os hosts VMM:

```

marsagentinstaller.exe /q /nu

```


## Etapa 7: definir as configurações da proteção de nuvem

1.	Crie um perfil de proteção de nuvem no Azure executando o seguinte comando:
	
	```
	
	$ReplicationFrequencyInSeconds = "300";
	$ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName -RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds 	$ReplicationFrequencyInSeconds;
	
	```
	
2.	Obtenha um contêiner de proteção executando os seguintes comandos:
	
	```
	
	$PrimaryCloud = "testcloud"
	$protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;	
	
	```
	
3.	Inicie a associação do contêiner de proteção com a nuvem:
	
	```
	
	$associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;		
	
	```
	
4.	Após a conclusão do trabalho, execute o seguinte comando:

	
		$job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
		if($job -eq $null -or $job.StateDescription -ne "Completed")
		{
		$isJobLeftForProcessing = $true;
		}


5.	Após a conclusão do processamento do trabalho, execute o seguinte comando:

	
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
		
	

Para verificar a conclusão da operação, execute as etapas em [Monitorar a Atividade](#monitor).

## Etapa 8: configurar o mapeamento de rede
Antes de começar o mapeamento de rede, verifique se as máquinas virtuais no servidor VMM de origem estão conectadas a uma rede VM. Além disso, crie uma ou mais redes virtuais do Azure. Observe que várias redes VM podem ser mapeadas para uma única rede do Azure.

Observe que, se a rede de destino tiver várias sub-redes, e uma dessas sub-redes tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, a máquina virtual de réplica será conectada à sub-rede de destino após o failover. Se não houver uma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.

O primeiro comando obtém servidores para o cofre atual do Azure Site Recovery. O comando armazena os servidores do Microsoft Azure Site Recovery na variável de matriz $Servers.

	$Servers = Get-AzureSiteRecoveryServer


O segundo comando obtém a rede de recuperação de site para o primeiro servidor na matriz $Servers. O comando armazena as redes na variável $Networks.


	$Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

O terceiro comando obtém suas assinaturas do Azure usando o cmdlet Get-AzureSubscription e, em seguida, armazena esse valor na variável $Subscriptions.

	$Subscriptions = Get-AzureSubscription



O quarto comando obtém redes virtuais do Azure usando o cmdlet Get-AzureVNetSite e, em seguida, armazena esse valor na variável $AzureVmNetworks.


	$AzureVmNetworks = Get-AzureVNetSite



O cmdlet final cria um mapeamento entre a rede principal e a rede de máquina virtual do Azure. O cmdlet especifica a rede principal como o primeiro elemento de $Networks. O cmdlet especifica uma rede de máquina virtual como o primeiro elemento de $AzureVmNetworks usando sua ID. O comando inclui a ID da assinatura do Azure.


	New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id


## Etapa 9: habilitar a proteção para máquinas virtuais

Depois de redes, servidores e nuvens estarem configurados corretamente, você pode ativar a proteção para máquinas virtuais na nuvem. Observe o seguinte:

Máquinas virtuais devem cumprir os [Pré-requisitos de máquina virtual do Azure](site-recovery-best-practices.md#virtual-machines).

Para habilitar a proteção, o sistema operacional e as propriedades do disco do sistema operacional devem estar definidos para as máquinas virtuais. Ao criar uma máquina virtual no VMM usando um modelo de máquina virtual, é possível definir a propriedade. Você também pode definir essas propriedades para máquinas virtuais existentes nas guias **Geral** e **Configuração de Hardware** das propriedades da máquina virtual. Se você não definir essas propriedades no VMM, poderá configurá-las no portal de Recuperação de Site do Azure.


	
1.	Para habilitar a proteção, execute o seguinte comando para obter o contêiner de proteção:
		
		$ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName
	

	
2. Obtenha a entidade de proteção (VM) executando o seguinte comando:
	
	
		$protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer
		
	
		
3. Habilite o DR para a VM executando o seguinte comando:


	
		$jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity 	-Protection Enable -Force
	

	
## Testar a implantação

Para testar sua implantação, você pode executar um failover de teste para uma única máquina virtual, ou criar um plano de recuperação consistente de várias máquinas virtuais e executar um failover de teste para o plano. O failover de teste simula o mecanismo de failover e recuperação em uma rede isolada. Observe que:

- Se você quiser se conectar à máquina virtual no Azure usando a Área de trabalho remota após o failover, habilite a Conexão de Área de Trabalho Remota na máquina virtual antes de executar o teste de failover.
- Após o failover, você usará um endereço IP público para conectar-se à máquina virtual no Azure usando a Área de Trabalho Remota. Se você quiser fazer isso, verifique se não tem nenhuma política de domínio que o impeça de se conectar a uma máquina virtual usando um endereço público.

Para verificar a conclusão da operação, execute as etapas em [Monitorar a Atividade](#monitor).

### Criar um plano de recuperação

1. Crie um arquivo .xml como um modelo para seu plano de recuperação usando os dados a seguir e salve-o como "C:\\RPTemplatePath.xml".
2. Altere a ID do nó RecoveryPlan, o Nome, PrimaryServerId e SecondaryServerId.
3. Altere o nó ProtectionEntity, PrimaryProtectionEntityId (vmid de VMM).
4. Você pode adicionar mais VMs adicionando mais nós ProtectionEntity.
	
	
	
		<#
		<?xml version="1.0" encoding="utf-16"?>
		<RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test" 	PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5" 	SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description="" 	Version="V2014_07">
		  <Actions />
		  <ActionGroups>
		    <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
		      <PreActionSequence />
		      <PostActionSequence />
		    </ShutdownAllActionGroup>
		    <FailoverAllActionGroup Id="FailoverAllActionGroup">
		      <PreActionSequence />
		      <PostActionSequence />
		    </FailoverAllActionGroup>
		    <BootActionGroup Id="DefaultActionGroup">
		      <PreActionSequence />
		      <PostActionSequence />
		      <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03-	cf163cc36ef8" />
		    </BootActionGroup>
		  </ActionGroups>
		  <ActionGroupSequence>
		    <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup" 	Before="FailoverAllActionGroup" />
		    <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup" 	After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
		    <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
		  </ActionGroupSequence>
		</RecoveryPlan>
		#>
	

	
4. Preencha os dados no modelo:
	
		
		$TemplatePath = "C:\RPTemplatePath.xml";
	

	
5. Crie o RecoveryPlan:

		$RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;
	
	
	
### Execute um teste de failover

1.	Obtenha o objeto RecoveryPlan executando o seguinte comando:

		$RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;

	
2.	Inicie o teste de failover executando o seguinte comando:
	
	
		$jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;
	
		
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

[Leia mais](https://msdn.microsoft.com/library/dn850420.aspx) sobre os cmdlets do PowerShell do Azure Site Recovery. </a>.

<!---HONumber=AcomDC_0720_2016-->