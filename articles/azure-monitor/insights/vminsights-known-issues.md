---
title: Problemas conhecidos do Azure Monitor para VMs (Versão prévia) | Microsoft Docs
description: O Azure Monitor para VMs é uma solução do Azure que combina integridade e monitoramento de desempenho do sistema operacional da VM do Azure, bem como a descoberta automática dos componentes de aplicativo e dependências com outros recursos e mapeia a comunicação entre eles. Este artigo aborda os problemas conhecidos.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: 99f84e9784c448091c0257218855c3bf32c2f8f4
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713755"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Problemas conhecidos com o Azure Monitor para VMs (Versão prévia)

Os seguintes são problemas conhecidos com o recurso de integridade do Azure Monitor para VMs:

- O recurso Funcionamento é ativado para todas as VMs conectadas ao espaço de trabalho do Log Analytics, mesmo quando iniciado e concluído a partir de uma única VM.
- Se depois de desabilitar o monitoramento de uma VM usando os métodos suportados, quando a integração for tentada novamente, isso deve ser feito na mesma área de trabalho.  Se uma nova área de trabalho for usada, ao exibir o estado de integridade dessa VM, ela poderá mostrar um comportamento anormal.
- Se uma VM do Azure não existir mais porque foi removida ou excluída, ela será exibida na visualização de lista da VM por três a sete dias. Além disso, clicar no estado de uma VM removida ou excluída iniciaria a visualização **Health Diagnostics** para ela, que então entra em um loop de carregamento. A seleção do nome de uma VM excluída inicia um blade com uma mensagem informando que a VM foi excluída.
- O período e a frequência dos critérios de integridade não podem ser modificados com essa versão. 
- Critérios de integridade não podem ser desabilitados. 
- Após a integração, pode levar tempo até os dados aparecerem no Azure Monitor -> Máquinas Virtuais -> Integridade ou da folha de recurso da VM -> Insights
- O Diagnóstico de Integridade tem atualizações mais rápidas do que qualquer outra exibição, portanto, você pode sofrer atrasos de informações ao alternar entre modos de exibição  
- O resumo de alertas incluído no Azure Monitor para VMs é apenas para alertas disparados por problemas de integridade detectados com as VMs do Azure monitoradas.
- A página de exibição **Lista de alertas** na única VM e no Azure Monitor mostra alertas cuja condição do monitor é definida como "disparada" nos últimos 30 dias.  Não são configuráveis. No entanto, depois de clicar no resumo, quando a página de exibição **Lista de alertas** for inicializada, você poderá alterar o valor do filtro tanto da condição do monitor quanto do intervalo de tempo.
- Na página de exibição **Lista de alertas**, sugerimos que você não modifique os filtros **Tipo de recurso**, **Recurso** e **Serviço do Monitor**, pois eles foram configurados especificamente para a solução (esse modo de exibição de lista mostra alguns campos extras em comparação ao Azure Monitor -> modo de exibição de lista de Alertas).    
- Em VMs do Linux, a exibição **Diagnóstico de Integridade** tem todo o nome de domínio da VM, em vez do nome da VM definido pelo usuário.
- Desligar VMs atualizará alguns dos seus critérios de integridade para um estado crítico e outros, para um estado íntegro com o estado de rede da VM em um estado crítico.
- A gravidade do alerta de integridade não pode ser modificada, só pode ser habilitada ou desabilitada.  Além disso, algumas severidades são atualizadas com base nos critérios de integridade.
- Modificar qualquer configuração de uma instância de critério de integridade levará à modificação da mesma configuração em todas as instâncias de critérios de integridade do mesmo tipo na VM. Por exemplo, se o limite da instância de critério de integridade de espaço livre em disco correspondente a um disco lógico C: for modificado, esse limite se aplicará a todos os outros discos lógicos descobertos e monitorados para a mesma VM.   
- Os limites para os seguintes critérios de integridade que segmentam uma VM do Windows não são modificáveis, pois os estados de integridade já estão definidos como **em execução** ou **disponível**. Quando consultado a partir de [API do Monitor de carga de trabalho](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager), o estado de integridade mostra o *comparisonOperator* valor de **LessThan** ou **GreaterThan**com um *limite* valor de **4** para a entidade ou o serviço se:
   - Integridade do serviço de cliente DNS - o serviço não está em execução 
   - Integridade do serviço do cliente DHCP - O serviço não está em execução 
   - Integridade do serviço RPC – serviço não está em execução 
   - Integridade do serviço de firewall do Windows - o serviço não está em execução
   - Integridade do serviço de log de eventos do Windows - o serviço não está em execução 
   - Integridade do serviço do servidor - o serviço não está em execução 
   - Integridade do serviço de gerenciamento remoto do Windows - o serviço não está em execução 
   - Erro ou corrupção do sistema de arquivos - o disco lógico não está disponível

- Os limites para os seguintes critérios de integridade do Linux não são modificáveis, pois o estado de integridade deles já está definido como **verdadeiro**.  O estado de integridade mostra o *comparisonOperator* com um valor **LessThan** e *limite* valor de **1** quando consultado da carga de trabalho Monitoramento de API para a entidade dependendo do contexto:
   - Status do disco lógico - Disco lógico não está on-line / disponível
   - Status do disco – disco não está online / disponíveis
   - Status do adaptador de rede - adaptador de rede está desabilitado  

- O **critério de integridade Utilização total da CPU** no Windows mostra um limite de **diferente de 4** do portal e quando consultado na API do Workload Monitoring quando a Utilização da CPU é maior que 95% e tamanho da fila do sistema é maior que 15. Esse critério de integridade não pode ser modificado nesta versão.  
- As alterações de configuração, como a atualização de um limite, levam até 30 minutos para serem efetivadas, embora o portal ou a API do Workload Monitor possa ser atualizado imediatamente.  
- Os critérios de integridade do processador lógico e do nível do processador lógico não estão disponíveis no Windows, apenas **A utilização total da CPU** está disponível para VMs do Windows.  
- Regras de alerta definidas para cada critério de integridade não são expostas no portal do Azure. Eles são configuráveis somente a partir da [API do Monitor de Carga de Trabalho](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) para ativar ou desativar uma regra de alerta de funcionamento.  
- Atribuir um [grupo de ações do Monitor do Azure](../../monitoring-and-diagnostics/monitoring-action-groups.md) para alertas de integridade não é possível no portal do Azure. Você só pode usar a API de configuração de notificação para configurar um grupo de ação a ser acionado sempre que um alerta de integridade for disparado. No momento, grupos de ação podem ser atribuídos em relação a uma VM, de modo que todos os *alertas de integridade* acionado contra o gatilho VM os mesmos grupos de ação. Não há conceito de um grupo de ações separado para cada regra de alerta de integridade, como os alertas tradicionais do Azure. Além disso, somente os grupos de ação configurados para notificar enviando um email ou SMS têm suporte quando os alertas de integridade são disparados. 

## <a name="next-steps"></a>Próximas etapas
Examine [Integrar o Azure Monitor para VMs](vminsights-onboard.md) para entender os requisitos e os métodos usados para habilitar o monitoramento das máquinas virtuais.