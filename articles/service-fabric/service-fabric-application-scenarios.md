<properties 
   pageTitle="Design e cenários de aplicativo com a Malha do Serviço" 
   description="Categorias de aplicativo. Design de aplicativo usando os serviços com e sem monitoração de estado" 
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
   ms.date="08/21/2015"
   ms.author="mfussell"/>

# Cenários de Aplicativos Malha do Serviço

O Service Fabric do Microsoft Azure oferece uma plataforma confiável e flexível que permite escrever e executar muitos tipos de serviço e aplicativo de negócios. Esses aplicativos e microsserviços podem ser com ou sem monitoramento de estado e têm os recursos balanceados entre as máquinas virtuais para melhorar a eficiência. A arquitetura exclusiva da Malha do Serviço permite que você execute análise de dados, computação na memória, transações paralelas e processamento de eventos quase em tempo real em seus aplicativos. Você pode escalar verticalmente (realmente reduzir ou expandir) seus aplicativos com facilidade, de acordo com os requisitos de recurso em constante mudança.

A plataforma Malha do Serviço no Azure é ideal para as seguintes categorias de aplicativos e serviços:

- **Serviços altamente disponíveis**: os serviços da Malha do Serviço fornece failover extremamente rápido. O Service Fabric permite criar várias réplicas de serviço secundárias. Se um nó, processo ou serviço individual falhar devido a uma falha de hardware, ou outra falha, uma das réplicas secundárias é imediatamente promovida a uma réplica primária com perda insignificante de serviço aos clientes. 

- **Serviços escalonáveis**: serviços individuais podem ser particionados, permitindo a sua expansão no cluster. Além disso, os serviços individuais podem ser criados e removidos dinamicamente. Os serviços podem ser escalados horizontalmente de modo rápido e fácil de algumas instâncias em alguns nós para milhares de instâncias em muitos nós e, em seguida, imediatamente reduzidos verticalmente novamente, dependendo das suas necessidades de recurso. Você pode usar o Service Fabric para criar esse serviço e gerenciar o respectivo ciclo de vida completo.
 
- **Computação em dados não estáticos**: o Service Fabric permite criar aplicativos com estado que fazem uso intenso de dados, E/S e computação. O Service Fabric permite a colocação de processamento (computação) e dados em aplicativos. Hoje, normalmente, quando seu aplicativo exige acesso a dados, há latência de rede que é associada a um cache de dados externo ou a uma camada de armazenamento. Com os serviços do Service Fabric com estado, essa latência é eliminada, o que permite leituras e gravações com mais desempenho. Por exemplo, digamos que você tenha um aplicativo que executa uma seleção de recomendação em tempo real para clientes com um requisito de tempo de ida e volta de menos de 100 ms. As características de latência e desempenho dos serviços do Service Fabric (onde a computação da seleção de recomendação é colocada com os dados e regras) fornece uma experiência responsiva ao usuário em comparação com o modelo de implementação padrão de ter que buscar os dados necessários no armazenamento remoto.
 
- **Aplicativos interativos baseados em sessão**: a Malha do Serviço é útil se os aplicativos, como jogos online ou mensagens instantâneas, exigirem baixa latência em leituras e gravações. O Service Fabric permite que você crie esses aplicativos interativos e com estado sem ter que criar um armazenamento ou cache separado necessários aos aplicativos sem estado (aumentando a latência e introduzindo potencialmente problemas de consistência).
 
- **Processamento gráfico distribuído**: o crescimento de redes sociais aumentou consideravelmente a necessidade de analisar gráficos em grande escala em paralelo. O dimensionamento rápido e o processamento paralelo de carga faz com que a Malha do Serviço seja uma plataforma natural para o processamento de gráficos em larga escala. A Malha do Serviço permite que você crie serviços altamente escalonáveis para grupos como redes sociais, pesquisa científica e business intelligence.
 
- **Fluxos de trabalho e análise de dados**: a leitura/gravação rápida da Malha do Serviço permite aplicativos que devem processar eventos ou fluxos de dados de forma confiável. A Malha do Serviço também permite aplicativos que descrevem um pipeline de processamento, em que os resultados devem ser confiáveis e encaminhados sem perda para o próximo estágio do processamento, incluindo sistemas transacionais e financeiros, onde as garantias de computação e consistência de dados são essenciais.

## Projetar aplicativos compostos de microsserviços com e sem monitoração ##
A criação de aplicativos com funções de trabalho do Azure Cloud Service é um exemplo de serviços sem monitoração de estado. Por outro lado, os microsserviços com estado mantêm o estado autoritativo além da solicitação e sua resposta, fornecendo alta disponibilidade e consistência desse estado por meio de APIs simples que fornecem garantias transacionais com o apoio da replicação. Os serviços com estado do Service Fabric democratiza a HA (alta disponibilidade), incorporando-a a todos os tipos de aplicativo, e não apenas a bancos de dados e outros armazenamentos de dados. Essa é uma progressão natural: do uso de bancos de dados puramente relacionais para HA, os aplicativos já passaram para os bancos de dados NoSQL; agora os próprios aplicativos podem ter seu estado "hot" e os dados gerenciados dentro deles para ganhos extras de desempenho, sem sacrificar a confiabilidade, a consistência ou a disponibilidade.

Ao criar aplicativos que consistem em microsserviços, você normalmente tem uma combinação de aplicativos Web sem estado (ASP.NET, node.js, etc.) chamando serviços de camada intermediária de negócios com e sem estado, tudo implantado no mesmo cluster do Service Fabric usando os comandos de implantação do Service Fabric. Cada um desses serviços independe da escala, da confiabilidade e do uso de recursos, melhorando consideravelmente a agilidade no gerenciamento de ciclo de vida e desenvolvimento.
  
Os microsserviços com monitoração de estado simplificam o design dos aplicativos, já que eliminam a necessidade de filas e caches adicionais que têm sido tradicionalmente necessários para abordar os requisitos de disponibilidade e de latência de um aplicativo totalmente sem monitoração de estado. Uma vez que os serviços com estado são, de forma natural, altamente disponíveis e de baixa latência, isso significa menos partes móveis para gerenciar no seu aplicativo como um todo. Os diagramas a seguir ilustram as diferenças entre criar um aplicativo sem monitoração de estado e um com monitoração de estado. Aproveitando os modelos de programação [Serviços Confiáveis](../Service-Fabric/service-fabric-reliable-services-introduction.md) e [Atores Confiáveis](service-fabric-reliable-actors-introduction.md), os serviços com estado reduzem a complexidade do aplicativo, ao mesmo tempo em que alcançam alta taxa de transferência e baixa latência.

## Um aplicativo criado usando serviços sem monitoração de estado##
![Aplicativo usando serviço sem monitoração de estado][Image1]

## Um aplicativo criado usando os serviços com monitoração de estado##
![Aplicativo usando serviço sem monitoração de estado][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas


Comece a criar serviços com e sem monitoração de estado com o modelos de programação da Malha do Serviço [serviços confiáveis](service-fabric-reliable-services-quick-start.md) e [atores confiáveis](service-fabric-reliable-actors-get-started.md).

Confira também os seguintes tópicos:

[Definir e gerenciar o Estado do Serviço](service-fabric-concepts-state.md)

[Disponibilidade de serviços](../service-fabric-concepts-availability-services.md)

[Escalabilidade de serviços da Malha do Serviço](service-fabric-concepts-scalability.md)

[Particionamento dos serviços da Malha do Serviço](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
 
 

<!---HONumber=Oct15_HO3-->