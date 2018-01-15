---
title: "Visão geral do ciclo de vida do Azure Service Fabric Reliable Services | Microsoft Docs"
description: Saiba mais sobre os diferentes eventos de ciclo de vida no Service Fabric Reliable Services
services: Service-Fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.openlocfilehash: 6945c3333b43877a62158e2a367b1aa3dd615129
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="reliable-services-lifecycle-overview"></a>Visão geral do ciclo de vida do Reliable Services
> [!div class="op_single_selector"]
> * [C# em Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java no Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Ao pensar sobre os ciclos de vida do Reliable Services, os conceitos básicos são os mais importantes. Em geral:

* Durante a inicialização
  * Serviços são construídos
  * Eles têm a oportunidade de construir e retornar zero ou mais ouvintes
  * Todos os ouvintes retornados são abertos, permitindo a comunicação com o serviço
  * O método runAsync do serviço é chamado, possibilitando um serviço de longa execução ou trabalho em segundo plano
* Durante o desligamento
  * O token de cancelamento passado para o runAsync é cancelado e os ouvintes são fechados
  * Assim que estiver concluído, o objeto do serviço será destruído

Há detalhes sobre a ordem exata desses eventos. Em especial, a ordem de eventos pode mudar um pouco dependendo se o Reliable Service tem estado ou não. Além disso, para serviços com estado, temos de lidar com o cenário de troca primário. Durante esta sequência, a função da Primária é transferida para outra réplica (ou retorna) sem o desligamento do serviço. Por fim, temos de pensar sobre as condições de erro ou falha.

## <a name="stateless-service-startup"></a>Inicialização de serviço sem estado
O ciclo de vida de um serviço sem estado é bastante simples. Aqui está a ordem de eventos:

1. O Serviço foi construído
2. Em seguida, ocorrem duas coisas em paralelo:
    - `StatelessService.createServiceInstanceListeners()` é invocado e todos os ouvintes retornados serão Abertos (`CommunicationListener.openAsync()` é chamado em cada ouvinte)
    - O método runAsync (`StatelessService.runAsync()`) do serviço é chamado
3. Se existir, o próprio método onOpenAsync do serviço será chamado (especificamente, `StatelessService.onOpenAsync()` será chamado. Esta é uma substituição incomum, mas está disponível).

É importante observar que não há nenhuma ordem entre as chamadas para criar e abrir os ouvintes e o runAsync. Os ouvintes podem ser abertos antes de o runAsync ser iniciado. Da mesma forma, o runAsync pode acabar sendo invocado antes que os ouvintes de comunicação sejam abertos ou até mesmo antes de serem construídos. Se nenhuma sincronização for necessária, isso será deixado como um exercício para o implementador. Soluções comuns:

* Algumas vezes os ouvintes não funcionam até que outras informações sejam criadas ou algum trabalho seja realizado. Para serviços sem monitoração de estado, esse trabalho geralmente pode ser realizado no construtor do serviço, durante a chamada `createServiceInstanceListeners()` ou como parte da construção do ouvinte propriamente dito.
* Às vezes, o código em runAsync não é iniciado até que os ouvintes estejam abertos. Nesse caso, coordenação adicional será necessária. Uma solução comum é um sinalizador dentro dos ouvintes indicando quando eles foram concluídos, que é verificado no runAsync antes de passar para o trabalho real.

## <a name="stateless-service-shutdown"></a>Desligamento de serviço sem estado
Ao desligar um serviço sem estado, o mesmo padrão é seguido, porém na ordem inversa:

1. Em paralelo
    - Todos os ouvintes abertos são Fechados (`CommunicationListener.closeAsync()` é chamado em cada ouvinte)
    - O token de cancelamento passado para `runAsync()` é cancelado (verificar a propriedade `isCancelled` do token de cancelamento retornará true e, se chamado, o método `throwIfCancellationRequested` do token retornará um `CancellationException`)
2. Uma vez que `closeAsync()` é concluído em cada ouvinte e `runAsync()` também é finalizado, o método `StatelessService.onCloseAsync()` do serviço é chamado, se existir (novamente, essa é uma substituição incomum).
3. Depois da conclusão de `StatelessService.onCloseAsync()`, o objeto de serviço é destruído

## <a name="stateful-service-startup"></a>Inicialização de serviço com estado
Serviços com estado têm um padrão semelhante aos serviços sem monitoração de estado, com algumas alterações. Para inicializar um serviço com estado, a ordem dos eventos é a seguinte:

1. O Serviço foi construído.
2. `StatefulServiceBase.onOpenAsync()` é chamado. Essa chamada não costuma ser substituída no serviço.
3. As seguintes ações ocorrem paralelamente:
    - `StatefulServiceBase.createServiceReplicaListeners()` é invocado. 
      - Se o serviço for Primário, todos os ouvintes retornados serão abertos. `CommunicationListener.openAsync()` é chamado em cada ouvinte.
      - Se o serviço for um serviço secundário, somente ouvintes marcados como `listenOnSecondary = true` serão abertos. Ter ouvintes abertos em secundários é menos comum.
    - Se o serviço for Primário no momento, o método `StatefulServiceBase.runAsync()` do serviço será chamado.
4. Depois que todas as chamadas `openAsync()` do ouvinte da réplica forem concluídas e `runAsync()` for chamado, `StatefulServiceBase.onChangeRoleAsync()` será chamado. Essa chamada não costuma ser substituída no serviço.

Semelhante aos serviços sem estado, não há coordenação entre a ordem em que os ouvintes são criados e abertos e quando **runAsync** é chamado. Se você precisar de coordenação, as soluções são muito parecidas. Há um caso adicional para o serviço com estado. Digamos que as chamadas que chegam aos ouvintes de comunicação precisem de informações mantidas dentro de algumas [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md). Como os ouvintes de comunicação podem ser abertos antes que as coleções confiáveis fiquem legíveis ou graváveis e antes de **runAsync** ser iniciado, é necessário realizar a coordenação. A solução mais simples e mais comum é os ouvintes de comunicação retornarem um código de erro que o cliente usa para repetir a solicitação.

## <a name="stateful-service-shutdown"></a>Desligamento de serviço com estado
Da mesma forma que os serviços sem monitoração de estado, os eventos de ciclo de vida durante o desligamento são os mesmos que durante a inicialização, porém invertidos. Quando um serviço com estado está sendo desligado, ocorrem os seguintes eventos:

1. Em paralelo:
    - Todos os ouvintes abertos são fechados. `CommunicationListener.closeAsync()` é chamado em cada ouvinte.
    - O token de cancelamento passado para `runAsync()` é cancelado. Uma chamada para o método `isCancelled()` do token de cancelamento retorna true e, se chamado, o método `throwIfCancellationRequested()` do token lança um `OperationCanceledException`.
2. Depois que `closeAsync()` terminar em cada ouvinte e `runAsync()` também terminar, o serviço `StatefulServiceBase.onChangeRoleAsync()` será chamado. Essa chamada não costuma ser substituída no serviço.

   > [!NOTE]  
   > A necessidade de aguardar **runAsync** terminar existirá apenas se essa réplica for primária.

3. Após o método `StatefulServiceBase.onChangeRoleAsync()` terminar, o método `StatefulServiceBase.onCloseAsync()` é chamado. Essa chamada não é uma substituição comum, mas está disponível.
3. Depois que `StatefulServiceBase.onCloseAsync()` for concluído, o objeto de serviço será destruído.

## <a name="stateful-service-primary-swaps"></a>Trocas Primárias de serviço com estado
Enquanto um serviço com estado está em execução, somente as réplicas primárias dos serviços com estado terão seus ouvintes de comunicação abertos e seu método **runAsync** chamado. Réplicas secundárias são construídas, mas não veem mais chamadas. Durante a execução de um serviço com estado, a réplica atualmente primária pode alterar. O que isso significa em termos dos eventos do ciclo de vida que uma réplica pode ver? O comportamento que uma réplica com estado vê depende se ela é a réplica que está sendo rebaixada ou promovida durante a troca.

### <a name="for-the-primary-thats-demoted"></a>Para a primária que é rebaixada
Para a réplica primária é rebaixada, o Service Fabric precisa que essa réplica interrompa o processamento de mensagens e feche qualquer trabalho em segundo plano que esteja executando. Como resultado, esta etapa tem uma aparência igual à de quando o serviço está desligado. Uma diferença é que o Serviço não é destruído nem fechado, pois ele permanece como Secundário. As seguintes APIs são chamadas:

1. Em paralelo:
    - Todos os ouvintes abertos são fechados. `CommunicationListener.closeAsync()` é chamado em cada ouvinte.
    - O token de cancelamento passado para `runAsync()` é cancelado. Uma verificação do método `isCancelled()` do token de cancelamento retorna true e, se chamado, o método `throwIfCancellationRequested()` do token lança um `OperationCanceledException`.
2. Depois que `closeAsync()` terminar em cada ouvinte e `runAsync()` também terminar, o serviço `StatefulServiceBase.onChangeRoleAsync()` será chamado. Essa chamada não costuma ser substituída no serviço.

### <a name="for-the-secondary-thats-promoted"></a>Para a secundária que é promovida
Da mesma forma, o Service Fabric precisa que a réplica Secundária que é promovida escute mensagens na transmissão e inicie quaisquer tarefas em segundo plano que precise concluir. Assim, esse processo tem aparência similar à de quando o serviço é criado, exceto que a réplica em si já existe. As seguintes APIs são chamadas:

1. Em paralelo:
    - `StatefulServiceBase.createServiceReplicaListeners()` é invocado e todos os ouvintes retornados são abertos. `CommunicationListener.openAsync()` é chamado em cada ouvinte.
    - O método `StatefulServiceBase.runAsync()` do serviço é chamado.
2. Depois que todas as chamadas `openAsync()` do ouvinte da réplica forem concluídas e `runAsync()` for chamado, `StatefulServiceBase.onChangeRoleAsync()` será chamado. Essa chamada não costuma ser substituída no serviço.


### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Problemas comuns durante o desligamento do serviço com estado e o rebaixamento da Primária
O Service Fabric altera o Primário de um serviço com estado por vários motivos. Os mais comuns são [rebalanceamento do cluster](service-fabric-cluster-resource-manager-balancing.md) e [upgrade de aplicativo](service-fabric-application-upgrade.md). Durante essas operações (bem como durante o desligamento normal do serviço, como você veria se o serviço fosse excluído), é importante que o serviço respeite o `cancellationToken`. 

Os serviços que não tratam o cancelamento corretamente podem enfrentar vários problemas. Essas operações são lentas porque o Service Fabric aguarda até que os serviços parem normalmente. Em última instância, isso leva a falhas de upgrade que atingem um tempo limite e são revertidas. Falha em cumprir o token de cancelamento também pode causar clusters desequilibrados. Os clusters tornam-se desequilibrados porque os nós ficam ativos, mas os serviços não podem ser reequilibrados porque demora muito para movê-los para outro lugar. 

Uma vez que os serviços têm estado, também é provável que eles usem [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md). No Service Fabric, quando um Primário é rebaixado, uma das primeiras coisas que acontece é que o acesso de gravação ao estado subjacente é revogado. Isso leva a um segundo conjunto de problemas que pode afetar o ciclo de vida do serviço. As coleções retornam exceções que se baseiam no tempo e em se a réplica está sendo movida ou desligada. Essas exceções devem ser tratadas corretamente. As exceções geradas pelo Service Fabric se classificam nas categorias permanentes [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) e transitórias [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception._fabric_transient_exception). As exceções permanentes devem ser registradas e geradas, enquanto as exceções transitórias podem ser recuperadas com base em alguma lógica de repetição.

O tratamento das exceções que resultam do uso de `ReliableCollections` em conjunto com eventos de ciclo de vida do serviço é uma importante etapa do teste e da validação de um Serviço Confiável. A recomendação é sempre executar seu serviço sob carga durante a execução de upgrades e [fazer o teste de caos](service-fabric-controlled-chaos.md) antes de implantar em produção. Essas etapas básicas ajudam a garantir que o serviço seja implementado corretamente, além de tratar os eventos do ciclo de vida corretamente.

## <a name="notes-on-service-lifecycle"></a>Observações sobre o ciclo de vida do serviço
* Tanto o método `runAsync()` quanto as chamadas `createServiceInstanceListeners/createServiceReplicaListeners` são opcionais. Um serviço pode ter um deles, ambos ou nenhum. Por exemplo, se o serviço fizer todo seu trabalho em resposta a chamadas do usuário, não será necessário implementar `runAsync()`. Apenas os ouvintes de comunicação e seu código associado são necessários. Da mesma forma, criar e retornar ouvintes de comunicação é opcional, pois o serviço pode ter apenas trabalho em segundo plano e só precisar implementar `runAsync()`
* Isso é válido para um serviço concluir `runAsync()` com êxito e retornar dele. Isso não é considerado uma condição de falha e representa o trabalho em segundo plano após o término do serviço. Para serviços confiáveis com estado, `runAsync()` seria chamado novamente se o serviço tiver sido rebaixado de primário e promovido novamente para primário.
* Se um serviço sai de `runAsync()` gerando uma exceção inesperada, há uma falha e o objeto de serviço é desligado e relatado um erro de integridade.
* Embora não haja um limite de tempo para o retorno desses métodos, você perde imediatamente a capacidade de gravar e, portanto, não pode concluir qualquer trabalho real. Recomendamos que você retorne o mais rápido possível após o recebimento da solicitação de cancelamento. Se o serviço não responder a essas chamadas à API dentro de um período razoável, o Service Fabric poderá forçar o encerramento do serviço. Geralmente isso ocorre apenas durante atualizações de aplicativo ou quando um serviço está sendo excluído. Esse tempo limite é de 15 minutos por padrão.
* Falhas no caminho de `onCloseAsync()` resultam em uma chamada de `onAbort()`, que é uma oportunidade de melhor esforço de última chance para o serviço ser limpo e liberar quaisquer recursos ocupados.

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Reliable Services](service-fabric-reliable-services-introduction.md)
* [Início Rápido dos Serviços Confiáveis](service-fabric-reliable-services-quick-start-java.md)
* [Uso avançado de Reliable Services](service-fabric-reliable-services-advanced-usage.md)
