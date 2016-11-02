<properties
   pageTitle="Noções básicas sobre microsserviços | Microsoft Azure"
   description="Uma visão geral sobre o motivo de a criação de aplicativos de nuvem com uma abordagem de microsserviços é importante para o desenvolvimento moderno de aplicativos, e como o Azure Service Fabric oferece uma plataforma para conseguir isso"
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
   ms.date="09/20/2016"
   ms.author="mfussell"/>


# <a name="why-a-microservices-approach-to-building-applications?"></a>Por que usar uma abordagem de microsserviço para construir aplicativos?
Para nós, desenvolvedores de software, não há qualquer novidade no modo como pensamos sobre a decomposição de um aplicativo em partes componentes. É o paradigma central da orientação de objetos, das abstrações de software e da “componentização”. Atualmente, essa fatoração tende a assumir a forma de classes e interfaces entre as camadas de tecnologia e as bibliotecas compartilhadas. Em geral, uma abordagem em camadas é obtida com um armazenamento de back-end, lógica de negócios de camada intermediária e uma IU de front-end. O que *mudou* nos últimos anos é que nós, desenvolvedores, estamos construindo aplicativos distribuídos para a nuvem orientados pelos negócios.

Entre as necessidades variáveis dos negócios estão:

- Criação e operação de um serviço em escala para alcançar os clientes em novas regiões geográficas (por exemplo).
- Entrega mais ágil de recursos a fim de conseguir responder rapidamente às demandas dos clientes.
- Melhor utilização de recursos para reduzir os custos.

Essas necessidades comerciais estão afetando o *modo* como criamos os aplicativos.

