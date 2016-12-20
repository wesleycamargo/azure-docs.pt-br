---
title: "Falha no Backup da VM do Azure: não foi possível se comunicar com o agente da VM para obter o status do instantâneo - a subtarefa da VM do instantâneo atingiu o tempo limite | Microsoft Docs"
description: "Sintomas, causas e resoluções para falhas de backup de VM do Azure relacionadas à incapacidade de se comunicar com o agente de VM para status do instantâneo. Erro da subtarefa de VM de instantâneo que atingiu o tempo limite"
services: backup
documentationcenter: 
author: genlin
manager: cfreeman
editor: 
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jimpark; markgal;genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 87387e4f182214fa0c34a6a1358c6cc2648be906


---
# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Falha no backup de VM do Azure: não foi possível se comunicar com o agente de VM para o status do instantâneo - a subtarefa da VM atingiu o tempo limite
## <a name="summary"></a>Resumo
Após registrar e agendar uma VM (Máquina Virtual) para o Backup do Azure, o serviço do Backup do Azure inicia o trabalho de backup no horário agendado se comunicando com a extensão de backup na VM para obter um instantâneo pontual. Existem condições que podem impedir que o instantâneo seja disparado que levam a uma falha de backup. Este artigo fornece etapas de solução de problemas relacionados a falhas de backup de VM do Azure referentes ao erro de tempo limite do instantâneo.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Sintoma
O Backup do Microsoft Azure para VM de IaaS (infraestrutura como serviço) falha e retorna a seguinte mensagem de erro nos detalhes do erro do trabalho no [portal do Azure](https://portal.azure.com/):

**Não foi possível se comunicar com o agente de VM para o status do instantâneo - a subtarefa da VM atingiu o tempo limite.**

## <a name="cause"></a>Causa
Há quatro causas comuns para esse erro:

* A VM não tem acesso à Internet.
* O agente de VM do Microsoft Azure instalado na VM está desatualizado (para VMs do Linux).
* A extensão de backup não pode ser atualizada ou carregada.
* Não é possível recuperar o status de instantâneos ou não é possível obter os instantâneos.

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>Causa 1: a VM não tem acesso à Internet
Pelo requisito de implantação, a VM não tem acesso à Internet ou tem restrições em vigor que impedem o acesso à infraestrutura do Azure.

A extensão de backup exige conectividade com os endereços IP públicos do Azure para funcionar corretamente. Isso ocorre porque ela envia comandos para um ponto de extremidade do Armazenamento do Azure (URL de HTTP) para gerenciar os instantâneos da VM. Se a extensão não tiver acesso à Internet pública, o backup, eventualmente, falhará.

### <a name="solution"></a>Solução
Nesse cenário, use um dos seguintes métodos para resolver o problema:

* Realizar a lista de autorizados de intervalos de IP do datacenter do Azure
* Criar um caminho para a transmissão do tráfego HTTP

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>Para realizar a lista branca de intervalos de IP do datacenter do Azure
1. Obtenha a [lista de IPs do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) a ser colocada na lista branca.
2. Desbloqueie os IPs usando o cmdlet New-NetRoute. Execute este cmdlet na VM do Azure em uma janela do PowerShell com privilégios elevados (execute como Administrador).
3. Adicione regras ao NSG (Grupo de Segurança de Rede) se você tiver um para permitir o acesso aos IPs.

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>Para criar um caminho para a transmissão do tráfego HTTP
1. Se você tiver restrições de rede em vigor (por exemplo, um NSG), implante um servidor proxy HTTP para encaminhar o tráfego.
2. Se você tiver um NSG (grupo de segurança de rede), adicione regras para permitir o acesso à Internet por meio do proxy HTTP.

Saiba como [configurar um proxy HTTP para backups de VM](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 2: o agente de VM do Microsoft Azure instalado na VM está desatualizado (para VMs do Linux)
### <a name="solution"></a>Solução
A maioria das falhas relacionadas ao agente ou relacionadas à extensão para VMs do Linux é causada por problemas que afetam um agente de VM antigo. Como uma diretriz geral, as primeiras etapas para solucionar esse problema são as seguintes:

1. [Instale o agente de VM do Azure mais recente](https://github.com/Azure/WALinuxAgent).
2. Certifique-se de que o agente do Azure esteja em execução na VM. Para fazer isso, execute o seguinte comando:     ```ps -e```
   
    Se esse processo não estiver em execução, use os seguintes comandos para reiniciá-lo.
   
    Para o Ubuntu: ```service walinuxagent start```
   
    Para outras distribuições:     ```service waagent start
   ```
3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.
   
    We require the following logs from the customer’s VM:
   
   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:
   
    Enable verbose logging (y|n)
2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension
1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| --- | --- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. |Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. |If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. |It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. |If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
| The VM cannot get host/fabric address from DHCP. |DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md). |




<!--HONumber=Nov16_HO3-->


