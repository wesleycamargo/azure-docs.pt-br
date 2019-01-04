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
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: b2a60ddee58a7a9c37582085132ee8a1df767cb4
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190349"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Problemas conhecidos com o Azure Monitor para VMs (versão prévia)

Este artigo aborda os problemas conhecidos com o Azure Monitor para VMs, uma solução do Azure que combina monitoramento da integridade e de desempenho do sistema operacional da VM do Azure. 

Os seguintes são problemas conhecidos com o recurso Integridade:

- O recurso Integridade está habilitado para todas as VMs conectadas ao workspace do Log Analytics. Isso ocorre mesmo quando a ação começa e termina em uma VM individual.
- Depois que você desabilitar o monitoramento de uma VM usando os métodos compatíveis e tentar implantá-la novamente, você deverá implantá-la no mesmo workspace. Se você usar um novo workspace e tentar exibir o estado de integridade dessa VM, ela poderá exibir um comportamento anormal.
- Se uma VM do Azure for removida ou excluída, ela será mostrada na exibição de lista da VM de três a sete dias. Além disso, ao clicar no estado de uma VM removida ou excluída, a exibição **Diagnóstico de Integridade** será aberta e, em seguida, um loop de carregamento será iniciado. A seleção do nome da VM excluída abre um painel com uma mensagem informando que a VM foi excluída.
- O período e a frequência dos critérios de integridade não podem ser modificados com essa versão. 
- Os critérios de integridade não podem ser desabilitados. 
- Após a implantação, poderá levar algum tempo para os dados serem exibidos no painel **Azure Monitor** > **Máquinas Virtuais** > **Integridade** ou no painel **Recurso de VM** > **Insights**.
- A experiência de Diagnóstico de Integridade é atualizada mais rapidamente do que qualquer outra exibição. As informações podem ser atrasadas quando você alterna entre exibições. 
- O resumo Alertas incluído com o Azure Monitor para VM exibe somente os alertas que resultam de problemas de integridade detectados com as VMs monitoradas do Azure.
- O painel **Lista de alertas** na VM individual e no Azure Monitor exibe os alertas cuja condição de monitor está definida como *disparada* nos últimos 30 dias. Os alertas não são configuráveis. No entanto, após a abertura do painel **Lista de alertas**, você poderá clicar no resumo para alterar o valor de filtro da condição de monitor e do intervalo de tempo.
- No painel **Lista de alertas**, recomendamos não modificar os filtros **Tipo de recurso**, **Recurso** e **Monitorar Serviço**. Eles já foram configurados especificamente para a solução. Esta exibição de lista mostra mais campos do que a exibição de lista **Azure Monitor** > **Alertas**.   
- Em VMs do Linux, a exibição **Diagnóstico de Integridade** tem mostra todo o nome de domínio da VM, em vez do nome da VM definido pelo usuário.
- O desligamento das VMs atualiza alguns dos critérios de integridade para *crítico* e outros para *íntegro*. O estado da VM de rede é exibido como *críticos*.
- A gravidade do alerta de integridade não pode ser modificada. Ela só pode ser habilitada ou desabilitada. Além disso, algumas severidades são atualizadas com base no estado dos critérios de integridade.
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

- O critério de integridade *utilização total da CPU* no Windows exibe um limite de **não é igual a 4** no portal e na API de Monitoramento da Carga de Trabalho. O limite é atingido quando a *utilização total da CPU* é maior que 95% e o tamanho da fila do sistema é maior que 15. Esse critério de integridade não pode ser modificado nesta versão. 
- As alterações de configuração, como a atualização de um limite, levam até 30 minutos, mesmo se o portal ou a API do Monitor da Carga de Trabalho atualizá-las imediatamente. 
- Os critérios de integridade no processador individual e no processador lógico não estão disponíveis no Windows. Somente Utilização total da CPU está disponível para VMs do Windows. 
- As regras de alerta definidas para cada critério de integridade não são exibidas no portal do Azure. É possível habilitar ou desabilitar uma regra de alerta de integridade somente na [API do Monitor da Carga de Trabalho](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager). 
- Não é possível atribuir um [grupo de ações do Azure Monitor](../../azure-monitor/platform/action-groups.md) para alertas de integridade no portal do Azure. Só é possível usar a API de configuração de notificação para configurar um grupo de ações a ser disparado sempre que um alerta de integridade é disparado. No momento, você pode atribuir grupos de ações em uma VM para que todos os *alertas de integridade* disparados na VM disparem os mesmos grupos de ações. Ao contrário dos alertas tradicionais do Azure, não há um conceito de grupo de ações separado para cada regra de alerta de integridade. Além disso, há suporte apenas para os grupos de ações configurados para fornecer notificações por SMS ou email quando os alertas de integridade são disparados. 

## <a name="next-steps"></a>Próximas etapas
Para entender os requisitos e os métodos para habilitar o monitoramento das máquinas virtuais, examine [Implantar o Azure Monitor para VMs](vminsights-onboard.md).