Para obter mais informações sobre a abordagem do Azure aos microsserviços, leia [Microsserviços: uma revolução de aplicativo proporcionada pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs.-microservice-design-approach"></a>Abordagem de design monolítico versus microsserviços
Todos os aplicativos evoluem ao longo do tempo. Os aplicativos bem-sucedidos evoluem sendo úteis às pessoas. Aplicativos sem sucesso não evoluem e eventualmente tornam-se obsoletos. A pergunta é: quanto você sabe sobre os requisitos de hoje e quais serão no futuro? Por exemplo, digamos que você esteja criando um aplicativo de relatório para um departamento. Você tem certeza que o aplicativo permanecerá dentro do escopo de sua empresa e que os relatórios serão de curta duração. Sua escolha de abordagem é diferente de, digamos, criar um serviço de entrega de conteúdo de vídeo para dezenas de milhões de clientes. Às vezes, lançar algo como prova de conceito é o fator determinante, com a noção de que o aplicativo pode ser reprojetado posteriormente. Não há muito sentido em trabalhar muito em algo que nunca será usado. É a compensação comum da engenharia. Por outro lado, quando as empresas falam sobre criação para a nuvem, a expectativa é de crescimento e uso. O problema é que o crescimento e o dimensionamento são imprevisíveis. Gostaríamos de ser capazes de criar um protótipo rapidamente e saber, ao mesmo tempo, que estamos no caminho certo para lidar com o sucesso no futuro. Essa é a abordagem de inicialização simples: compilar, medir, aprender, iterar.

Durante a era cliente-servidor, nossa tendência era se concentrar na construção de aplicativos em camadas usando tecnologias específicas em cada camada. O termo “aplicativo monolítico" surgiu para essas abordagens. As interfaces tendiam a ficar entre as camadas e um design mais rigidamente acoplado era usado entre os componentes em cada camada. Os desenvolvedores projetavam e fatoravam as classes compiladas em bibliotecas e as reuniam em alguns arquivos executáveis e DLLs. Há benefícios em tal abordagem de design monolítico. Em geral, é mais simples de projetar e tem chamadas mais rápidas entre os componentes, pois elas são normalmente por IPC. Além disso, todos testam um único produto, que tende a ser mais eficiente em termos de recursos e pessoas. A desvantagem é que ocorre um acoplamento rígido entre as camadas e você não pode dimensionar os componentes individuais. Se você precisar executar correções ou atualizações, precisará esperar que as demais pessoas concluam o teste. É mais difícil de ser ágil.

Os microsserviços resolvem essas desvantagens e alinham-se melhor com os requisitos de negócios anteriores, mas eles também têm vantagens e desvantagens. Os benefícios dos microsserviços são que cada um normalmente encapsula uma funcionalidade de negócios mais simples, que você dimensiona, testa, implanta e gerencia de forma independente. Uma vantagem importante de uma abordagem de microsserviço é que as equipes são mais orientadas por cenários de negócios do que por tecnologia, o que era encorajado pela abordagem em camadas. Na prática, as equipes menores desenvolvem um microsserviço baseado em um cenário de cliente usando qualquer tecnologia escolhida. Em outras palavras, a organização não precisa padronizar a tecnologia para manter aplicativos monolíticos. As equipes individuais que possuem serviços podem fazer o que fizer sentido para elas com base na experiência da equipe ou no que é mais apropriado para o problema a ser resolvido. Na prática, ter um conjunto recomendado de tecnologias, como determinado armazenamento NoSQL ou uma estrutura de aplicativo Web, é preferível.

A desvantagem dos microsserviços é gerenciar o crescente número de entidades separadas e lidar com implantações mais complexas e controle da versão. O tráfego da rede entre os microsserviços aumenta, bem como as latências de rede correspondentes. Ter muitos serviços informais e granulares é uma receita desastrosa para o desempenho. Sem ferramentas para ajudar a exibir essas dependências, é difícil "ver" todo o sistema. São os padrões que fazem a abordagem do microsserviço funcionar, concordando sobre como se comunicar e sendo tolerante apenas às coisas que você precisa de um serviço, em vez de contratos rígidos. É importante definir esses contatos antecipadamente no design, pois os serviços são atualizados de forma independente. Outra descrição inventada para a criação com uma abordagem de microsserviços é "SOA refinada".


***Em sua forma mais simples, a abordagem de design com microsserviços se resume a uma federação dissociada de serviços, com alterações independentes e padrões acordados para a comunicação.***


Conforme mais aplicativos de nuvem são produzidos, as pessoas descobrem que essa decomposição do aplicativo geral em serviços independentes, voltados para o cenário, é uma melhor abordagem de longo prazo.

## <a name="comparison-between-application-development-approaches"></a>Comparação entre abordagens de desenvolvimento de aplicativos

![Desenvolvimento de aplicativos da plataforma Service Fabric][Image1]

1. Um aplicativo monolítico contém uma funcionalidade específica ao domínio e é normalmente dividido por camadas funcionais, como Web, negócios e dados.

2. Você dimensiona os aplicativos monolíticos clonando-os em vários servidores/VMs/contêineres.

3. Um aplicativo de microsserviço separa a funcionalidade em serviços menores separados.

4. Essa abordagem pode ser dimensionada por meio da implantação de cada serviço de forma independente, criando instâncias desses serviços entre servidores/VMs/contêineres.


Criar usando uma abordagem de microsserviço não é uma solução para todos os projetos, mas ela se alinha mais aos objetivos comerciais descritos anteriormente. Começar com uma abordagem monolítica poderá ser aceitável se você souber que posteriormente terá a oportunidade de refazer o código em um design de microsserviço, se for necessário. Normalmente, você começa com um aplicativo monolítico e o desmembra em etapas, começando com as áreas funcionais que precisam ser mais escalonáveis ou ágeis.

Para resumir, a abordagem de microsserviço é para compor o aplicativo com muitos serviços menores.  Os serviços são executados em contêineres implantados em um cluster de máquinas. Equipes menores desenvolvem um serviço que foca em um cenário e testa, cria a versão, implanta e dimensiona independentemente cada serviço para que o aplicativo inteiro possa desenvolver-se.

## <a name="what-is-a-microservice?"></a>O que é um microsserviço?

Há definições diferentes de microsserviços, e uma pesquisa na Internet proporciona bons recursos que fornecem seus próprios pontos de vista e definições. No entanto, a maioria das características dos microsserviços descritas a seguir é aceita:

- Abrange um cenário de cliente ou comercial. Qual é o problema que você está solucionando?
- Desenvolvidos por uma pequena equipe de engenharia.
- Escritos em qualquer linguagem de programação e usam qualquer estrutura.
- Consistem em código e (opcionalmente) estado, com versão, implantação e dimensionamento independentes.
- Interagem com outros microsserviços em protocolos e interfaces bem definidos.
- Têm nomes exclusivos (URLs) usados para determinar seus locais.
- Permanecem consistentes e disponíveis na presença de falhas.

Você pode resumir essas características em:

***Os aplicativos de microsserviços são compostos por serviços pequenos e focados no cliente, com controle de versão independente e escalonáveis que se comunicam entre si por meio de protocolos padrão com interfaces bem definidas.***


Abordamos os primeiros dois pontos na seção anterior e agora expandimos e esclarecemos os outros.

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Escritos em qualquer linguagem de programação e usam qualquer estrutura
Como desenvolvedores, deveríamos ser livres para escolher qualquer linguagem ou estrutura que quisermos dependendo de nossas habilidades ou das necessidades do serviço. Em alguns serviços, você pode valorizar os benefícios do desempenho do C++ acima de tudo. Em outros serviços, a facilidade de desenvolvimento gerenciado no C# ou Java pode ser mais importante. Em alguns casos, talvez seja necessário usar um uma biblioteca de terceiros, uma tecnologia de armazenamento de dados ou meios de expor o serviço aos clientes.

Após a escolha de uma tecnologia, chega-se ao gerenciamento operacional ou do ciclo de vida e ao dimensionamento do serviço.

### <a name="allows-code-and-state-to-be-independently-versioned,-deployed,-and-scaled"></a>Permite que o código e o estado tenham versão, implantação e dimensionamento independentes  

Não importa como você escolhe escrever os microsserviços, o código e, opcionalmente, o estado devem implantar, atualizar e dimensionar-se de forma independente. Na verdade, isso é um dos problemas mais difíceis de resolver, pois é uma questão de escolha das tecnologias. Para o dimensionamento, entender como particionar (ou fragmentar) o código e o estado é um desafio. Quando o código e o estado usam tecnologias separadas (que, atualmente, é o que costuma ser feito), os scripts de implantação de seu microsserviço precisam ser capazes de lidar com o dimensionamento de ambos. Isso está relacionado também à agilidade e à flexibilidade; portanto, você pode atualizar alguns microsserviços sem precisar atualizar todos de uma vez.
Voltando à questão da abordagem monolítica versus microsserviço por um momento, o diagrama a seguir mostra as diferenças nas abordagens para armazenar o estado.

#### <a name="state-storage-between-application-styles"></a>Armazenamento de estado entre os estilos de aplicativo
![Armazenamento de estado da plataforma Service Fabric][Image2]

***À esquerda está a abordagem monolítica com um banco de dados individual e camadas de tecnologias específicas.***

***À direita está a abordagem de microsserviços, um gráfico de microsserviços interconectados, no qual o estado normalmente tem como escopo o microsserviço e várias tecnologias são usadas.***

Em uma abordagem monolítica, normalmente há um único banco de dados usado pelo aplicativo. A vantagem é que se trata de um único local, facilitando a implantação. Cada componente pode ter uma única tabela para armazenar seu estado. As equipes precisam ser rigorosas na separação do estado, o que é um desafio. Há, inevitavelmente, tentações para adicionar uma nova coluna a uma tabela de clientes existente, para fazer uma associação entre as tabelas e para criar dependências na camada de armazenamento. Quando isso acontecer, você não poderá dimensionar os componentes individuais. Na abordagem dos microsserviços, cada serviço gerencia e armazena seu próprio estado. Cada serviço é responsável por dimensionar o estado e o código juntos para atender às demandas do serviço. A desvantagem é que quando houver necessidade de criar exibições, ou consulta, dos dados de seus aplicativos, você precisará consultar esses armazenamentos de estado diferentes. Normalmente, isso é resolvido com um microsserviço separado que cria um modo de exibição em uma coleção de microsserviços. Se você precisar executar várias consultas ad hoc nos dados, cada microsserviço deverá considerar a gravação de seus dados em um serviço de data warehouse para análise offline.

O controle da versão é específico da versão implantada de um microsserviço, de modo que diversas versões diferentes são implantadas e executadas lado a lado. O controle da versão aborda os cenários nos quais uma versão mais recente de um microsserviço falha durante a atualização e precisa reverter para uma versão anterior. O outro cenário para controle de versão é a realização de um teste ao estilo A/B, no qual usuários diferentes experimentam versões diferentes do serviço. Por exemplo, é comum atualizar um microsserviço para um conjunto específico de clientes testar a nova funcionalidade, antes de fazê-lo mais amplamente. Após o gerenciamento do ciclo de vida de microsserviço, isso nos leva à comunicação entre eles.


### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interage com outros microservices em protocolos e interfaces bem-definidos

Este tópico precisa de um pouco atenção aqui, já que não há uma literatura abrangente sobre a arquitetura orientada a serviços publicada nos últimos 10 anos que descreve os padrões de comunicação. Em geral, a comunicação do serviço usa uma abordagem do REST com os protocolos TCP e HTTP, e XML ou JSON como formato de serialização. Do ponto de vista da interface, isso se resume à adoção da abordagem de design da Web. Mas não há nada que impeça você de usar protocolos binários ou seus próprios formatos de dados. Esteja preparado para a possibilidade de as pessoas terem mais dificuldade em usar seus microsserviços se eles estiverem abertamente disponíveis.

### <a name="has-a-unique-name-(url)-used-to-resolve-its-location"></a>Tem um nome exclusivo (URL) usado para determinar sua localização

Lembra-se de como continuamos afirmando que a abordagem de microsserviço é como a Web? Assim como a Web, seu microsserviço precisa ser endereçável em qualquer lugar que esteja sendo executado. Se você ficar pensando em máquinas, e qual delas está executando um determinado microsserviço, as coisas sairão dos trilhos rapidamente. Da mesma forma como o DNS resolve uma URL específica para determinado computador, seu microsserviço precisa ter um nome exclusivo para que a localização atual possa ser descoberta. Os microsserviços precisam de nomes endereçáveis que os tornem independentes da infraestrutura na qual estão executando. Isso significa que há uma interação entre como seu serviço é implantado e como ele é descoberto, já que deve haver um registro do serviço. Igualmente, quando um computador falha no registro, o serviço deve informar onde este está sendo executado agora. Isso nos leva ao próximo tópico: resiliência e consistência.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Permanece consistente e disponível na presença de falhas

Lidar com falhas inesperadas é um dos problemas mais difíceis de se resolver, especialmente em um sistema distribuído. Boa parte do código que escrevemos, como desenvolvedores, serve para lidar exceções, e também é nisso que a maior parte do tempo é gasto durante o teste. Mas o problema é mais complicado do que escrever código para tratar de falhas. O que acontece quando o computador onde o microserviço está em execução falha? Você não precisará apenas detectar essa falha do microsserviço (um problema difícil por si só), mas também precisará de algo para reiniciar o microsserviço. Um microsserviço precisa ser resiliente a falhas e reiniciar com frequência em outra máquina por motivos de disponibilidade. Com isso, também há a pergunta: qual estado foi salvo em nome do microsserviço, de onde ele pode recuperar esse estado, e se ele é capaz de reiniciar com êxito. Em outras palavras, é necessário haver resiliência na computação (o processo reinicia) e no estado ou nos dados (sem perda de dados e os dados permanecem consistentes).

Os problemas de resiliência são agravados durante outros cenários, por exemplo, quando falhas ocorrem durante uma atualização do aplicativo. O microsserviço, trabalhando com o sistema de implantação, não precisa fazer a recuperação. Ele também precisa decidir se pode continuar a seguir com a versão mais recente ou se reverte para uma versão anterior a fim de manter um estado consistente. Perguntas como se há máquinas suficientes para continuar avançando e como recuperar versões anteriores do microsserviço precisam ser levadas em consideração. Isso exige que o microsserviço emita informações de integridade para poder tomar essas decisões.

### <a name="reports-health-and-diagnostics"></a>Relatórios de integridade e diagnóstico

Pode parecer óbvio e muitas vezes é ignorado, mas é essencial que um microsserviço relate sua integridade e diagnóstico. Caso contrário, há pouca percepção de uma perspectiva de operações. Correlacionar os eventos de diagnóstico em um conjunto de serviços independentes e lidar com a defasagem do relógio do computador para entender a ordem dos eventos é um desafio. Da mesma forma como você interage com um microsserviço nos protocolos acordados e formatos de dados, surge a necessidade de padronização de como registrar os eventos de integridade e diagnóstico que, em última análise, terminam em um armazenamento de eventos para consultar e exibir. Em uma abordagem de microsserviços, é importante que as diferentes equipes concordem com um único formato de log.  Precisa ser uma abordagem consistente para exibir os eventos de diagnóstico no aplicativo inteiro.

Integridade é diferente de diagnóstico. Integridade significa que o microsserviço reporta seu estado atual para tomar as devidas ações. Um bom exemplo é trabalhar com mecanismos de atualização e implantação para manter a disponibilidade. Um serviço pode não ser íntegro no momento devido a uma falha de processo ou reinicialização do computador, mas ainda é operacional. A última coisa de que você precisa é piorar a situação executando uma atualização. A melhor abordagem é fazer uma investigação primeiro ou aguardar até que o microsserviço se recupere. Os eventos de integridade de um microsserviço permitem tomar decisões informadas e ajudam realmente a criar serviços que se recuperam automaticamente.

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric como uma plataforma de microsserviço

O Service Fabric do Azure nasceu da transição da Microsoft do fornecimento de produtos prontos, que eram normalmente monolíticos em estilo, para o fornecimento de serviços. A experiência de criar e operar serviços grandes, como bancos de dados do SQL Azure e Banco de Dados de Documentos, modelou o Service Fabric. A plataforma evoluiu ao longo do tempo conforme mais e mais serviços adotaram-na. Acima de tudo, o Service Fabric precisava ser executado em qualquer lugar: não apenas no Azure, mas também nas implantações autônomas do Windows Server.

***O objetivo do Service Fabric é resolver os problemas difíceis de criação e execução de um serviço, e utilizar os recursos da infraestrutura com eficiência para que as equipes possam resolver os problemas de negócios usando uma abordagem de microsserviços.***

O Service Fabric fornece duas áreas amplas para ajudar você com a criação de aplicativos usando uma abordagem de microsserviço:

- Uma plataforma que fornece serviços do sistema para implantar, atualizar, detectar e reiniciar os serviços com falha, descobrir a localização do serviço, gerenciar o estado e monitorar a integridade. Esses serviços do sistema permitem, de fato, muitas características dos microsserviços descritos acima.

-  APIs de programação, ou estruturas, para ajudá-lo a criar aplicativos como microsserviços: [atores confiáveis e serviços confiáveis](service-fabric-choose-framework.md). Obviamente, você pode usar qualquer código de sua escolha para criar seu microsserviço. Mas essas APIs facilitam o trabalho e se integram com a plataforma em um nível mais profundo. Dessa forma, por exemplo, você pode obter informações de integridade e de diagnóstico ou pode tirar proveito da alta disponibilidade interna.

***O Service Fabric não se importa com o modo de criação de seu serviço, e você pode usar qualquer tecnologia. No entanto, fornece APIs de programação internas que facilitam a criação de microsserviços.***

### <a name="are-microservices-right-for-my-application?"></a>Os microsserviços são ideais para meu aplicativo?

Talvez. O que experimentamos foi que quando mais equipes na Microsoft começaram a construir para a nuvem por motivos comerciais, muitas perceberam os benefícios de adotar uma abordagem semelhante ao microsserviço. O Bing, por exemplo, vem desenvolvendo os microsserviços em pesquisa por anos. Para as outras equipes, a abordagem dos microsserviços foi nova. Elas descobriram que havia problemas difíceis de resolver fora de suas principais áreas de força. É por isso que o Service Fabric ganhou adeptos como a tecnologia preferida para a criação de serviços.

O objetivo do Service Fabric é reduzir a complexidade da criação de aplicativos com uma abordagem de microsserviço para que você não precise passar por diversas reformulações dispendiosas. Comece pequeno, expanda quando necessário, substitua serviços, adicione serviços novos e evolua de acordo com o uso do cliente. Essa é a abordagem. Também sabemos que, na realidade, há muitos outros problemas que ainda precisam ser resolvidos para tornar os microsserviços mais acessíveis para a maioria dos desenvolvedores. Os contêineres e o modelo de programação de ator são exemplos de pequenas etapas nessa direção, e temos certeza de que outras inovações surgirão para tornar isso mais fácil.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

* Para mais informações:
    * [Visão geral da terminologia do Service Fabric](service-fabric-technical-overview.md)
    * [Microsserviços: uma revolução de aplicativo proporcionada pela nuvem](https://azure.microsoft.com/en-us/blog/microservices-an-application-revolution-powered-by-the-cloud/)


[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png



<!--HONumber=Oct16_HO2-->


