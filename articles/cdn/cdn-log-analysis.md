---
title: Analisar padrões de uso da CDN do Azure | Microsoft Docs
description: Este artigo descreve os diferentes tipos de relatórios de análise disponíveis para produtos da CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: magattus
ms.openlocfilehash: 45b3698dd77bda815218b43405d64819c3e4789f
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091259"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analisar os padrões de uso da CDN do Azure

Após habilitar a CDN para seu aplicativo, será possível monitorar o uso da CDN, verificar a integridade da sua entrega e resolver eventuais problemas. A CDN do Azure fornece esses recursos das seguintes maneiras: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Análise de núcleo por meio de logs de diagnósticos do Azure

A análise de núcleo está disponível para pontos de extremidade CDN em todos os tipos de preço. Os logs de diagnósticos do Azure permitem que as análises principais sejam exportadas para o armazenamento do Azure, para os hubs de eventos ou para o Azure Log Analytics. O Azure Log Analytics oferece uma solução com grafos configuráveis pelo usuário e personalizáveis. Para obter mais informações sobre logs de diagnóstico do Azure, consulte [Logs de diagnóstico do Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Relatórios de núcleo da Verizon

Como um usuário da CDN do Azure com um perfil **CDN Standard do Azure da Verizon** ou **CDN Premium do Azure da Verizon**, você pode exibir os relatórios principais da Verizon no portal suplementar da Verizon. Os relatórios de núcleo da Verizon podem ser acessados por meio da opção **Gerenciar** do Portal do Azure e oferecem uma variedade de grafos e visualizações. Para obter mais informações, consulte [Relatórios de núcleo da Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Relatórios personalizados da Verizon

Como um usuário da CDN do Azure com um perfil **CDN Standard do Azure da Verizon** ou **CDN Premium do Azure da Verizon**, você pode exibir os relatórios personalizados da Verizon no portal suplementar da Verizon. Os relatórios personalizados da Verizon podem ser acessados por meio da opção **Gerenciar** do Portal do Azure. A página de relatórios personalizados da Verizon mostra o número de ocorrências ou dados transferidos para cada borda CName que pertence a um perfil de CDN do Azure. Os dados podem ser agrupados por status de cache ou de código de resposta HTTP em qualquer período. Para obter mais informações, consulte [Relatórios personalizados da Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Relatórios de CDN Premium do Azure da Verizon

Com o **Azure CDN Premium da Verizon**, você também pode acessar os seguintes relatórios:
   * [Relatórios avançados de HTTP](cdn-advanced-http-reports.md)
   * [Estatísticas em tempo real](cdn-real-time-stats.md)
   * [Desempenho do nó de borda](cdn-edge-performance.md)

