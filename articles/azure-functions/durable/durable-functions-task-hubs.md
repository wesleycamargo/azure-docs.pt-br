---
title: Hubs de tarefas nas Funções Duráveis – Azure
description: Saiba o que é um hub de tarefas na extensão de Funções Duráveis do Azure Functions. Saiba como configurar hubs de tarefas.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 7a6346e594c5a7cc4cf02f3ea658aac4977e641a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637451"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Hubs de tarefas nas Funções Duráveis (Azure Functions)

Um *hub de tarefas* nas [Funções Duráveis](durable-functions-overview.md) é um contêiner lógico dos recursos do Armazenamento do Azure usado para orquestrações. Funções de orquestrador e atividade só podem interagir entre si quando pertencem ao mesmo hub de tarefas.

Cada aplicativo de funções tem um hub de tarefas separado. Se vários aplicativos de funções compartilham uma conta de armazenamento, ela conterá vários hubs de tarefas. O diagrama a seguir ilustra um hub de tarefas por aplicativo de funções em contas de armazenamento compartilhadas e dedicadas.

![Diagrama mostrando contas de armazenamento compartilhadas e dedicadas.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Recursos de Armazenamento do Azure

Um hub de tarefas é composto pelos seguinte recursos de armazenamento: 

* Uma ou mais filas de controle.
* Uma fila de item de trabalho.
* Uma tabela de histórico.
* Uma tabela de instâncias.
* Um contêiner de armazenamento que contém um ou mais blobs de concessão.

Todos esses recursos são criados automaticamente na conta padrão do Armazenamento do Azure quando funções de atividade ou orquestrador são executadas ou estão agendadas para execução. O artigo [Desempenho e Escala](durable-functions-perf-and-scale.md) explica como esses recursos são usados.

## <a name="task-hub-names"></a>Nomes de hub de tarefas

Os hubs de tarefas são identificados por um nome declarado no arquivo *host.json*, conforme mostrado no exemplo a seguir:

### <a name="hostjson-functions-v1"></a>host.json (Funções v1)
```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```
### <a name="hostjson-functions-v2"></a>host.json (funções v2)
```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "HubName": "MyTaskHub"
    }
  }
}
```
Os hubs de tarefas também podem ser configurados usando as configurações do aplicativo, conforme mostrado no seguinte arquivo de exemplo *host.json*:

### <a name="hostjson-functions-v1"></a>host.json (Funções v1)
```json
{
  "durableTask": {
    "HubName": "%MyTaskHub%"
  }
}
```
### <a name="hostjson-functions-v2"></a>host.json (funções v2)
```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "HubName": "%MyTaskHub%"
    }
  }
}
```
O nome do hub de tarefas será definido com o valor da configuração do aplicativo `MyTaskHub`. O seguinte `local.settings.json` demonstra como definir a `MyTaskHub`configurar como `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" :  "samplehubname" 
  }
}
```

Nomes de hubs de tarefas devem começar com uma letra e devem ser compostos somente por letras e números. Se não for especificado, o nome padrão será **DurableFunctionsHub**.

> [!NOTE]
> O nome é o que diferencia um hub de tarefas de outro quando há vários em uma conta de armazenamento compartilhada. Se você tiver vários aplicativos de funções compartilhando uma conta de armazenamento, será necessário configurar nomes diferentes para cada hub de tarefas nos arquivos *host.json*.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como lidar com o controle de versão](durable-functions-versioning.md)
