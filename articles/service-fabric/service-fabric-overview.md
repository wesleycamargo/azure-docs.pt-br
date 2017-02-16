---
title: "Visão geral da Service Fabric | Microsoft Docs"
description: "Uma visão geral do Service Fabric no qual os aplicativos são compostos por muitos microsserviços para fornecer dimensionamento e resiliência. O Service Fabric é uma plataforma de sistemas distribuídos usada para compilar aplicativos escalonáveis, confiáveis e facilmente gerenciados para a nuvem."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/22/2016
ms.author: mfussell
translationtype: Human Translation
ms.sourcegitcommit: 6b3c49f6721ffc149bbb7f0433ff67f529abf938
ms.openlocfilehash: c61541d65fe2f84bdd629d8b8d8c7ce00a049133


---
# <a name="overview-of-azure-service-fabric"></a>Visão geral do Azure Service Fabric
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microsserviços escalonáveis e confiáveis. O Service Fabric resolve os desafios significativos de desenvolvimento e gerenciamento de aplicativos em nuvem. Desenvolvedores e administradores podem evitar problemas complexos de infraestrutura e se concentrarem na implementação de cargas de trabalho essenciais e exigentes que são escalonáveis, confiáveis e gerenciáveis. O Service Fabric representa a plataforma de middleware de última geração para criação e gerenciamento de aplicativos escalonáveis de nuvem de camada 1 e nível corporativo.

Este breve vídeo do Channel9 apresenta o Service Fabric e os microsserviços: <center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-overview/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

Esse vídeo da Microsoft Virtual Academy descreve os principais conceitos do Service Fabric:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-overview/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>


## <a name="applications-composed-of-microservices"></a>Aplicativos compostos por microsserviços
O Service Fabric permite compilar e gerenciar aplicativos escalonáveis e confiáveis compostos por microsserviços executados a uma densidade muito alta em um pool compartilhado de computadores, conhecido como um cluster. Ele fornece um tempo de execução sofisticado para compilar microsserviços distribuídos e escalonáveis com e sem estado. Também fornece recursos de gerenciamento de aplicativos abrangente para provisionar, implantar, monitorar, atualizar/aplicar patch e excluir aplicativos implantados.

Por que uma abordagem de microsserviços é importante? Os dois motivos principais são:

* Você pode dimensionar diferentes partes de seu aplicativo dependendo das necessidades do aplicativo.
* As equipes de desenvolvimento podem ser mais ágeis, uma vez que distribuem alterações e, portanto, fornecem recursos aos clientes de modo mais rápido e frequente.

O Service Fabric é a tecnologia de muitos serviços atuais da Microsoft, como Banco de Dados SQL do Azure, Azure DocumentDB, Cortana, Microsoft Power BI, Microsoft Intune, Hubs de Eventos do Azure, Hub IoT do Azure, Skype for Business e muitos serviços principais do Azure.

O Service Fabric é projetado para criar serviços em nuvem nativos que podem começar pequenos, conforme a necessidade, e expandirem-se para escala em massa com centenas ou milhares de computadores.

Os serviços em escala da Internet de hoje são criados por microsserviços. Exemplos de microsserviços são gateways de protocolo, perfis de usuário, carrinhos de compra, processamento de inventário, filas e caches. O Service Fabric é uma plataforma de microsserviços que fornece a cada microsserviço um nome exclusivo que pode ser com ou sem estado.

O Service Fabric fornece recursos abrangentes de gerenciamento de ciclo de vida e tempo de execução para aplicativos compostos por esses microsserviços. Ela hospeda microsserviços em contêineres que são implantados e ativados no cluster da Malha do Serviço. Passar de máquinas virtuais para contêineres possibilita um aumento de ordem de magnitude na densidade. De modo similar, outra ordem de magnitude na densidade torna-se possível passando de contêineres para microsserviços. Por exemplo, um único cluster para um Banco de Dados SQL do Azure engloba centenas de computadores que executam dezenas de milhares de contêineres que hospedam um total de centenas de milhares de bancos de dados. Cada banco de dados é um microsserviço com estado do Service Fabric. O mesmo é verdadeiro para outros serviços mencionados anteriormente, motivo pelo qual o termo *hiperescala* é usado para descrever os recursos do Service Fabric. Se os contêineres fornecem alta densidade, os microsserviços fornecem hiperescala.

