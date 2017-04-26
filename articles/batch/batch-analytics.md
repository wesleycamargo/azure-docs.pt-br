---
title: "Análise de lote – Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 2fda4d9c-f782-4088-9320-656b450e3100
caps.latest.revision: 7
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 4b73637e0ec6798b701d5a13f96c504bcbb14cc7
ms.lasthandoff: 04/13/2017

---
# <a name="batch-analytics"></a>Análise de lote
Os tópicos na Análise de lote contêm informações de referência para os eventos e alertas disponíveis para recursos de serviço em lotes.

Consulte [Registro em log de diagnóstico de Lote do Azure](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) para obter mais informações sobre como habilitar e consumir logs de diagnóstico de lote.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

O serviço Lote do Azure emite os eventos de log de diagnóstico a seguir durante o tempo de vida de determinados recursos do lote.

**Eventos do Log de Serviço**
* [Criação de pool](batch-pool-create-event.md)
* [Início de exclusão de pool](batch-pool-delete-start-event.md)
* [Conclusão da exclusão de pool](batch-pool-delete-complete-event.md)
* [Início de redimensionamento de pool](batch-pool-resize-start-event.md)
* [Conclusão de redimensionamento de pool](batch-pool-resize-complete-event.md)
* [Início da tarefa](batch-task-start-event.md)
* [Conclusão da tarefa](batch-task-complete-event.md)
* [Falha da tarefa](batch-task-fail-event.md)
