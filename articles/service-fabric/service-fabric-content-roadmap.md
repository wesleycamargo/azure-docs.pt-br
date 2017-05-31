---
title: Saiba mais sobre o Azure Service Fabric | Microsoft Docs
description: "Uma visão geral e um guia de introdução para o Azure Service Fabric. Saiba mais sobre o Service Fabric e comece a desenvolver aplicativos escalonáveis, confiáveis e facilmente gerenciados, compostos por microsserviços."
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
ms.date: 04/18/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e1b8eba3e6ed91f87c4f2adfbba19d8fe2712920
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Então você deseja saber mais sobre o Service Fabric?
Este livro de instruções fornece uma breve visão geral sobre o Service Fabric, uma introdução aos principais conceitos e à terminologia, um guia para um ponto de partida e uma visão geral de cada área do Service Fabric. Estas instruções não contêm uma lista abrangente de conteúdo, mas vincula a visão geral e os artigos de introdução de cada área do Service Fabric. 

## <a name="the-five-minute-overview"></a>A visão geral de cinco minutos
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microsserviços escalonáveis e confiáveis. O Service Fabric resolve os desafios significativos de desenvolvimento e gerenciamento de aplicativos em nuvem. Ao usar o Service Fabric, os desenvolvedores e administradores podem evitar a resolução de problemas complexos de infraestrutura. Em vez disso, podem se concentrar na implementação de cargas de trabalho críticas e exigentes, sabendo que elas são escalonáveis, confiáveis e gerenciáveis. O Service Fabric representa a plataforma de middleware de última geração para criação e gerenciamento de aplicativos escalonáveis de nuvem de Camada 1 e nível corporativo. 

Este breve vídeo do Channel9 apresenta o Service Fabric e os microsserviços: <center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-content-roadmap/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="the-detailed-overview"></a>A visão geral detalhada
O Service Fabric permite que você crie e gerencie aplicativos escalonáveis e confiáveis, compostos por microsserviços. Esses microsserviços são executados em densidade alta em um pool compartilhado de computadores, que é conhecido como um cluster. Ele fornece um tempo de execução sofisticado para compilar microsserviços distribuídos e escalonáveis com e sem estado. Também fornece recursos de gerenciamento de aplicativos abrangente para provisionar, implantar, monitorar, atualizar/aplicar patch e excluir aplicativos implantados. Leia [Visão geral do Service Fabric](service-fabric-overview.md) para saber mais.

Por que um microsserviços criar abordagem? Todos os aplicativos evoluem ao longo do tempo. Os aplicativos bem-sucedidos evoluem sendo úteis às pessoas. Quanto você sabe sobre os requisitos de hoje e quais haverá no futuro? Às vezes, lançar um simples aplicativo como prova de conceito é o fator determinante, com a noção de que o aplicativo pode ser reprojetado posteriormente. Por outro lado, quando as empresas falam sobre criação para a nuvem, a expectativa é de crescimento e uso. O problema é que o crescimento e o dimensionamento são imprevisíveis. Os desenvolvedores desejam criar o protótipo rapidamente, sabendo que o aplicativo pode ser dimensionado para reagir ao uso e crescimento imprevisíveis. [O que são os microsserviços?](service-fabric-overview-microservices.md) descreve como a abordagem de design de microsserviços supera esses desafios e como é possível criar microsserviços que podem ser expandidos ou reduzidos e podem ser testados, implantados e gerenciados de forma independente.

O Azure Service Fabric oferece uma plataforma confiável e flexível que permite escrever e executar muitos tipos de serviço e aplicativo de negócios. Você também pode executar qualquer um dos seus aplicativos existentes (escritos em qualquer linguagem). Esses aplicativos e microsserviços podem ser com ou sem monitoração de estado e têm os recursos balanceados entre máquinas virtuais para melhorar a eficiência. A arquitetura exclusiva da Malha do Serviço permite que você execute análise de dados, computação na memória, transações paralelas e processamento de eventos quase em tempo real em seus aplicativos. Você pode [escalar ou reduzir verticalmente seus aplicativos](service-fabric-concepts-scalability.md) com facilidade (realmente reduzir ou expandir), de acordo com os requisitos de recurso em constante mudança. Leia [Cenários de aplicativos](service-fabric-application-scenarios.md) e [Padrões e cenários](service-fabric-patterns-and-scenarios.md) para saber mais sobre as categorias de aplicativos e serviços que você pode criar, bem como estudos de caso de clientes.

