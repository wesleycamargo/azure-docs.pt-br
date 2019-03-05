---
title: Consumir dados de monitoramento do Azure Stack | Microsoft Docs
description: Saiba mais sobre as opções para o consumo de dados de monitoramento do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2018
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: e2144f293c446a311a9c903ff620c8ef1d1d4efc
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341435"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Como consumir dados de monitoramento do Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Você pode encontrar os dados de monitoramento em um único lugar com o pipeline do Azure Monitor, assim como Monitor do Azure no Azure global. Mas nem todos os dados de monitoramento encontrados no Azure global está disponível no Azure Stack. Neste artigo, você pode encontrar um resumo das várias maneiras que você pode programaticamente ingerir dados de monitoramento do serviço.
 
## <a name="options-for-data-consumption"></a>Opções para consumo de dados

| Tipo de dados | Categoria | Serviços com suporte | Métodos de acesso |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Métricas em nível de plataforma do Azure Monitor | Métricas | [Métricas compatíveis com o Azure Monitor no Azure Stack](azure-stack-metrics-supported.md) | API REST |
| Calcular métricas de sistema operacional convidado (por exemplo, a contagem de desempenho) | Métricas | Windows e máquinas virtuais do Linux | Tabela de armazenamento ou blob:<br>Windows ou o diagnóstico do Linux do Azure <br>Hub de eventos:<br>Diagnóstico do Windows Azure |
| Métricas de armazenamento | Métricas | Armazenamento do Azure | Tabela de armazenamento:<br>Análise de Armazenamento |
| Log de atividades | Eventos | Todos os Serviços do Azure | API REST:<br>API de eventos do Azure Monitor |
| Logs do sistema operacional convidado (por exemplo, o IIS, ETW, syslogs) de computação | Eventos | Windows e máquinas virtuais do Linux | Tabela de armazenamento ou blob:<br>Windows ou o diagnóstico do Linux do Azure <br>Hub de eventos:<br>Diagnóstico do Windows Azure |
| Logs de armazenamento | Eventos | Armazenamento do Azure | Tabela de armazenamento:<br>Análise de Armazenamento |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [monitorar o Azure no Azure Stack](azure-stack-metrics-azure-data.md).
