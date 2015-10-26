<properties
	pageTitle="Proteger VMs de um site do VMM para outro com o PowerShell e Gerenciador de Recursos do Microsoft Azure"
	description="Automatizar a proteção entre dois sites VMM locais e o Azure usando o PowerShell e o Gerenciador de Recursos do Azure."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="08/26/2015"
	ms.author="raynew"/>
	

#  Site VMM para site VMM com o PowerShell e o Gerenciador de Recursos do Azure


## Visão geral

O Azure Site Recovery colabora com sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação. Para obter uma lista completa de cenários de implantação, consulte a [Visão geral do Azure Site Recovery](site-recovery-overview.md).

O PowerShell no Azure é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell. Ele pode trabalhar com dois tipos de modulo — o módulo de Perfil do Azure ou o módulo ARM (Gerenciador de Recursos do Azure).

Este artigo descreve como usar o Windows PowerShell® juntamente com o ARM na implantação do Azure Site Recovery para configurar e administrar a proteção de máquina virtual entre dois sites VMM. As máquinas virtuais em execução nos servidores host do Hyper-V que estão localizadas nas nuvens privadas VMM em um site primário serão replicadas e farão failover em um site VMM secundário usando a Réplica do Hyper-V.

Não é preciso ser um especialista no PowerShell para usar este artigo, mas é pressuposto que você entenda os conceitos básicos, como módulos, cmdlets e sessões. Para obter mais informações sobre o Windows PowerShell, consulte [Introdução ao Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx). Saiba mais em [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](powershell-azure-resource-manager.md).

Este artigo inclui pré-requisitos para o cenário, além de mostrar como configurar o Azure PowerShell para trabalhar com o Site Recovery, criar um cofre do Site Recovery, instalar o Provedor do Azure Site Recovery em servidores VMM e registrá-los no cofre, definir as configurações de proteção para nuvens VMM que serão aplicadas a todas as máquinas virtuais protegidas e, em seguida, habilitar a proteção para essas máquinas virtuais. Finalizaremos testando o failover para verificar se tudo está funcionando conforme o esperado.


## Antes de começar

Verifique se estes pré-requisitos estão em vigor:

