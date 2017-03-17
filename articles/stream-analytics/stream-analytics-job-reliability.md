---
title: "Evitar interrupções de serviço com trabalhos do Stream Analytics do Azure | Microsoft Docs"
description: "Orientação sobre como tornar resilientes suas atualizações de trabalhos do Stream Analytics."
services: stream-analytics
documentationCenter: 
authors: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/27/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: a2892343432f7dced535efb3917d915736580dfb
ms.openlocfilehash: cf8eba0f68e1e803026079f02b91f1bbaec189da
ms.lasthandoff: 03/01/2017

---

# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantir a confiabilidade do trabalho do Stream Analytics durante atualizações do serviço

A capacidade de introduzir novos aprimoramentos e funcionalidades de serviço em um ritmo acelerado faz parte do serviço completamente gerenciado. Como resultado, o Stream Analytics pode ter uma implantação semanal (ou mais frequente) da atualização do serviço. Não importa a quantidade de testes realizados, ainda existe o risco de um trabalho atualmente em execução ser interrompido devido à introdução de um bug. Para clientes que executam trabalhos de processamento de streaming críticos, esses riscos precisam ser evitados. Há um mecanismo que os clientes podem usar para reduzir esse risco. É o modelo de **[região emparelhada](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** do Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Como as regiões emparelhadas do Azure podem resolver essa questão?

O Stream Analytics garante que os trabalhos em regiões emparelhadas sejam atualizados em lotes separados. Como resultado, há um intervalo de tempo suficiente entre as atualizações para identificar possíveis bugs que causam interrupções e para corrigi-los.

_Com exceção da Índia central_ (cuja região emparelhada, sul da Índia, não tem a presença do Stream Analytics), não ocorrerá a implantação de uma atualização simultânea do Stream Analytics em um conjunto de regiões emparelhadas. Implantações em várias regiões **no mesmo grupo de** podem ocorrer **ao mesmo tempo**.

Consulte o gráfico a seguir para obter a lista de grupos de emparelhamento:

Regiões do Grupo A |  | Regiões do Grupo B
------- | ------- | -------
Leste do Japão | emparelhada com | Oeste do Japão
Norte da Europa |  | Europa Ocidental
Centro dos EUA |  | Leste dos EUA&2;
Ásia Oriental |  | Sudeste da Ásia
Centro-Norte dos EUA |  | Centro-Sul dos Estados Unidos
Leste da Austrália |  | Sudeste da Austrália
Leste dos EUA |  | Oeste dos EUA
Sul do Brasil |  | Centro-Sul dos Estados Unidos
Norte da China |  | Leste da China
Nordeste da Alemanha |  | Alemanha Central

Os clientes são aconselhados a implantar trabalhos idênticos para ambas as regiões emparelhadas. Além do monitoramento interno do Stream Analytics, os clientes também devem monitorar os trabalhos como se **ambos** fossem trabalhos de produção. Se uma interrupção for identificada como sendo o resultado da atualização do serviço do Stream Analytics, escalone adequadamente e realize o failover de consumidores downstream para que a saída do trabalho seja íntegra. O escalonamento ao suporte impedirá a região emparelhada de ser afetada pela nova implantação e manterá a integridade dos trabalhos emparelhados.

