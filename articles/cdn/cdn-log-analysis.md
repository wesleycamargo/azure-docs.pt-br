---
title: "Analisar padrões de uso da CDN do Azure | Microsoft Docs"
description: "O cliente pode habilitar a análise de log para a CDN do Azure."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: v-semcev
ms.openlocfilehash: ce9ec021250a3548e23ad87273a9225cdf700c70
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2017
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analisar os padrões de uso da CDN do Azure

Após habilitar a CDN para seu aplicativo, será possível monitorar o uso da CDN, verificar a integridade da sua entrega e resolver eventuais problemas. A CDN do Azure fornece esses recursos das seguintes maneiras: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Análise de núcleo por meio de logs de diagnósticos do Azure

A análise de núcleo está disponível para todos os pontos de extremidade da CDN que pertencem a perfis CDN Verizon (Standard e Premium) e Akamai (Standard). Os logs de diagnóstico do Azure permitem que a análise de núcleo seja exportada para o armazenamento do Azure, para os hubs de eventos ou para o OMS (Operations Management Suite) Log Analytics. O OMS Log Analytics oferece uma solução com grafos configuráveis pelo usuário e personalizáveis. Para obter mais informações, consulte [Logs de diagnóstico do Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Relatórios de núcleo da Verizon

Como usuário da CDN do Azure com um perfil padrão ou premium da Verizon, é possível exibir os relatórios de núcleo da Verizon no portal complementar Verizon. Os relatórios de núcleo da Verizon podem ser acessados por meio da opção **Gerenciar** do Portal do Azure e oferecem uma variedade de grafos e visualizações. Para obter mais informações, consulte [Relatórios de núcleo da Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Relatórios personalizados da Verizon

Como usuário da CDN do Azure com um perfil standard ou premium da Verizon, é possível exibir os relatórios de personalizados da Verizon no portal complementar Verizon. Os relatórios personalizados da Verizon podem ser acessados por meio da opção **Gerenciar** do Portal do Azure. A página de relatórios personalizados da Verizon mostra o número de ocorrências ou dados transferidos para cada borda CName que pertence a um perfil de CDN do Azure. Os dados podem ser agrupados por status de cache ou de código de resposta HTTP em qualquer período. Para obter mais informações, consulte [Relatórios personalizados da Verizon](cdn-verizon-custom-reports.md).

## <a name="verizon-premium-reports"></a>Relatórios Premium da Verizon

Com o **Azure CDN Premium da Verizon**, você também pode acessar os seguintes relatórios:
   * [Relatórios avançados de HTTP](cdn-advanced-http-reports.md)
   * [Estatísticas em tempo real](cdn-real-time-stats.md)
   * [Desempenho do nó de borda](cdn-edge-performance.md)