- Você precisará de uma conta do [Microsoft Azure](http://azure.microsoft.com/). Você precisará de uma conta do [Microsoft Azure](http://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](pricing/free-trial/). Além disso, você pode ler sobre os [preços do Azure Site Recovery Manager](http://azure.microsoft.com/pricing/details/site-recovery/).
- Você precisará de um servidor VMM nos sites primário e secundário em execução no System Center 2012 R2.
- Cada servidor VMM deve ter pelo menos uma nuvem que contenha:
	- Um ou mais grupos de hosts do VMM
	- Um ou mais servidores host Hyper-V ou clusters em cada grupo de hosts.
	- Uma ou mais máquinas virtuais no servidor Hyper-V de origem.
	- Saiba mais sobre como configurar nuvens VMM:

		- [Novidades na nuvem privada com o System Center 2012 R2 VMM](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=dfgvHAmYryA) e em [VMM 2012 e as nuvens](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html).
		- [Configurando a malha de nuvem do VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
		- [Criando uma nuvem privada no VMM](https://technet.microsoft.com/library/jj860425.aspx) e [Passo a passo: criando nuvens privadas com o System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
- Um ou mais servidores do Hyper-V executando pelo menos o Windows Server 2012 com a função Hyper-V e as últimas atualizações instaladas. O servidor ou cluster deve estar incluído em uma nuvem VMM.
- Se você estiver executando o Hyper-V em um cluster, observe que o agente de cluster não será criado automaticamente se você tiver um cluster de baseados em endereços IP estáticos. Você precisará configurar o agente de cluster manualmente. Para obter instruções, consulte [Configurar o agente de réplica do Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).

	- Você precisará do Azure PowerShell. Verifique se você está executando o Azure PowerShell versão 0.9.6 ou posterior. Consulte [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md). 
	- A instalação do Azure PowerShell instala um console personalizado. Você pode executar os comandos do PowerShell desse console ou de qualquer outro programa host, como o ISE do Windows PowerShell.

## Etapa 1: configurar o PowerShell


1. Abra um console do PowerShell e execute este comando para alternar para o módulo ARM:

    `Switch-AzureMode AzureResourceManager`

3. Agora execute este comando para adicionar sua conta do Azure à sessão do PowerShell. O cmdlet solicita as credenciais de logon da sua conta.

    `Add-AzureAccount`

	Observe que se for um parceiro CSP trabalhando em nome de um locatário, você precisará especificar o cliente como um locatário quando adicionar a conta do Azure:

    `Add-AzureAccount-Tenant "customer"`

5. Uma conta pode ter várias assinaturas, de modo que você precisará associar à conta a assinatura que deseja usar.

    `Select-AzureSubscription -SubscriptionName $SubscriptionName`

6. Se estiver usando cmdlets do Site Recovery pela primeira vez na assinatura, será preciso registrar o provedor do Azure para o Site Recovery:

    `Register-AzureProvider -ProviderNamespace Microsoft.SiteRecovery`

## Etapa 2: configurar o cofre do Site Recovery

1. Caso você não tenha um cofre do Site Recovery, será preciso criar um executando o cmdlet [New-AzureSiteRecoveryVault](https://msdn.microsoft.com/library/azure/dn954225.aspx):

    `New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
    $vault = Get-AzureSiteRecoveryVault -Name $VaultName;`

## Etapa 3: gerar uma chave de registro do cofre

1. Execute o cmdlet [Get-AzureSiteRecoveryVaultSettingsFile](https://msdn.microsoft.com/library/azure/dn850404.aspx) para obter uma chave de registro do cofre. Essa chave é necessária para registrar servidores VMM no cofre:

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;`

## Etapa 4: instalar o Provedor do Azure Site Recovery

1. Baixe o arquivo de instalação do Provedor da página Início Rápido no cofre do Site Recovery.
2. Em cada servidor VMM, crie uma pasta usando este comando:

    `pushd C:\ASR`

3. Execute este comando para extrair os arquivos baixados do Provedor:

    `AzureSiteRecoveryProvider.exe /x:. /q`

4. Instale o provedor executando:

    `.\SetupDr.exe /i` `$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
                $isNotInstalled = $true;
                if(Test-Path $installationRegPath)
                {
                                $isNotInstalled = $false;
                }
	}While($isNotInstalled)`

5. Aguarde até que a instalação seja finalizada e registre o servidor no cofre:

    `$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice`

## Etapa 5: configurar o contexto do cofre

1. Execute o cmdlet Get-AzureSiteRecoveryVault para ter certeza de que todos os comandos sejam executados no cofre específico:

    `$Vault = Get-AzureSiteRecoveryVault -ResouceGroupName $ResourceGroupName | where { $_.Name -eq $VaultName}`

2. Baixe as configurações do cofre:

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Vault $Vault -Path $OutputPathForSettingsFile`

3. Para ter certeza de que os cmdlets sejam executados no cofre, execute:

    `Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSetingsFile.FilePath`

## Etapa 3: definir as configurações da proteção de nuvem

Depois que o servidor VMM estiver registrado no cofre, você poderá definir as configurações de proteção de nuvem que serão aplicadas a todas as máquinas virtuais nos hosts Hyper-V localizados em nuvens no servidor VMM registrado.

1. Crie um contêiner no cofre das nuvens primária e secundária:

    `$PrimaryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $PrimaryCloudName` `$$RecoveryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $RecoveryCloudName`

2. Defina as configurações de proteção para aplicar às nuvens:

    `New-AzureSiteRecoveryProtectionProfile -Name $ProtectionProfileName -ReplicationProvider HyperVReplica -ReplicationMethod Online -ReplicationFrequencyInSeconds 30 -RecoveryPoints 1 -ApplicationConsistentSnapshotFrequencyInHours 0 -ReplicationPort 8083 -Authentication Kerberos -AllowReplicaDeletion`

3. Inicie um trabalho para associar os contêineres de nuvem às configurações de proteção de nuvem:

    `Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $ProtectionProfile -PrimaryProtectionContainer $PrimaryContainer -RecoveryProtectionContainer $RecoveryContainer`


## Etapa 4: habilitar a proteção de VM

Habilite a proteção para VMs nas nuvens VMM:

1. Obtenha a VM que deseja proteger:

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName $VMName `

2. Habilite a proteção para a VM:

    `Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $VM -Protection Enable`


## Etapa 5: executar um failover de teste

1.	Selecione a VM da qual deseja fazer failover:

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName  $VMName`

2. Execute um teste de failover:

    `$ currentJob = Start-AzureSiteRecoveryTestFailoverJob -ProtectionEntity $VM -Direction PrimaryToRecovery`

3. Verifique se a VM do failover aparece no site secundário e conclua o failover:

    `Resume-AzureSiteRecoveryJob -Id $currentJob.Name`


## Próximas etapas

Em caso de dúvidas e comentários sobre esse cenário, visite o [fórum do Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr/)

<!---HONumber=Oct15_HO3-->