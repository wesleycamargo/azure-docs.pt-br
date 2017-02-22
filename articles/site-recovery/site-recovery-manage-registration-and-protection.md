---
title: "Remover os servidores e desabilitar a proteção | Microsoft Docs"
description: "Este artigo descreve como cancelar o registro de servidores de um cofre de Recuperação de Site e desabilitar a proteção para máquinas virtuais e servidores físicos."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: ef1f31d5-285b-4a0f-89b5-0123cd422d80
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/28/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: f57c88cbace41af233f542880c6199b3e278700e
ms.openlocfilehash: c8d893dbac1a4f6cb3f05f857e186bca155e5865


---

# <a name="remove-servers-and-disable-protection"></a>Remover os servidores e desabilitar a proteção

O serviço Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre). O serviço orquestra a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. As máquinas podem ser replicadas no Azure ou em um datacenter local secundário. Para ter uma breve visão geral, leia [O que é Azure Site Recovery?](site-recovery-overview.md)

Este artigo descreve como cancelar o registro de servidores de um cofre dos Serviços de Recuperação no Portal do Azure, e como desabilitar a proteção de máquinas protegidas pelo Site Recovery.

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="unregister-a-connected-configuration-server"></a>Cancelar o registro de um servidor de configuração conectado

Se você replicar VMs VMware ou servidores físicos com Windows/Linux no Azure, será possível cancelar o registro de um servidor de configuração conectado em um cofre da seguinte maneira:

1. Desabilite o proteção da máquina. Em **Itens Protegidos** > **Itens Replicados**, clique com o botão direito na máquina > **Excluir**.
2. Desassocie todas as políticas. Em **Infraestrutura do Site Recovery** > **Para Máquinas Físicas e de VMWare ** > **Políticas de Replicação**, clique duas vezes na política associada. Clique com botão direito no servidor de configuração > **Desassociar**.
3. Remova qualquer outro processo local ou servidores de destino mestre. Em **Infraestrutura do Site Recovery** > **Para Máquinas Físicas e de VMWare** > **Servidores de Configuração**, clique com o botão direito no servidor > **Excluir**.
4. Excluir o servidor de configuração.
5. Desinstale manualmente o serviço Mobilidade que está em execução no servidor de destino mestre (será um servidor separado ou em execução no servidor de configuração).
6. Desinstale quaisquer servidores de processo adicionais.
7. Desinstale o servidor de configuração.
8. No servidor de configuração, desinstale a instância do MySQL foi instalado pelo Site Recovery.
9. No registro do servidor de configuração, exclua a chave ``HKEY_LOCAL_MACHINE\Software\Microsoft\Azure Site Recovery``.

## <a name="unregister-a-unconnected-configuration-server"></a>Cancelar o registro de um servidor de configuração não conectado

Se você replicar VMs VMware ou servidores físicos com Windows/Linux no Azure, será possível cancelar o registro de um servidor de configuração não conectado em um cofre da seguinte maneira:

1. Desabilite o proteção da máquina. Em **Itens Protegidos** > **Itens Replicados**, clique com o botão direito na máquina > **Excluir**. Selecione **Interromper o gerenciamento da máquina**.
2. Remova qualquer outro processo local ou servidores de destino mestre. Em **Infraestrutura do Site Recovery** > **Para Máquinas Físicas e de VMWare** > **Servidores de Configuração**, clique com o botão direito no servidor > **Excluir**.
3. Excluir o servidor de configuração.
4. Desinstale manualmente o serviço Mobilidade que está em execução no servidor de destino mestre (será um servidor separado ou em execução no servidor de configuração).
5. Desinstale quaisquer servidores de processo adicionais.
6. Desinstale o servidor de configuração.
7. No servidor de configuração, desinstale a instância do MySQL foi instalado pelo Site Recovery.
8. No registro do servidor de configuração, exclua a chave ``HKEY_LOCAL_MACHINE\Software\Microsoft\Azure Site Recovery``.

## <a name="unregister-a-connected-vmm-server"></a>Cancelar o registro de um servidor VMM conectado

Como prática recomendada, recomendamos o cancelamento do registro do servidor VMM quando ele estiver conectado ao Azure. Isso garante que as configurações nos servidores do VMM (e em outros servidores VMM com nuvens emparelhadas) sejam limpas corretamente. Remova um servidor desconectado somente se houver um problema de conectividade permanente. Se o servidor VMM não estiver conectado, você precisará executar manualmente um script para limpar as configurações.

