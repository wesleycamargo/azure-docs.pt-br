
<properties
   pageTitle="Padrão de composição do serviço com estado | Microsoft Azure"
   description="O padrão de design dos Reliable Actors do Service Fabric que usa atores com estado para manter o estado entre chamadas de serviço, bem como resultados do serviço anterior armazenado em cache."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Padrão de design de Atores Confiáveis: composição de serviço com estado

Os desenvolvedores passaram a última década e meia criando serviços sem estado de N camadas no mundo corporativo. Eles criaram serviços nos bancos de dados, serviços de ordem superior sobre outros serviços, bem como mecanismos de orquestração e middleware orientado a mensagem para coordenar esses serviços. À medida que as cargas de trabalho foram evoluindo, seja exigindo mais interatividade, seja em escala, a arquitetura orientado ao serviço sem estado começou a mostrar seus pontos fracos.

## O método antigo: serviços SOA

Embora os serviços SOA tenham sido escalados horizontalmente de modo perfeito devido à sua natureza sem estado, eles criaram um afunilamento na camada de armazenamento — simultaneidade e taxa de transferência. O acesso ao armazenamento foi se tornando cada vez mais caro. Como uma prática comum, a maioria dos desenvolvedores introduziu cache à respectiva solução para reduzir a demanda no armazenamento, mas essa solução tinha suas desvantagens — outra camada para gerenciar, acesso simultâneo ao cache, limitações e alterações semânticas e, por fim, consistência. Conforme detalhado anteriormente no padrão Cache Inteligente, o modelo de ator virtual fornece uma solução perfeita para isso.

Alguns desenvolvedores tentarem resolver o problema replicando a camada de armazenamento. No entanto, essa abordagem não escalava bem e rapidamente atingia os limites CAP. O segundo desafio desenvolveu-se em torno dos requisitos em constante mudança; usuários finais e empresas estão exigindo serviços interativos — responder às solicitações em milissegundos, em vez de segundos. Para responder, os desenvolvedores começaram a criar serviços de fachada sobre outros serviços, em alguns casos, dezenas de serviços para criar serviços centrados no usuário. No entanto, compor vários serviços de downstream rapidamente mostrou os problemas de latência.

Mais uma vez, os desenvolvedores voltaram-se para caches e repositórios de objeto na memória, em alguns casos, diferentes implementações para atender aos requisitos de desempenho. Eles começaram a criar processos de trabalho de back-end para criar o cache periodicamente, a fim de minimizar a onerosa população do cache sob demanda. Por fim, eles começaram a desconstruir suas cargas de trabalho para isolar operações assíncronas das síncronas a fim de ganhar mais espaço para operações interativas e reagir a mudanças no estado, o que é particularmente difícil no SOA.

Eles introduziram mais camadas, como filas e trabalhos, adicionando mais complexidade às soluções. Basicamente, os desenvolvedores começaram a procurar soluções para criar “serviços com estado”; em outras palavras, dispor “estado” e “comportamento de serviço” para atender às experiências interativas e centradas no usuário. E é aí que os Atores da Malha do Serviço do Azure entram como um camada de composição de serviço, e não como uma substituição desses serviços.

O diagrama abaixo ilustra esse ponto:

![][1]

## Melhor solução com atores

No caso de composição de serviços, os atores podem ser sem ou com estado.

* Atores sem estado podem ser usados como proxies para os serviços subjacentes. Esses atores podem ser dinamicamente escalados no cluster da Malha do Serviço do Azure e armazenar em cache determinadas informações relacionadas ao serviço, como seu ponto de extremidade assim que ele é descoberto.
* O ator com estado pode manter o estado entre chamadas de serviços, bem como armazenar em cache os resultados do serviço anterior. O estado pode ser persistente ou transitório.

Esse padrão também se aplica em muitos cenários; na maioria dos casos, os atores precisam fazer chamadas externas para invocar uma operação em um determinado serviço. Vamos ilustrar com um exemplo que usa aplicativos de comércio eletrônico modernos. Esses aplicativos são criados com base em serviços que fornecem várias funcionalidades, como Gerenciamento de Perfil de Usuários, Recomendações, Gerenciamento de Cestas, Gerenciamento de Listas de Desejos, Compras e muito mais.

A maioria dos desenvolvedores deseja usar uma abordagem centrada no usuário em sua arquitetura, bastante semelhante às de desenvolvimento de experiências sociais, uma vez que as experiências de comércio eletrônico basicamente giram em torno de usuários e produtos. Geralmente, isso é conseguido com o envio de uma fachada de serviços, muito provavelmente com suporte de um cache por motivos de desempenho.