Para saber mais sobre a abordagem de microsserviços, leia [Por que usar uma abordagem de microsserviço para construir aplicativos?](service-fabric-overview-microservices.md).

## <a name="container-deployment-and-orchestration"></a>Orquestração e implantação de contêiner
O Service Fabric é um [orquestrador](service-fabric-cluster-resource-manager-introduction.md) de microsserviços em um cluster de computadores. Os microsserviços podem ser desenvolvidos de várias maneiras usando os [modelos de programação do Service Fabric ](service-fabric-choose-framework.md) para implantar [executáveis de convidado](service-fabric-deploy-existing-app.md). O Service Fabric pode implantar serviços de imagens de contêiner. É importante observar que você pode misturar serviços em processos e serviços em contêineres no mesmo aplicativo. Se você quiser apenas [implantar e gerenciar imagens de contêiner](service-fabric-containers-overview.md) em um cluster de computadores, o Service Fabric é uma opção perfeita.

## <a name="create-clusters-for-service-fabric-anywhere"></a>Criar clusters para o Service Fabric em qualquer lugar
Você pode criar clusters para o Service Fabric em muitos ambientes, incluindo o Azure, ou localmente, no Windows Server ou no Linux. Além disso, o ambiente de desenvolvimento no SDK é idêntico ao ambiente de produção, sem nenhum emulador envolvido. Em outras palavras, o que é executado no cluster de desenvolvimento local será implantado no mesmo cluster em outros ambientes.

Para saber mais sobre a criação de clusters locais, leia sobre [como criar um cluster no Windows Server ou no Linux](service-fabric-deploy-anywhere.md) ou, para o Azure, como criar um cluster [por meio do Portal do Azure](service-fabric-cluster-creation-via-portal.md).

![Plataforma Service Fabric][Image1]

## <a name="stateless-and-stateful-micrososervices-for-service-fabric"></a>Microsserviços com e sem estado para o Service Fabric
O Service Fabric permite compilar aplicativos que consistam em microsserviços. Os microsserviços sem estado (como gateways de protocolo e proxies Web) não mantêm um estado mutável fora de uma solicitação e sua resposta do serviço. As funções de trabalho dos Serviços de Nuvem do Azure são um exemplo de serviço sem estado. Os microsserviços com estado (como contas de usuário, bancos de dados, dispositivos, carrinhos de compra e filas) mantêm um estado mutável e autoritativo além da solicitação e sua resposta. Os aplicativos em escala da Internet de hoje consistem em uma combinação de microsserviços com e sem estado.

Por que ter microsserviços com estado junto microsserviços sem estado? Os dois motivos principais são:

* É possível compilar serviços OLAP (processamento de transações online tolerante a falha) de alta taxa de transferência e baixa latência mantendo o códigos e dados próximos no mesmo computador. Alguns exemplos são vitrines interativas, pesquisa, sistemas de Internet das coisas (IoT), sistemas de comércio, sistemas de detecção de fraudes e processamento de cartão de crédito e gerenciamento de registros pessoais.
* Você pode simplificar o design do aplicativo. Os microsserviços com estado eliminam a necessidade de filas e caches adicionais tradicionalmente necessários para atender aos requisitos de disponibilidade e latência de um aplicativo puramente sem estado. Os serviços com estado são naturalmente de alta disponibilidade e baixa latência, reduzindo o número de partes móveis para gerenciar em seu aplicativo como um todo.

Para saber mais sobre padrões de aplicativo com o Service Fabric, leia [Application scenarios](service-fabric-application-scenarios.md) (Cenários de aplicativos) e [Choosing a programming model framework](service-fabric-choose-framework.md) (Escolhendo uma estrutura de modelo de programação) para seu serviço.

Você também pode assistir a este vídeo da Microsoft Virtual Academy para ter uma visão geral dos serviços com e sem estado:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=HhD9566yC_4106218965">  
<img src="./media/service-fabric-overview/ReliableServicesVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="application-lifecycle-management"></a>Gerenciamento do ciclo de vida de aplicativos
O Service Fabric oferece suporte para o gerenciamento de todo o ciclo de vida do aplicativo para aplicativos em nuvem. Esse ciclo de vida inclui o desenvolvimento por meio de implantação, gerenciamento diário e manutenção até o possível encerramento.

