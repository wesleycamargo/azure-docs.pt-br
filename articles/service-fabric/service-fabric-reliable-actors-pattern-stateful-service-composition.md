
<properties
   pageTitle="Padrão de composição do serviço com estado | Microsoft Azure"
   description="O padrão de design dos Reliable Actors do Service Fabric usa atores com estado para manter o estado entre chamadas de serviço e armazenar em cache os resultados de serviço anteriores."
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

# Padrão de design dos Reliable Actors: composição de serviço com estado

Os desenvolvedores passaram a última década e meia criando serviços sem estado de N camadas no mundo corporativo. Eles criaram serviços tendo como base os bancos de dados. Eles criaram serviços de ordem superior com base em outros serviços. E, finalmente, criaram mecanismos de orquestração e middleware orientado à mensagem para coordenar esses serviços. À medida que as cargas de trabalho do usuário foram evoluindo, exigindo mais interatividade ou maior escala, a SOA (arquitetura orientado ao serviço) sem estado começou a mostrar seus pontos fracos.

## O método antigo: serviços SOA

Embora os serviços SOA tenham sido escalados horizontalmente de modo perfeito devido à sua natureza sem estado, eles criaram um afunilamento na simultaneidade e produtividade da camada de armazenamento. Isso tornou cada vez mais caro acessar o armazenamento. Como uma prática comum, a maioria dos desenvolvedores introduziu o caching à respectiva solução para reduzir a demanda por armazenamento. De qualquer forma, essa solução tinha suas desvantagens. Ela exigia outra camada que precisava de gerenciamento, acesso simultâneo ao cache, limitações e alterações semânticas, e consistência. Conforme detalhado [no padrão de cache inteligente](service-fabric-reliable-actors-pattern-smart-cache.md), o modelo de ator virtual fornece uma solução perfeita para esses problemas. Alguns desenvolvedores tentam resolver os problemas de SOA replicando a camada de armazenamento. No entanto, essa abordagem não tem muita flexibilidade e rapidamente atinge os limites do Common Alerting Protocol (Protocolo de Alerta Comum).

O segundo desafio desenvolveu-se em torno dos requisitos em constante mudança. Usuários e empresas exigem serviços interativos que possam responder às solicitações em milissegundos, em vez de segundos, como manda o regulamento. Para atender a essa demanda, os desenvolvedores começaram a criar serviços de fachada com base em outros serviços. Em alguns casos, dezenas de serviços de fachada foram desenvolvidos para criar serviços centrados no usuário. Mas a adição de vários serviços de downstream cria rapidamente problemas de latência.

Os desenvolvedores também têm se voltado para os caches e repositórios de objetos na memória. Em alguns casos, foram usadas diferentes implementações para atender aos requisitos de desempenho. Nessa abordagem, os desenvolvedores normalmente criam processos de trabalho de back-end para criar o cache periodicamente. Isso minimiza a cara população do cache sob demanda. Em seguida, eles decompõem suas cargas de trabalho para isolar operações assíncronas das síncronas. Isso dá mais espaço para operações interativas reagirem às mudanças no estado, o que é particularmente difícil na SOA.

Geralmente, eles também introduzem mais camadas, como filas e trabalhos. Estas podem adicionar ainda mais complexidade às soluções.

Basicamente, os desenvolvedores têm procurado soluções para criar "serviços com estado" que disponham "estado" e "comportamento de serviço" para atender às experiências interativas e centradas no usuário. E é aí que o modelo dos Reliable Actors do Service Fabric do Azure entram como um camada de composição de serviço, e não como uma substituição desses serviços.

O diagrama abaixo ilustra esse ponto:

![Reliable Actors, composição de serviço e persistência de estado][1]

## Implementar melhores soluções com atores

Na composição de serviços, os atores podem ser sem ou com estado.

* Atores sem estado podem ser usados como proxies para serviços subjacentes. Esses atores podem ser dinamicamente escalados no cluster do Service Fabric e podem armazenar em cache determinadas informações relacionadas ao serviço. Isso pode incluir o respectivo ponto de extremidade quando ele for descoberto.
* Os atores com estado podem manter o estado entre chamadas de serviços, bem como armazenar em cache os resultados do serviço anterior. O estado pode ser persistente ou transitório.

Esse padrão aplica-se em muitos cenários. Na maioria dos casos, um ator precisa fazer uma chamada externa para invocar uma operação em um determinado serviço. Vamos ilustrar isso usando o exemplo de um aplicativo de comércio eletrônico moderno. Tais aplicativos são criados com base em serviços que fornecem uma variedade de funcionalidades, incluindo gerenciamento de perfil de usuário, recomendações, gerenciamento de cestas, gerenciamento de listas de desejos e compras.

A maioria dos desenvolvedores de comércio eletrônico tenta adotar uma abordagem centrada no usuário para sua arquitetura — o que é semelhante ao desenvolvimento de experiências em redes sociais. Isso porque as experiências de comércio eletrônico também giram basicamente em torno de usuários e produtos. Geralmente, as soluções do desenvolvedor são alcançadas enviando uma fachada de serviços que, por motivos de desempenho, muito provavelmente tem suporte de um cache.

