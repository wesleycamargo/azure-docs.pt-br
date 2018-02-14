---
title: "Solucionar problemas de falha de Backup do Azure: indisponível no status de agente convidado | Microsoft Docs"
description: "Sintomas, causas e resoluções para falhas do Backup do Azure relacionados a agente, extensão e discos."
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
keywords: Backup do Azure; agente da VM; conectividade de rede;
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: genli;markgal;sogup;
ms.openlocfilehash: c205023b025a477ee05ddcbfc536573f31426167
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Solucionar problemas de falha do Backup do Azure: problemas com o agente ou a extensão

Este artigo fornece etapas de solução de problemas que podem ajudar você a resolver erros de Backup do Azure relacionados à comunicação com a extensão e o agente da VM.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>Agente de VM não consegue se comunicar com o Backup do Azure

Mensagem de erro: "Agente de VM não consegue se comunicar com o Backup do Azure"

> [!NOTE]
> Se os backups do Azure de VM do Linux falharem com este erro a partir de 4 de janeiro de 2018, execute o seguinte comando na VM e, em seguida, repita os backups: `sudo rm -f /var/lib/waagent/*.[0-9]*.xml`

Depois de registrar e agendar uma VM para o serviço de Backup, o Backup iniciará o trabalho comunicando-se com o agente de VM para obter um instantâneo pontual. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Quando um instantâneo não é disparado, o backup pode falhar. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:

