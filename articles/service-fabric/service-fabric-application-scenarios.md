---
title: Design e cenários de aplicativos | Microsoft Docs
description: Visão geral das categorias de aplicativos em nuvem no Service Fabric. Discute o design de aplicativo que usa serviços com e sem monitoração de estado.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/24/2019
ms.author: atsenthi
ms.openlocfilehash: f7ad295eb30d257cd195ae02d5a5b1d85de76bda
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228491"
---
# <a name="service-fabric-application-scenarios"></a>Cenários de aplicativos do Service Fabric
O Service Fabric do Azure oferece uma plataforma confiável e flexível que permite escrever e executar muitos tipos de serviço e aplicativo de negócios. Esses aplicativos e microsserviços podem ser com ou sem monitoração de estado e têm os recursos balanceados entre máquinas virtuais para melhorar a eficiência. A arquitetura exclusiva da Malha do Serviço permite que você execute análise de dados, computação na memória, transações paralelas e processamento de eventos quase em tempo real em seus aplicativos. Você pode escalar verticalmente (realmente reduzir ou expandir) seus aplicativos com facilidade, de acordo com os requisitos de recurso em constante mudança.

Para obter diretrizes de design na criação de aplicativos, leia [arquitetura de microsserviços no Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) e [práticas recomendadas para design de aplicativo usando o Service Fabric](service-fabric-best-practices-applications.md)

A plataforma do Service Fabric é ideal para os seguintes tipos de aplicativos:

