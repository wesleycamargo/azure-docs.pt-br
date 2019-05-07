---
title: Recursos - Azure Functions de visualização de funções duráveis
description: Saiba mais sobre recursos de visualização para funções duráveis.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.date: 04/23/2019
ms.author: azfuncdf
ms.openlocfilehash: 6b3b49049ea1ed36a08fad9619183017b0f07d99
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077734"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Versão prévia 2.0 de funções durável (Azure Functions)

As *Funções Duráveis* são uma extensão do [Azure Functions](../functions-overview.md) e do [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) que permitem que você escreva funções com estado em um ambiente sem servidor. A extensão gerencia estado, pontos de verificação e reinicializações para você. Se você já não estiver familiarizado com as funções duráveis, consulte o [documentação de visão geral](durable-functions-overview.md).

As funções duráveis é um recurso de GA (disponibilidade geral) do Azure Functions, mas também contém vários sub-recursos que estão atualmente em visualização pública. Este artigo descreve os recursos de visualização recém-lançado e apresenta detalhes sobre como eles funcionam e como você pode começar a usá-los.

> [!NOTE]
> Estes recursos de visualização são parte de uma versão 2.0 de funções duráveis, o que é atualmente uma **versão alfa qualidade** com várias alterações significativas. O Azure Functions durável compilações do pacote de extensão podem ser encontrada em nuget.org com versões na forma de **2.0.0-alpha**. Essas compilações não são adequadas para quaisquer cargas de trabalho de produção e versões posteriores podem conter alterações significativas adicionais.

## <a name="breaking-changes"></a>Alterações de última hora

Várias alterações significativas são apresentadas no 2.0 de funções duráveis. Os aplicativos existentes não devem ser compatíveis com 2.0 de funções duráveis sem alterações de código. Esta seção lista algumas das alterações:

### <a name="dropping-net-framework-support"></a>Removendo o suporte do .NET Framework

Suporte para o .NET Framework (e, portanto, o Functions 1.0) foi descartado para 2.0 de funções duráveis. É o principal motivo habilitar o não-Windows colaboradores para facilmente criar e testar as alterações feitas por eles para funções duráveis de plataformas macOS e Linux. O motivo secundário é ajudar a encorajar os desenvolvedores a se mover para a versão mais recente do tempo de execução do Azure Functions.

### <a name="hostjson-schema"></a>Esquema de host. JSON