**Causa 1: [a VM não tem acesso à Internet](#the-vm-has-no-internet-access)**  
**Causa 2: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 3: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 4: [não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**Causa 5: [a extensão de backup falha ao ser atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)**  

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Falha na operação de instantâneo porque a máquina virtual não está conectada à rede

Mensagem de erro: "Houve falha na operação de instantâneo por falta de conectividade à rede na máquina virtual"

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:    
**Causa 1: [a VM não tem acesso à Internet](#the-vm-has-no-internet-access)**  
**Causa 2: [não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 3: [a extensão de backup falha ao ser atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)**  

## <a name="vmsnapshot-extension-operation-failed"></a>Falha na operação da extensão VMSnapshot

Mensagem de erro: "Falha na operação da extensão VMSnapshot"

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:  
**Causa 1: [não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [a extensão de backup falha ao ser atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)**  
**Causa 3: [a VM não tem acesso à Internet](#the-vm-has-no-internet-access)**  
**Causa 4: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 5: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backup-fails-because-the-vm-agent-is-unresponsive"></a>Falha no backup porque o agente de VM está sem resposta

Mensagem de erro: "Não é possível executar a operação porque o Agente de VM está sem reposta"

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:  
**Causa 1: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [a VM não tem acesso à Internet](#the-vm-has-no-internet-access)**  

## <a name="backup-fails-with-an-internal-error"></a>Falha do backup, com um erro interno

Mensagem de erro: "Falha no backup com um erro interno – tente novamente a operação dentro de alguns minutos"

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:  
**Causa 1: [a VM não tem acesso à Internet](#the-vm-has-no-internet-access)**  
**Causa 2: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 3: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 4: [não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 5: [a extensão de backup falha ao ser atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)**  
**Causa 6: [o serviço de Backup não tem permissão para excluir os pontos de restauração antigos por causa de um bloqueio de grupo de recursos](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)**

## <a name="disk-configuration-is-not-supported"></a>Configuração de disco não compatível

Mensagem de erro: "A Configuração de disco especificada não é compatível"

> [!NOTE]
> Temos uma versão prévia privada para dar suporte a backups para VMs que têm discos maiores que 1 TB. Para obter detalhes, consulte [Versão prévia privada para suporte de backup de VM de discos grandes](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a).
>
>

Atualmente, o Backup do Azure não é compatível com discos [maiores que 1.023 GB](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm). Se você tem discos maiores que 1 TB:  
1. [Anexe novos discos](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) que sejam menores que 1 TB.  
2. Copie os dados dos discos que são maiores que 1 TB para os discos recém-criados, menores que 1 TB.  
3. Verifique se todos os dados foram copiados. Em seguida, remova os discos que são maiores que 1 TB.  
4. Inicie o backup.

## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-vm-has-no-internet-access"></a>A VM não tem acesso à Internet
Devido ao requisito de implantação, a VM não tem acesso à Internet. Ou, talvez haja restrições que impedem o acesso à infraestrutura do Azure.

Para funcionar corretamente, a extensão de Backup exige conectividade com endereços IP públicos do Azure. A extensão envia comandos para um ponto de extremidade de armazenamento do Azure (URL de HTTP) para gerenciar os instantâneos da VM. Se a extensão não tiver acesso à Internet pública, o backup, eventualmente, falhará.

####  <a name="solution"></a>Solução
Para resolver o problema, tente usar um dos seguintes métodos:

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>Permitir o acesso ao armazenamento do Azure correspondente à região

Você pode usar [marcas de serviço](../virtual-network/security-overview.md#service-tags) para permitir conexões ao armazenamento da região específica. Verifique se a regra que permite o acesso à conta de armazenamento tem prioridade maior que a regra que bloqueia o acesso à Internet. 

![Grupo de segurança de rede com marcas de armazenamento para uma região](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> As marcas de serviço de armazenamento estão em versão prévia. Eles estão disponíveis somente em regiões específicas. Para obter a lista de regiões, consulte [Marcas de serviço para armazenamento](../virtual-network/security-overview.md#service-tags).

##### <a name="create-a-path-for-http-traffic"></a>Criar um caminho para o tráfego HTTP

1. Se você tiver alguma restrição de rede no local (um grupo de segurança de rede, por exemplo), implante um servidor proxy HTTP para rotear o tráfego.
2. Para permitir o acesso à Internet por meio do servidor proxy HTTP, adicione regras ao grupo de segurança de rede, se você tiver um.

Para saber como configurar um proxy HTTP para backups VM, veja [preparar seu ambiente para fazer backup de máquinas virtuais do Azure](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

Se você usa o Managed Disks do Azure, pode ser necessário abrir outra porta (porta 8443) nos firewalls.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente está instalado na VM, mas sem resposta (para VMs do Windows)

#### <a name="solution"></a>Solução
O agente de VM pode ter sido corrompido ou o serviço pode ter sido interrompido. A reinstalação do agente de VM permite obter a versão mais recente. Ela também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o serviço Agente Convidado do Windows está em execução nos serviços de VM (services.msc). Tente reiniciar o serviço Agente Convidado do Windows e iniciar o backup.    
2. Se o serviço Agente Convidado do Windows não estiver visível nos serviços, no Painel de Controle, acesse **Programas e Recursos** para determinar se o serviço Agente Convidado do Windows está instalado.
4. Se o Agente Convidado do Windows aparecer em **Programas e Recursos**, desinstale o Agente Convidado do Windows.
5. Baixe e instale a [versão mais recente do MSI do agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa ter direitos de Administrador para concluir a instalação.
6. Verifique se os serviços do Agente Convidado do Windows aparecem nos serviços.
7. Execute um backup sob demanda: 
    * No portal, selecione **Fazer Backup Agora**.

Verifique também se o [Microsoft .NET 4.5 está instalado](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na VM. O .NET 4.5 é necessário para que o agente de VM se comunique com o serviço.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado na VM está desatualizado (para VMs Linux)

#### <a name="solution"></a>Solução
A maioria das falhas relacionadas ao agente ou relacionadas à extensão para VMs do Linux é causada por problemas que afetam um agente de VM desatualizado. Para solucionar esse problema, siga estas diretrizes gerais:

1. Siga as instruções para [atualizar o agente de VM do Linux ](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > Estamos *altamente recomendável* que você atualizar o agente apenas por meio de um repositório de distribuição. Não é recomendável baixar o código do agente diretamente do GitHub e atualizá-lo. Se o agente mais recente para a sua distribuição não está disponível, entre em contato com o suporte da distribuição para obter instruções de como instalá-lo. Para verificar o agente mais recente, vá para a página [agente Linux do Windows Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório do GitHub.

2. Verifique se o agente do Azure está em execução na VM, executando o seguinte comando: `ps -e`

 Se o processo não estiver em execução, reinicie-o usando os seguintes comandos:

 * Para o Ubuntu: `service walinuxagent start`
 * Para outras distribuições: `service waagent start`

3. [Configure o agente de reinicialização automática](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Execute um novo backup de teste. Se a falha persistir, colete os seguintes logs da VM:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Se precisarmos do registro detalhado para waagent, siga estas etapas:

1. No arquivo /etc/waagent.conf, localize a seguinte linha: **habilitar o log detalhado (y | n)**
2. Altere o valor **Logs.Verbose** de *n* para *y*.
3. Salve a alteração e, em seguida, reinicie o waagent, completando as etapas descritas anteriormente nesta seção.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo
O backup da máquina virtual depende de emitir um comando de instantâneo para a conta de armazenamento subjacente. O backup pode falhar porque não tem acesso à conta de armazenamento ou porque a execução da tarefa de instantâneo está atrasada.

#### <a name="solution"></a>Solução
As seguintes condições podem causar falha na tarefa de instantâneo:

| Causa | Solução |
| --- | --- |
| A máquina virtual tem o backup do SQL Server configurado. | Por padrão, o backup de VM executa um backup completo do VSS (Serviço de Cópias de Sombra de Volume) em VMs do Windows. Nas VMs que executam servidores baseados no SQL Server e em que o backup do SQL Server está configurado, pode ocorrer atrasos na execução do instantâneo.<br><br>Se houver uma falha de backup devido a um problema de instantâneo, defina a seguinte chave do Registro:<br><br>**[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE"** |
| O status da VM foi informado incorretamente porque a VM foi desligada no protocolo RDP. | Se você desligar a VM no RDP, verifique o portal para determinar se o status da VM está correto. Se não estiver correto, desligue a VM no portal usando a opção de **Desligar** no painel de VM. |
| A VM não pode obter o endereço do host ou da malha do DHCP. | O DHCP deve estar habilitado no convidado para que o backup da VM IaaS funcione. Se a VM não puder obter o endereço do host ou da malha por meio da resposta 245 do DHCP, ela não poderá baixar ou executar nenhuma extensão. Se você precisar de um endereço IP privado estático, configure-o usando a plataforma. A opção DHCP na VM deve ser ativada. Para saber mais, veja [Definir um IP interno estático privado](../virtual-network/virtual-networks-reserved-private-ip.md). |

### <a name="the-backup-extension-fails-to-update-or-load"></a>A extensão de backup não pode ser atualizada ou carregada
Se as extensões não puderem ser carregadas, o backup falhará porque não é possível obter um instantâneo.

#### <a name="solution"></a>Solução

**Para convidados do Windows:** verifique se o serviço iaasvmprovider está habilitado e tem um tipo de inicialização *automática*. Se o serviço não estiver configurado dessa forma, habilite o serviço para determinar se o próximo backup será bem-sucedido.

**Para convidados do Linux:** verifique se a versão mais recente do VMSnapshot para Linux (a extensão usada pelo Backup) é a 1.0.91.0.<br>


Se a extensão de backup ainda falhar ao ser atualizada ou carregada, desinstale a extensão para forçar a extensão VMSnapshot a ser recarregada. A próxima tentativa de backup recarregará a extensão.

Para desinstalar a extensão:

1. No [Portal do Azure](https://portal.azure.com/), acesse a VM que está apresentando falha de backup.
2. Escolha a opção **Configurações**.
3. Selecione **Extensões**.
4. Selecione **Extensão Vmsnapshot**.
5. Selecione **Desinstalar**.

A realização dessas etapas faz com que a extensão seja reinstalada durante o próximo backup.

### <a name="backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock"></a>O serviço de Backup não tem permissão para excluir os pontos de restauração antigos por causa de um bloqueio de grupo de recursos
Esse problema é específico de VMs gerenciadas nas quais o usuário bloqueia o grupo de recursos. Nesse caso, o serviço de backup não consegue excluir os pontos de restauração mais antigos. Como há um limite de 18 pontos de restauração, os novos backups começam a falhar.

#### <a name="solution"></a>Solução

Para resolver o problema, realize as etapas a seguir para remover a coleção de pontos de restauração: <br>
 
1. Remova o bloqueio no grupo de recursos em que a VM está localizada. 
2. Instale o ARMClient usando o Chocolatey: <br>
   https://github.com/projectkudu/ARMClient
3. Faça logon no ARMClient: <br>
    `.\armclient.exe login`
4. Obtenha a coleção de pontos de restauração que corresponde à VM: <br>
    `.\armclient.exe get https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30`

    Exemplo: `.\armclient.exe get https://management.azure.com/subscriptions/f2edfd5d-5496-4683-b94f-b3588c579006/resourceGroups/winvaultrg/providers/Microsoft.Compute/restorepointcollections/AzureBackup_winmanagedvm?api-version=2017-03-30`
5. Exclua a coleção de pontos de restauração: <br>
    `.\armclient.exe delete https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30` 
6. O próximo backup agendado criará automaticamente a coleção de pontos de restauração e os novos pontos de restauração.

 
O problema voltará a ocorrer se você bloquear novamente o grupo de recursos. 

