---
title: Perguntas frequentes sobre o Azure Resource Health | Microsoft Docs
description: "Visão geral do Azure Resource Health"
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: resource-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 06/01/2016
ms.author: BernardoAMunoz
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 570688ae363e223d792bec8e6b13d4ec50412130
ms.lasthandoff: 04/04/2017


---

# <a name="azure-resource-health-faq"></a>Perguntas frequentes sobre o Azure Resource Health
Conheça as respostas para as perguntas comuns sobre o Azure Resource Health.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
* [O que é a integridade de recursos do Azure?](#what-is-azure-resource-health)
* [Qual é o objetivo do Resource Health?](#what-is-the-resource-health-intended-for)
* [Quais verificações de integridade são realizadas pelo Resource Health?](#what-health-checks-are-performed-by-resource-health)
* [O que significa cada status de integridade?](#what-does-each-of-the-health-status-mean)
* [O que significa o status desconhecido? Há algo errado com meu recurso?](#what-does-the-unknown-status-mean-is-something-wrong-with-my-resource)
* [Como posso obter ajuda para um recurso que não está disponível?](#how-can-i-get-help-for-a-resource-that-is-unavailable)
* [O Resource Health diferencia a indisponibilidade causada por problemas de plataforma de algo que eu fiz?](#does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did)
* [Posso integrar o Resource Health com minhas ferramentas de monitoramento?](#can-i-integrate-resource-health-with-my-monitoring-tools)
* [Onde posso encontrar o Resource Health?](#where-do-i-find-resource-health)
* [O Resource Health está disponível para todos os tipos de recursos?](#is-resource-health-available-for-all-resource-types)
* [O que devo fazer se meu recurso estiver aparecendo disponível, mas eu acreditar que não esteja?](#what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not)
* [O Resource Health está disponível para todas as regiões do Azure?](#is-resource-health-available-for-all-azure-regions)
* [De que forma o Resource Health é diferente do Painel de Integridade do Serviço ou das notificações de serviço do portal do Azure?](#how-is-resource-health-different-from-the-service-health-dashboard-or-the-azure-portal-service-notifications)
* [Preciso ativar o Resource Health para cada recurso?](#do-i-need-to-activate-resource-health-for-each-resource)
* [Precisamos habilitar o Resource Health para minha organização?](#do-we-need-to-enable-resource-health-for-my-organization)
* [O Resource Health está disponível gratuitamente?](#is-resource-health-available-free-of-charge)
* [Quais são as recomendações fornecidas pelo Resource Health?](#what-are-the-recommendations-that-resource-health-provides)


## <a name="what-is-azure-resource-health"></a>O que é o Azure Resource Health?
O Resource Health ajuda a diagnosticar e obter suporte quando um problema do Azure afeta seus recursos. Ele informa sobre a integridade atual e anterior de seus recursos e ajuda a reduzir os problemas. O Resource Health fornece suporte técnico quando você precisa de ajuda com problemas de serviço do Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>Qual é o objetivo do Resource Health?
Após a detecção de um problema com um recurso, o Resource Health pode ajudar você a diagnosticar a causa raiz. Ele fornece ajuda para atenuar o problema e o suporte técnico quando você precisar de mais ajuda com problemas de serviço do Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Quais verificações de integridade são realizadas pelo Resource Health?
O Resource Health executa várias verificações com base no [tipo de recurso](resource-health-checks-resource-types.md). Essas verificações foram projetadas para implementar três tipos de problemas: 
1. Eventos não planejados, por exemplo, uma reinicialização inesperada do host
2. Eventos planejados, como atualizações agendadas do sistema operacional host
3. Eventos disparados por ações do usuário, por exemplo, um usuário que reinicializa uma máquina virtual

## <a name="what-does-each-of-the-health-status-mean"></a>O que significa cada status de integridade?
Há três status de integridade diferentes:
1. Disponível: não há problemas conhecidos na plataforma do Azure que poderiam afetar esse recurso
2. Não disponível: o Resource Health detectou problemas que estão afetando o recurso
3. Desconhecido: o Resource Health pode determinar a integridade de um recurso por ter parado de receber informações sobre ele. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>O que significa o status desconhecido? Há algo errado com meu recurso?
O status de integridade é definido como desconhecido quando o Resource Health para de receber informações sobre um recurso específico. Embora esse status não seja uma indicação definitiva do estado do recurso, nos casos em que você estiver enfrentando problemas, isso pode indicar um problema com o Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Como posso obter ajuda para um recurso que não está disponível?
Você pode enviar uma solicitação de suporte na folha do Resource Health. Você não precisa de um contrato de suporte com a Microsoft para abrir uma solicitação quando o recurso não estiver disponível devido a eventos da plataforma.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>O Resource Health diferencia a indisponibilidade causada por problemas de plataforma de algo que eu fiz?
Sim, quando um recurso não estiver disponível, o Resource Health identificará a causa raiz dentro de uma destas categorias: 
1.    Ação iniciada pelo usuário
2.    Evento planejado 
3.    Evento não planejado

No portal, as ações iniciadas pelo usuário aparecem usando um ícone de notificação azul, enquanto eventos planejados e não planejados são mostrados com um ícone de aviso vermelho. Mais detalhes serão fornecidos na [Visão geral do Resource Health](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Posso integrar o Resource Health com minhas ferramentas de monitoramento?
O Resource Health é um serviço projetado para ajudar você a diagnosticar e atenuar problemas de serviço do Azure que afetam seus recursos. Embora seja possível usar a API do Resource Health para obter programaticamente o status de integridade, recomendamos o uso de métricas para monitorar seus recursos. Quando um problema é detectado, o Resource Health ajuda você a determinar a causa raiz e orienta você por meio de ações para resolvê-la. Visite [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) para saber mais sobre como você pode usar métricas para verificar seus recursos.

## <a name="where-do-i-find-resource-health"></a>Onde posso encontrar o Resource Health?
Depois de fazer logon no portal do Azure, há várias maneiras de acessar o Resource Health:
1. Navegue até seu recurso. No painel de navegação à esquerda, clique em **Resource Health**.
2. Acesse a folha do Azure Monitor.  No painel de navegação à esquerda, clique em **Resource Health**.
3. Abra a folha **Ajuda + Suporte** clicando no ponto de interrogação no canto superior direito e selecionando **Ajuda + Suporte**. Quando a folha abrir, clique em **Resource Health**

Você também pode usar a API do Resource Health para obter informações sobre a integridade de seus recursos.

## <a name="is-resource-health-available-for-all-resource-types"></a>O Resource Health está disponível para todos os tipos de recursos?
A lista de verificações de integridade e os tipos de recursos com suporte por meio do Resource Health podem ser encontrados [aqui](resource-health-checks-resource-types.md)

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>O que devo fazer se meu recurso estiver aparecendo disponível, mas eu acreditar que não esteja?
Ao verificar a integridade de um recurso, imediatamente abaixo do status de integridade você pode clicar em **Relatar o status de integridade incorreto**. Antes de enviar o relatório, você tem a opção de fornecer detalhes adicionais sobre o motivo de você achar que o status de integridade atual está incorreto.

## <a name="is-resource-health-available-for-all-azure-regions"></a>O Resource Health está disponível para todas as regiões do Azure? 
O Resource Health está disponível em todas as geografias do Azure, exceto nas seguintes regiões:
* Gov. dos EUA – Virgínia
* Gov do Iowa nos EUA
* DoD do Leste dos EUA
* DoD Central dos EUA
* Alemanha Central
* Nordeste da Alemanha
* Leste da China
* Norte da China

## <a name="how-is-resource-health-different-from-the-service-health-dashboard-or-the-azure-portal-service-notifications"></a>De que forma o Resource Health é diferente do Painel de Integridade do Serviço ou das notificações de serviço do portal do Azure?
As informações fornecidas pelo Resource Health são mais específicas do que aquelas fornecidas pelo Painel de Integridade de Serviço do Azure.

Enquanto o [Status Azure](https://status.azure.com) e as notificações de serviço do portal informam você sobre problemas de serviço que afetam um amplo conjunto de clientes (por exemplo, uma região do Azure), o Resource Health expõe eventos mais granulares que são relevantes apenas ao recurso específico. Por exemplo, se um host for reinicializado inesperadamente, o Resource Health alertará somente os clientes cujas máquinas virtuais estavam em execução nesse host.

É importante observar que para fornecer visibilidade completa sobre eventos que afetam seus recursos, o Resource Health também expõe eventos publicados em notificações de Serviço e o Painel de Integridade do Serviço.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Preciso ativar o Resource Health para cada recurso?
Não, as informações de integridade estão disponíveis para todos os tipos de recursos disponíveis por meio do Resource Health. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Precisamos habilitar o Resource Health para minha organização?
Não.  O Azure Resource Health é acessível dentro do portal do Azure sem qualquer requisito de instalação.

## <a name="is-resource-health-available-free-of-charge"></a>O Resource Health está disponível gratuitamente?
Sim.  O Azure Resource Health está disponível gratuitamente.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Quais são as recomendações fornecidas pelo Resource Health?
Com base no status de integridade, o Resource Health fornece recomendações com o objetivo de reduzir o tempo gasto com a solução de problemas. Para os recursos disponíveis, as recomendações se concentram em como resolver os problemas mais comuns que os clientes encontram. Se o recurso não estiver disponível devido a um evento não planejado do Azure, o foco estará no auxílio durante e após o processo de recuperação. 

## <a name="next-steps"></a>Próximas etapas

Confira estes recursos para saber mais sobre o Resource Health:
-  [Visão geral do Azure Resource Health](Resource-health-overview.md)
-  [Tipos de recurso e verificações de integridade disponíveis por meio da integridade de recursos do Azure](resource-health-checks-resource-types.md)

