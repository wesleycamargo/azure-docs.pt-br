---
title: 'Solucionar problemas de falha de Backup do Azure: indisponível no status de agente convidado'
description: Sintomas, causas e resoluções para falhas do Backup do Azure relacionados a agente, extensão e discos.
services: backup
author: genlin
manager: cshepard
keywords: Backup do Azure; agente da VM; conectividade de rede;
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 496afab869d8cf1b7b00791913c3082e31b45327
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633913"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Solucionar problemas de falha do Backup do Azure: problemas com o agente ou a extensão

Este artigo fornece etapas de solução de problemas que podem ajudar você a resolver erros de Backup do Azure relacionados à comunicação com a extensão e o agente da VM.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable – o agente de VM não pode se comunicar com o Backup do Azure

**Código de erro**: UserErrorGuestAgentStatusUnavailable <br>
**Mensagem de erro**: o Agente de VM não pode se comunicar com o Backup do Azure<br>

Depois de registrar e agendar uma VM para o serviço de Backup, o Backup iniciará o trabalho comunicando-se com o agente de VM para obter um instantâneo pontual. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Quando um instantâneo não é disparado, o backup pode falhar. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:<br>
**Causa 1: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 2: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**Causa 4: [a extensão de backup falha ao ser atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)**  
**Causa 5: [a VM não tem acesso à Internet](#the-vm-has-no-internet-access)**

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError – não foi possível se comunicar com o agente de VM para o status do instantâneo

**Código de erro**: GuestAgentSnapshotTaskStatusError<br>
**Mensagem de erro**: Não foi possível se comunicar com o agente VM para status do instantâneo <br>

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:  
**Causa 1: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [a VM não tem acesso à Internet](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached – o limite máximo da coleção de pontos de restauração foi atingido

**Código de erro**: UserErrorRpCollectionLimitReached <br>
**Mensagem de erro**: o limite máximo da coleção de pontos de restauração foi atingido. <br>
* Esse problema pode ocorrer se há um bloqueio no grupo de recursos de ponto de recuperação evitando a limpeza automática de um ponto de recuperação.
* Esse problema também pode ocorrer se vários backups são disparados por dia. No momento, é recomendável fazer apenas um backup por dia, já que os pontos de recuperação instantâneos são mantidos por 7 dias e apenas 18 pontos de recuperação instantâneos podem ser associados a uma VM em um determinado momento. <br>

Ação recomendada:<br>
Para resolver esse problema, remova o bloqueio do grupo de recursos e repita a operação para disparar a limpeza.

> [!NOTE]
    > O serviço de backup cria um grupo de recursos separado que o grupo de recursos da VM para armazenar a coleção de pontos de restauração. Os clientes são aconselhados a não bloquear o grupo de recursos criado para uso pelo serviço de Backup. O formato de nomenclatura do grupo de recursos criado pelo serviço de Backup é: AzureBackupRG_`<Geo>`_`<number>` Por exemplo: AzureBackupRG_northeurope_1

**Etapa 1: [ Remover bloqueio do grupo de recursos do ponto de restauração](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Etapa 2: [limpar a coleção de pontos de restauração](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - O backup não tem permissões suficientes do cofre de chaves para fazer backup de VMs criptografadas.

**Código de erro**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Mensagem de erro**: o backup não tem permissões suficientes do cofre de chaves para fazer backup de VMs criptografadas. <br>

Para que a operação de backup tenha êxito em VMs criptografadas, é necessário ter permissões para acessar o cofre de chaves. Isso pode ser feito usando o [portal do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption#provide-permissions-to-backup) ou por meio do [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection)

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork – falha na operação de instantâneo por falta de conectividade à rede na máquina virtual

**Código de erro**: ExtensionSnapshotFailedNoNetwork<br>
**Mensagem de erro**: falha na operação de instantâneo por falta de conectividade à rede na máquina virtual<br>

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:    
**Causa 1: [não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [a extensão de backup falha ao ser atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)**  
**Causa 3: [a VM não tem acesso à Internet](#the-vm-has-no-internet-access)**

## <a name="ExtentionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtentionOperationFailed – falha na operação da extensão VMSnapshot

**Código de erro**: ExtentionOperationFailed <br>
**Mensagem de erro**: falha na operação da extensão VMSnapshot<br>

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:  
**Causa 1: [não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [a extensão de backup falha ao ser atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)**  
**Causa 3: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 4: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 – falha no backup, com um erro interno

**Código de erro**: BackUpOperationFailed/BackUpOperationFailedV2 <br>
**Mensagem de erro**: falha no backup com um erro interno – tente novamente a operação dentro de alguns minutos <br>

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:  
**Causa 1: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 4: [a extensão de backup falha ao ser atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)**  
**Causa 5: [o serviço de Backup não tem permissão para excluir os pontos de restauração antigos por causa de um bloqueio de grupo de recursos](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)** <br>
**Causa 6: [a VM não tem acesso à Internet](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-1023gb"></a>UserErrorUnsupportedDiskSize - Atualmente, o Backup do Azure não suporta tamanhos de disco maiores que 1023 GB

**código de erro**: UserErrorUnsupportedDiskSize <br>
**Mensagem de erro**: atualmente, o Backup do Azure não suporta tamanhos de disco maiores que 1023 GB <br>

Sua operação de backup pode falhar ao fazer o backup da VM com tamanho de disco maior que 1023 GB, pois o seu cofre não é atualizado para a pilha V2 do VM Backup do Azure. A atualização para a pilha V2 do Backup do VMware do Azure fornecerá suporte de até 4 TB. Analise esses [benefícios](backup-upgrade-to-vm-backup-stack-v2.md), [considerações](backup-upgrade-to-vm-backup-stack-v2.md#considerations-before-upgrade) e, em seguida, faça o upgrade seguindo estas [instruções](backup-upgrade-to-vm-backup-stack-v2.md#upgrade).  

## <a name="usererrorstandardssdnotsupported---currently-azure-backup-does-not-support-standard-ssd-disks"></a>UserErrorStandardSSDNotSupported - Atualmente, o Backup do Azure não suporta discos SSD padrão

**Código de erro**: UserErrorStandardSSDNotSupported <br>
**Mensagem de erro**: no momento, o Backup do Azure não oferece suporte a discos SSD Standard <br>

Atualmente, o Backup do Azure oferece suporte a discos SSD padrão apenas para os vaults atualizados para a pilha V2 do VM Backup do Azure. Analise esses [benefícios](backup-upgrade-to-vm-backup-stack-v2.md), [considerações](backup-upgrade-to-vm-backup-stack-v2.md#considerations-before-upgrade) e, em seguida, faça o upgrade seguindo estas [instruções](backup-upgrade-to-vm-backup-stack-v2.md#upgrade).


## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-vm-has-no-internet-access"></a>A VM não tem acesso à Internet
Devido ao requisito de implantação, a VM não tem acesso à Internet. Ou, talvez haja restrições que impedem o acesso à infraestrutura do Azure.

Para funcionar corretamente, a extensão de Backup exige conectividade com endereços IP públicos do Azure. A extensão envia comandos para um ponto de extremidade de armazenamento do Azure (URL de HTTP) para gerenciar os instantâneos da VM. Se a extensão não tiver acesso à Internet pública, o backup, eventualmente, falhará.

É possível implantar um servidor proxy para rotear o tráfego VM.
##### <a name="create-a-path-for-https-traffic"></a>Criar um caminho para o tráfego HTTP

1. Se você tiver restrições de rede (por exemplo, um grupo de segurança de rede), implante um servidor proxy HTTPs para rotear o tráfego.
2. Para permitir o acesso à Internet por meio do servidor proxy HTTP, adicione regras ao grupo de segurança de rede, se você tiver um.

Para saber como configurar um proxy HTTP para backups VM, veja [Preparar seu ambiente para fazer backup de máquinas virtuais do Azure](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

A VM de backup ou o servidor proxy por meio do qual o tráfego é roteado requer acesso a endereços IP públicos do Azure

####  <a name="solution"></a>Solução
Para resolver o problema, tente usar um dos seguintes métodos:

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>Permitir o acesso ao armazenamento do Azure correspondente à região

Você pode usar [marcas de serviço](../virtual-network/security-overview.md#service-tags) para permitir conexões ao armazenamento da região específica. Verifique se a regra que permite o acesso à conta de armazenamento tem prioridade maior que a regra que bloqueia o acesso à Internet.

![Grupo de segurança de rede com marcas de armazenamento para uma região](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

Para entender o procedimento passo a passo para configurar marcações de serviço, assista [este vídeo](https://youtu.be/1EjLQtbKm1M).

> [!WARNING]
> As marcas de serviço de armazenamento estão em versão prévia. Eles estão disponíveis somente em regiões específicas. Para obter a lista de regiões, consulte [Marcas de serviço para armazenamento](../virtual-network/security-overview.md#service-tags).

Se você usa o Managed Disks do Azure, pode ser necessário abrir outra porta (porta 8443) nos firewalls.

Além disso, se sua sub-rede não tiver uma rota para o tráfego de saída da Internet, você precisará adicionar um ponto de extremidade de serviço com a marca de serviço "Microsoft.Storage" à sua sub-rede.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente está instalado na VM, mas sem resposta (para VMs do Windows)

#### <a name="solution"></a>Solução
O agente de VM pode ter sido corrompido ou o serviço pode ter sido interrompido. A reinstalação do agente de VM permite obter a versão mais recente. Ela também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o serviço Agente Convidado do Windows está em execução nos serviços de VM (services.msc). Tente reiniciar o serviço Agente Convidado do Windows e iniciar o backup.    
2. Se o serviço Agente Convidado do Windows não estiver visível nos serviços, no Painel de Controle, acesse **Programas e Recursos** para determinar se o serviço Agente Convidado do Windows está instalado.
4. Se o Agente Convidado do Windows aparecer em **Programas e Recursos**, desinstale o Agente Convidado do Windows.
5. Baixe e instale a [versão mais recente do MSI do agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa ter direitos de Administrador para concluir a instalação.
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
2. Altere o valor **Logs.Verbose** de *n* para *s*.
3. Salve a alteração e, em seguida, reinicie o waagent, completando as etapas descritas anteriormente nesta seção.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo
O backup da máquina virtual depende de emitir um comando de instantâneo para a conta de armazenamento subjacente. O backup pode falhar porque não tem acesso à conta de armazenamento ou porque a execução da tarefa de instantâneo está atrasada.

#### <a name="solution"></a>Solução
As seguintes condições podem causar falha na tarefa de instantâneo:

| Causa | Solução |
| --- | --- |
| O status da VM foi informado incorretamente porque a VM foi desligada no protocolo RDP. | Se você desligar a VM no RDP, verifique o portal para determinar se o status da VM está correto. Se não estiver correto, desligue a VM no portal usando a opção de **Desligar** no painel de VM. |
| A VM não pode obter o endereço do host ou da malha do DHCP. | O DHCP deve estar habilitado no convidado para que o backup da VM IaaS funcione. Se a VM não puder obter o endereço do host ou da malha por meio da resposta 245 do DHCP, ela não poderá baixar ou executar nenhuma extensão. Se você precisar de um endereço IP privado estático, configure-o usando a plataforma. A opção DHCP na VM deve ser ativada. Para saber mais, veja [Definir um IP interno estático privado](../virtual-network/virtual-networks-reserved-private-ip.md). |

### <a name="the-backup-extension-fails-to-update-or-load"></a>A extensão de backup não pode ser atualizada ou carregada
Se as extensões não puderem ser carregadas, o backup falhará porque não é possível obter um instantâneo.

#### <a name="solution"></a>Solução

Desinstale a extensão para forçar o recarregamento da extensão VMSnapshot. A próxima tentativa de backup recarregará a extensão.

Para desinstalar a extensão:

1. No [Portal do Azure](https://portal.azure.com/), acesse a VM que está apresentando falha de backup.
2. Escolha a opção **Configurações**.
3. Selecione **Extensões**.
4. Selecione **Extensão Vmsnapshot**.
5. Selecione **Desinstalar**.

Para VM Linux, se a extensão VMSnapshot não for mostrada no Portal do Azure, [atualize o Agente Linux do Azure](../virtual-machines/linux/update-agent.md) e, em seguida, execute o backup.

A realização dessas etapas faz com que a extensão seja reinstalada durante o próximo backup.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Remover o bloqueio do grupo de recursos de ponto de recuperação
1. Entre no [Portal do Azure](http://portal.azure.com/).
2. Vá para a **opção Todos os Recursos** e selecione o grupo de recursos de coleção de ponto de restauração no seguinte formato: AzureBackupRG_`<Geo>`_`<number>`.
3. Na seção **Configurações**, selecione **Bloqueios** para exibir os bloqueios.
4. Para remover o bloqueio, selecione as reticências e clique em **Excluir**.

    ![Excluir bloqueio ](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a> Limpar a coleção de pontos de restauração
Depois de remover o bloqueio, os pontos de restauração precisam ser limpos. Para limpar os pontos de restauração, siga qualquer um dos seguintes métodos:<br>
* [Limpar a coleção de pontos de restauração executando backup ad-hoc](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [Limpar a coleção de pontos de restauração do portal do Azure](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>Limpar a coleção de pontos de restauração executando backup ad-hoc
Depois de remover o bloqueio, dispare um backup ad-hoc/manual. Isso garantirá que os pontos de restauração sejam limpos automaticamente. É provável que essa operação ad-hoc/manual falhe na primeira vez. No entanto, ela garantirá limpeza automática, em vez de exclusão manual dos pontos de restauração. Após a limpeza, o próximo backup agendado deverá ser bem-sucedido.

> [!NOTE]
    > A limpeza automática ocorrerá algumas horas após disparar o backup ad-hoc/manual. Se o backup agendado ainda falhar, tente excluir manualmente a coleção de pontos de restauração usando as etapas listadas [aqui](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Limpar coleção de pontos de restauração do portal do Azure <br>

Para limpar manualmente a coleção de pontos de restauração que não está desmarcada devido ao bloqueio no grupo de recursos, tente as seguintes etapas:
1. Entre no [Portal do Azure](http://portal.azure.com/).
2. No menu **Hub**, clique em **Todos os recursos**, selecione o grupo de recursos com o formato AzureBackupRG_`<Geo>`_`<number>` a seguir em que sua VM se encontra.

    ![Excluir bloqueio ](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Clique em Grupo de recursos, a folha **Visão geral** é exibida.
4. Selecione a opção **Mostrar tipos ocultos** opção para exibir todos os recursos ocultos. Selecione as coleções de ponto de restauração com o seguinte formato: AzureBackupRG_`<VMName>`_`<number>`.

    ![Excluir bloqueio ](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Clique em **Excluir** para limpar a coleção de pontos de restauração.
6. Tente a operação de backup novamente.
