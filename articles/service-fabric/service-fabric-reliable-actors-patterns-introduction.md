<properties
   pageTitle="Padrões e antipadrões dos Reliable Actors | Microsoft Azure"
   description="Fornece uma visão geral do modelo de programação do ator, padrões de design que funcionam bem com Reliable Actors do Service Fabric e alguns antipadrões a serem evitados."
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
   ms.date="08/11/2015"
   ms.author="vturecek"/>

# Introdução aos padrões de design de Reliable Actors

O modelo de programação dos Reliable Actors no Service Fabric do Azure é uma plataforma criada com base no modelo de ator para resolver problemas reais em escala de nuvem. O Service Fabric é uma plataforma para a criação de aplicativos altamente confiáveis e escalonáveis que são fáceis de desenvolver e gerenciar, tanto na nuvem quanto no local.

O objetivo deste artigo é ser uma discussão prática sobre problemas práticos. Após a leitura dos vários padrões, você deverá entender como é possível usar o modelo dos Reliable Actors para criar soluções de nuvem e corporativas.

## Padrões

Nesta seção, listaremos um conjunto de padrões e cenários associados que estabelecemos em interações com clientes. Esses padrões representam classes de problemas que são aplicáveis a uma ampla gama de soluções que nossos clientes estão criando o Microsoft Azure. Embora os cenários sejam baseados em casos reais, eliminamos a maioria das preocupações específicas de domínio para tornar os padrões mais claros. Você pode achar que grande parte do código de exemplo é simples ou óbvio. Incluímos o código para esgotarmos todas as possibilidades e não porque é particularmente inteligente ou impressionante.

Os padrões apresentados aqui não pretendem ser abrangentes nem canônicos. Alguns desenvolvedores talvez resolvam os mesmos problemas ou padrões de forma diferente das que apresentamos.

[Padrão: cache inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Padrão: redes e gráficos distribuídos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Padrão: governança de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Padrão: composição de serviço com estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Padrão: Internet das Coisas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Padrão: computação distribuída](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alguns antipadrões](service-fabric-reliable-actors-anti-patterns.md)

### Atores, um breve histórico

