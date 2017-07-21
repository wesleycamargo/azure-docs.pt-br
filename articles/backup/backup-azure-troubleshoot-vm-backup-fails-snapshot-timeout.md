---
title: "Solucionar problemas de falha de Backup do Azure: indisponível no status de agente convidado | Microsoft Docs"
description: "Sintomas, causas e resoluções para falhas de backup do Azure relacionados ao erro: não foi possível se comunicar com o agente da VM"
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
ms.topic: article
ms.date: 06/13/2017
ms.author: genli;markgal;
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: dd4ac14a703663175bb477de587da8f4ad510c7c
ms.contentlocale: pt-br
ms.lasthandoff: 06/14/2017

---

# <a name="troubleshoot-azure-backup-failure-vm-agent-unable-to-communicate-with-azure-backup"></a>Solucionar problemas de falha de Backup do Azure: o agente da VM não pode se comunicar com o Backup do Azure
## <a name="summary"></a>Resumo
Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer um dos quatro condições podem impedir que o instantâneo do que está sendo disparado, que por sua vez, pode levar a falhas de Backup. Este artigo fornece etapas de solução de problemas para ajudar você a resolver falhas de backup relacionadas a problemas de comunicação com o agente da VM e a extensão.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Sintoma
Backup do Azure para uma infraestrutura conforme uma VM de serviço (IaaS) falha, retornando a seguinte mensagem de erro nos detalhes de erro do trabalho no [portal do Azure](https://portal.azure.com/): "Não foi possível comunicar o agente da VM com o Serviço de Backup do Azure”, “A operação do instantâneo falhou devido a falta de conectividade de rede na máquina virtual”.

## <a name="cause-1-the-vm-has-no-internet-access"></a>Causa 1: A VM tem sem acesso à Internet
Pelo requisito de implantação, a VM não tem acesso à Internet ou tem restrições em vigor que impedem o acesso à infraestrutura do Azure.

Para funcionar corretamente, a extensão de backup exige conectividade com os endereços IP públicos do Azure. A extensão envia comandos para um ponto de extremidade do Armazenamento do Azure (URL de HTTP) para gerenciar os instantâneos da VM. Se a extensão não tiver acesso à Internet pública, eventualmente falha de Backup.

### <a name="solution"></a>Solução
Para resolver o problema, tente um dos seguintes métodos listados aqui.
#### <a name="allow-access-to-the-azure-datacenter-ip-ranges"></a>Permitir o acesso para os intervalos IP do datacenter do Azure

1. Obtenha a [lista de IPs do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) para os quais o acesso será permitido.
2. Desbloquear o IPs executando o **New-NetRoute** cmdlet na VM do Azure em uma janela elevada do PowerShell. Execute o cmdlet como um administrador.
3. Para permitir o acesso aos IPs, adicione regras ao grupo de segurança de rede, se você tiver um.

#### <a name="create-a-path-for-http-traffic-to-flow"></a>Criar um caminho para a transmissão do tráfego HTTP

1. Se você tiver alguma restrição de rede no local (um grupo de segurança de rede, por exemplo), implante um servidor proxy HTTP para rotear o tráfego.
2. Para permitir o acesso à Internet por meio do proxy HTTP, adicione regras ao grupo de segurança de rede, se você tiver uma.

Para saber como configurar um proxy HTTP para backups VM, veja [preparar seu ambiente para fazer backup de máquinas virtuais do Azure](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

Caso você esteja usando o Managed Disks, talvez seja necessário uma porta adicional (8443) aberta nos firewalls.

## <a name="cause-2-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 2: o agente instalado na VM está desatualizado (para VMs do Linux)

### <a name="solution"></a>Solução
A maioria das falhas relacionadas ao agente ou relacionadas à extensão para VMs do Linux é causada por problemas que afetam um agente de VM desatualizado. Para solucionar esse problema, siga estas diretrizes gerais:

1. Siga as instruções para [atualizar o agente de VM do Linux ](../virtual-machines/linux/update-agent.md).

 >[!NOTE]
 >Estamos *altamente recomendável* que você atualizar o agente apenas por meio de um repositório de distribuição. Não é recomendável baixar o código do agente diretamente do GitHub e atualizá-lo. Se o agente mais recente está disponível para a sua distribuição, contate o suporte de distribuição para obter instruções sobre como instalá-lo. Para verificar o agente mais recente, vá para a página [agente Linux do Windows Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório do GitHub.

2. Certifique-se de que o agente do Azure está em execução na máquina virtual executando o seguinte comando:`ps -e`

 Se o processo não está em execução, reinicie-o usando os seguintes comandos:

 * Para o Ubuntu: `service walinuxagent start`
 * Para outras distribuições: `service waagent start`

3. [Configure o agente de reinicialização automática](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Execute um novo backup de teste. Se a falha persistir, reúna os seguintes logs da VM do cliente:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Se precisarmos do registro detalhado para waagent, siga estas etapas:

1. No arquivo /etc/waagent.conf, localize a seguinte linha: **habilitar o log detalhado (y | n)**
2. Altere o valor **Logs.Verbose** de *n* para *y*.
3. Salve a alteração e reinicie o waagent seguindo as etapas anteriores nesta seção.

## <a name="cause-3-the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Causa 3: o agente está instalado na VM, mas sem resposta (para VMs do Windows)

### <a name="solution"></a>Solução
O agente da VM pode ter sido corrompido ou o serviço deve ter sido interrompido. Reinstalar o agente da VM ajudaria a obter a versão mais recente e reiniciar a comunicação.

1. Verifique se você pode exibir o serviço de agente convidado do Windows nos serviços do computador (services.msc)
2. Se não estiver visível lá, verifique em Programas e Recursos se o serviço de agente convidado do Windows está instalado.
3. Se você for capaz de exibir em programas e recursos, desinstale o Agente convidado do Windows.
4. Baixe e instale o [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa de privilégios de Administrador para concluir a instalação.
5. Em seguida, você deve ser capaz de exibir os serviços do agente de convidado do Windows nos serviços
6. Tente executar um backup sob demanda/adhoc clicando em "Fazer Backup agora" no portal.

Verifique também se você tem o **.NET 4.5 instalado no sistema**. Ele é necessário para o agente da VM se comunicar com o serviço

## <a name="cause-4-the-backup-extension-fails-to-update-or-load"></a>Causa 4: a extensão de backup não pode ser atualizada ou carregada
Se as extensões não puderem ser carregadas, o Backup falhará porque não é possível obter um instantâneo.

### <a name="solution"></a>Solução

Para convidados do Windows:

Verifique se o serviço iaasvmprovider está habilitado e tem um tipo de inicialização *automática*. Se o serviço não está configurado dessa forma, habilitá-la determinar se o próximo backup for bem-sucedido.

Para convidados com Linux:

A versão mais recente do VMSnapshot para Linux (a extensão usada pelo Backup) é a 1.0.91.0.

Se a extensão de backup ainda não conseguir ser atualizada ou carregada, você poderá forçar a extensão VMSnapshot ser recarregada desinstalando a extensão. A próxima tentativa de backup recarregará a extensão.

Para desinstalar a extensão, faça o seguinte:

1. Vá para o [Portal do Azure](https://portal.azure.com/).
2. Localize a VM com problemas de backup.
3. Clique em **Configurações**.
4. Clique em **Extensões**.
5. Clique em **Extensão Vmsnapshot**.
6. Clique em **Desinstalar**.  

Este procedimento faz com que a extensão seja reinstalada durante o próximo backup.

## <a name="cause-5-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Causa 5: não é possível recuperar o status de instantâneos ou não é possível obter os instantâneos
O backup da máquina virtual depende de emitir um comando de instantâneo para a conta de armazenamento subjacente. Backup pode falhar porque ele não tem acesso à conta de armazenamento ou atrasar a execução da tarefa de instantâneo.

### <a name="solution"></a>Solução
As condições a seguir podem causar a falha da tarefa do instantâneo:

| Causa | Solução |
| --- | --- |
| A máquina virtual tem o backup do SQL Server configurado. | Por padrão, o backup de VM executa um backup completo VSS em VMs do Windows. Em VMs que executam servidores baseados no SQL Server e em que o backup do SQL Server está configurado, podem ocorrer atrasos na execução do instantâneo.<br><br>Se houver uma falha de Backup devido a um problema instantâneo, defina a seguinte chave do registro:<br><br>**[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE"** |
| O status da VM é informado incorretamente porque a VM está desligada em RDP. | Se você desligar a VM no protocolo de área de trabalho remota (RDP), verifique o portal para determinar se o status da VM está correto. Se não estiver correto, desligue a VM no portal usando a opção de **Desligar** no painel de VM. |
| Várias VMs do mesmo serviço de nuvem são configuradas para backup simultaneamente. | É uma prática recomendada distribuir as agendas de backup para VMs do mesmo serviço de nuvem. |
| A VM está em execução com alto uso de CPU ou memória. | Se a VM estiver em execução com alta utilização de CPU (mais de 90%) ou alto uso de memória, a tarefa do instantâneo será enfieirada e postergada e, eventualmente, atingirá o tempo limite. Nessa situação, tente um backup sob demanda. |
| A VM não pode obter o endereço do host/malha do DHCP. | O DHCP deve estar habilitado no convidado para que o backup da VM IaaS funcione.  Se a VM não puder obter o endereço do host/malha da resposta DHCP 245, ela não poderá baixar ou executar qualquer extensão. Se você precisar de um endereço IP privado estático, deverá configurá-lo usando a plataforma. A opção DHCP na VM deve ser ativada. Para saber mais, veja [Definição de um IP interno estático privado](../virtual-network/virtual-networks-reserved-private-ip.md). |

