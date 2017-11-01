---
title: "Hubs de tarefas nas Funções Duráveis – Azure"
description: "Saiba o que é um hub de tarefas na extensão de Funções Duráveis do Azure Functions. Saiba como configurar hubs de tarefas."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: b241bad7b0060551eba5e78efbb1b729bf5d0098
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2017
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Hubs de tarefas nas Funções Duráveis (Azure Functions)

Um *hub de tarefas* nas [Funções Duráveis](durable-functions-overview.md) é um contêiner lógico dos recursos do Armazenamento do Azure usado para orquestrações. Funções de orquestrador e atividade só podem interagir entre si quando pertencem ao mesmo hub de tarefas.

Cada aplicativo de funções tem um hub de tarefas separado. Se vários aplicativos de funções compartilham uma conta de armazenamento, ela conterá vários hubs de tarefas. O diagrama a seguir ilustra um hub de tarefas por aplicativo de funções em contas de armazenamento compartilhadas e dedicadas.

![Diagrama mostrando contas de armazenamento compartilhadas e dedicadas.](media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Recursos de Armazenamento do Azure

Um hub de tarefas é composto pelos seguinte recursos de armazenamento: 

* Uma ou mais filas de controle.
* Uma fila de item de trabalho.
* Uma tabela de histórico.
* Um contêiner de armazenamento que contém um ou mais blobs de concessão.

Todos esses recursos são criados automaticamente na conta padrão do Armazenamento do Azure quando funções de atividade ou orquestrador são executadas ou estão agendadas para execução. O artigo [Desempenho e Escala](durable-functions-perf-and-scale.md) explica como esses recursos são usados.

## <a name="task-hub-names"></a>Nomes de hub de tarefas

Os hubs de tarefas são identificados por um nome declarado no arquivo *host.json*, conforme mostrado no exemplo a seguir:

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Nomes de hubs de tarefas devem começar com uma letra e devem ser compostos somente por letras e números. Se não for especificado, o nome padrão será **DurableFunctionsHub**.

> [!NOTE]
> O nome é o que diferencia um hub de tarefas de outro quando há vários em uma conta de armazenamento compartilhada. Se você tiver vários aplicativos de funções compartilhando uma conta de armazenamento, será necessário configurar nomes diferentes para cada hub de tarefas nos arquivos *host.json*.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como lidar com o controle de versão](durable-functions-versioning.md)