Esse vídeo da Microsoft Virtual Academy descreve os principais conceitos do Service Fabric:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="get-started-and-create-your-first-app"></a>Introdução e crie seu primeiro aplicativo 
Usando as ferramentas e SDKs do Service Fabric, você pode desenvolver aplicativos em ambientes Windows, Linux ou MacOS e implantar esses aplicativos em clusters em execução no Windows ou Linux. Os guias a seguir fará com que você implantar um aplicativo em minutos. Depois de executar seu primeiro aplicativo, baixar e executar alguns dos nossos [aplicativos de exemplo](http://aka.ms/servicefabricsamples). Comece especificamente com [Amostras de Introdução](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)

### <a name="on-windows"></a>No Windows
O SDK do Service Fabric inclui um suplemento do Visual Studio que fornece modelos e ferramentas para criar, implantar e depurar aplicativos do Service Fabric. Esses tópicos percorrer o processo de criar seu primeiro aplicativo no Visual Studio e executá-lo em seu computador de desenvolvimento.

[Configurar o ambiente de desenvolvimento](service-fabric-get-started.md)
[Criar seu primeiro aplicativo (C#)](service-fabric-create-your-first-application-in-visual-studio.md)

#### <a name="practical-hands-on-labs"></a>Laboratórios práticos
Experimente esta extensa [Parte 1 do laboratório prático](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) para se familiarizar com o fluxo de desenvolvimento de ponta a ponta para o Service Fabric. Saiba como criar um serviço sem estado, configure o monitoramento e relatórios de integridade, e realize uma atualização do aplicativo. Depois da Parte 1, avance até a [Parte 2, laboratório prático](http://aka.ms/sflab2), que conduz você pelos serviços com estado.

O vídeo do Channel9 a seguir guia você pelo processo de criação de um aplicativo C# no Visual Studio:  
<center><a target="_blank" href="https://channel9.msdn.com/Blogs/Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio">  
<img src="./media/service-fabric-content-roadmap/first-app-vid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-linux"></a>No Linux
O Service Fabric fornece SDKs para compilação de serviços no Linux em .NET Core e Java. Esses tópicos guiam você pelo processo de criação de seu primeiro aplicativo Java ou C# no Linux e pela execução em seu computador de desenvolvimento: [Configurar seu ambiente de desenvolvimento](service-fabric-get-started-linux.md), [Criar seu primeiro aplicativo (Java)](service-fabric-create-your-first-linux-application-with-java.md) e [Criar seu primeiro aplicativo (C#)](service-fabric-create-your-first-linux-application-with-csharp.md).

O vídeo do Microsoft Virtual Academy a seguir explica o processo de criação de um aplicativo Java no Linux:  
<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-content-roadmap/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-macos"></a>No MacOS
Você pode criar aplicativos de Service Fabric no MacOS X para que eles sejam executados em clusters do Linux. Esses artigos descrevem como configurar seu Mac para o desenvolvimento e orientam você na criação de um aplicativo Java no MacOS e na execução em uma máquina virtual do Ubuntu: [Configurar seu ambiente de desenvolvimento](service-fabric-get-started-mac.md), [Criar seu primeiro aplicativo (Java)](service-fabric-create-your-first-linux-application-with-java.md).

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
Um tipo de aplicativo é o nome/versão atribuído a uma coleção de tipos de serviço. Isso é definido em um arquivo *ApplicationManifest.xml*, que é inserido em um diretório do pacote de aplicativos. O pacote de aplicativos é então copiado para o repositório de imagens do cluster do Service Fabric. Assim, você pode criar um aplicativo nomeado desse tipo de aplicativo que, em seguida, é executado no cluster. 

Um tipo de serviço é o nome/versão atribuída aos pacotes de códigos, pacotes de dados e pacotes de configuração de um serviço. Isso é definido em um arquivo ServiceManifest.xml, que é inserido em um diretório do pacote de serviço. Em seguida, o diretório do pacote de serviço é referenciado por um arquivo *ApplicationManifest.xml* do pacote de aplicativos. Dentro do cluster, depois de criar um aplicativo nomeado, você pode criar um serviço nomeado a partir de um dos tipos de serviço do tipo de aplicativo. Um tipo de serviço é descrito pelo seu arquivo *ServiceManifest.xml*. O tipo de serviço é composto por definições de configuração de serviço do código executável, que são carregadas em tempo de execução e por dados estáticos que são consumidos pelo serviço.

![Tipos de aplicativos do Service Fabric e tipos de serviço][cluster-imagestore-apptypes]

O pacote de aplicativos é um diretório do disco que contém o arquivo *ApplicationManifest.xml* do tipo de aplicativo, que referencia os pacotes de serviço de cada tipo de serviço que compõe o tipo de aplicativo. Por exemplo, um pacote de aplicativos para um tipo de aplicativo de email pode conter referências a um pacote de serviços Fila, um pacote de serviços front-end e um pacote de serviços de banco de dados. Os arquivos no diretório do pacote de aplicativos são copiados no armazenamento de imagens do cluster do Service Fabric. 

Um pacote de serviço é um diretório do disco que contém o arquivo *ServiceManifest.xml* do tipo de serviço, que referencia o código, os dados estáticos e os pacotes de configuração do tipo de serviço. Os arquivos do diretório do pacote de serviço são referenciados pelo arquivo *ApplicationManifest.xml* do tipo de aplicativo. Por exemplo, um pacote de serviços pode fazer referência ao código, aos dados estáticos e aos pacotes de configuração que compõem um serviço de banco de dados.

### <a name="run-time-clusters-and-nodes-named-apps-named-services-partitions-and-replicas"></a>Tempo de execução: clusters e nós, chamados de aplicativos, chamado de serviços, partições e réplicas
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados. Os clusters podem ser dimensionados para milhares de máquinas.

Uma máquina ou VM que faz parte de um cluster é chamado de nó. Cada nó recebe um nome de nó (uma cadeia de caracteres). Os nós têm características como propriedades de posicionamento. Cada máquina ou VM tem um serviço do Windows de início automático, `FabricHost.exe` que começa a ser executado na inicialização e então inicia dois executáveis: `Fabric.exe` e `FabricGateway.exe`. Esses dois executáveis compõem o nó. Para cenários de desenvolvimento ou teste, você pode hospedar vários nós em um único computador ou VM executando várias instâncias de `Fabric.exe` e `FabricGateway.exe`.

Um aplicativo nomeado é uma coleção de serviços membros que executam uma determinada função ou funções. Um serviço executa uma função autônoma completa (que pode iniciar e ser executada independentemente de outros serviços) e é composto de código, configuração e dados. Depois de copiar um pacote de aplicativos para o repositório de imagens, você cria uma instância do aplicativo no cluster, especificando o tipo de aplicativo do pacote de aplicativos (usando seu nome/versão). Cada instância do tipo de aplicativo tem um nome URI atribuído que se parece com *fabric:/MyNamedApp*. Em um cluster, você pode criar vários aplicativos nomeados a partir de um tipo de aplicativo único. Você também pode criar aplicativos nomeados a partir de diferentes tipos de aplicativos. Cada aplicativo nomeado é gerenciado e possui controle de versão independente.

Depois de criar um aplicativo nomeado, você pode criar uma instância de um de seus tipos de serviço (um serviço nomeado) no cluster, especificando o tipo de serviço (usando seu nome/versão). Cada instância de tipo de serviço recebe um nome de URI com escopo dentro do URI de seu aplicativo nomeado. Por exemplo, se você criar um serviço chamado "MeuBancoDeDados" dentro de um aplicativo chamado "MeuAplicativoNomeado”, o URI terá esta aparência: *fabric:/MeuAplicativoNomeado/MeuBancoDeDados*. Dentro de um aplicativo nomeado, você pode criar um ou mais serviços nomeados. Cada serviço nomeado pode ter seu próprio esquema de partição e contagens de instância/réplica. 

Há dois tipos de serviços: com e sem monitoração. Os serviços sem monitoração de estado podem armazenar o estado persistente em um serviço de armazenamento externo, como o Armazenamento do Azure, o Banco de Dados SQL do Azure ou o Azure Cosmos DB. Use um serviço sem estado quando o serviço não tiver nenhum armazenamento persistente. Um serviço com estado usa o Service Fabric para gerenciar o estado de seu serviço por meio de seus modelos de programação Reliable Collections ou Reliable Actors. 

Ao criar um serviço nomeado, você especifica um esquema de partição. Serviços com grandes quantidades de estado dividem os dados entre partições. Cada partição é responsável por uma parte do estado completo do serviço, que é distribuído entre os nós do cluster. Dentro de uma partição, serviços nomeados sem estado têm instâncias, enquanto serviços nomeados com estado têm réplicas. Normalmente, os serviços nomeadas sem estado têm apenas uma partição, já que não têm estado interno. Os serviços nomeados com estado mantêm seu estado dentro de réplicas e cada partição tem seu próprio conjunto de réplicas. As operações de leitura e gravação são realizadas em uma réplica (chamada de Primária). As alterações no estado, devidas a operações de gravação, são replicadas para outras réplicas (chamadas de Secundárias Ativas). 

O diagrama a seguir mostra a relação entre aplicativos e instâncias de serviço, partições e réplicas.

![Partições e réplicas dentro de um serviço][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Particionamento, dimensionamento e disponibilidade
O [particionamento](service-fabric-concepts-partitioning.md) não é exclusivo do Service Fabric. Uma forma bem conhecida de particionamento é o particionamento de dados ou fragmentação. Serviços com estado, com grandes quantidades de estado, dividem os dados entre partições. Cada partição é responsável por uma parte do estado completo do serviço. 

As réplicas de cada partição são distribuídas entre os nós do cluster, o que permite que o estado do serviço nomeado seja [escalonado](service-fabric-concepts-scalability.md). Conforme os dados precisam crescer, as partições crescem e o Service Fabric redistribui as partições entre os nós para fazer uso eficiente dos recursos de hardware. Se você adicionar novos nós ao cluster, o Service Fabric reequilibrará as réplicas de partição entre o número aumentado de nós. O desempenho geral do aplicativo é melhorado e a contenção para o acesso à memória é reduzida. Se os nós no cluster não estiverem sendo usados com eficiência, você poderá diminuir o número de nós no cluster. O Service Fabric redistribui novamente as réplicas de partição entre o número reduzido de nós, para fazer melhor uso do hardware em cada nó.

Dentro de uma partição, serviços nomeados sem estado têm instâncias, enquanto serviços nomeados com estado têm réplicas. Normalmente, os serviços nomeadas sem estado têm apenas uma partição, já que não têm estado interno. As instâncias de partição proporcionam [disponibilidade](service-fabric-availability-services.md). Se uma instância falha, outras instâncias continuam a funcionar normalmente e, em seguida, o Service Fabric cria uma nova instância. Os serviços nomeados com estado mantêm seu estado dentro de réplicas e cada partição tem seu próprio conjunto de réplicas. As operações de leitura e gravação são realizadas em uma réplica (chamada de Primária). As alterações no estado, devidas a operações de gravação, são replicadas para outras réplicas (chamadas de Secundárias Ativas). Caso uma réplica falhe, o Service Fabric compila uma nova réplica a partir das réplicas existentes.

## <a name="supported-programming-models"></a>Modelos de programação com suporte
O Service Fabric oferece várias maneiras de escrever e gerenciar seus serviços. Os serviços podem usar as APIs do Service Fabric para aproveitarem ao máximo os recursos e as estruturas de aplicativo da plataforma. Os serviços também podem ser um programa executável compilado, escrito em qualquer linguagem e hospedado em um cluster do Service Fabric. Para saber mais, veja [Modelos de programação com suporte](service-fabric-choose-framework.md).

### <a name="guest-executables"></a>Executáveis de convidado
Um [executável convidado](service-fabric-deploy-existing-app.md) é um executável existente e arbitrário (escrito em qualquer linguagem), hospedado em um cluster do Service Fabric junto com outros serviços. Os executáveis convidados não se integram diretamente com as APIs do Service Fabric, no entanto. Os executáveis convidados não se beneficiam do conjunto completo de recursos que a plataforma oferece, como o relatório personalizado de integridade e de carga, o registro do ponto de extremidade de serviço e a computação com estado.

### <a name="containers"></a>Contêineres
Por padrão, o Service Fabric implanta e ativa esses serviços como processos. O Service Fabric também pode implantar serviços em [contêineres](service-fabric-containers-overview.md). É importante observar que você pode misturar serviços em processos e serviços em contêineres no mesmo aplicativo. Atualmente, o Service Fabric dá suporte à implantação de contêineres de Docker em contêineres do Linux e do Windows Server no Windows Server 2016. No modelo de aplicativo do Service Fabric, um contêiner representa um host de aplicativo no qual várias réplicas de serviço são colocadas. Você pode implantar aplicativos existentes, serviços sem monitoração de estado ou serviços com estado em contêineres usando o Service Fabric. 

### <a name="reliable-services"></a>Reliable Services
O [Reliable Services](service-fabric-reliable-services-introduction.md) é uma estrutura leve para o desenvolvimento de serviços que são integrados à plataforma do Service Fabric e se beneficiam do conjunto completo de recursos da plataforma. O Reliable Services pode ser sem monitoração de estado (semelhante à maioria das plataformas de serviço, como servidores Web ou Funções de Trabalho nos Serviços de Nuvem do Azure), em que o estado é persistido em uma solução externa, como o BD do Azure ou o Armazenamento de Tabelas do Azure. O Reliable Services também pode ser com estado, em que o estado é persistido diretamente no próprio serviço usando Coleções Confiáveis. O estado se torna [altamente disponível](service-fabric-availability-services.md) por meio de replicação e é distribuído por meio de [particionamento](service-fabric-concepts-partitioning.md), tudo gerenciado automaticamente pelo Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Criada com base no Reliable Services, a estrutura [Reliable Actor](service-fabric-reliable-actors-introduction.md) é uma estrutura de aplicativo que implementa o padrão Ator Virtual baseado no padrão de design de ator. A estrutura Reliable Actor usa unidades independentes de computação e de estado com execução single-threaded chamadas atores. A estrutura Reliable Actor fornece comunicação interna para atores e configurações de expansão e persistência de estado predefinido.

## <a name="app-lifecycle"></a>Ciclo de vida do aplicativo
Semelhante a outras plataformas, um aplicativo no Service Fabric geralmente passa pelas seguintes fases: design, desenvolvimento, teste, implantação, atualização, manutenção e remoção. O Service Fabric dá um excelente suporte ao ciclo de vida completo dos aplicativos em nuvem, desde o desenvolvimento até a implantação, gerenciamento diário, manutenção e possível encerramento. O modelo de serviço permite que várias funções diferentes participem do ciclo de vida do aplicativo de forma independente. O [ciclo de vida de aplicativo do Service Fabric](service-fabric-application-lifecycle.md) fornece uma visão geral das APIs e de como elas são usadas pelas diferentes funções em todas as fases do ciclo de vida de aplicativo do Service Fabric. 

Todo o ciclo de vida do aplicativo pode ser gerenciado usando [cmdlets do PowerShell](/powershell/module/ServiceFabric/), [APIs de C#](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [APIs de Java](/java/api/system.fabric._application_management_client) e [APIs REST](/rest/api/servicefabric/). Você também pode configurar pipelines de integração contínua/implantação contínua, usando ferramentas como o [Visual Studio Team Services](service-fabric-set-up-continuous-integration.md) ou [Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md)

O vídeo da Microsoft Virtual Academy a seguir descreve como gerenciar o ciclo de vida do aplicativo:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-content-roadmap/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="test-apps-and-services"></a>Testar aplicativos e serviços
Para criar serviços que são realmente em escala de nuvem, é essencial verificar se os aplicativos e serviços podem dar suporte a falhas reais. O Serviço de Análise de Falha foi desenvolvido para testar serviços criados com base no Service Fabric. Com o Serviço de Análise de Falha (service-fabric-testability-overview.md), é possível induzir falhas significativas e executar cenários de teste completos nos aplicativos. Esses cenários e falhas praticam e validam os vários estados e transições pelos quais um serviço passa durante seu tempo de vida, tudo de maneira consistente, segura e controlada.

As [Ações](service-fabric-testability-actions.md) destinam-se ao teste de um serviço, usando falhas individuais. Um desenvolvedor de serviço pode usá-las como blocos de construção para escrever cenários complicados. Exemplos de falhas simuladas são:

* Reinicie um nó para simular qualquer quantidade de situações nas quais um computador ou VM é reinicializada.
* Mova uma réplica de seu serviço com estado para simular o balanceamento de carga, failover ou atualização do aplicativo.
* Invoque a perda de quorum em um serviço com estado para criar uma situação na qual não é possível realizar operações de gravação, pois não há réplicas "secundárias" ou de "backup" suficientes para aceitar novos dados.
* Invoque a perda de dados em um serviço com estado para criar uma situação na qual todo o estado na memória é completamente apagado.

Os [cenários](service-fabric-testability-scenarios.md) são operações complexas compostas de uma ou mais ações. O Serviço de Análise de Falha fornece dois cenários internos completos:

* [Cenário caótico](service-fabric-controlled-chaos.md) – simula falhas intercaladas contínuas (amigáveis e não amigáveis), em todo o cluster durante longos períodos de tempo.
* [Cenário de failover](service-fabric-testability-scenarios.md#failover-test) – uma versão do cenário de teste de caos que tem como alvo uma partição de serviço específica, deixando outros serviços não afetados.

## <a name="clusters"></a>Clusters
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados. Os clusters podem ser dimensionados para milhares de máquinas. Um computador ou VM que faz parte de um cluster é chamado de nó de cluster. Cada nó recebe um nome de nó (uma cadeia de caracteres). Os nós têm características como propriedades de posicionamento. Cada computador ou VM tem um serviço de início automático, `FabricHost.exe`, que começa a ser executado na inicialização e, em seguida, inicia dois executáveis: Fabric.exe e FabricGateway.exe. Esses dois executáveis compõem o nó. Para cenários de teste, você pode hospedar vários nós em um único computador ou VM, executando várias instâncias do `Fabric.exe` e do `FabricGateway.exe`.

Os clusters do Service Fabric podem ser criados em máquinas virtuais ou computadores físicos que executam o Windows Server ou Linux. Você pode implantar e executar aplicativos do Service Fabric em qualquer ambiente em que tenha um conjunto de computadores do Windows Server ou do Linux interconectados: locais, no Microsoft Azure ou em qualquer provedor de nuvem.

O seguinte vídeo da Microsoft Virtual Academy descreve os clusters do Service Fabric: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/ClusterOverview.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="clusters-on-azure"></a>Clusters no Azure
A execução de clusters do Service Fabric no Azure proporciona integração a outros recursos e serviços do Azure, o que facilita e torna as operações e o gerenciamento do cluster mais confiável. Um cluster é um recurso do Azure Resource Manager, portanto você pode modelar clusters da mesma maneira que qualquer outro recurso no Azure. O Resource Manager também proporciona o gerenciamento fácil de todos os recursos usados pelo cluster como uma única unidade. Os clusters no Azure são integrados com o diagnóstico do Azure e o Log Analytics. Os nodetypes do cluster são [conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/index) e, portanto, a funcionalidade de dimensionamento automático é interna.

Você pode criar um cluster no Azure por meio do [Portal do Azure](service-fabric-cluster-creation-via-portal.md), de um [modelo](service-fabric-cluster-creation-via-arm.md) ou do [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

A visualização do Service Fabric no Linux permite criar, implantar e gerenciar aplicativos altamente disponíveis e altamente escalonáveis no Linux, assim como você faria no Windows. As estruturas do Service Fabric (Reliable Services e Reliable Actors) estão disponíveis em Java no Linux, além do C# (.NET Core). Você também pode compilar os [serviços executáveis convidados](service-fabric-deploy-existing-app.md) com qualquer linguagem ou estrutura. Além disso, a preview também dá suporte à orquestração de contêineres de Docker. Os contêineres de Docker podem executar arquivos executáveis de convidado ou serviços nativos do Service Fabric, que usam as estruturas do Service Fabric. Para obter mais informações, leia [Service Fabric no Linux](service-fabric-linux-overview.md).

Como o Service Fabric no Linux é uma visualização, há alguns recursos que têm suporte no Windows, mas não no Linux. Para saber mais, leia [Diferenças entre o Service Fabric no Linux e no Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Clusters independentes
O Service Fabric fornece um pacote de instalação para você criar clusters autônomos do Service Fabric localmente ou em qualquer provedor de nuvem. Os clusters autônomos proporcionam a liberdade de hospedar um cluster em qualquer local que você desejar. Se seus dados estão sujeitos a restrições de normas ou de conformidade, ou se deseja manter seus dados locais, você pode hospedar seu próprio cluster e aplicativos. Os aplicativos do Service Fabric podem ser executados em vários ambientes de hospedagem sem alterações, portanto, seu conhecimento de criação de aplicativos é transportado de um ambiente de hospedagem para o outro. 

[Criar seu primeiro cluster autônomo do Service Fabric](service-fabric-get-started-standalone-cluster.md)

Ainda não há suporte para clusters autônomos do Linux.

### <a name="cluster-security"></a>Segurança de cluster
Os clusters sempre devem ser protegidos para evitar que usuários não autorizados se conectem ao cluster, especialmente quando ele tiver cargas de trabalho de produção em execução. Embora seja possível criar um cluster não seguro, fazer isso permitirá que usuários anônimos se conectem a ele, caso os pontos de extremidade de gerenciamento sejam expostos à Internet pública. Não é possível habilitar posteriormente a segurança em um cluster não seguro: a segurança de cluster é habilitada no momento da criação do cluster.

Os cenários de segurança do cluster são:
* Segurança de nó para nó
* Segurança de cliente para nó
* RBAC (Controle de Acesso Baseado em Função)

Para obter mais informações, leia [Proteger um cluster](service-fabric-cluster-security.md).

### <a name="scaling"></a>Dimensionamento
Se você adiciona novos nós ao cluster, o Service Fabric reequilibra as réplicas de partição e instâncias entre o número aumentado de nós. O desempenho geral do aplicativo é melhorado e a contenção para o acesso à memória é reduzida. Se os nós no cluster não estiverem sendo usados com eficiência, você poderá diminuir o número de nós no cluster. O Service Fabric redistribui novamente as réplicas de partição e instâncias entre o número reduzido de nós, para fazer melhor uso do hardware em cada nó. Você pode dimensionar clusters no Azure [manualmente](service-fabric-cluster-scale-up-down.md) ou [programaticamente](service-fabric-cluster-programmatic-scaling.md). Clusters autônomos podem ser dimensionados [manualmente](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Atualizações do cluster
Periodicamente, são lançadas novas versões do tempo de execução do Service Fabric. Realize as atualizações de tempo de execução ou de malha no cluster para que você sempre tenha uma [versão com suporte](service-fabric-support.md) em execução. Além das atualizações de malha, você também pode atualizar a configuração do cluster, como os certificados ou as portas de aplicativo.

Um cluster do Service Fabric é um recurso cujo proprietário é você, mas que é parcialmente gerenciado pela Microsoft. A Microsoft é responsável por corrigir o sistema operacional subjacente e realizar atualizações de malha em seu cluster. Você pode definir para que o cluster receba atualizações automáticas de malha, quando a Microsoft lança uma nova versão, ou optar por selecionar uma versão de malha com suporte que você queira. As atualizações de malha e de configuração podem ser definidas por meio do Portal do Azure ou por meio do Resource Manager. Para obter mais informações, leia [Atualizar um cluster do Service Fabric](service-fabric-cluster-upgrade.md). 

Um cluster autônomo é um recurso que é totalmente seu. Você é responsável por corrigir o sistema operacional subjacente e iniciar atualizações de malha. Se seu cluster puder se conectar ao [https://www.microsoft.com/download](https://www.microsoft.com/download), você poderá configurá-lo para baixar e provisionar automaticamente o novo pacote de tempo de execução do Service Fabric. Em seguida, você iniciaria a atualização. Se seu cluster não puder acessar o [https://www.microsoft.com/download](https://www.microsoft.com/download), você poderá baixar manualmente o novo pacote de tempo de execução em um computador conectado à Internet e, em seguida, iniciar a atualização. Para obter mais informações, leia [Atualizar um cluster autônomo do Service Fabric](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Monitoramento da integridade
O Service Fabric apresenta um [modelo de integridade](service-fabric-health-introduction.md) desenvolvido para sinalizar condições de cluster e aplicativo não íntegras em entidades específicas (como nós de cluster e réplicas de serviço). O modelo de integridade usa relatores de integridade (componentes do sistema e watchdogs). O objetivo é facilitar e agilizar o diagnóstico e o reparo. Os desenvolvedores de serviço precisam pensar antecipadamente a respeito da integridade e como [criar relatórios de integridade](service-fabric-report-health.md#design-health-reporting). Qualquer condição que possa afetar a integridade deve ser apontada, especialmente se ela puder ajudar a sinalizar problemas próximos da raiz. As informações de integridade podem economizar tempo e esforço na depuração e investigação quando o serviço estiver funcionando em escala na produção.

Os relatores da Malha de Serviço monitoram condições identificadas de interesse. Eles informam essas condições com base na respectiva exibição local. O [repositório de integridade](service-fabric-health-introduction.md#health-store) agrega dados de integridade enviados por todos os relatores para determinar se as entidades estão globalmente íntegras. O modelo foi desenvolvido com a intenção de ser completo, flexível e fácil de usar. A qualidade dos relatórios de integridade determina a precisão da exibição de integridade do cluster. Falsos positivos que mostram incorretamente problemas de integridade podem afetar negativamente atualizações ou outros serviços que usam dados de integridade. Exemplos de tais serviços são os serviços de reparo e os mecanismos de alerta. Portanto, é necessário um pouco de criatividade para fornecer relatórios que capturem condições de interesse da melhor forma possível.

A emissão de relatórios pode ser feita:
* Na réplica ou instância de serviço monitorado do Service Fabric.
* De watchdogs internos implantados como um serviço do Service Fabric (por exemplo, um serviço sem estado do Service Fabric que monitora as condições e emite relatórios). Os watchdogs podem ser implantados em todos os nós ou podem ser relacionados por afinidade com o serviço monitorado.
* Em watchdogs internos que são executados nos nós do Service Fabric, mas que não são implementados como serviços do Service Fabric.
* Em watchdogs externos que testam o recurso de fora do cluster do Service Fabric (por exemplo, um serviço de monitoramento, como o Gomez).

Os componentes do Service Fabric apresentam relatórios de integridade prontos para uso sobre todas as entidades do cluster. Os [relatórios de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) fornecem visibilidade da funcionalidade do cluster e de aplicativos e sinalizam problemas por meio da integridade. Para aplicativos e serviços, os relatórios de integridade do sistema verificam se as entidades estão implementadas e se comportando corretamente da perspectiva do tempo de execução do Service Fabric. Os relatórios não fornecem monitoramento de integridade da lógica de negócios do serviço nem detectam processos que pararam de responder. Para adicionar informações de integridade específicas à lógica do seu serviço, [implemente relatórios de integridade personalizados](service-fabric-report-health.md) em seus serviços.

O Service Fabric fornece várias maneiras de [exibir relatórios de integridade](service-fabric-view-entities-aggregated-health.md) agregados no repositório de integridade:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização/
* Consultas de integridade (por meio do [PowerShell](/powershell/module/ServiceFabric/), das [APIs FabricClient de C#](/api/system.fabric.fabricclient.healthclient) e das [APIs FabricClient de Java](/java/api/system.fabric._health_client), ou das [APIs REST](/rest/api/servicefabric)).
* Consultas gerais que retornam uma lista de entidades que têm a integridade como uma das propriedades (por meio do PowerShell, da API ou de REST).

O vídeo da Microsoft Virtual Academy a seguir descreve o modelo de integridade do Service Fabric e seu uso: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-content-roadmap/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="next-steps"></a>Próximas etapas
* Saiba como criar um [cluster no Azure](service-fabric-cluster-creation-via-portal.md) ou um [cluster autônomo no Windows](service-fabric-cluster-creation-for-windows-server.md).
* Tente criar um serviço usando os modelos de programação [Reliable Services](service-fabric-reliable-services-quick-start.md) ou [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Saiba como [migrar dos Serviços de Nuvem](service-fabric-cloud-services-migration-differences.md).
* Saiba como [monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Aprenda a [testar seus aplicativos e serviços](service-fabric-testability-overview.md).
* Aprenda a [gerenciar e orquestrar recursos do cluster](service-fabric-cluster-resource-manager-introduction.md).
* Examine os [exemplos do Service Fabric](http://aka.ms/servicefabricsamples).
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md).
* Leia o [blog da equipe](https://blogs.msdn.microsoft.com/azureservicefabric/) para artigos e comunicados.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png