Compare isso com uma abordagem baseada em ator. Um ator de usuário pode representar o comportamento do usuário (como procurar o catálogo, curtir um produto, adicionar um item à cesta ou recomendar um produto a um amigo). Mas ele também pode representar o estado composto do usuário, incluindo o perfil do usuário, os itens na cesta, os itens recomendados pelos amigos, o histórico de compra do usuário e a localização geográfica atual do usuário.

## Popular o estado usando atores com estado

Primeiramente, vamos observar um exemplo em que o ator de usuário precisa popular seu estado usando vários serviços. Não vamos fornecer um exemplo de código para isso, pois tudo que discutimos [no padrão de cache inteligente](service-fabric-reliable-actors-pattern-smart-cache.md) também se aplica aqui. Um ator de usuário pode ser ativado no logon e populado com dados suficientes dos serviços de back-end. O estado, inteiro ou parcial, também pode ser pré-populado sob demanda, em um temporizador, ou usando as duas opções, e isso pode ser armazenado no cache do ator. Neste exemplo, os serviços **Profile** e **Wish List** são ilustrados abaixo:

![Serviços de perfil e lista de desejos][2]

Os desenvolvedores podem popular o estado para usuário frequentes e deixá-lo pronto para quando eles se conectarem. Os desenvolvedores também podem popular o estado no momento da conexão para usuários que visitam o serviço mensalmente. Você também pode verificar esses padrões na seção de cache inteligente.

Quando o User 23 (conforme mostrado na imagem acima) faz logon, o ator de usuário (23) é ativado, caso ainda não tenha sido. O ator de usuário então busca a lista de desejos e as informações relevantes de perfil de usuário nos serviços de back-end. O ator de usuário provavelmente também armazena em cache as informações de chamadas subsequentes. Se, por exemplo, um item deve ser adicionado à lista de desejos, isso pode ser feito por write-behind ou write-through, conforme discutido anteriormente.

Agora vamos examinar um exemplo em que um usuário clica no botão **Curtir** de um produto. Essa ação pode exigir várias invocações de vários serviços. Essas ações podem incluir enviar um "curtir" ao serviço de catálogo, disparar o próximo conjunto de recomendações e publicar uma atualização em uma rede social.

Isso é ilustrado abaixo:

![Curtir um produto e lista de desejos, perfil e serviços de catálogo][3]

## Empregar atores em comunicação assíncrona e na composição
O modelo de programação dos Reliable Actors do Service Fabric se destaca quando um desenvolver quer compor operações de estilo solicitação/resposta com operações assíncronas. Por exemplo, enquanto curtir um produto adiciona o item curtido imediatamente à lista de desejos do usuário, as ações de publicar em redes sociais e disparar o próximo conjunto de operações de recomendação podem ser feitas de modo assíncrono usando buffers e temporizadores.

Outro importante benefício de usar um ator de usuário para serviços é que o ator fornece um local natural para o estado armazenado em cache. E o que é mais importante, o ator também reage às mudanças em seu estado de modo assíncrono. Esse é um cenário com serviços sem estado particularmente desafiador. Por exemplo, um usuário pode executar uma série de ações como parte de uma "jornada do usuário", e esses eventos podem ser capturados em tempo real em um ator. Em seguida, um fluxo pode ser montado, que pode ser consultado no tempo do evento ou de modo assíncrono em um temporizador para mudar o comportamento do ator.

Nesse ponto, os puristas do SOA, sem dúvida, perceberam que esses não são serviços no sentido de atores como pontos de extremidade expostos por um protocolo independente de linguagem. O modelo dos Reliable Actors do Service Fabric não é um componente de interoperação nem uma plataforma para interoperação de serviço. Apesar disso, não há nada que impeça os desenvolvedores de pensar em termos da granularidade dos serviços de estilo SOA quando eles modelam atores ou a separação de preocupações da mesma forma. Tais serviços são conhecidos como microsserviços. Também não há nada que impeça os desenvolvedores de colocar um ponto de extremidade REST ou SOAP como uma camada de interoperação na frente dos Reliable Actors do Service Fabric.

Além disso, a composição do serviço com estado também se aplica aos fluxos de trabalho, e não apenas a cenários transacionais como os de comércio eletrônico. O Service Fabric foi desenvolvido como um mecanismo de fluxo de trabalho/orquestração. Ele pode ser usado para modelar fluxos de trabalho que envolvem interações de serviço e manter o estado dessas interações.

Você pode ver as desvantagens do "serviço sem estado" na criação de serviços escalonáveis que proporcionam experiências dinâmicas. Ao reunir estado e comportamento, o modelo de programação dos Reliable Actors do Service Fabric ajuda os desenvolvedores a criar experiências escalonáveis e interativas sobre seus investimentos existentes.


## Próximas etapas

[Padrão: cache inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Padrão: redes e gráficos distribuídos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Padrão: governança de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Padrão: Internet das Coisas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Padrão: computação distribuída](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alguns antipadrões](service-fabric-reliable-actors-anti-patterns.md)

[Introdução aos Reliable Actors do Service Fabric](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-2.png
[3]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-3.png

<!---HONumber=AcomDC_0121_2016-->