Agora, vamos falar sobre uma abordagem baseada em ator. Um ator de usuário pode representar o comportamento do usuário (navegando em um catálogo, curtindo um produto, adicionando um item à cesta, recomendando um produto a um amigo), bem como seu estado composto (seu perfil, itens na cesta, lista de itens recomendados pelos amigos, seu histórico de compras, localização geográfica atual, etc.).

## Usando atores com estado

Primeiramente, vamos observar um exemplo onde o ator de usuário precisa popular seu estado usando vários serviços. Não vamos fornecer um exemplo de código para isso porque tudo que discutimos no padrão Cache Inteligente também se aplica aqui. Podemos ativar o ator de usuário no momento do logon, populando-o com dados suficientes de serviços de back-end. Obviamente, como vimos em muitas ocasiões anteriores neste documento, o estado total e parcial pode ser pré-populado sob demanda, com base em um temporizador, ou um pouco dos dois, e armazenado em cache no ator. Neste exemplo, Perfil e Lista de Desejos são ilustrados abaixo:

![][2]

Por exemplo, podemos pré-popular o estado dos usuários frequentes e deixá-lo pronto para quando eles fizerem logon ou pré-populá-lo no momento do logon para os usuários que visitam o serviço uma vez por mês. Vimos esses padrões na seção Cache Inteligente.

Quando o Usuário 23 faz logon, se ainda não estiver ativado, o ator de usuário (23) é ativado e busca as informações relevantes do perfil de usuário e a lista de desejos nos serviços de back-end. O ator de usuário provavelmente armazena em cache as informações de chamadas subsequentes. E se, por exemplo, precisarmos adicionar um item à lista de desejos, podemos usar write-behind ou write-through, conforme discutido anteriormente. Em segundo lugar, vamos observar um exemplo em que o usuário clica no botão “curtir” e curte um produto. Essa ação pode exigir várias invocações para vários serviços, conforme ilustrado abaixo: enviar um “curtir” para um serviço de catálogo, acionar o próximo conjunto de recomendações e talvez postar uma atualização em uma rede social.

Isso é ilustrado abaixo:

![][3]

## Como a composição de atores e a comunicação assíncrona podem ajudar
Na verdade, os Atores da Malha do Serviço do Azure se destacam quando queremos compor operações de estilo de solicitação/resposta com operações assíncronas. Por exemplo, enquanto “Curtir Produto” coloca o item curtido imediatamente na lista de desejos do usuário, postar em redes sociais e disparar o próximo conjunto de recomendações podem ser operações assíncronas usando buffers e temporizadores.

Outro importante benefício de usar um ator de usuário com serviços é que os atores fornecem um local natural para estado em cache e, o mais importante, reagem a mudanças em seu estado de modo assíncrono. Esse é um cenário com serviços sem estado particularmente desafiador. Por exemplo, um usuário executa uma série de ações, talvez parte de uma "viagem de usuário". Esses eventos podem ser capturados em tempo real no ator e podemos montar um fluxo, que podemos consultar na hora do evento ou de modo assíncrono em um temporizador para alterar o comportamento do ator.

Nesse ponto, os puristas do SOA, sem dúvida, perceberam que esses não são serviços no sentido de atores como pontos de extremidade expostos por um protocolo independente de linguagem. Os Atores da Malha do Serviço do Azure não são um componente de interoperação, nem uma plataforma para interoperação de serviços. Todavia, não há nada que realmente nos impeça de pensar em termos de granularidade dos serviços estilo SOA quando modelamos nossos atores ou ao modelar a separação de preocupações da mesma forma. Tais serviços são conhecidos como “microsserviços”. Da mesma forma, não há absolutamente nada nos impedindo de colocar um ponto de extremidade REST ou um ponto de extremidade SOAP como uma camada de interoperabilidade na frente dos Atores da Malha do Serviço do Azure.

A composição do serviço com estado também se aplica aos fluxos de trabalho, e não apenas a cenários transacionais como os de comércio eletrônico. A Malha do Serviço do Azure foi desenvolvida como um mecanismo de fluxo de trabalho/orquestração para que possa ser usado para modelar fluxos de trabalho que envolvem interações de serviço e manter o estado dessas interações.

Vemos desvantagens do “serviço sem estado” na criação de serviços escalonáveis para fornecer experiências dinâmicas. Os Atores da Malha do Serviço do Azure, fundamentalmente ao reunir estado e comportamento, ajudam os desenvolvedores a criar experiências escalonáveis e interativas sobre os investimentos já feitos.


## Próximas etapas

[Padrão: cache inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Padrão: redes e gráficos distribuídos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Padrão: controle de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Padrão: Internet das Coisas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Padrão: computação distribuída](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alguns antipadrões](service-fabric-reliable-actors-anti-patterns.md)

[Introdução aos Atores da Malha do Serviço](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-2.png
[3]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-3.png

<!---HONumber=Nov15_HO2-->