O trecho a seguir mostra o novo esquema para o host. JSON. A principal mudança nos conhecer o novo `"storageProvider"` seção e o `"azureStorage"` seção abaixo dela. Essa alteração foi feita para apoiar [alternativos de provedores de armazenamento](durable-functions-preview.md#alternate-storage-providers).

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "traceInputAndOutputs": <bool?>,
      "eventGridTopicEndpoint": <string?>,
      "eventGridKeySettingName": <string?>,
      "eventGridPublishRetryCount": <string?>,
      "eventGridPublishRetryInterval": <hh:mm:ss?>,
      "eventGridPublishRetryHttpStatus": <int[]?>,
      "eventgridPublishEventTypes": <string[]?>,
      "customLifeCycleNotificationHelperType"
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "logReplayEvents": <bool?>
  }
}
```

Como durável Functions 2.0 continua a se estabilizar, mais alterações serão apresentaremos o `durableTask` seção host. JSON. Para obter mais informações sobre essas alterações, consulte [esse problema de GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Alterações de interface pública

Os diversos objetos de "contexto" compatíveis com as funções duráveis tinham classes base abstratas destinadas para uso em testes de unidade. Como parte do 2.0 de funções duráveis, essas classes base abstratas foram substituídos com interfaces. Código de função que usa os tipos concretos diretamente não são afetados.

A tabela a seguir representa as principais alterações:

| Tipo antigo | Novo tipo |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

No caso em que uma classe base abstrata contido métodos virtuais, esses métodos virtuais foram substituídos por métodos de extensão definidos em `DurableContextExtensions`.

## <a name="entity-functions"></a>Funções da entidade

Definem as funções da entidade operações para ler e atualizar pequenos pedaços de estado, conhecido como *entidades duráveis*. Como funções de orquestrador, funções de entidade são funções com um tipo especial de gatilho, *disparador entidade*. Diferentemente das funções de orquestrador, funções da entidade não tem quaisquer restrições de código específico. Funções de entidade também gerenciam estado explicitamente em vez de implicitamente que representa o estado por meio do fluxo de controle.

O código a seguir é um exemplo de uma função de entidade simples que define uma *contador* entidade. A função define três operações, `add`, `remove`, e `reset`, cada do que atualizar um valor inteiro, `currentValue`.

```csharp
public static async Task Counter(
    [EntityTrigger(EntityName = "Counter")] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    int operand = ctx.GetInput<int>();

    switch (ctx.OperationName)
    {
        case "add":
            currentValue += operand;
            break;
        case "subtract":
            currentValue -= operand;
            break;
        case "reset":
            await SendResetNotificationAsync();
            currentValue = 0;
            break;
    }

    ctx.SetState(currentValue);
}
```

Entidade *instâncias* são acessadas por meio de um identificador exclusivo, o *ID da entidade*. Uma ID de entidade é simplesmente um par de cadeias de caracteres que identifica exclusivamente uma instância de entidade. Ele consiste em:

1. uma **nome da entidade**: um nome que identifica o tipo da entidade (por exemplo, "Counter")
2. uma **chave de entidade**: uma cadeia de caracteres que identifica exclusivamente a entidade entre todas as outras entidades do mesmo nome (por exemplo, um GUID)

Por exemplo, uma *contador* função de entidade pode ser usada para manter a pontuação em um jogo online. Cada instância do jogo terá uma ID de entidade exclusivo, como `@Counter@Game1`, `@Counter@Game2`e assim por diante.

### <a name="comparison-with-virtual-actors"></a>Comparação com atores virtuais

O design de entidades durável é amplamente influenciado pelo [modelo de ator](https://en.wikipedia.org/wiki/Actor_model). Se você já estiver familiarizado com os atores, os conceitos por trás de entidades duráveis devem ser familiares para você. Em particular, as entidades duráveis são semelhantes às [atores virtuais](https://research.microsoft.com/en-us/projects/orleans/) de várias maneiras:

* Entidades duráveis são endereçáveis por meio de um *ID da entidade*.
* Entidade durável operações são executadas em série, um de cada vez, para evitar condições de corrida.
* Entidades duráveis são criadas automaticamente quando eles são chamados ou sinalizados.
* Quando a não execução de operações, entidades duráveis são silenciosamente descarregadas da memória.

Há algumas diferenças importantes, no entanto, que vale a pena observar:

* Entidades duráveis são modeladas como funções puras. Esse design é diferente da maioria das estruturas orientada a objeto que representam atores usando o suporte de idioma específico para classes, propriedades e métodos.
* Entidades duráveis priorizar *durabilidade* pela *latência*e, portanto, pode não ser adequado para aplicativos com requisitos estritos de latência.
* As mensagens enviadas entre as entidades são entregues com confiança e em ordem.
* Entidades duráveis podem ser usadas junto com orquestrações duráveis e podem servir como bloqueios distribuídos, que são descritos neste artigo.
* Padrões de solicitação/resposta nas entidades estão limitados a orquestrações. Para a comunicação de entidade de entidade, apenas a mensagens unidirecionais (também conhecido como "sinalização") são permitidas, como no modelo de ator original. Esse comportamento impede deadlocks distribuídos.

### <a name="durable-entity-apis"></a>APIs de entidade duráveis

Suporte ao Entity envolve várias APIs. Por exemplo, há uma nova API para definir funções de entidade, como mostrado acima, que especificam o que deve acontecer quando uma operação é invocada em uma entidade. Além disso, as APIs existentes para clientes e orquestrações foram atualizadas com a nova funcionalidade para interação com entidades.

### <a name="implementing-entity-operations"></a>Implementar operações de entidade

A execução de uma operação em uma entidade pode chamar esses membros no objeto de contexto (`IDurableEntityContext` no .NET):

* **OperationName**: obtém o nome da operação.
* **GetInput\<T >**: obtém a entrada para a operação.
* **GetState\<T >**: obtém o estado atual da entidade.
* **SetState**: atualiza o estado da entidade.
* **SignalEntity**: envia uma mensagem unidirecional para uma entidade.
* **Self**: obtém a ID da entidade.
* **Retornar**: retorna um valor para o cliente ou uma orquestração que chamou a operação.
* **IsNewlyConstructed**: retorna `true` se a entidade não existia antes da operação.
* **DestructOnExit**: exclui a entidade depois de concluir a operação.

As operações são menos restritas que orquestrações:

* Operações podem chamar e/s externos, usando as APIs síncronas ou assíncronas (é recomendável usar apenas as assíncronas).
* Operações podem ser não determinístico. Por exemplo, é seguro chamar `DateTime.UtcNow`, `Guid.NewGuid()` ou `new Random()`.

### <a name="accessing-entities-from-clients"></a>Acessar as entidades de clientes

Entidades duráveis podem ser chamadas de funções comuns por meio de `orchestrationClient` associação (`IDurableOrchestrationClient` no .NET). Há suporte para os seguintes métodos:

* **ReadEntityStateAsync\<T >**: lê o estado de uma entidade.
* **SignalEntityAsync**: envia uma mensagem unidirecional para uma entidade e aguarda até que ele seja enfileirado.

Esses métodos priorizar desempenho sobre a consistência: `ReadEntityStateAsync` pode retornar um valor obsoleto, e `SignalEntityAsync` pode retornar antes da operação for concluída. Por outro lado, é altamente consistente chamando entidades de orquestrações (conforme descrito a seguir).

### <a name="accessing-entities-from-orchestrations"></a>Acessar as entidades de orquestrações

Orquestrações podem acessar entidades usando o objeto de contexto. Eles podem escolher entre comunicação unidirecional (disparar e esquecer) e a comunicação bidirecional (solicitação e resposta). Os métodos respectivos são

* **SignalEntity**: envia uma mensagem unidirecional para uma entidade.
* **CallEntityAsync**: envia uma mensagem para uma entidade e aguarda uma resposta que indica se a operação foi concluída.
* **CallEntityAsync\<T >**: envia uma mensagem a uma entidade e aguarda uma resposta que contém um resultado do tipo T.

Ao usar a comunicação bidirecional, todas as exceções geradas durante a execução da operação também são transmitidas novamente para a orquestração chamada e gerada novamente. Por outro lado, ao usar disparar e esquecer, as exceções não são observadas.

### <a name="locking-entities-from-orchestrations"></a>Entidades de bloqueio de orquestrações

Orquestrações podem bloquear entidades. Esse recurso fornece uma maneira simples de evitar corridas indesejadas usando *seções críticas*.

O objeto de contexto fornece os seguintes métodos:

* **LockAsync**: adquire bloqueios em uma ou mais entidades.
* **IsLocked**: retorna true se estiver atualmente em uma seção crítica, false caso contrário.

A seção crítica termina e todos os bloqueios são liberados, quando termina a orquestração. No .NET, `LockAsync` retorna um `IDisposable` termina a seção crítica quando descartado, o que pode ser usada junto com um `using` cláusula para obter uma representação sintática da seção crítica.

Por exemplo, considere uma orquestração que precisa para testar se dois jogadores estão disponíveis e, em seguida, atribuí-los para um jogo. Essa tarefa pode ser implementada usando uma seção crítica da seguinte maneira:

```csharp

