<properties
   pageTitle="Introdução dos Atores Confiáveis do Service Fabric a padrões e antipadrões"
   description="Saiba mais sobre o modelo de programação dos Atores Confiáveis do Service Fabric e os padrões de design que funcionam bem com Atores."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="claudioc"/>

# Introdução aos padrões de design de Atores Confiáveis
O modelo de programação dos Atores Confiáveis do Service Fabric é uma plataforma criada em torno do modelo de ator para resolver problemas do mundo real em escala de nuvem. A Malha de Serviço do Azure é uma plataforma para a criação aplicativos altamente confiáveis e escalonáveis tanto para a nuvem e como para local que sejam fáceis de serem desenvolvidos e gerenciados. Este artigo destina-se a ser um documento prático sobre problemas práticos. Após a leitura por meio de vários padrões, você deve estar apto a entender como é possível usar o Modelo de Ator de Malha do Serviço para criar soluções de soluções de "empresa" ou "nuvem".

## Padrões
Nesta seção, listaremos um conjunto de padrões e cenários associados que aproveitamos durante nossos contatos com clientes. Esses padrões representam classes de problemas que são aplicáveis a uma ampla gama de soluções que nossos clientes estão criando o Microsoft Azure. Embora os cenários estejam baseados em casos reais, eliminamos a maioria das preocupações específicas de domínio para tornar os padrões mais claros para o leitor. Você pode achar que grande parte do código de exemplo é simples ou óbvio. Estamos incluindo o código para esgotarmos todas as possibilidade e não porque seja algo particularmente inteligente ou impressionante.

Os padrões apresentados neste artigo não têm o objetivos de serem abrangentes ou canônicos. Alguns desenvolvedores podem resolver o mesmo problema ou padrão de formas diferentes do padrão que apresentamos.

[Padrão: cache inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Padrão: redes e gráficos distribuídos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Padrão: controle de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Padrão: composição de serviço com estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Padrão: Internet das Coisas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Padrão: computação distribuída](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alguns antipadrões](service-fabric-reliable-actors-anti-patterns.md)

### Saiba mais sobre os atores, um breve histórico
O [artigo](http://dl.acm.org/citation.cfm?id=1624804) escrito por Hewitt et al. que é a origem do modelo de ator foi publicado em 1973, embora o modelo de ator tenha ganhado mais atenção somente mais recentemente como um meio de lidar com simultaneidade e a complexidade em sistemas distribuídos. O modelo de ator oferece suporte a objetos individuais refinados — atores — que são isolados uns dos outros. Eles se comunicam através de mensagens assíncronas que passam, o que permite a comunicação direta entre os atores. Um ator é executado com semântica de thread único. Juntamente com o encapsulamento do estado do ator e o isolamento de outros atores, isso simplifica a criação de sistemas altamente paralelos, eliminando as preocupações de simultaneidade do código do ator. Os atores são criados dinamicamente no pool de recursos de hardware disponíveis.

[Erlang](http://www.erlang.org/) é a implementação mais popular do modelo de ator. Os desenvolvedores começaram a redescobrir o modelo de ator, o que estimulou o interesse renovado na Erlang e na criação de novas soluções semelhantes à Erlang: atores em [Scala](http://www.scala-lang.org/), [Akka](http://akka.io), [Akka.net](http://getakka.net/), [DCell](http://research.microsoft.com/pubs/75988/dcell.pdf).

## Breve visão geral da Malha de Serviço do Azure
Os Atores da Malha do Azure constituem uma implementação do modelo de ator que usa algumas ideias da Erlang e sistemas de objetos distribuídos, adiciona uma camada de indireção do ator e os expõe em um modelo integrado de programação que aproveita a plataforma de Malha do Serviço do Azure.

Os principais benefícios de Atores de Malha do Azure são: 1) **produtividade do desenvolvedor**, mesmo para os programadores não-especialistas; e 2) **escalabilidade transparente por padrão** sem nenhum esforço especial do programador. Atores de Malha do Azure é uma biblioteca do .NET executado no topo da Malha do Azure e das ferramentas que facilitam muito o desenvolvimento de aplicativos distribuídos complexos e tornam os aplicativos resultantes dimensionáveis por projeto. Expandimos cada um desses benefícios abaixo. O modelo de programação de Atores de Malha do Azure aumenta a produtividade dos programadores especialistas e não-especialistas, fornecendo os seguintes abstrações chave, garantias e serviços do sistema.

* * Paradigma da programação orientada a objeto (OOP) familiar*. Atores são classes .NET que implementam interfaces de ator do .NET com propriedades e métodos assíncronos. Portanto, os atores aparecem ao programador como objetos remotos cujos métodos/propriedades podem ser diretamente chamados. Isso fornece ao programador o paradigma OOP familiar, ativando chamadas do método em mensagens, roteá-los para os pontos de extremidade certos, invocando os métodos do ator de destino e lidando com falhas e situações extremas de uma forma completamente transparente.

* *Execução de thread único de atores.* O tempo de execução da Malha do Azure garante que um ator nunca seja executado em mais de um thread por vez. Combinado com o isolamento de outros atores, o programador nunca enfrenta simultaneidade no nível do ator e, portanto, nunca precisa usar bloqueios ou outros mecanismos de sincronização para controlar o acesso a dados compartilhados. Este recurso sozinho torna o desenvolvimento de aplicativos distribuídos tratável para programadores não-especialistas.