* **Coleta de dados, processamento e IoT**: Como o Service Fabric lida com grande escala e tem baixa latência por meio de seus serviços com monitoração de estado, é ideal para processamento de dados em milhões de dispositivos em que os dados para o dispositivo e a computação são colocados.

    Clientes que criaram serviços de IoT usando o Service Fabric incluem [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), e [malha sistemas](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Jogos e baseadas em sessão aplicativos interativos**: O Service Fabric é ideal se seu aplicativo requer baixa latência em leituras e gravações, como em jogos online ou mensagens instantâneas. O Service Fabric permite que você crie esses aplicativos interativos e com monitoração de estado sem ter de criar um armazenamento separado ou cache. Visite [soluções de jogos do Azure](https://azure.microsoft.com/solutions/gaming/) para obter diretrizes de design sobre [usando o Service Fabric nos serviços de jogos](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    Clientes que criaram serviços de jogos incluem [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) e [a Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Clientes que criaram sessões interativas incluem [Honeywell com Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **Análise de dados e processamento de fluxo de trabalho**: Aplicativos que devem processar de modo confiável eventos ou fluxos de dados também se beneficiam das otimizado de leituras e gravações no Service Fabric. Além disso, o Service Fabric dá suporte a pipelines de processamento do aplicativo, onde os resultados devem ser confiáveis e passado para o próximo estágio de processamento sem perda. Isso inclui sistemas transacionais e financeiros, em que as garantias de computação e consistência de dados são essenciais.

    Clientes que criaram serviços de fluxo de trabalho de negócios incluem [grupo Zeiss](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) e [a Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)

* **Computação em dados**: O Service Fabric permite que você crie aplicativos com monitoração de estado com uso intensivo de computação de dados. O Service Fabric permite a colocalização de dados e processamento (computação) em aplicativos. Normalmente, quando seu aplicativo requer acesso aos dados, latência de rede associada com uma camada de cache ou armazenamento de dados externa limita o tempo de computação. Com os serviços com monitoração de estado do Service Fabric, essa latência é eliminada, permitindo mais otimizados leituras e gravações. Por exemplo, considere um aplicativo que executa uma seleção de recomendação em tempo real para clientes com um requisito de tempo de ida e volta de menos de 100 milissegundos. As características de latência e desempenho de serviços do Service Fabric (onde a computação da seleção de recomendação é colocada com os dados e regras) fornece uma experiência responsiva ao usuário em comparação com o modelo de implementação padrão do ter que buscar os dados necessários do armazenamento remoto.

    Clientes que criaram serviços de computação incluem [resposta Solidsoft](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) e [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)

* **Serviços altamente disponíveis**: O Service Fabric fornece failover rápido criando várias réplicas de serviço secundário. Se um nó, processo ou serviço individual falhar devido a uma falha de hardware, ou outra falha, uma das réplicas secundárias é promovida a uma réplica primária com perda mínima de serviço.

* **Serviços escalonáveis**: serviços individuais podem ser particionados, permitindo que seu estado seja escalado horizontalmente no cluster. Além disso, os serviços individuais podem ser criados e removidos dinamicamente. Os serviços podem rapidamente e facilmente ser dimensionado de algumas instâncias em alguns nós para milhares de instâncias em muitos nós e, em seguida, reduzidos horizontalmente novamente, dependendo das suas necessidades de recursos. Você pode usar o Service Fabric para criar esses serviços e gerenciar o ciclo de vida completo.

## <a name="application-design-case-studies"></a>Estudos de caso de design do aplicativo
Vários estudos de caso mostrando como o Service Fabric é usado para projetar aplicativos são publicados no [histórias de clientes](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) e o [site de soluções de microsserviços](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Projetar aplicativos compostos de microsserviços com e sem monitoração de estado
A criação de aplicativos com funções de trabalho com o Serviço de Nuvem do Azure é um exemplo de serviço sem estado. Por outro lado, os microsserviços com monitoração de estado mantêm o estado autoritário além da solicitação e de sua resposta. Essa funcionalidade fornece alta disponibilidade e consistência do estado por meio de APIs simples que oferecem garantias transacionais feitas pela replicação. Os serviços com monitoração de estado do Service Fabric democratiza a alta disponibilidade, incorporando-a a todos os tipos de aplicativo, e não apenas a bancos de dados e outros armazenamentos de dados. Essa é uma progressão natural. Os aplicativos já passaram do uso de bancos de dados totalmente relacionais para bancos de dados NoSQL de alta disponibilidade. Agora os próprios aplicativos podem ter seu estado e dados "hot" gerenciados dentro deles para ganhos de desempenho adicionais sem sacrificar a disponibilidade, a consistência e a confiabilidade.

Ao criar aplicativos que consistem em microsserviços, você normalmente tem uma combinação de aplicativos Web sem estado (ASP.NET, Node.js etc.) chamando serviços de camada intermediária de negócios com e sem monitoração de estado, tudo implantado no mesmo cluster do Service Fabric usando os comandos de implantação do Service Fabric. Cada um desses serviços é independente em relação à escala, confiabilidade e uso de recursos, melhorando consideravelmente a agilidade e flexibilidade no desenvolvimento e gerenciamento de ciclo de vida.

Os microsserviços com monitoração de estado simplificam o design dos aplicativos porque eliminam a necessidade de filas e caches adicionais que têm sido tradicionalmente necessários para abordar os requisitos de disponibilidade e de latência de um aplicativo totalmente sem monitoração de estado. Como os serviços com estado naturalmente têm alta disponibilidade e baixa latência, há menos partes móveis para gerenciar em seu aplicativo como um todo. Os diagramas a seguir ilustram as diferenças entre criar um aplicativo sem monitoração de estado e um com monitoração de estado. Ao aproveitar os modelos de programação dos [Reliable Services](service-fabric-reliable-services-introduction.md) e [Reliable Actors](service-fabric-reliable-actors-introduction.md), os serviços com estado reduzem a complexidade do aplicativo, ao mesmo tempo que alcançam uma alta taxa de transferência e baixa latência.

## <a name="an-application-built-using-stateless-services"></a>Um aplicativo criado usando serviços sem monitoração de estado
![Aplicativo usando serviço sem estado][Image1]

## <a name="an-application-built-using-stateful-services"></a>Um aplicativo criado usando os serviços com monitoração de estado
![Aplicativo usando serviço sem estado][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [estudos de caso de cliente](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc)
* Saiba mais sobre [padrões e cenários](service-fabric-patterns-and-scenarios.md)

* Comece a criar serviços com e sem estado com o modelos de programação dos [Reliable Services](service-fabric-reliable-services-quick-start.md) e [Reliable Actors](service-fabric-reliable-actors-get-started.md) do Service Fabric.
* Visite o Centro de arquitetura do Azure para obter orientação sobre [criando microsserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* Vá para [práticas recomendadas de aplicativo do Azure Service Fabric e o cluster](service-fabric-best-practices-overview.md) para diretrizes de design do aplicativo.

* Confira também os seguintes tópicos:
  * [Fale-me sobre os microsserviços](service-fabric-overview-microservices.md)
  * [Definir e gerenciar o estado do serviço](service-fabric-concepts-state.md)
  * [Disponibilidade dos serviços de malha do serviço](service-fabric-availability-services.md)
  * [Dimensionar serviços do Service Fabric](service-fabric-concepts-scalability.md)
  * [Particionar serviços do Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
