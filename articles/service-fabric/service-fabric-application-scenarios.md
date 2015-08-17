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
   ms.date="04/24/2015"
   ms.author="mfussell"/>

# Cenários de Aplicativos Malha do Serviço

A Malha do Serviço e o Azure oferecem uma plataforma de infraestrutura em nuvem confiável e flexível que permite executar muitos tipos de aplicativos e serviços comerciais. Esses aplicativos e microsserviços podem ser com ou sem monitoramento de estado e têm os recursos balanceados entre as máquinas virtuais para melhorar a eficiência. A arquitetura exclusiva da Malha do Serviço permite que você execute análise de dados, computação na memória, transações paralelas e processamento de eventos quase em tempo real em seus aplicativos. Você pode escalar ou reduzir verticalmente seus aplicativos com facilidade, dependendo da alteração dos requisitos de recursos.

A plataforma Malha do Serviço no Azure é ideal para as seguintes categorias de aplicativos e serviços:

- **Serviços altamente disponíveis**: os serviços da Malha do Serviço fornece failover extremamente rápido. Várias réplicas de serviço secundário permanecem disponíveis. Se um nó falhar devido a uma falha de hardware, uma das réplicas secundárias é imediatamente promovida a uma réplica primária com perda insignificante de serviço aos clientes. Os serviços podem ser expandidos fácil e rapidamente de algumas para milhares de instâncias, e reduzidos para algumas dependendo das suas necessidades de recursos. Você pode usar a Malha do Serviço para criar e gerenciar o ciclo de vida desses serviços em nuvem escalonáveis.

- **Serviços escalonáveis**: serviços individuais podem ser particionados, permitindo a sua expansão no cluster. Além disso, os vários serviços da Malha do Serviço podem ser criados e expandidos.
 
- **Computação em dados não estáticos**: a Malha do Serviço permite que você crie E/S de dados e aplicativos com monitoração de estado e uso intensivo de computação. Os recursos de processamento e de dados em aplicativos da Malha do Serviço estão localizados juntos; assim, quando seu aplicativo exigir leitura e gravação rápidas, a latência da rede que está associada a uma camada de cache ou armazenamento de dados externos é eliminada. Por exemplo, digamos que você tem um aplicativo que executa a seleção de anúncio quase em tempo real com um tempo resposta menor que 100 ms, onde o cálculo de regras de seleção do anúncio fornece uma experiência dinâmica ao usuário.
 
- **Aplicativos interativos baseados em sessão**: a Malha do Serviço é útil se os aplicativos, como jogos online ou mensagens instantâneas, exigirem baixa latência em leituras e gravações. A Malha do Serviço permite que você crie esses aplicativos interativos e com monitoração de estado sem ter que criar um armazenamento separado ou cache necessários aos aplicativos sem monitoração de estado.
 
- **Processamento gráfico distribuído**: o crescimento de redes sociais aumentou consideravelmente a necessidade de analisar gráficos em grande escala em paralelo. O dimensionamento rápido e o processamento paralelo de carga faz com que a Malha do Serviço seja uma plataforma natural para o processamento de gráficos em larga escala. A Malha do Serviço permite que você crie serviços altamente escalonáveis para grupos como redes sociais, pesquisa científica e business intelligence.
 
- **Fluxos de trabalho e análise de dados**: a leitura/gravação rápida da Malha do Serviço permite aplicativos que devem processar eventos ou fluxos de dados de forma confiável. A Malha do Serviço também permite aplicativos que descrevem um pipeline de processamento, em que os resultados devem ser confiáveis e encaminhados sem perda para o próximo estágio do processamento, incluindo sistemas transacionais e financeiros, onde as garantias de computação e consistência de dados são essenciais.

## Projetar aplicativos compostos de microsserviços com e sem monitoração ##
A criação de aplicativos com funções de trabalho do Azure Cloud Service é um exemplo de serviços sem monitoração de estado. Por outro lado, os microsserviços com monitoração de estado mantêm o estado autoritativo além da solicitação e de sua resposta, mas fornecendo replicação de estado por meio de APIs simples. Os serviços com monitoração de estado são a democratização da alta disponibilidade (HA) para todos os aplicativos, e não apenas para bancos de dados e outros armazenamentos de dados. Essa é a progressão natural no design, já que os aplicativos seguiram do uso de bancos de dados totalmente relacionais para a alta disponibilidade para bancos de dados NoSQL e, agora, para os próprios aplicativos que têm estado gerenciado neles para que os dados "importantes" sejam mantidos próximos de sua computação.

Ao criar aplicativos que consistem em microsserviços, você normalmente tem uma combinação de aplicativos Web sem monitoração de estado (ASP.NET, Node.js etc) chamando serviços de camada intermediária de negócios com e sem monitoração implantadas no mesmo cluster Malha do Serviço usando os comandos de implantação da Malha do Serviço. A independência do dimensionamento, da confiabilidade e do uso de recursos para cada um desses microsserviços fornece essa agilidade no desenvolvimento e no gerenciamento do ciclo de vida.
  
Os microsserviços com monitoração de estado simplificam o design dos aplicativos, já que eliminam a necessidade de filas e caches adicionais que têm sido tradicionalmente necessários para abordar os requisitos de disponibilidade e de latência de um aplicativo totalmente sem monitoração de estado. Já que o serviço com monitoração de estado é naturalmente altamente disponível e de baixa latência, isso significa menos partes móveis para se gerenciar em seu aplicativo como um todo. Os diagramas a seguir ilustram as diferenças entre criar um aplicativo sem monitoração de estado e um com monitoração de estado. No caso da monitoração de estado, aproveitando os modelos de programação [serviços confiáveis](../Service-Fabric/service-fabric-reliable-services-introduction.md) e [atores confiável](service-fabric-reliable-actors-introduction.md), os serviços com monitoração de estado reduzem a complexidade do aplicativo, ao mesmo tempo em que alcançam alta taxa de transferência e baixa latência.

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
 
 

<!---HONumber=August15_HO6-->