* *Ativação transparente.* O tempo de execução da Malha do Azure ativa um ator conforme necessário, somente quando há uma mensagem a ser processada. Isso separa claramente a noção de criação de lógica de um ator, que é visível para o código do aplicativo e é controlada por ele, da ativação física do ator na memória, que é transparente para o aplicativo. Os Atores da Malha do Azure são semelhantes à memória virtual nos sentido de que eles decidem quando fazer um “page out” (desativar) ou um “page in” (ativar) de um ator; o aplicativo tem acesso contínuo ao “espaço de memória” total dos atores criados logicamente, estejam eles ou não na memória em um ponto no tempo específico. A ativação transparente permite balanceamento de carga dinâmico e adaptável por meio de posicionamento e da migração de atores entre o pool de recursos de hardware.

* *Transparência da localização.* Uma referência de ator (objeto de proxy) que o programador usa para chamar os métodos do ator ou passar para outros componentes que contenham apenas a identidade lógica do ator. A conversão de identidade de lógica do ator para o local físico e o roteamento correspondente de mensagens é feita de forma transparente no tempo de execução da Malha do Azure. O código do aplicativo se comunica com atores alheios à sua localização física, que podem ser alterados ao longo do tempo devido a falhas ou gerenciamento de recursos, ou porque um ator está desativado no momento em que ele é chamado.

* *Integração transparente com armazenamento persistente.* Os Atores de Malha do Azure permitem mapeamento declarativo do estado na memória dos atores para fins de armazenamento persistente. Eles sincronizam atualizações, garantindo, de forma transparente, que os chamadores recebam resultados somente depois que o estado persistente tenha sido atualizado com êxito.

* *Alta disponibilidade, suporte de failover e gerenciamento de ciclo de vida do aplicativo.* O estado dos Atores de Malha do Azure é gerenciado pela plataforma e replicado de forma que ele possa ser restaurado se um nó do cluster falhar, por exemplo. A Malha de Serviço do Azure também gerencia o ciclo de vida do aplicativo e possibilita atualizações de aplicativo com tempo de inatividade zero. O modelo de programação dos Atores de Malha do Azure foi criado para orientar os programadores a buscar um caminho de sucesso provável ao dimensionar seus aplicativos ou serviços por meio de várias ordens de magnitude. Isso é feito pela incorporação de padrões e práticas recomendadas comprovadas e pelo fornecimento de uma implementação eficiente de funcionalidade de sistema de nível inferior. Aqui estão alguns fatores importantes que permitem o dimensionamento e o desempenho de aplicativos de Malha do Azure.

* *Particionamento refinado implícito do estado do aplicativo.* Ao usar os atores como entidades diretamente endereçáveis, os programadores implicitamente desmembram o estado geral de seus aplicativos. Embora o modelo de programação dos Atores de Malha do Azure não prescreva o quão grande ou pequeno um ator deve ser, na maioria dos casos faz sentido ter um número grande relativo de atores – milhões ou mais – com cada um representando uma entidade natural do aplicativo, como uma conta de usuário, uma ordem de compra, etc. Com atores sendo individualmente endereçáveis e sua localização física sendo abstraída pelo tempo de execução, os Atores de Malha do Azure têm grande flexibilidade para balancear de carga e lidar com pontos de acesso de modo transparente e genérico sem nenhuma preocupação por parte do desenvolvedor do aplicativo.

* *Gerenciamento de recursos adaptável.* Com atores que não fazem nenhuma suposição sobre a localidade de outros atores com os quais eles interagem e por causa da transparência da localização, o tempo de execução de Malha do Azure pode gerenciar e ajustar a alocação de recursos de hardware disponíveis de forma muito dinâmica ao tomar decisões refinadas sobre o posicionamento/migração de atores em todo o cluster de computação como uma reação para padrões de carga e de comunicação sem solicitações de entrada de falha. Ao criar várias réplicas de um ator específico, o tempo de execução pode aumentar a taxa de transferência do ator, se necessário, sem fazer nenhuma alteração ao código do aplicativo.

* *Comunicação multiplexada.* Os Atores na Malha do Azure têm pontos de extremidade lógicos e as mensagens entre eles são multiplexada entre um conjunto fixo de conexões físicas todos para todos (soquetes TCP). Isso permite que o tempo de execução de Atores de Malha do Azure hospedem um número muito grande (milhões) de entidades endereçáveis com sobrecarga zero do sistema operacional por ator. Além disso, a ativação/desativação de um ator não aumentam o custo de registrar/cancelar o registro do ponto de extremidade físico, como uma porta TCP ou uma URL HTTP.

* *O agendamento eficiente.* O tempo de execução de Malha do Azure agenda a execução de um grande número de atores de thread único em um pool de threads personalizado com um thread por núcleo de processador físico. Com o código de ator escrito em um estilo sem bloqueio baseado com base em continuação (um requisito do modelo de programação de Atores de Malha do Azure) o código do aplicativo é executado em uma maneira "cooperativa" multithread muito eficiente sem nenhuma contenção. Isso permite que o sistema alcance uma alta taxa de transferência e seja executado em uma CPU de utilização muito alta (até 90 + %) com grande estabilidade. O fato de que um aumento no número de atores no sistema e da carga não resulte em threads adicionais ou outras primitivas de sistema operacional ajuda no dimensionamento de nós individuais e todo o sistema.

* *Assincronismo explícito.* O modelo de programação de Atores de Malha do Azure torna explícita a natureza assíncrona de um aplicativo distribuído e orienta os programadores a produzirem códigos assíncronos sem bloqueio. Isso permite um alto grau de paralelismo distribuído e produtividade geral sem o uso explícito de vários threads.

<!---HONumber=06-->