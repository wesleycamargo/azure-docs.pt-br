---
title: Solucionar problemas de replicação para recuperação de desastre de VMs VMware e servidores físicos para o Azure usando o Azure Site Recovery | Microsoft Docs
description: Este artigo fornece informações sobre a solução de problemas comuns de replicação durante a recuperação de desastre de VMs VMware e servidores físicos para o Azure usando o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 3221b2bd18b8b0756f280d88fffc6016d0498b8f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924821"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Solução de problemas de replicação para VMs VMware e servidores físicos

Este artigo descreve alguns problemas comuns e erros específicos que você pode encontrar ao replicar VMs do VMware locais e servidores físicos no Azure usando [recuperação de Site](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Etapa 1: Monitorar integridade de servidor de processo

Usos de recuperação de site do [servidor de processo](vmware-physical-azure-config-process-server-overview.md#process-server) para receber e otimizar os dados replicados e enviá-lo para o Azure.

É recomendável que você monitore a integridade dos servidores de processo no portal, para garantir que eles estão conectados e funcionando corretamente e que a replicação está em andamento para os computadores de origem associados ao servidor de processo.

- [Saiba mais sobre](vmware-physical-azure-monitor-process-server.md) monitoramento de servidores de processo.
- [Examinar práticas recomendadas](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Solucionar problemas de](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) a integridade do servidor do processo.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Etapa 2: Solucionar problemas de conectividade e replicação

Falhas de replicação inicial e contínua geralmente são causadas por problemas de conectividade entre o servidor de origem e o servidor de processo ou entre o servidor de processo e o Azure. 

Para resolver esses problemas [solucionar problemas de conectividade e replicação](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Etapa 3: Solucionar problemas de computadores de origem que não estão disponíveis para replicação

Ao tentar selecionar o computador de origem para habilitar a replicação por meio do Azure Site Recovery, o computador pode não estar disponível para você por um dos seguintes motivos:

* **Duas máquinas virtuais com o mesmo UUID de instância**: Se houver duas máquinas virtuais no vCenter com o mesmo UUID de instância, a primeira máquina virtual descoberta pelo servidor de configuração será mostrada no portal do Azure. Para resolver esse problema, assegure que não haja duas máquinas virtuais com o mesmo UUID de instância. Esse cenário é visto comumente em instâncias em que uma VM de backup se torna ativo e tiver feito logon em nossos registros de descoberta. Consulte [Azure Site Recovery – VMware para o Azure: Como limpar entradas duplicadas ou obsoletas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) para resolver esse problema.
* **Credenciais incorretas de usuário do vCenter**: Verifique se você adicionou as credenciais corretas do vCenter durante a configuração do servidor de configuração, usando o modelo OVF ou a configuração unificada. Para verificar as credenciais que você adicionou durante a instalação, consulte [Modificar credenciais para a descoberta automática](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **Privilégios insuficientes do vCenter**: Se as permissões fornecidas para acessar o vCenter não têm as permissões necessárias, pode ocorrer falha ao descobrir máquinas virtuais. Verifique se as permissões descritas em [Preparar uma conta para a descoberta automática](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) foram adicionadas à conta de usuário do vCenter.
* **Servidores de gerenciamento do Azure Site Recovery**: Se a máquina virtual for usada como o servidor de gerenciamento em uma ou mais das seguintes funções – servidor de Configuração/servidor de processo de expansão/servidor de destino Mestre, você não poderá escolher a máquina virtual no portal. Os servidores de gerenciamento não podem ser replicados.
* **Já protegida/com failover por meio dos serviços do Azure Site Recovery**: Se a máquina virtual já estiver protegida ou com failover por meio do Site Recovery, essa máquina virtual não estará disponível para seleção de proteção no portal. Verifique se a máquina virtual que você está procurando no portal já não está protegida por algum outro usuário ou sob uma assinatura diferente.
* **vCenter não conectado**: Verifique se o vCenter está no estado conectado. Para verificar, acesse o cofre dos Serviços de Recuperação > Infraestrutura do Site Recovery > Servidores de Configuração > clique no respectivo servidor de configuração > uma folha será aberta à direita com os detalhes dos servidores associados. Verifique se o vCenter está conectado. Se ele estiver em um estado "Conectado", resolva o problema e, em seguida [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no portal. Depois disso, a máquina virtual será listada no portal.
* **ESXi desligado**: Se o host ESXi no qual a máquina virtual reside estiver no estado desligado, a máquina virtual não será listada ou não será selecionável no portal do Azure. Ligue o host ESXi e [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no portal. Depois disso, a máquina virtual será listada no portal.
* **Reinicialização pendente**: Se houver uma reinicialização pendente na máquina virtual, você não poderá selecioná-la no portal do Azure. Conclua as atividades de reinicialização pendente e [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, a máquina virtual será listada no portal.
* **IP não encontrado**: Se a máquina virtual não tiver um endereço IP válido associado a ela, você não poderá selecioná-la no portal do Azure. Atribua um endereço IP válido à máquina virtual e [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, a máquina virtual será listada no portal.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Solucionar problemas de máquinas virtuais protegidas esmaecidas no portal

As máquinas virtuais que são replicadas no Site Recovery não estão disponíveis no portal do Azure se há entradas duplicadas no sistema. Para saber como excluir entradas obsoletas e resolver o problema, consulte [Azure Site Recovery – VMware para o Azure: Como limpar entradas duplicadas ou obsoletas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="common-errors-and-solutions"></a>Erros comuns e soluções

### <a name="initial-replication-issues-error-78169"></a>Problemas de replicação inicial [Erro 78169]

Ao longo de um acima garantindo que há nenhuma conectividade, largura de banda ou tempo sincronizar questões relacionadas, certifique-se de que:

- Nenhum software antivírus está bloqueando o Azure Site Recovery. Saiba mais [mais](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) em exclusões de pastas necessárias para o Azure Site Recovery.

### <a name="missing-app-consistent-recovery-points-error-78144"></a>Pontos de recuperação consistente do aplicativo ausente [Erro 78144]

 Isso ocorre devido a problemas com cópia de sombra de Volume Service (VSS). Para resolver esse erro: 
 
- Verifique se a versão instalada do agente do Azure Site Recovery é pelo menos 9.22.2. 
- Verifique se o provedor VSS é instalado como um serviço nos serviços do Windows e verifique também se o serviço de componentes do MMC para verificar se o Azure Site Recovery VSS Provider está listado.
- Se o provedor do VSS não está instalado, consulte o [Falha na instalação do artigo de solução de problemas](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Se estiver desabilitado VSS,
    - Verificar se o tipo de inicialização do serviço do provedor do VSS está definido como **automática**.
    - Reinicie os serviços a seguir:
        - Serviço VSS
        - Provedor de VSS do Azure Site Recovery
        - Serviço VDS

### <a name="source-machines-with-high-churn-error-78188"></a>Computadores de origem com alta rotatividade [Erro 78188]

Possíveis causas:
- A taxa de alteração de dados (bytes de gravação/s) nos discos listados da máquina virtual é mais do que o [limites com suporte do Azure Site Recovery](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) para o tipo de conta de armazenamento de destino de replicação.
- Há um aumento repentino na taxa de rotatividade devido à qual alta quantidade de dados está pendente para upload.

Como resolver o problema:
- Certifique-se de que o tipo de conta de armazenamento de destino (Standard ou Premium) é provisionado, de acordo com o requisito de taxa de variação na origem.
- Se a variação observada é temporária, aguarde algumas horas para que o carregamento de dados pendentes para acompanhar e criar pontos de recuperação.
- Se o problema persistir, use o Site Recovery [Planejador de implantação](site-recovery-deployment-planner.md#overview) para ajudar a planejar a replicação.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Computadores de origem com nenhuma pulsação [Erro 78174]

Isso acontece quando o agente de mobilidade do Azure Site Recovery na máquina de origem não está se comunicando com o servidor de configuração (CS).

Para resolver o problema, use as etapas a seguir para verificar a conectividade de rede da VM de origem para o servidor de configuração:

1. Verifique se que o computador de origem está em execução.
2. Entrar no computador de origem usando uma conta que tenha privilégios de administrador.
3. Verificar se os seguintes serviços estão em execução e se não reiniciar os serviços:
   - Svagents (InMage Scout VX Agent)
   - Serviço de Aplicativo InMage Scout
4. No computador de origem, examine os logs no local para obter detalhes do erro:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Servidor de processo com nenhuma pulsação [Erro 806]
Caso não haja nenhuma pulsação do servidor de processo (PS), verifique se:
1. PS VM está em execução
2. Verifique o seguinte logon do PS para detalhes do erro:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Servidor de destino mestre com nenhuma pulsação [Erro 78022]

Isso acontece quando o agente de mobilidade do Azure Site Recovery no destino mestre não está se comunicando com o servidor de configuração.

Para resolver o problema, use as etapas a seguir para verificar o status do serviço:

1. Verifique se que a VM de destino mestre está em execução.
2. Entrar para a VM de destino mestre usando uma conta que tenha privilégios de administrador.
    - Verifique se o serviço svagents está em execução. Se estiver em execução, reinicie o serviço
    - Verifique os logs no local para obter detalhes do erro:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log



## <a name="next-steps"></a>Próximas etapas

Se precisar de mais ajuda, poste sua pergunta no [fórum do Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Temos uma comunidade ativa e um dos nossos engenheiros poderá ajudá-lo.
