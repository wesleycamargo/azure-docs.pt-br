<properties
   pageTitle="Design e cenários de aplicativos | Microsoft Azure"
   description="Visão geral das categorias de aplicativos em nuvem no Service Fabric. Discute o design de aplicativo que usa serviços com e sem monitoração de estado."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/05/2016"
   ms.author="mfussell"/>

# Cenários de aplicativos do Service Fabric

O Service Fabric do Azure oferece uma plataforma confiável e flexível que permite escrever e executar muitos tipos de serviço e aplicativo de negócios. Esses aplicativos e microsserviços podem ser com ou sem monitoração de estado e têm os recursos balanceados entre máquinas virtuais para melhorar a eficiência. A arquitetura exclusiva da Malha do Serviço permite que você execute análise de dados, computação na memória, transações paralelas e processamento de eventos quase em tempo real em seus aplicativos. Você pode escalar verticalmente (realmente reduzir ou expandir) seus aplicativos com facilidade, de acordo com os requisitos de recurso em constante mudança.

A plataforma Malha do Serviço no Azure é ideal para as seguintes categorias de aplicativos e serviços:

- **Serviços altamente disponíveis**: serviços do Service Fabric fornecem failover rápido criando várias réplicas de serviço secundárias. Se um nó, processo ou serviço individual falhar devido a uma falha de hardware, ou outra falha, uma das réplicas secundárias é promovida a uma réplica primária com perda mínima de serviço.

- **Serviços escalonáveis**: serviços individuais podem ser particionados, permitindo que seu estado seja escalado horizontalmente no cluster. Além disso, os serviços individuais podem ser criados e removidos dinamicamente. Os serviços podem ser escalados horizontalmente de modo rápido e fácil de algumas instâncias em alguns nós para milhares de instâncias em muitos nós e, em seguida, reduzidos horizontalmente novamente, dependendo das suas necessidades de recurso. Você pode usar o Service Fabric para compilar esse serviço e gerenciar os respectivos ciclos de vida completos.

- **Computação em dados não estáticos**: o Service Fabric permite compilar aplicativos com monitoração de estado que fazem uso intenso de dados, entrada/saída e computação. O Service Fabric permite a colocação de processamento (computação) e dados em aplicativos. Normalmente, quando seu aplicativo exige acesso a dados, há latência de rede que é associada a um cache de dados externo ou a uma camada de armazenamento. Com os serviços do Service Fabric com monitoração de estado, essa latência é eliminada, o que habilita leituras e gravações com mais desempenho. Por exemplo, digamos que você tenha um aplicativo que executa uma seleção de recomendação em tempo real para clientes com um requisito de tempo de ida e volta de menos de 100 milissegundos. As características de latência e desempenho dos serviços do Service Fabric (onde a computação da seleção de recomendação é colocada com os dados e regras) fornece uma experiência responsiva ao usuário em comparação com o modelo de implementação padrão de ter que buscar os dados necessários no armazenamento remoto.

- **Aplicativos interativos baseados em sessão**: o Service Fabric é útil se os aplicativos, como jogos online ou mensagens instantâneas, exigirem baixa latência em leituras e gravações. O Service Fabric permite que você compile esses aplicativos interativos e com monitoração de estado sem ter que criar um armazenamento separado ou cache, como é exigido aos aplicativos sem monitoração de estado. (Isso aumenta a latência e potencialmente apresenta problemas de consistência).

- **Processamento gráfico distribuído**: o crescimento de redes sociais aumentou consideravelmente a necessidade de analisar gráficos em grande escala em paralelo. O dimensionamento rápido e o processamento paralelo de carga faz com que a Malha do Serviço seja uma plataforma natural para o processamento de gráficos em larga escala. A Malha do Serviço permite que você crie serviços altamente escalonáveis para grupos como redes sociais, pesquisa científica e business intelligence.

- **Fluxos de trabalho e análise de dados**: a leitura e gravação rápida do Service Fabric permite aplicativos que devem processar eventos ou fluxos de dados de forma confiável. O Service Fabric também permite aplicativos que descrevem pipelines de processamento, em que os resultados devem ser confiáveis es passado para o próximo estágio de processamento sem perda. Isso inclui sistemas transacionais e financeiros, em que as garantias de computação e consistência de dados são essenciais.

## Projetar aplicativos compostos de microsserviços com e sem monitoração de estado
A criação de aplicativos com funções de trabalho com o serviço de nuvem do Azure é um exemplo de serviços sem monitoração de estado. Por outro lado, os microsserviços com monitoração de estado mantêm o estado autoritário além da solicitação e de sua resposta. Isso fornece alta disponibilidade e consistência do estado por meio de APIs simples que oferecem garantias transacionais feitas pela replicação. Os serviços com monitoração de estado do Service Fabric democratiza a alta disponibilidade, incorporando-a a todos os tipos de aplicativo, e não apenas a bancos de dados e outros armazenamentos de dados. Essa é uma progressão natural. Os aplicativos já passaram do uso de bancos de dados totalmente relacionais para bancos de dados NoSQL de alta disponibilidade. Agora os próprios aplicativos podem ter seu estado e dados "hot" gerenciados dentro deles para ganhos de desempenho adicionais sem sacrificar a disponibilidade, a consistência e a confiabilidade.

Ao criar aplicativos que consistem em microsserviços, você normalmente tem uma combinação de aplicativos Web sem estado (ASP.NET, Node.js etc.) chamando serviços de camada intermediária de negócios com e sem monitoração de estado, tudo implantado no mesmo cluster do Service Fabric usando os comandos de implantação do Service Fabric. Cada um desses serviços não depende de escala, confiabilidade e uso de recursos, melhorando consideravelmente a agilidade no gerenciamento de ciclo de vida e desenvolvimento.

Os microsserviços com monitoração de estado simplificam o design dos aplicativos porque eliminam a necessidade de filas e caches adicionais que têm sido tradicionalmente necessários para abordar os requisitos de disponibilidade e de latência de um aplicativo totalmente sem monitoração de estado. Uma vez que os serviços com monitoramento de estado são, de forma natural, altamente disponíveis e baixa latência, isso significa que há menos partes móveis para gerenciar no seu aplicativo como um todo. Os diagramas a seguir ilustram as diferenças entre criar um aplicativo sem monitoração de estado e um com monitoração de estado. Ao aproveitar os modelos de programação dos [serviços confiáveis](service-fabric-reliable-services-introduction.md) e [atores confiáveis](service-fabric-reliable-actors-introduction.md), os serviços com monitoração de estado reduzem a complexidade do aplicativo, ao mesmo tempo que alcançam uma alta taxa de transferência e baixa latência.

## Um aplicativo criado usando serviços sem monitoração de estado##
![Aplicativo usando serviço sem monitoração de estado][Image1]

## Um aplicativo criado usando os serviços com monitoração de estado##
![Aplicativo usando serviço sem monitoração de estado][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

* Comece a criar serviços com e sem monitoração de estado com o modelos de programação dos [reliable services](service-fabric-reliable-services-quick-start.md) e [reliable actors](service-fabric-reliable-actors-get-started.md) do Service Fabric.
* Confira também os seguintes tópicos:
    * [Fale-me sobre os microsserviços](service-fabric-overview-microservices.md)
    * [Definir e gerenciar o estado do serviço](service-fabric-concepts-state.md)
    * [Disponibilidade dos serviços de malha do serviço](service-fabric-availability-services.md)
    * [Dimensionar serviços do Service Fabric](service-fabric-concepts-scalability.md)
    * [Particionar serviços do Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg

<!---HONumber=AcomDC_0713_2016-->