1. Pare de replicar VMs em nuvens no servidor VMM que você quer remover.
2. Exclua qualquer mapeamento de rede usado por nuvens no servidor VMM que você quer excluir. Em **Infraestrutura do Site Recovery** > **Para VMM do System Center** > **Mapeamento de Rede**, clique com o botão direito no mapeamento de rede > **Excluir**.
3. Desassocie políticas de replicação de nuvens no servidor VMM que você quer remover.  Em **Infraestrutura do Site Recovery** > **Para VMM do System Center** >  **Políticas de Replicação**, clique duas vezes na política associada. Clique com o botão direito na nuvem > **Desassociar**.
4. Exclua o servidor VMM ou o nó ativo do VMM. Em **Infraestrutura do Site Recovery** > **Para VMM do System Center** > **Servidores VMM**, clique com o botão direito do servidor > **Excluir**.
5. Desinstalar manualmente o Provedor no servidor VMM. Se você tiver um cluster, remova todos os nós.
6. Se você estiver replicando para o Azure, remova manualmente o agente dos Serviços de Recuperação da Microsoft dos hosts Hyper-V nas nuvens excluídas.



### <a name="unregister-an-unconnected-vmm-server"></a>Cancelar o registro de um servidor VMM desconectado

1. Pare de replicar VMs em nuvens no servidor VMM que você quer remover.
2. Exclua qualquer mapeamento de rede usado por nuvens no servidor VMM que você quer excluir. Em **Infraestrutura do Site Recovery** > **Para VMM do System Center** > **Mapeamento de Rede**, clique com o botão direito no mapeamento de rede > **Excluir**.
3. Anote a ID do servidor VMM.
4. Desassocie políticas de replicação de nuvens no servidor VMM que você quer remover.  Em **Infraestrutura do Site Recovery** > **Para VMM do System Center** >  **Políticas de Replicação**, clique duas vezes na política associada. Clique com o botão direito na nuvem > **Desassociar**.
5. Exclua o servidor VMM ou o nó ativo. Em **Infraestrutura do Site Recovery** > **Para VMM do System Center** > **Servidores VMM**, clique com o botão direito do servidor > **Excluir**.
6. Baixe e execute o [script de limpeza](http://aka.ms/asr-cleanup-script-vmm) no servidor VMM. Abra o PowerShell com a opção **Executar como Administrador** para alterar a política de execução para o escopo padrão (LocalMachine). No script, especifique a ID do servidor VMM que você quer remover. O script remove o registro e as informações de emparelhamento na nuvem do servidor.
5. Execute o script de limpeza em outros servidores VMM que contêm nuvens emparelhadas com nuvens no servidor VMM que você quer remover.
6. Execute o script de limpeza em quaisquer outros nós de cluster passivos do VMM nos quais o Provedor está instalado.
7. Desinstalar manualmente o Provedor no servidor VMM. Se você tiver um cluster, remova todos os nós.
8. Se você estiver replicando no Azure, remova o agente dos Serviços de Recuperação da Microsoft dos hosts Hyper-V nas nuvens excluídas.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Cancelar o registro de um host Hyper-V em um site do Hyper-V

Os hosts Hyper-V que não são gerenciados pelo VMM são reunidos em um site do Hyper-V. Remova um host de um site do Hyper-V da seguinte maneira:

1. Desabilite a replicação para VMs do Hyper-V localizadas no host.
2. Desassocie as políticas para o site do Hyper-V. Em **Infraestrutura do Site Recovery** > **Para sites do Hyper-V** >  **Políticas de Replicação**, clique duas vezes na política associada. Clique com o botão direito no site > **Desassociar**.
3. Exclua os hosts Hyper-V. Em **Infraestrutura do Site Recovery** > **Para VMM do System Center** > **Hosts Hyper-V**, clique com o botão direito do servidor > **Excluir**.
4. Exclua site do Hyper-V depois que todos os hosts tiverem sido removidos dele. Em **Infraestrutura do Site Recovery** > **Para VMM do System Center** > **Sites do Hyper-V**, clique com o botão direito do site > **Excluir**.
5. Execute o seguinte script em cada host Hyper-V removido. O script limpa as configurações no servidor e cancela o registro do cofre.


        `` pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }

            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'

            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."    
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }

            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {    
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd``



## <a name="disable-protection-for-a-vmware-vm-or-physical-server"></a>Desabilitar a proteção de uma VM do VMware ou servidor físico

1. Em **Itens Protegidos** > **Itens Replicados**, clique com o botão direito na máquina > **Excluir**.
2. Em **Remover Máquina**, selecione uma destas opções:
    - **Desabilitar proteção da máquina (recomendado)**. Use esta opção para interromper a replicação da máquina. As configurações do Site Recovery serão limpas automaticamente. Você só verá essa opção nas seguintes circunstâncias:
        - **Você redimensionou o volume da VM**—Quando você redimensiona um volume, a máquina virtual entra em um estado crítico. Selecione essa opção para desabilitar a proteção enquanto retém os pontos de recuperação no Azure. Quando você habilita novamente a proteção para a máquina, os dados do volume redimensionado são transferidos para o Azure.
        - **Você executou recentemente um failover**—Após a execução de um failover para testar o ambiente, selecione essa opção para voltar a proteger os computadores locais. Isso desabilita cada máquina virtual e, depois, será necessário reabilitar a proteção para elas. Desabilitar a máquina com essa configuração não afeta a máquina virtual de réplica no Azure. Não desinstale o serviço de Mobilidade da máquina virtual.
    - **Interrompa o gerenciamento da máquina**. Se você selecionar essa opção, a máquina será removida somente do cofre. As configurações de proteção local da máquina não serão afetadas. Para remover as configurações na máquina e remover a máquina da assinatura do Azure, será necessário limpar as configurações desinstalando o serviço de Mobilidade.

## <a name="disable-protection-for-a-hyper-v-vm-in-a-vmm-cloud"></a>Desabilitar a proteção de uma VM Hyper-V em uma nuvem do VMM

1. Em **Itens Protegidos** > **Itens Replicados**, clique com o botão direito na máquina > **Excluir**.
2. Em **Remover Máquina**, selecione uma destas opções:

    - **Desabilitar proteção da máquina (recomendado)**. Use esta opção para interromper a replicação da máquina. As configurações do Site Recovery serão limpas automaticamente.
    - **Interrompa o gerenciamento da máquina**. Se você selecionar essa opção, a máquina será removida somente do cofre. As configurações de proteção local da máquina não serão afetadas. Para remover as configurações na máquina e remover a máquina da assinatura do Azure, será necessário limpar as configurações manualmente usando as instruções abaixo. Observe que se você optar por excluir a máquina virtual e seus discos rígidos, eles serão removidos do local de destino.

### <a name="clean-up-protection-settings---replication-to-a-secondary-vmm-site"></a>Limpar as configurações de proteção - replicação para um site secundário do VMM

Se você selecionou **Parar de gerenciar a máquina** e estiver replicando para um site secundário, execute este script no servidor primário para limpar as configurações para a máquina virtual primária. No console do VMM, clique no botão do PowerShell para abrir o console do PowerShell do VMM. Substitua SQLVM1 pelo nome da máquina virtual.

         ``$vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. No servidor VMM secundário, execute este script para limpar as configurações da máquina virtual secundária:

        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force``
3. No servidor do VMM secundário, atualize as máquinas virtuais no servidor do host Hyper-V para que a VM secundária seja novamente detectada no console do VMM.
4. As etapas acima limpam as configurações de replicação no servidor do VMM. Se você quiser interromper a replicação para a máquina virtual, execute o seguinte script nas VMs primárias e secundárias. Substitua SQLVM1 pelo nome da máquina virtual.

        ``Remove-VMReplication –VMName “SQLVM1”``

### <a name="clean-up-protection-settings---replication-to-azure"></a>Limpar as configurações de proteção - replicação no Azure

1. Se você selecionou **Parar de gerenciar a máquina** e tiver replicado no Azure, execute este script no servidor VMM de origem, usando o PowerShell no console do VMM.
        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. As etapas acima limpam as configurações de replicação no servidor do VMM. Para interromper a replicação para a máquina virtual em execução no servidor do host Hyper-V, execute este script. Substitua SQLVM1 pelo nome de sua máquina virtual e host01.contoso.com pelo nome do servidor do host Hyper-V.

        ``$vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)``


