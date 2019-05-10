---
title: Azure Service Fabric application práticas recomendadas de design | Microsoft Docs
description: Práticas recomendadas para o desenvolvimento de aplicativos do Service Fabric.
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/26/2019
ms.author: msfussell
ms.openlocfilehash: 55f043effc7cdb102acea856e89c58f660d0cde5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237741"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric application práticas recomendadas de design

Este artigo fornece diretrizes de práticas recomendadas para a criação de aplicativos e serviços no Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Familiarize-se com o Service Fabric
* [Portanto, você deseja saber mais sobre o Service Fabric?](service-fabric-content-roadmap.md)
* Leia sobre [cenários de aplicativo do Service Fabric](service-fabric-application-scenarios.md)
* Em seguida, compreender as opções de modelo de programação com [visão geral do modelo de programação do Service Fabric](service-fabric-choose-framework.md)



## <a name="application-design-guidance"></a>Diretrizes de design de aplicativo
Familiarize-se com o [arquitetura geral](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) de um aplicativo do Service Fabric e seu [considerações de design](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Escolha um gateway de API
Use um serviço de gateway de API que se comunica com os serviços de back-end que podem ser escalados horizontalmente. Os serviços de gateway de API mais comuns usados são:

- [Gerenciamento de API do Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), que é [integrado com o Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)
- [O IoT Hub](https://docs.microsoft.com/azure/iot-hub/) ou [Hubs de eventos](https://docs.microsoft.com/azure/event-hubs/) usando o [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) para ler de partições do Hub de eventos
- [Proxy reverso do Træfik](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/) usando o [provedor do Azure Service Fabric](https://docs.traefik.io/configuration/backends/servicefabric/)
- [O Gateway de aplicativo do Azure](https://docs.microsoft.com/azure/application-gateway/) Observação: isso não é integrado diretamente com o Service Fabric e gerenciamento de API do Azure normalmente é uma opção preferencial
- Criar seu próprio [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) gateway de aplicativo web

### <a name="choose-stateless-services"></a>Escolha os serviços sem monitoração de estado
É recomendável que você comece pela criação de serviços sem monitoração de estado usando sempre [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) armazenar o estado em um banco de dados do Azure, Azure CosmosDB ou armazenamento do Azure. Ter estado externalizado é a abordagem mais familiar para a maioria dos desenvolvedores e permite que você também tirar proveito dos recursos de consulta no repositório.  

### <a name="when-to-choose-stateful-services"></a>Quando escolher os serviços com estado
Quando você tiver um cenário de baixa latência e precisa manter os dados próximos a computação, considere os serviços com estado. Alguns exemplos incluem dispositivos do IoT gêmeo digital, o estado do jogo, o estado de sessão, cache de dados de um banco de dados e fluxos de trabalho para rastrear chamadas para outros serviços de longa execução.

Decida o período de retenção de dados:

- Dados armazenados em cache - é usar o cache em que a latência em armazenamentos externos é um problema. Usar um serviço com estado, como seus próprios dados de cache ou considere usar o [SoCreate service-fabric--cache distribuído software livre](https://github.com/SoCreate/service-fabric-distributed-cache). Nesse cenário, você pode perder todos os dados no cache e não importa.
- Dados de limite de tempo - você precisa manter dados próximos para calcular a latência por um período de tempo, mas esse tipo de dados pode sejam perdidas em uma *desastres* cenário. Por exemplo, em muitas soluções de IoT dados precisam estar próximo a computação, por exemplo calcular a temperatura média nos últimos dias, no entanto, se esses dados são perdidos, em seguida, os específicos de pontos de dados registradas não é tão importante. Também neste cenário não normalmente faz sobre o backup dos pontos de dados individuais, somente dos valores de média de computadas que são gravados no armazenamento externo periodicamente.  
- Dados de longo prazo - coleções confiáveis podem armazenar seus dados permanentemente. No entanto, nesse caso, você precisará [preparar para recuperação de desastres](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery) incluindo [configurar políticas de backup periódicas](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) para seus clusters. Na verdade, você configurar o que acontece se o cluster for destruído em um cenário de desastre, em que você precisa criar um novo cluster e como implantar novas instâncias de aplicativo e recuperar a partir do backup mais recente.

Economizar custos e melhorar a disponibilidade:
- Você pode reduzir os custos com serviços com estado, pois não incorrem em custos de acesso e transações de dados do armazenamento remoto e não é necessário usar outro serviço, como Redis do Azure.
- Usando os serviços com monitoração de estado principalmente para armazenamento e não para a computação é caro e não é recomendado. Considere a possibilidade de serviços com monitoração de estado como a computação com barato armazenamento local.
- Removendo dependências em outros serviços, você pode melhorar a disponibilidade do serviço. Ter o estado gerenciado com alta disponibilidade no cluster, você isola de outros tempos de inatividade do serviço ou problemas de latência.

## <a name="how-to-properly-work-with-reliable-services"></a>Como trabalhar corretamente com os Reliable Services
Reliable Services permitem que você crie facilmente serviços com e sem monitoração de estado. Leia o [Introdução aos Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)
- Sempre respeitam a [token de cancelamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) na `RunAsync()` método para serviços com e sem estado e o `ChangeRole()` método para serviços com monitoração de estado. Sem isso, o Service Fabric não sabe se seu serviço pode ser fechado. Por exemplo, não respeitar o token de cancelamento pode resultar em tempos de atualização de aplicativo muito mais.
-   Abertura e fechamento [ouvintes de comunicação](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) de maneira oportuna e honrar os tokens de cancelamento.
-   Nunca misture código de sincronização com o código assíncrono. Por exemplo, não use `.GetAwaiter().GetResult()` em suas chamadas de async; ele precisa ser assíncrono *totalmente* por meio da pilha de chamadas.

## <a name="how-to-properly-work-with-reliable-actors"></a>Como trabalhar corretamente com Reliable Actors
Reliable Actors permite que você crie facilmente atores com monitoração de estado, virtuais. Leia o [Introdução aos Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)

- Considere seriamente o uso de mensagens entre seus atores para dimensionar o aplicativo pub/sub. Por exemplo, o [livre SoCreate pub/sub](https://service-fabric-pub-sub.socreate.it/) ou [do barramento de serviço do Azure](https://docs.microsoft.com/azure/service-bus/).
- Verifique o estado do ator como [granular quanto possível](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Gerenciar o [ciclo de vida do ator](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Exclua atores se você não vai usá-los novamente. Isso é especialmente verdadeiro ao usar o [VolatileState provedor](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication) como todo o estado é armazenado na memória.
- Devido a seus [por sua vez com base em simultaneidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency) atores são melhor usados como objetos independentes. Não criar grafos de chamadas de método síncrono de ator várias (cada um deles provavelmente se torna uma chamada de rede separado) ou têm solicitações de ator circular; Esses serão afetar significativamente o desempenho e escala.
- Não misture códigos de sincronização com o código assíncrono; ele precisa ser assíncrono todo o caminho, para evitar problemas de desempenho.
- Não faça chamadas de longa execução nos atores, ela bloqueará outras chamadas para o mesmo ator, devido à simultaneidade baseada em turno.
- Se comunicando-se com outros serviços usando o [comunicação remota do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) e você estiver criando um `ServiceProxyFactory`, em seguida, crie a fábrica no [serviço de ator](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) nível e *não*no nível de ator.


## <a name="application-diagnostics"></a>Diagnóstico de aplicativos
- Seja criterioso ao adicionando [log de aplicativo](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) em chamadas de serviço. Ele ajuda no diagnóstico de cenários em que os serviços chamam uns aos outros. Por exemplo, quando A -> B -> C -> D chamada poderia falhar em qualquer lugar; Se não for suficiente registro em log, é difícil de diagnosticar. Se os serviços estão fazendo muito por causa de volumes de chamada, pelo menos não se esqueça de registrar erros e avisos.

## <a name="iot-and-messaging-applications"></a>Aplicativos de mensagens e IoT
Ao ler mensagens do [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub/) ou [Hubs de eventos](https://docs.microsoft.com/azure/event-hubs/) usar [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) que se integra com o Service Fabric Reliable Services para manter o partições de estado de leitura do Hub de eventos e envia novas mensagens para seus serviços por meio de `IEventProcessor::ProcessEventsAsync()` método.


## <a name="design-guidance-on-azure"></a>Diretrizes de design no Azure
* Visite o [Centro de arquitetura do Azure](https://docs.microsoft.com/azure/architecture/microservices/) para obter diretrizes de design sobre [criando microsserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/)

* Visite [Introdução ao Azure para jogos](https://docs.microsoft.com/gaming/azure/) para obter diretrizes de design sobre [usando o Service Fabric nos serviços de jogos](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