As funcionalidades de gerenciamento de ciclo de vida do aplicativo do Service Fabric permitem que os administradores de aplicativos e os operadores de TI usem fluxos de trabalho simples com pouco envolvimento humano para provisionar, implantar e aplicar patch e monitorar aplicativos. Esses fluxos de trabalho internos reduzem enormemente a carga em operações de TI para manter os aplicativos disponíveis continuamente.

A maioria dos aplicativos consiste em uma combinação de microsserviços com e sem estado e outros executáveis/tempos de execução que são implantados juntos. Tendo tipos fortes nos aplicativos e microsserviços empacotados, o Service Fabric permite a implantação de várias instâncias de aplicativo. Cada instância é gerenciada e atualizada independentemente. Vale destacar que o Service Fabric pode implantar *qualquer* executável ou tempo de execução e torná-lo confiável. Por exemplo, o Service Fabric implanta ASP.NET Core 1, Node.js, máquinas virtuais Java, scripts ou qualquer outro item que componha seu aplicativo.

Para saber mais sobre o gerenciamento do ciclo de vida do aplicativo, leia [Ciclo de vida do aplicativo](service-fabric-application-lifecycle.md). Para obter mais informações sobre como implantar qualquer código, consulte [Implantar um executável convidado](service-fabric-deploy-existing-app.md).

Você também pode assistir a este vídeo da Microsoft Virtual Academy para ter uma visão geral do gerenciamento do ciclo de vida de aplicativo: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">  
<img src="./media/service-fabric-overview/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="key-capabilities"></a>Principais recursos
Ao usar a Malha do Serviço, você pode:

* Desenvolver aplicativos massivamente escalonáveis, que são autorrecuperáveis.
* Desenvolva aplicativos compostos por microsserviços usando o modelo de programação do Service Fabric. Ou você pode simplesmente hospedar executáveis convidados e outras estruturas de aplicativo de sua escolha, como ASP.NET Core 1 ou Node.js.
* Desenvolva microsserviços com e sem monitoração de estado altamente confiáveis.
* Implante e orquestre contêineres que incluam contêineres do Windows e contêineres do Docker em um cluster. Esses contêineres podem conter executáveis de convidado ou microsserviços com e sem estado confiáveis. Em ambos os casos, você obtém o mapeamento da porta do contêiner para a porta do host, detectabilidade do contêiner e failover automático.
* Simplifique o design do seu aplicativo usando microsserviços com monitoramento de estado em vez de caches e filas.
* Implante no Azure ou em data centers locais que executem o Windows ou Linux com zero alteração de código. Escreva uma vez e implante em qualquer lugar para qualquer cluster do Service Fabric.
* Desenvolver usando uma abordagem "datacenter no seu computador". O ambiente de desenvolvimento local usa o mesmo código que é executado nos datacenters do Azure.
* Implantar aplicativos em segundos.
* Implantar aplicativos em densidade mais alta do que as máquinas virtuais, implantando centenas ou milhares de aplicativos por computador.
* Implante diferentes versões do mesmo aplicativo lado a lado e atualize cada aplicativo de modo independente.
* Gerenciar o ciclo de vida dos seus aplicativos com estado sem nenhum tempo de inatividade, incluindo atualizações imediatas ou não.
* Gerencie aplicativos usando APIs do .NET, Java (Linux), PowerShell, interface de linha de comando do Azure (Linux) ou interface REST.
* Atualizar microsserviços e aplicar patch neles dentro dos aplicativos de modo independente.
* Monitorar e diagnosticar a integridade de seus aplicativos e definir políticas para realizar reparos automáticos.
* Escale horizontalmente ou reduza horizontalmente o número de nós em um cluster e escale verticalmente ou reduza verticalmente o tamanho de cada nó. Ao dimensionar os nós, seus aplicativos são dimensionados automaticamente e distribuídos de acordo com os recursos disponíveis.
* Observe o balanceador de recursos de auto-recuperação administrar a redistribuição de aplicativos no cluster. O Service Fabric se recupera de falhas e otimiza a distribuição a distribuição de carga com base nos recursos disponíveis.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
* Para mais informações:
  * [Por que usar uma abordagem de microsserviço para construir aplicativos?](service-fabric-overview-microservices.md)
  * [Visão geral da terminologia](service-fabric-technical-overview.md)
* Configurando o [ambiente de desenvolvimento](service-fabric-get-started.md)  
* [Como escolher uma estrutura do modelo de programação](service-fabric-choose-framework.md) para seu serviço

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png



<!--HONumber=Dec16_HO2-->


