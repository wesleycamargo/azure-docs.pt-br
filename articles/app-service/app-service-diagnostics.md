---
title: "Visão geral de diagnóstico do Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Saiba como solucionar problemas com seu aplicativo Web com o diagnóstico do Serviço de Aplicativo."
keywords: "serviço de aplicativo, serviço de aplicativo do azure, diagnósticos, suporte, aplicativo web, solução de problemas, autoajuda"
services: app-service
documentationcenter: 
author: jen7714
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.openlocfilehash: 9526817ce7969edcd5e9c56ec153bb4e3ebaa501
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2018
---
# <a name="azure-app-service-diagnostics-overview"></a>Visão geral de diagnóstico do Serviço de Aplicativo do Azure 

Quando você estiver executando um aplicativo Web, você deseja estar preparado para os problemas que podem surgir, desde os erros 500 até os usuários informando que seu site está inoperante. O diagnóstico do Serviço de Aplicativo é uma experiência interativa e inteligente para ajudá-lo a solucionar problemas de seu aplicativo Web sem configuração necessária. Quando você tiver problemas com seu aplicativo Web, o diagnóstico do Serviço de Aplicativo apontará o que está errado para guiar você até as informações adequadas para a forma mais fácil e rápida de solucionar problemas. 
 
Embora essa experiência seja útil quando você estiver tendo problemas com seu aplicativo Web nas últimas 24 horas, todos os gráficos de diagnóstico estarão disponíveis para você analisar o tempo todo. Ferramentas e links de solução de problemas adicionais para documentação e fóruns úteis localizados na coluna à direita.

## <a name="open-app-service-diagnostics"></a>Abra o diagnóstico do Serviço de Aplicativo

Para acessar o diagnóstico do Serviço de Aplicativo, navegue até seu aplicativo Web do Serviço de Aplicativo no [portal do Azure](https://portal.azure.com). 

No painel de navegação esquerdo, clique em **Diagnosticar e resolver problemas**.

![Home page](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Verificação de integridade

Se você não souber o que há de errado com seu aplicativo Web ou não souber por onde começar a solucionar seus problemas, a verificação de integridade é um bom lugar para começar. A verificação de integridade analisará seus aplicativos Web para lhe dar uma visão geral rápida e interativa que indique se o que está íntegro e o que está errado, informando a você onde pesquisar para investigar o problema. Sua interface inteligente e interativa fornece orientações durante o processo de solução de problemas.  

![Verificação de integridade](./media/app-service-diagnostics/HealthCheckup2.png)

Se for detectado um problema com uma categoria de problema específico nas últimas 24 horas, você poderá exibir o relatório de diagnóstico completo e o diagnóstico do Serviço de Aplicativo pode solicitar que você exiba mais conselhos de solução de problemas e as próximas etapas para uma experiência mais guiada.

![Solução de problemas e próximas etapas](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Atalhos de bloco

Se você souber exatamente qual tipo de solução de problemas de informações está procurando, os atalhos de bloco levarão você diretamente para o relatório de diagnóstico completo da categoria de problema em que você está interessado. Em comparação à verificação de integridade, os atalhos de bloco são mais diretos, mas sã uma maneira menos guiada de acessar suas métricas de diagnóstico.  

![Atalhos de bloco](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Relatório de diagnóstico

Se você deseja obter mais informações depois de executar uma [verificação de integridade](#health-checkup) ou se tiver clicado em um dos [atalhos de bloco](#tile-shortcuts), o relatório de diagnóstico completo mostrará as métricas gráficas relevantes das últimas 24 horas. Se seu aplicativo tiver qualquer tempo de inatividade, ele será representado por uma barra laranja abaixo da linha do tempo. Você pode selecionar um dos tempos de inatividade para obter observações analisadas sobre o tempo de inatividade e as soluções sugeridas. 

![Relatório de diagnóstico](./media/app-service-diagnostics/DiagnosticReport5.png)