## <a name="disable-protection-for-a-hyper-v-vm-in-a-hyper-v-site"></a>Desabilitar a proteção de uma VM do Hyper-V em um site do Hyper-V

Use este procedimento se você estiver replicando VMs do Hyper-V para o Azure sem um servidor do VMM.

1. Em **Itens Protegidos** > **Itens Replicados**, clique com o botão direito na máquina > **Excluir**.
2. Em **Remover Máquina**, você pode selecionar as seguintes opções:

   - **Desabilitar proteção da máquina (recomendado)**. Use esta opção para interromper a replicação da máquina. As configurações do Site Recovery serão limpas automaticamente.
   - **Interrompa o gerenciamento da máquina**. Se você selecionar essa opção, a máquina será removida somente do cofre. As configurações de proteção local da máquina não serão afetadas. Para remover as configurações na máquina e remover a máquina virtual da assinatura do Azure, será necessário limpar as configurações manualmente. Se você optar por excluir a máquina virtual e seus discos rígidos, eles serão removidos do local de destino.
3. Se você selecionou **Parar o gerenciamento da máquina**, execute este script no servidor do host Hyper-V de origem para remover a replicação para a máquina virtual. Substitua SQLVM1 pelo nome da máquina virtual.

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)



<!--HONumber=Jan17_HO1-->