EntityId player1 = /* ... */;
EntityId player2 = /* ... */;

using (await ctx.LockAsync(player1, player2))
{
    bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
    bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

    if (available1 && available2)
    {
        Guid gameId = ctx.NewGuid();

        await ctx.CallEntityAsync(player1, "assign-game", gameId);
        await ctx.CallEntityAsync(player2, "assign-game", gameId);
    }
}
```

Dentro da seção crítica, ambas as entidades de player são bloqueadas, que significa que elas não estiverem em execução a todas as operações que não sejam aqueles que são chamados de dentro da seção crítica). Esse comportamento impede corridas com operações conflitantes, como players de que está sendo atribuídos a outro jogo ou assinatura desativada.

Podemos impõe várias restrições em seções críticas como pode ser usado. Essas restrições servem para evitar deadlocks e reentrância.

* Seções críticas não podem ser aninhadas.
* Seções críticas não é possível criar suborchestrations.
* Seções críticas podem chamar apenas as entidades que eles bloqueados.
* Seções críticas não é possível chamar a mesma entidade usando várias chamadas paralelas.
* Seções críticas podem sinalizar apenas as entidades que eles não bloqueados.

## <a name="alternate-storage-providers"></a>Provedores de armazenamento alternativo

O Framework de tarefa durável dá suporte a vários provedores de armazenamento atualmente, incluindo [armazenamento do Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [do barramento de serviço do Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), um [emulador na memória](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)e um experimental [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) provedor. No entanto, até agora, a extensão de tarefa durável do Azure Functions só tem suporte do provedor de armazenamento do Azure. A partir do 2.0 de funções duráveis, suporte para provedores de armazenamento alternativo está sendo adicionada, começando com o provedor do Redis.

> [!NOTE]
> Durável Functions 2.0 dá suporte apenas a provedores de .NET Standard 2.0 compatível. No momento da escrita, o provedor de barramento de serviço do Azure não oferece suporte a .NET Standard 2.0 e, portanto, não está disponível como um provedor de armazenamento alternativo.

### <a name="emulator"></a>Emulador

O [DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) provedor é uma memória local, o provedor de armazenamento não durável adequado para cenários de teste locais. Ele pode ser configurado usando o seguinte mínimo **host. JSON** esquema:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (experimental)

O [DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) provedor persiste todos os estados de orquestração para um cluster Redis configurado.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

O `connectionStringName` deve fazer referência ao nome de uma variável de ambiente ou configuração de aplicativo. Essa variável de ambiente ou configuração de aplicativo deve conter um valor de cadeia de caracteres de conexão do Redis na forma de *porta do servidor:*. Por exemplo, `localhost:6379` para se conectar a um cluster local do Redis.

> [!NOTE]
> O provedor de Redis está atualmente experimental e só oferece suporte a aplicativos de função em execução em um único nó.