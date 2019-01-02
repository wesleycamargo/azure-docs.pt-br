---
title: Visão geral do Azure Resource Health | Microsoft Docs
description: Visão geral do Azure Resource Health
services: Resource health
documentationcenter: ''
author: stephbaron
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 11/16/2018
ms.author: stbaron
ms.openlocfilehash: 918ff674e57a14f685c7f9ef2aaaca5ca064976e
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012837"
---
# <a name="azure-resource-health-overview"></a>Visão geral do Azure Resource Health
 
O Azure Resource Health ajuda a diagnosticar e obter suporte quando um problema de serviço do Azure afeta seus recursos. Ele informa sobre a integridade atual e anterior dos seus recursos. E fornece suporte técnico para ajudá-lo a mitigar os problemas.

Considerando que o [Status do Azure](https://status.azure.com) informa sobre os problemas de serviço que afetam um amplo conjunto de clientes do Azure, o Resource Health oferece um painel de integridade personalizado dos recursos. O Resource Health mostra todas as vezes que seus recursos não estavam disponíveis no passado devido a problemas de serviço do Azure. Então, isso simplifica a tarefa de compreender se um SLA foi violado. 

## <a name="resource-definition-and-health-assessment"></a>Definição de recurso e avaliação de integridade
Um recurso é uma instância específica de um serviço do Azure, por exemplo: uma máquina virtual, um aplicativo Web ou um Banco de Dados SQL.

O Resource Health baseia-se em sinais emitidos pelos diferentes serviços do Azure para avaliar se um recurso está íntegro ou não. Se um recurso não estiver íntegro, o Resource Health analisará informações adicionais para determinar a origem do problema. Ele também identifica ações que a Microsoft está tomando para corrigir o problema ou as ações que você poderá tomar para resolver a causa do problema. 

Para obter detalhes adicionais sobre como a integridade é avaliada, revise a lista completa de tipos de recursos e verificações de integridade no [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Status de integridade
A integridade de um recurso é exibida como um dos estados a seguir.

### <a name="available"></a>Disponível
Um status de **Disponível** significa que o serviço não detectou nenhum evento que afete a integridade do recurso. Nos casos em que o recurso se recuperou do tempo de inatividade não planejado durante as últimas 24 horas, você verá uma notificação **Recentemente Resolvidos**.

![Status de "Disponível" para uma máquina virtual com uma notificação "Recentemente Resolvidos"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Indisponível
Um status de **Não disponível** significa que o serviço detectou uma plataforma em andamento ou evento de não plataforma que afeta a integridade do recurso.

#### <a name="platform-events"></a>Eventos de plataforma
Os eventos da plataforma são disparados por vários componentes da infraestrutura do Azure. Eles incluem ações agendadas (por exemplo, manutenção planejada) e incidentes inesperados (por exemplo, uma reinicialização do host não planejada).

O Resource Health fornece detalhes adicionais sobre o evento e o processo de recuperação. Ele também permite que você entre em contato com o suporte mesmo se você não tiver um contrato de suporte ativo da Microsoft.

![Status de "Não disponível" para uma máquina virtual devido a um evento de plataforma](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventos de não plataforma
Eventos de não plataforma são disparados pelas ações dos usuários. Exemplos são parar uma máquina virtual ou alcançar o número máximo de conexões para um Cache do Azure para Redis.

![Status de "Não disponível" para uma máquina virtual devido a um evento de não plataforma](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Desconhecido
O status de integridade de **Desconhecido** indica que o Resource Health não recebeu informações sobre esse recurso por mais de 10 minutos. Embora esse status não seja uma indicação definitiva sobre o estado do recurso, é um ponto de dados importante no processo de solução de problemas.

Se o recurso estiver em execução conforme o esperado, o status do recurso mudará para **Disponível** após alguns minutos.

Se você estiver tendo problemas com o recurso, o estado de integridade **Desconhecido** poderá sugerir que um evento na plataforma esteja afetando o recurso.

![Status de "Desconhecido" para uma máquina virtual](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Degradado
O status de integridade de **Degradado** indica que seu recurso detectou uma perda no desempenho, embora ainda esteja disponível para uso.
Diferentes recursos têm seus próprios critérios para quando eles especificam que um recurso está degradado.

![Status de "Degradado" para uma máquina virtual](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Relatar um status incorreto
Se você acredita que o status de integridade atual está incorreto, você poderá nos informar, selecionando **Relatar o status de integridade incorreto**. Nos casos em que você estiver sendo afetado por um problema do Azure, recomendamos que contate o suporte do Resource Health. 

![Caixa para enviar informações sobre um status incorreto](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Informações de histórico
É possível acessar até 14 dias de histórico de integridade, na seção **Histórico de integridade** do Resource Health. 

![Lista de eventos do Resource Health nas últimas duas semanas](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Introdução
Abrir Resource Health para um recurso:
1.  Entre no Portal do Azure.
2.  Procure o recurso.
3.  No menu de recursos no painel esquerdo, selecione **Resource Health**.

![Abrir o Resource Health a partir da exibição de recursos](./media/resource-health-overview/from-resource-blade.png)

Também é possível acessar o Resource Health, selecionando **Todos os serviços** e digitando **resource health** na caixa de texto de filtro. No painel **Ajuda + suporte**, selecione [Resource Health](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Abrir o Resource Health a partir de "Todos os serviços"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Próximas etapas

Confira esses recursos para saber mais sobre o Resource Health:
-  [Tipos de recursos e verificações de integridade no Azure Resource Health](resource-health-checks-resource-types.md)
-  [Perguntas frequentes sobre o Azure Resource Health](resource-health-faq.md)




