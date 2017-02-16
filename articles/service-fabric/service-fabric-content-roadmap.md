---
title: Saiba mais sobre o Azure Service Fabric | Microsoft Docs
description: "Um guia de visão geral e de introdução do Service Fabric no qual os aplicativos são compostos por muitos microsserviços para fornecer dimensionamento e resiliência."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/09/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: 8e7c14d9eeeab43d596eec99cb380f06cf9c39b3


---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Então você deseja saber mais sobre o Service Fabric?
Este roteiro de aprendizagem ajudará você a começar a desenvolver aplicativos escalonáveis, confiáveis e de fácil gerenciamento no Service Fabric.

## <a name="the-five-minute-overview"></a>A visão geral de cinco minutos
O Azure Service Fabric é uma plataforma de sistemas distribuídos que torna mais fácil empacotar, implantar e gerenciar microsserviços escalonáveis e confiáveis, bem como resolver desafios significativos no desenvolvimento e gerenciamento de aplicativos em nuvem. Ao usar o Service Fabric, desenvolvedores e administradores podem evitar a resolução de problemas complexos de infraestrutura e, em vez disso, se concentram na implementação de cargas de trabalho essenciais e exigentes sabendo que elas são escalonáveis, confiáveis e gerenciáveis. O Service Fabric representa a plataforma de middleware de última geração para criação e gerenciamento de aplicativos escalonáveis de nuvem de Camada&1; e nível corporativo.  

Este breve vídeo do Channel9 apresenta o Service Fabric e os microsserviços: <center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-content-roadmap/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="the-detailed-overview"></a>A visão geral detalhada
O Service Fabric permite compilar e gerenciar aplicativos escalonáveis e confiáveis compostos por microsserviços executados a uma densidade muito alta em um pool compartilhado de computadores, conhecido como um cluster. Ele fornece um tempo de execução sofisticado para compilar microsserviços distribuídos e escalonáveis com e sem estado. Também fornece recursos de gerenciamento de aplicativos abrangente para provisionar, implantar, monitorar, atualizar/aplicar patch e excluir aplicativos implantados.  Leia [Visão geral do Service Fabric](service-fabric-overview.md) para saber mais.

Por que um microsserviços criar abordagem? Todos os aplicativos evoluem ao longo do tempo. Os aplicativos bem-sucedidos evoluem sendo úteis às pessoas. Quanto você sabe sobre os requisitos de hoje e quais haverá no futuro? Às vezes, lançar um simples aplicativo como prova de conceito é o fator determinante, com a noção de que o aplicativo pode ser reprojetado posteriormente. Por outro lado, quando as empresas falam sobre criação para a nuvem, a expectativa é de crescimento e uso. O problema é que o crescimento e o dimensionamento são imprevisíveis. Gostaríamos de ser capaz de protótipo rapidamente ao saber que o aplicativo pode ser dimensionado para reagir a uso e crescimento imprevisível.  [Quais são os microsserviços?](service-fabric-overview-microservices.md) descreve como a abordagem de design microsserviços superar esses desafios e como é possível criar microsserviços que você pode escalar vertical ou horizontalmente, testar, implantar e gerenciar de forma independente.

O Azure Service Fabric oferece uma plataforma confiável e flexível que permite escrever e executar muitos tipos de serviço e aplicativo de negócios.  Você também pode executar qualquer um dos seus aplicativos existentes (escritos em qualquer linguagem).  Esses aplicativos e microsserviços podem ser com ou sem monitoração de estado e têm os recursos balanceados entre máquinas virtuais para melhorar a eficiência. A arquitetura exclusiva da Malha do Serviço permite que você execute análise de dados, computação na memória, transações paralelas e processamento de eventos quase em tempo real em seus aplicativos. Você pode escalar verticalmente (realmente reduzir ou expandir) seus aplicativos com facilidade, de acordo com os requisitos de recurso em constante mudança. Leitura [cenários de aplicativos](service-fabric-application-scenarios.md) para saber mais sobre as categorias de aplicativos e serviços que você pode criar, bem como estudos de caso do cliente.