O [documento](http://dl.acm.org/citation.cfm?id=1624804) escrito por Carl Hewitt e seus colaboradores que deu origem ao modelo de ator foi publicado em 1973. Mas somente recentemente o modelo de ator ganhou atenção como um meio de lidar com a simultaneidade e a complexidade em sistemas distribuídos.

O modelo de ator oferece suporte a objetos individuais refinados — atores — que são isolados uns dos outros. Eles se comunicam por meio da passagem de mensagens assíncronas, o que permite comunicação direta entre os atores. Um ator é executado com semântica de single-thread. Com o encapsulamento do estado do ator e o isolamento de outros atores, essa abordagem simplifica a criação de sistemas altamente paralelos. Ela faz isso removendo preocupações de simultaneidade do código de um ator. Os atores são criados dinamicamente no pool de recursos de hardware disponíveis.

[Erlang](http://www.erlang.org/) é a implementação mais popular do modelo de ator. Os desenvolvedores começaram a redescobrir o modelo de ator, o que estimulou o interesse renovado na Erlang e na criação de novas soluções semelhantes à Erlang, como atores em [Scala](http://www.scala-lang.org/), [Akka](http://akka.io), [Akka.NET](http://getakka.net/) e [DCell](http://research.microsoft.com/pubs/75988/dcell.pdf).

## Benefícios dos Reliable Actors do Service Fabric

O modelo de programação dos Reliable Actors é uma implementação do modelo de ator que usa algumas ideias da Erlang e de sistemas de objetos distribuídos. Ele adiciona uma camada de indireção e expõe os atores em um modelo de programação integrado que aproveita a plataforma do Service Fabric.

Os principais benefícios dos Reliable Actors são **produtividade do desenvolvedor**, mesmo para os programadores não especialistas e **escalabilidade transparente por padrão** sem nenhum esforço especial dos programadores. O modelo de programação dos Reliable Actors usa uma biblioteca .NET que é executada com base no Service Fabric. Ele fornece ferramentas que facilitam muito o desenvolvimento de aplicativos distribuídos complexos. Essas ferramentas também tornam os aplicativos resultantes estruturalmente escalonáveis. Expandimos cada um desses benefícios abaixo. O modelo de programação aumenta a produtividade dos programadores especialistas e dos não especialistas, fornecendo os principais serviços de sistema, garantias e abstrações a seguir:

* *Um paradigma da OOP (programação orientada a objeto) familiar*. Atores são classes .NET que implementam interfaces de ator do .NET com propriedades e métodos assíncronos. Portanto, os atores aparecem aos programadores como objetos remotos cujos métodos e propriedades podem ser diretamente invocados. Isso fornece aos programadores o paradigma da OOP familiar, ativando chamadas do método em mensagens, roteando-as para os pontos de extremidade certos, invocando os métodos do ator de destino e lidando com falhas e situações extremas de uma forma completamente transparente.

* *Execução de atores single-thread*. O tempo de execução dos Reliable Actors garante que um ator nunca seja executado em mais de um thread por vez. Devido a isso e ao isolamento de cada ator de outros atores, os programadores nunca enfrentam simultaneidade no nível de ator. Os programadores nunca precisam usar bloqueios ou outros mecanismos de sincronização para controlar o acesso aos dados compartilhados. Este recurso sozinho torna o desenvolvimento de aplicativos distribuídos tratável para programadores não especialistas.

* *Ativação transparente*. O tempo de execução ativa um ator conforme necessário, somente quando há uma mensagem a ser processada. Isso separa claramente a noção da criação lógica de um ator, que é visível para o código do aplicativo e é controlada por ele, da ativação física do ator na memória, que é transparente para o aplicativo. A abordagem dos Reliable Actors é semelhante à memória virtual, já que decide quando "sair da página" (desativar) ou "entrar na página" (ativar) de um ator. O aplicativo tem acesso contínuo ao "espaço de memória" completo dos atores logicamente criados, estejam eles ou não na memória física em um determinado momento. A ativação transparente permite balanceamento de carga dinâmico e adaptável por meio de posicionamento e da migração de atores entre o pool de recursos de hardware.

* *Transparência da localização*. Uma referência de ator (objeto de proxy) que um programador usa para invocar os métodos de um ator ou passá-los para outros componentes que contenham apenas a identidade lógica do ator. A conversão de identidade de lógica do ator para o local físico e o roteamento correspondente de mensagens é feita de forma transparente no tempo de execução dos Reliable Actors. O código do aplicativo se comunica com atores alheios à sua localização física, que podem ser alterados ao longo do tempo devido a falhas ou ao gerenciamento de recursos. O local de um ator pode também pode mudar porque o ator é desativado no momento que é chamado.

* *Integração transparente com armazenamento persistente*. O modelo de programação dos Reliable Actors permite mapeamento declarativo do estado na memória de um ator para um repositório persistente. Eles sincronizam atualizações, garantindo, de forma transparente, que os chamadores recebam resultados somente depois que o estado persistente tenha sido atualizado com êxito.

* *Alta disponibilidade, suporte a failover e gerenciamento de ciclo de vida do aplicativo*. O estado de um ator é gerenciado pela plataforma e replicado de forma que ele possa ser restaurado se um nó do cluster falhar, por exemplo. O Service Fabric também gerencia o ciclo de vida do aplicativo e permite que o aplicativo seja atualizado sem tempo de inatividade. O modelo de programação dos Reliable Actors foi criado para orientar os programadores a serem bem-sucedidos ao escalar seus aplicativos e serviços por meio de várias ordens de grandeza. Isso é conseguido pela incorporação de padrões e práticas recomendadas comprovados e pelo fornecimento de uma implementação eficiente de funcionalidade de sistema de nível inferior. Veja alguns fatores importantes que permitem escalabilidade e desempenho de aplicativos do Service Fabric:

  * *Particionamento refinado implícito do estado do aplicativo*. Ao usar os atores como entidades diretamente endereçáveis, os programadores implicitamente desmembram o estado geral de seus aplicativos. Embora o modelo de programação dos Reliable Actors não determine o quão grande ou pequeno um ator deve ser, na maioria dos casos faz sentido ter um grande número relativo de atores – milhões ou mais. Cada um desses milhões de atores representa uma entidade natural do aplicativo, como uma conta de usuário ou ordem de compra. Considerando que os atores são individualmente endereçáveis e que seus respectivos locais físicos são abstraídos pelo tempo de execução, há uma grande flexibilidade no balanceamento de carga e ao lidar com pontos de acesso. Isso é feito de modo transparente e genérico sem qualquer preocupação dos desenvolvedores de aplicativo.

  * *Gerenciamento de recursos adaptável*. Os atores não fazem suposição sobre a localidade de outros atores com os quais interagem. Devido à transparência da localização, o tempo de execução pode gerenciar e ajustar a alocação de recursos de hardware disponíveis de maneira bastante dinâmica. Isso é feito pela tomada de decisões bem pensadas sobre a colocação e a migração dos atores no cluster de cálculo em resposta aos padrões de carga e comunicação, sem falha de solicitações de entrada. Ao criar várias réplicas de um ator específico, o tempo de execução pode aumentar a produtividade do ator sem fazer nenhuma alteração no código do aplicativo.

  * *Comunicação multiplexada*. Os Atores no Service Fabric têm pontos de extremidade lógicos e as mensagens entre eles são multiplexada entre um conjunto fixo de conexões físicas todos para todos (soquetes TCP). Isso permite que o tempo de execução hospede um número muito grande (milhões) de entidades endereçáveis sem sobrecarga do sistema operacional por ator. Além disso, a ativação ou desativação de um ator não incorre no custo de registrar ou cancelar o registro do ponto de extremidade físico, como uma porta TCP ou uma URL HTTP.

  * *Agendamento eficiente*. O tempo de execução dos Reliable Actors agenda a execução de um grande número de atores single-thread em um pool de threads personalizado usando um thread para cada núcleo de processador físico. O código de ator escrito em um estilo sem bloqueio com base em continuação (um requisito do modelo de programação), de modo que o código do aplicativo é executado de uma maneira "cooperativa" multithread muito eficiente sem nenhuma contenção. Isso permite que o sistema alcance uma alta produtividade e seja executado fazendo uso muito alto da CPU (90% ou mais) com grande estabilidade. O aumento no número de atores no sistema e da carga não resulta em threads adicionais nem em outros comandos básicos do sistema operacional, o que ajuda na escalabilidade de nós individuais e de todo o sistema.

  * *Assincronismo explícito*. O modelo de programação dos Reliable Actors torna explícita a natureza assíncrona de um aplicativo distribuído e orienta os programadores a escreverem códigos assíncronos sem bloqueio. Isso permite um alto grau de paralelismo distribuído e produtividade geral sem o uso explícito de multithreading.

<!---HONumber=AcomDC_0128_2016-->