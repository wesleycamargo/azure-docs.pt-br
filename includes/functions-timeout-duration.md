---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: 189683a9e98f161ce537284cc7b0349c94be2bf0
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57410903"
---
## <a name="timeout"></a>Duração de tempo limite do aplicativo de função 

A duração de tempo limite de um aplicativo de funções é definida pela propriedade functionTimeout na [host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) arquivo de projeto. A tabela a seguir mostra os valores máximo e padrão para ambos os planos e em ambas as versões de tempo de execução:

| Plano | Versão de tempo de execução | Padrão | Máximo |
|------|---------|---------|---------|
| Consumo | 1.x | 5 | 10 |
| Consumo | 2. x | 5 | 10 |
| Serviço de Aplicativo | 1.x | Ilimitado | Ilimitado |
| Serviço de Aplicativo | 2. x | 30 | Ilimitado |

> [!NOTE] 
> Independentemente da configuração de tempo limite do aplicativo de função, 230 segundos é a quantidade máxima de tempo que uma função HTTP disparada pode levar para responder a uma solicitação. Isso é devido a [padrão de tempo limite de ociosidade do balanceador de carga do Azure](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Para tempos de processamento, considere o uso de [padrão assíncrono de funções duráveis](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) ou [adiar o trabalho real e retornar uma resposta imediata](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