Esse vídeo da Microsoft Virtual Academy descreve os principais conceitos do Service Fabric:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="get-started-and-create-your-first-app"></a>Introdução e crie seu primeiro aplicativo 
Usando as ferramentas e SDKs do Service Fabric, você pode desenvolver aplicativos em ambientes Windows, Linux ou MacOS e implantar esses aplicativos em clusters em execução no Windows ou Linux.  Os guias a seguir fará com que você implantar um aplicativo em minutos.  Depois de executar seu primeiro aplicativo, baixar e executar alguns dos nossos [aplicativos de exemplo](http://aka.ms/servicefabricsamples).

### <a name="on-windows"></a>No Windows
O SDK do Service Fabric inclui um suplemento do Visual Studio que fornece modelos e ferramentas para criar, implantar e depurar aplicativos do Service Fabric. Esses tópicos percorrer o processo de criar seu primeiro aplicativo no Visual Studio e executá-lo em seu computador de desenvolvimento.

[Configurar o ambiente de desenvolvimento](service-fabric-get-started.md)
[criar sua primeira app(C#)](service-fabric-create-your-first-application-in-visual-studio.md)

Experimente este extenso [laboratório prático](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) para se familiarizar com o fluxo de desenvolvimento de ponta a ponta para o Service Fabric.  Saiba como criar um serviço sem estado, configure o monitoramento e relatórios de integridade, e realize uma atualização do aplicativo. 

O vídeo do Channel9 a seguir guia você pelo processo de criação de um aplicativo C# no Visual Studio:  
<center><a target="_blank" href="https://channel9.msdn.com/Blogs/Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio">  
<img src="./media/service-fabric-content-roadmap/first-app-vid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-linux"></a>No Linux
O Service Fabric fornece SDKs para compilação de serviços no Linux em .NET Core e Java. Esses tópicos guiam você pelo processo de criação de seu primeiro aplicativo Java ou C# no Linux e de execução em seu computador de desenvolvimento.
[Configurar o ambiente de desenvolvimento](service-fabric-get-started-linux.md)
[Criar seu primeiro aplicativo (Java)](service-fabric-create-your-first-linux-application-with-java.md)
[Criar seu primeiro aplicativo (C#)](service-fabric-create-your-first-linux-application-with-csharp.md)

O vídeo do Microsoft Virtual Academy a seguir explica o processo de criação de um aplicativo Java no Linux:  
<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-content-roadmap/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-macos"></a>No MacOS
Você pode criar aplicativos de Service Fabric no MacOS X para que eles sejam executados em clusters do Linux. Esses artigos descrevem como configurar seu Mac para o desenvolvimento e orientam você durante o processo de criar seu primeiro aplicativo Java no MacOS e executando-o em uma máquina virtual do Ubuntu.
[Configurar o ambiente de desenvolvimento](service-fabric-get-started-mac.md)
[criar seu primeiro aplicativo (Java)](service-fabric-create-your-first-linux-application-with-java.md)

## <a name="core-concepts"></a>Conceitos principais
[Terminologia do Service Fabric](service-fabric-technical-overview.md), [Modelo de aplicativo](service-fabric-application-model.md) e [Modelos de programação com suporte](service-fabric-choose-framework.md) oferecem mais conceitos e descrições, mas aqui estão os fundamentos básicos.

<table><tr><th>Conceitos principais</th><th>Tempo de design</th><th>Tempo de execução</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-app-type-service-type-app-package-and-manifest-service-package-and-manifest"></a>Tempo de design: o tipo de aplicativo, tipo de serviço, o pacote de aplicativo e manifesto, pacote de serviço e manifesto
Um tipo de aplicativo é o nome/versão atribuído a uma coleção de tipos de serviço. São definidas em um arquivo ApplicationManifest.xml, inserido em um diretório de pacote de aplicativos que é então copiado para o repositório de imagens do cluster do Service Fabric. Assim, você pode criar um aplicativo nomeado desse tipo de aplicativo que então é executado no cluster. 

Um tipo de serviço é o nome/versão atribuída aos pacotes de códigos, pacotes de dados e pacotes de configuração de um serviço. São definidas em um arquivo ServiceManifest.xml, inserido em um diretório de pacote de serviços, e o diretório de pacote de serviços é referenciado pelo arquivo ApplicationManifest.xml de um pacote de aplicativos. Dentro do cluster, depois de criar um aplicativo nomeado, você pode criar um serviço nomeado a partir de um dos tipos de serviço do tipo de aplicativo. Um tipo de serviço é descrito pelo arquivo do servicemanifest. XML e é composto de definições de configuração de serviço do código executável que são carregadas em tempo de execução e dados estáticos que são consumidos pelo serviço.

![Tipos de aplicativos do Service Fabric e tipos de serviço][cluster-imagestore-apptypes]

O pacote de aplicativos é um diretório de disco que contém o arquivo de ApplicationManifest.xm do tipo de aplicativo, que faz referência os pacotes de serviço para cada tipo de serviço que compõe o tipo de aplicativo. Por exemplo, um pacote de aplicativos para um tipo de aplicativo de email pode conter referências a um pacote de serviços Fila, um pacote de serviços front-end e um pacote de serviços de banco de dados. Os arquivos no diretório do pacote de aplicativos são copiados no armazenamento de imagens do cluster do Service Fabric. 

Um pacote de serviço é um diretório de disco que contém o arquivo de servicemanifest. XML do tipo de serviço, que faz referência a código, dados estáticos e pacotes de configuração para o tipo de serviço. Os arquivos no diretório do pacote de serviços são referenciados pelo arquivo ApplicationManifest.xml do tipo de aplicativo. Por exemplo, um pacote de serviços pode fazer referência ao código, aos dados estáticos e aos pacotes de configuração que compõem um serviço de banco de dados.

### <a name="run-time-clusters-and-nodes-named-apps-named-services-partitions-and-replicas"></a>Tempo de execução: clusters e nós, chamados de aplicativos, chamado de serviços, partições e réplicas
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados. Os clusters podem ser dimensionados para milhares de máquinas.

Uma máquina ou VM que faz parte de um cluster é chamado de nó. Cada nó recebe um nome de nó (uma cadeia de caracteres). Os nós têm características como propriedades de posicionamento. Cada máquina ou VM tem um serviço do Windows auto-start, FabricHost.exe, que começa a ser executado na inicialização e, em seguida, inicia dois executáveis: Fabric.exe e FabricGateway.exe. Esses dois executáveis compõem o nó. Para cenários de teste ou de desenvolvimento, você pode hospedar vários nós em uma VM ou um único computador executando várias instâncias do Fabric.exe e FabricGateway.exe.

Um aplicativo nomeado é uma coleção de serviços membros que executam uma determinada função ou funções. Um serviço executa uma função autônoma completa (que pode iniciar e ser executada independentemente de outros serviços) e é composto de código, configuração e dados. Depois de copiar um pacote de aplicativos para o repositório de imagens, você cria uma instância do aplicativo no cluster, especificando o tipo de aplicativo do pacote de aplicativos (usando seu nome/versão). Cada instância do tipo de aplicativo recebe um nome URI com esta aparência: *fabric:/NomeDoMeuAplicativo*. Em um cluster, você pode criar vários aplicativos nomeados a partir de um tipo de aplicativo único. Você também pode criar aplicativos nomeados a partir de diferentes tipos de aplicativos. Cada aplicativo nomeado é gerenciado e possui controle de versão independente.

Depois de criar um aplicativo nomeado, você pode criar uma instância de um de seus tipos de serviço (um serviço nomeado) no cluster, especificando o tipo de serviço (usando seu nome/versão). Cada instância de tipo de serviço recebe um nome de URI com escopo dentro do URI de seu aplicativo nomeado. Por exemplo, se você criar um serviço chamado "MeuBancoDeDados" dentro de um aplicativo chamado "MeuAplicativoNomeado”, o URI terá esta aparência: *fabric:/MeuAplicativoNomeado/MeuBancoDeDados*. Dentro de um aplicativo nomeado, você pode criar um ou mais serviços nomeados. Cada serviço nomeado pode ter seu próprio esquema de partição e contagens de instância/réplica. 

Há dois tipos de serviços: com e sem monitoração.  Um estado persistente do serviço sem estado for armazenado em um serviço de armazenamento externo, como o Armazenamento do Azure, Banco de Dados SQL do Azure ou Azure DocumentDB. Use um serviço sem estado quando o serviço não tiver nenhum armazenamento persistente. Um serviço com estado usa o Service Fabric para gerenciar o estado de seu serviço por meio de seus modelos de programação Reliable Collections ou Reliable Actors.  

Ao criar um serviço nomeado, você especifica um esquema de partição. Serviços com grandes quantidades de estado dividem os dados entre as partições, que se espalham pelos nós do cluster. Isso permite que o estado de seu serviço nomeado seja dimensionado. Dentro de uma partição, serviços nomeados sem estado têm instâncias, enquanto serviços nomeados com estado têm réplicas. Normalmente, os serviços nomeadas sem estado têm apenas uma partição, já que não têm estado interno. As instâncias de partição fornecem disponibilidade; se uma instância falha, outras instâncias continuam a funcionar normalmente e, em seguida, o Service Fabric cria uma nova instância. Serviços nomeados com estado mantêm seu estado dentro de réplicas, e cada partição tem sua própria réplica, com todo o estado mantido em sincronia. Caso uma réplica falhe, o Service Fabric compila uma nova réplica a partir das réplicas existentes.

O diagrama a seguir mostra a relação entre aplicativos e instâncias de serviço, partições e réplicas.

![Partições e réplicas dentro de um serviço][cluster-application-instances]

## <a name="supported-programming-models"></a>Modelos de programação com suporte
O Service Fabric oferece várias maneiras de escrever e gerenciar seus serviços. Os serviços podem escolher usar as APIs do Service Fabric para se beneficiarem dos recursos e estruturas de aplicativo da plataforma ou podem simplesmente ser um programa executável compilado escrito em qualquer linguagem e simplesmente hospedado em um cluster do Service Fabric. Para saber mais, veja [Modelos de programação com suporte](service-fabric-choose-framework.md).

### <a name="guest-executables"></a>Executáveis de convidado
Um [executável convidado](service-fabric-deploy-existing-app.md) é um executável arbitrário escrito em qualquer linguagem e, portanto, você pode hospedar seus aplicativos existentes em um cluster do Service Fabric junto com outros serviços. Os executáveis convidados não se integram diretamente com as APIs do Service Fabric e, por isso, não se beneficiam do conjunto completo de recursos que a plataforma oferece, como a integridade personalizada e relatórios de carga, registro do ponto de extremidade de serviço e computação com estado.

### <a name="containers"></a>Contêineres
Por padrão, o Service Fabric implanta e ativa esses serviços como processos. O Service Fabric também pode implantar serviços em [imagens de contêiner](service-fabric-containers-overview.md). É importante observar que você pode misturar serviços em processos e serviços em contêineres no mesmo aplicativo.  Atualmente, o Service Fabric dá suporte à implantação de contêineres de Docker em contêineres do Linux e do Windows Server no Windows Server 2016. No modelo de aplicativo do Service Fabric, um contêiner representa um host de aplicativo no qual várias réplicas de serviço são colocadas.  Você pode implantar aplicativos, serviços sem monitoração de estado ou serviços com monitoração de estado em um contêiner usando o Service Fabric.  

### <a name="reliable-services"></a>Reliable Services
O [Reliable Services](service-fabric-reliable-services-introduction.md) é uma estrutura leve para o desenvolvimento de serviços que se integram com a plataforma do Service Fabric e aproveitam o conjunto completo de recursos de plataforma. O Reliable Services pode ser sem estado, semelhante à maioria das plataformas de serviço, como servidores Web ou Funções de Trabalho nos Serviços de Nuvem do Azure, em que o estado é persistido em uma solução externa, como Banco de Dados do Azure ou Armazenamento de Tabelas do Azure. O Reliable Services também pode ser com estado, em que o estado é persistido diretamente no próprio serviço usando Coleções Confiáveis. O estado fica altamente disponível por meio de replicação e é distribuído por meio de particionamento, tudo gerenciado automaticamente pelo Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Criada com base no Reliable Services, a estrutura [Reliable Actor](service-fabric-reliable-actors-introduction.md) é uma estrutura de aplicativo que implementa o padrão Ator Virtual baseado no padrão de design de ator. A estrutura Reliable Actor usa unidades independentes de computação e de estado com execução single-threaded chamadas atores. A estrutura Reliable Actor fornece comunicação interna para atores e configurações de expansão e persistência de estado predefinido.

## <a name="next-steps"></a>Próximas etapas
* Saiba como criar um [cluster no Azure](service-fabric-cluster-creation-via-portal.md) ou um [cluster autônomo no Windows](service-fabric-cluster-creation-for-windows-server.md).
* Tente criar um serviço usando os modelos de programação [Reliable Services](service-fabric-reliable-services-quick-start.md) ou [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Saiba mais sobre o [ciclo de vida do aplicativo](service-fabric-application-lifecycle.md).
* Saiba como [inspecionar a integridade do aplicativo e do cluster](service-fabric-health-introduction.md).
* Saiba como [monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md).


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png


<!--HONumber=Jan17_HO4-->


