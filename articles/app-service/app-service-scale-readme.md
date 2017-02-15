---
title: "Serviço de Aplicativo do Azure: dimensionamento de aplicativos do Serviço de Aplicativo"
description: "Saiba as vantagens e desvantagens do dimensionamento do aplicativo no Serviço de Aplicativo."
keywords: "serviço de aplicativo, serviço de aplicativo do azure, escala, escalonável, plano de serviço de aplicativo, custo de serviço de aplicativo"
services: app-service
documentationcenter: 
author: btardif
manager: wpickett
editor: 
ms.assetid: f403c971-4450-432b-8cea-3eeb426c0147
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ec043a9e01aca2e843e50cef8b90328ba1939ea5


---
# <a name="azure-app-service-scaling-app-service-applications"></a>Serviço de Aplicativo do Azure: dimensionamento de aplicativos do Serviço de Aplicativo
Aplicativos hospedados no Serviço de Aplicativo do Azure podem [chegar a uma escala enorme](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/).
No entanto, o dimensionamento de um aplicativo é um problema complexo que não tem uma solução que se aplique a todas as situações. Para dimensionar corretamente o aplicativo, há três áreas principais que contribuirão para o sucesso do seu aplicativo:

1. Entender a arquitetura do aplicativo e seus pontos fracos.
   * Seu aplicativo é com estado? Sem estado?
   * O que são todos os componentes do seu aplicativo?
     * Onde estão os gargalos no aplicativo?
   * Quando a carga é aplicada ao seu aplicativo, o que será interrompido primeiro?
2. Entender os requisitos de desempenho e carga esperados.
   * O aplicativo precisa atender mil usuários? Ou um milhão?
   * O tráfego virá de um único local geográfico ou é global?
   * Há variações sazonais? Picos de tráfego?
   * Com que rapidez o aplicativo deve responder? 1 segundo? 1 milissegundo?
3. Entender e aproveitar corretamente a plataforma de hospedagem de seu aplicativo.
   * Quais recursos devo aproveitar para alcançar meus objetivos de escala?

Esta seção o ajudará a entender todos os fatores e ajudá-lo a conceber uma estratégia que aproveita os recursos do Serviço de Aplicativo necessários para atingir suas metas de escalabilidade do dispositivo.

[!INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]




<!--HONumber=Nov16_HO3-->


