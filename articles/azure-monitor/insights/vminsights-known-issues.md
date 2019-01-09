---
title: Problemas conhecidos do Azure Monitor para VMs (versão prévia) | Microsoft Docs
description: Este artigo aborda os problemas conhecidos com o Azure Monitor para VMs, uma solução do Azure que combina monitoramento da integridade e de desempenho do sistema operacional da VM do Azure. O Azure Monitor para VMs descobre automaticamente os componentes de aplicativos e as dependências com outros recursos e mapeia a comunicação entre eles.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/26/2018
ms.author: magoedte
ms.openlocfilehash: c329e1fa80c6647bb78b11917ecd012461e62ea4
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790496"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Problemas conhecidos com o Azure Monitor para VMs (versão prévia)

Este artigo aborda os problemas conhecidos com o Azure Monitor para VMs, uma solução do Azure que combina monitoramento da integridade e de desempenho do sistema operacional da VM do Azure. 

Os seguintes são problemas conhecidos com o recurso Integridade:

- O recurso Integridade está habilitado para todas as VMs conectadas ao workspace do Log Analytics. Isso ocorre mesmo quando a ação começa e termina em uma VM individual.
- Para VMs do Linux, o título da página listando os critérios de integridade para exibição de uma única VM tem o nome de domínio inteiro da VM em vez do nome da VM definido pelo usuário.  
- Depois que você desabilitar o monitoramento de uma VM usando os métodos compatíveis e tentar implantá-la novamente, você deverá implantá-la no mesmo workspace. Se você usar um novo workspace e tentar exibir o estado de integridade dessa VM, ela poderá apresentar um comportamento anormal.
- Se uma VM do Azure for removida ou excluída, ela será mostrada na exibição de lista da VM durante algum tempo. Além disso, ao clicar no estado de uma VM removida ou excluída, a exibição **Diagnóstico de Integridade** será aberta e, em seguida, um loop de carregamento será iniciado. A seleção do nome da VM excluída abre um painel com uma mensagem informando que a VM foi excluída.
- O período e a frequência dos critérios de integridade não podem ser modificados com essa versão. 
- Os critérios de integridade não podem ser desabilitados. 
- Após a implantação, poderá levar algum tempo para os dados serem exibidos no painel **Azure Monitor** > **Máquinas Virtuais** > **Integridade** ou no painel **Recurso de VM** > **Insights**.
- A experiência de Diagnóstico de Integridade é atualizada mais rapidamente do que qualquer outra exibição. As informações podem ser atrasadas quando você alterna entre exibições. 
- O resumo Alertas incluso no Azure Monitor para VM exibe somente os alertas que resultam de problemas de integridade detectados com as VMs do Azure monitoradas.
- O desligamento das VMs atualiza alguns dos critérios de integridade para *crítico* e outros para *íntegro*. O estado da VM de rede é exibido como *críticos*.
- A gravidade do alerta de integridade não pode ser modificada, só pode ser habilitada ou desabilitada. Além disso, algumas severidades são atualizadas com base nos critérios de integridade.   
- Se você modificar qualquer configuração de uma instância de critério de integridade, todas as instâncias de critérios de integridade do mesmo tipo na VM serão modificadas. Por exemplo, se o limite da instância de critério de integridade de espaço livre em disco correspondente ao disco lógico C: for modificado, esse limite se aplicará a todos os outros discos lógicos descobertos e monitorados para a mesma VM.  
- Os limites para os critérios de integridade que se destinam a uma VM do Windows não são modificáveis, pois seus estados de integridade estão definidos como *em execução* ou *disponível*. Quando você consulta o estado de integridade na [API do Monitor de Carga de Trabalho](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager), ela exibe o valor *comparisonOperator* igual a **LessThan** ou **GreaterThan** com um valor *limite* igual a **4** para o serviço ou a entidade se:
   - Integridade do Serviço de Cliente DNS – o serviço não está em execução. 
   - Integridade do serviço de cliente DHCP – o serviço não está em execução. 
   - Integridade do serviço RPC – o serviço não está em execução. 
   - Integridade do serviço do Firewall do Windows – o serviço não está em execução.
   - Integridade do serviço de log de eventos do Windows – o serviço não está em execução. 
   - Integridade do serviço de servidor – o serviço não está em execução. 
   - Integridade do serviço de gerenciamento remoto do Windows – o serviço não está em execução. 
   - Erro ou corrupção do sistema de arquivos – o Disco Lógico não está disponível.

- Os limites para os critérios de integridade do Linux a seguir não são modificáveis, pois o estado de integridade deles já está definido como *true*. O estado de integridade mostra o *comparisonOperator* com um valor **LessThan** e o valor *limite* igual a **1** quando consultado na API de Monitoramento da Carga de Trabalho para a entidade, dependendo do contexto:
   - Status do Disco Lógico – o disco lógico não está online/disponível
   - Status do Disco – o disco não está online/disponível
   - Status do adaptador de rede - adaptador de rede está desabilitado  

- As alterações de configuração, como a atualização de um limite, levam até 30 minutos, mesmo se o portal ou a API do Monitor da Carga de Trabalho atualizá-las imediatamente. 
- Os critérios de integridade no processador individual e no processador lógico não estão disponíveis no Windows. Somente Utilização total da CPU está disponível para VMs do Windows. 
- As regras de alerta definidas para cada critério de integridade não são exibidas no portal do Azure. É possível habilitar ou desabilitar uma regra de alerta de integridade somente na [API do Monitor da Carga de Trabalho](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). 
- Não é possível atribuir um [grupo de ações do Azure Monitor](../../azure-monitor/platform/action-groups.md) para alertas de integridade no portal do Azure. Só é possível usar a API de configuração de notificação para configurar um grupo de ações a ser disparado sempre que um alerta de integridade é disparado. No momento, você pode atribuir grupos de ações em uma VM para que todos os *alertas de integridade* disparados na VM disparem os mesmos grupos de ações. Ao contrário dos alertas tradicionais do Azure, não há um conceito de grupo de ações separado para cada regra de alerta de integridade. Além disso, há suporte apenas para os grupos de ações configurados para fornecer notificações por SMS ou email quando os alertas de integridade são disparados. 

## <a name="next-steps"></a>Próximas etapas
Para entender os requisitos e os métodos para habilitar o monitoramento das máquinas virtuais, examine [Implantar o Azure Monitor para VMs](vminsights-onboard.md).
