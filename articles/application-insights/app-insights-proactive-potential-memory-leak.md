---
title: "Detecção Inteligente - Potencial perda de memória detectada pelo Azure Application Insights | Microsoft Docs"
description: "Monitorar aplicativos com o Azure Application Insights para possíveis perdas de memória."
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
ms.openlocfilehash: e98caaa387418d746905990436b69925a591b260
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="memory-leak-detection-preview"></a>Detecção de perda de memória (visualização)

O Application Insights analisa automaticamente o consumo de memória de cada processo em seu aplicativo e avisa sobre possíveis perdas de memória ou maior consumo de memória.

Este recurso não exige nenhuma configuração especial, diferente de [configurar contadores de desempenho](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) para seu aplicativo. Ele está ativo quando seu aplicativo gera telemetria de contadores de desempenho de memória suficiente (por exemplo, Bytes particulares).


## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando eu receberia este tipo de notificação de detecção inteligente?
Uma notificação típica seguirá um aumento consistente no consumo de memória por um longo período de tempo (algumas horas), em um ou mais processos e/ou em uma ou mais máquinas, que fazem parte do seu aplicativo.
Algoritmos de aprendizado de máquina são usados para detectar o maior consumo de memória que corresponde a um padrão de uma perda de memória, em contraste com maior consumo de memória devido a aumentar naturalmente o uso do aplicativo.

## <a name="does-my-app-definitely-have-a-problem"></a>Meu aplicativo definitivamente tem um problema?
Não, uma notificação não significa que seu aplicativo definitivamente tem um problema. Embora os padrões de perda de memória geralmente indiquem um problema de aplicativo, esses padrões podem ser típicos para seu processo específico, ou podem ter uma justificativa de negócios natural e podem ser ignorados.

## <a name="how-do-i-fix-it"></a>Como corrigi-la?
As notificações incluem informações de diagnóstico para oferecer suporte no processo de análise de diagnóstico:
1. **Triagem.** A notificação mostra o aumento na quantidade de memória (em GB) e o intervalo de tempo em que a memória aumentou. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Escopo.** Quantas máquinas apresentaram o padrão de perda de memória? Quantas exceções foram acionadas durante a potencial perda de memória? Essas informações podem ser obtidas na notificação.
3. **Diagnosticar.** A detecção contém o padrão de perda de memória, mostrando o consumo de memória do processo ao longo do tempo. Você também pode usar o relatórios de vinculação para dar suporte a informações e itens relacionados para ajudá-lo a diagnosticar o problema.
