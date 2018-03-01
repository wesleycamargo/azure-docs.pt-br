---
title: "Replicar VMs do Hyper-V em nuvens de VMM para um site secundário com o PowerShell (Azure Resource Manager) | Microsoft Docs"
description: "Descreve como replicar VMs do Hyper-V em nuvens de VMM para um site de VMM secundário usando PowerShell (Gerenciador de Recursos)"
services: site-recovery
author: sujaytalasila
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: sutalasi
ms.openlocfilehash: a5e36546494223b20844303f3f76782746658411
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="replicate-hyper-v-vms-to-a-secondary-site-using-powershell-resource-manager"></a>Replicar VMs do Hyper-V para um site secundário usando PowerShell (Gerenciador de Recursos)

Este artigo mostra como automatizar as etapas para a replicação de VMs do Hyper-V em nuvens de System Center Virtual Machine Manager (VMM) para uma nuvem de VMM em um site local secundário usando o [Azure Site Recovery](site-recovery-overview.md).

## <a name="prerequisites"></a>pré-requisitos

- Examine os [componentes e a arquitetura do cenário](hyper-v-vmm-architecture.md).
- Examine os [requisitos de suporte](site-recovery-support-matrix-to-sec-site.md) de todos os componentes.
- Verifique se os servidores do VMM e os hosts do Hyper-V estão em conformidade com [os requisitos de suporte](site-recovery-support-matrix-to-sec-site.md).
- Verifique se as VMs que você deseja replicar estão em conformidade com o [suporte ao computador replicado](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions)


## <a name="prepare-for-network-mapping"></a>Prepare-se para o mapeamento de rede

O [mapeamento de rede](hyper-v-vmm-network-mapping.md) mapeia entre as redes de VM do VMM locais de origem e as nuvens de destino. Mapeamento faz o seguinte:

- Conecta as VMs às redes de VM de destino apropriadas depois do failover. 
- Quando possível, coloca VMs de réplica nos servidores host do Hyper-V de destino. 
- Se você não configurar o mapeamento de rede, as VMs de réplica não serão conectadas a uma rede de VM após o failover.

Prepare o VMM da seguinte maneira:

