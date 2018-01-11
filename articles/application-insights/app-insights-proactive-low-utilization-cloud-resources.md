---
title: "Detecção Inteligente - Baixa utilização de recursos de nuvem detectada pelo Azure Application Insights | Microsoft Docs"
description: "Monitorar aplicativos com o Azure Application Insights para baixa utilização de recursos de nuvem."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 4c852d07d771a1eef0e6c2e4ef27cd36f31d8ddd
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2017
---
# <a name="low-utilization-of-cloud-resources-preview"></a>Baixa utilização de recursos de nuvem (visualização)

O Application Insights analisa automaticamente o consumo de CPU de cada instância de função em seu aplicativo e detecta instâncias com baixa utilização da CPU. Essa detecção permite diminuir os recursos do Azure e reduzir os custos, diminuindo o número de instâncias de função que cada função utiliza ou reduzindo o número de funções.

Este recurso não exige nenhuma configuração especial, diferente de [configurar contadores de desempenho](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters) para seu aplicativo. Ele está ativo quando seu aplicativo gera telemetria de contador de desempenho da CPU suficiente (% tempo do processador).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando eu receberia este tipo de notificação de detecção inteligente?
Uma notificação comum ocorre quando muitas de suas instâncias de função web/trabalho exibem a baixa utilização da CPU.

## <a name="does-my-app-definitely-consume-too-many-resources"></a>Meu aplicativo realmente consume muitos recursos?
Não, uma notificação não significa que seu aplicativo realmente consuma muitos recursos. Embora esses padrões de baixa utilização da CPU geralmente indicam que o consumo de recursos pode ser diminuído, esse comportamento pode ser comum para sua função específica, ou pode ter uma justificativa de negócios natural e pode ser ignorado. Por exemplo, é possível que várias instâncias são necessárias para outros recursos, como memória/rede e não da CPU.

## <a name="how-do-i-fix-it"></a>Como corrigi-la?
As notificações incluem informações de diagnóstico para oferecer suporte no processo de diagnóstico:
1. **Triagem.** A notificação mostra as funções em seu aplicativo que exibem a baixa utilização da CPU. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Escopo.** Quantas funções exibiram baixa utilização da CPU, e quantas instâncias de cada função utilizam pouca CPU? Essas informações podem ser obtidas na notificação.
3. **Diagnosticar.** A detecção contém a porcentagem da CPU utilizada, mostrando a utilização de CPU de cada instância ao longo do tempo. Você também pode usar o relatórios de vinculação para dar suporte a informações, como percentuais da utilização da CPU e itens relacionados para ajudá-lo a diagnosticar o problema.