1. Verifique se você tem [redes lógicas do VMM](https://docs.microsoft.com/system-center/vmm/network-logical) na origem e servidores do VMM de destino.
    - A rede lógica no servidor de origem deve ser associada à nuvem de origem na qual os hosts do Hyper-V estão localizados.
    - A rede lógica no servidor de destino deve ser associada com a nuvem de destino.
1. Verifique se você tem [Redes de VM](https://docs.microsoft.com/system-center/vmm/network-virtual) nos servidores do VMM de origem e destino. As redes de VM devem ser vinculadas à rede lógica em cada local.
2. Conecte as VMs nos hosts do Hyper-V de origem à rede de VM de origem. 

## <a name="prepare-for-powershell"></a>Preparar para o PowerShell

Verifique se você tem o PowerShell do Azure pronto para uso.

- Se você já estiver usando o PowerShell, precisará atualizar para a versão 0.8.10 ou posterior.  [Saiba mais](/powershell/azureps-cmdlets-docs) sobre como configurar o PowerShell.
- Depois de ter instalado e configurado o PowerShell, revise os [cmdlets de serviço](/powershell/azure/overview).
- Para saber mais sobre como usar os valores, as entradas e as saídas do parâmetro no Microsoft Azure PowerShell, leia o guia [Introdução](/powershell/azure/get-started-azureps).

## <a name="set-up-a-subscription"></a>Configurar uma assinatura
1. No Azure PowerShell, faça logon em sua conta do Azure:

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Recupere uma lista de suas assinaturas com as IDs de assinatura. Anote a ID da assinatura na qual você deseja criar o cofre dos Serviços de Recuperação.   

        Get-AzureRmSubscription
3. Configure a assinatura para o cofre:

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação
1. Se você não tiver um, crie um grupo de recursos do Azure Resource Manager.

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Crie um novo cofre de Serviços de Recuperação e salve o objeto de cofre em uma variável que será usada posteriormente: 

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location
   
    Você pode recuperar o objeto de cofre após criá-lo usando o cmdlet Get-AzureRMRecoveryServicesVault.

## <a name="set-the-vault-context"></a>Definir o contexto do cofre
1. Recupere um cofre existente:

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. Defina o contexto do cofre:

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-azure-site-recovery-provider"></a>Instalar o Provedor de Recuperação do Site do Azure
1. Na máquina da VMM, crie um diretório executando o seguinte comando:

       New-Item c:\ASR -type directory
2. Extraia os arquivos usando o arquivo de instalação baixado do Provedor: pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Instale o provedor e aguarde a conclusão da instalação:

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

4. Registre o servidor no cofre:

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Criar e associar uma política de replicação
1. Crie uma política de replicação (nesse caso, para Hyper-V 2012 R2), como a seguir:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > A nuvem de VMM pode conter hosts Hyper-V executando versões diferentes do Windows Server, mas a política de replicação é específica para uma versão do sistema operacional. Se você tiver hosts diferentes em execução em diferentes sistemas operacionais, crie políticas de replicação separadas para cada sistema. Por exemplo: se você tiver cinco hosts em execução no Windows Server 2012 e três no Windows Server 2012 R2, crie duas políticas de replicação – uma para cada tipo de sistema operacional.

2. Recupere o contêiner de proteção principal (nuvem de VMM principal) e o contêiner de proteção de recuperação (nuvem de VMM de recuperação):

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
2. Recupere a política de replicação criada usando o nome amigável:

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Inicie a associação do contêiner de proteção (Nuvem do VMM) com a política de replicação:

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
4. Aguarde o conclusão do trabalho de associação da política. Você pode verificar se o trabalho foi concluído usando o seguinte trecho do PowerShell:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

   Após a conclusão do processamento do trabalho, execute o seguinte comando:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Para verificar a conclusão da operação, siga as etapas em [Monitorar a atividade](#monitor-activity).

##  <a name="configure-network-mapping"></a>Configurar o mapeamento de rede
1. Use este comando para recuperar os servidores para o cofre atual. O comando armazena os servidores do Azure Site Recovery na variável de matriz $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Execute este comando para recuperar as redes para o servidor de VMM de origem e o servidor de VMM de destino.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > O servidor de VMM de origem pode ser o primeiro ou o segundo na matriz de servidores. Verifique os nomes de servidor de VMM e recupere as redes adequadamente.


3. O cmdlet cria um mapeamento entre a rede principal e a rede de recuperação. Ele especifica a rede principal como o primeiro elemento da $PrimaryNetworks e a rede de recuperação como o primeiro elemento da $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Habilitar a proteção para VMs
Depois que os servidores, nuvens e redes estiverem configurados corretamente, você poderá habilitar a proteção para VMs na nuvem.

1. Para habilitar a proteção, execute o seguinte comando para recuperar o contêiner de proteção:

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Obtenha a entidade de proteção (VM) da maneira a seguir:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Habilite a replicação para a VM:

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Execute um teste de failover

Para testar sua implantação, você pode executar um failover de teste para uma única máquina virtual, ou criar um plano de recuperação que consista de várias VMs e executar um failover de teste para o plano. O failover de teste simula o mecanismo de failover e recuperação em uma rede isolada.

1. Recupere a VM na qual as VMs irão falhar:

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]
2. Execute o failover de teste da seguinte maneira:
    - Para uma única VM

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
    - Para um plano de recuperação:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Para verificar a conclusão da operação, siga as etapas em [Monitorar a atividade](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Execute failovers planejados e não planejados

1. Execute um failover planejado da seguinte maneira:
    -  Para uma única VM:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity
    - Para um plano de recuperação

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan
2. Execute um failover não planejado da seguinte maneira:
    - Para uma única VM
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

    - Para um plano de recuperação:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Monitorar a atividade
Use os comandos a seguir para monitorar a atividade de failover. Observe que é necessário aguardar a conclusão do processamento entre os trabalhos.

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



## <a name="next-steps"></a>Próximas etapas

[Saiba mais](/powershell/module/azurerm.recoveryservices.backup/#recovery) sobre o Site Recovery com cmdlets do PowerShell do Azure Resource Manager.
