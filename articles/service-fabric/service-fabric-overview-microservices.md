<properties
   pageTitle="Noções básicas sobre microsserviços | Microsoft Azure"
   description="Uma visão geral sobre o motivo de a criação de aplicativos de nuvem com uma abordagem de microsserviços é importante para o desenvolvimento moderno de aplicativos, e como o Azure Service Fabric oferece uma plataforma para conseguir isso"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/24/2016"
   ms.author="msfussell"/>

# Por que usar uma abordagem de microsserviço para construir aplicativos?
Para nós, desenvolvedores de software, não há qualquer novidade no modo como pensamos sobre a decomposição de um aplicativo em partes componentes. É o paradigma central da orientação de objetos, das abstrações de software e da “componentização”. Hoje, essa fatoração tende a assumir a forma de classes e interfaces entre bibliotecas compartilhadas e camadas de tecnologia, normalmente por meio de uma abordagem em camadas, com um repositório back-end, lógica de negócios de camada intermediária e uma interface de usuário front-end. O que *mudou* nos últimos anos é que nós, desenvolvedores, estamos construindo aplicativos distribuídos para a nuvem orientados pelos negócios.

Entre as necessidades variáveis dos negócios estão:

- A necessidade de construir e operar um serviço em larga escala a fim de possibilitar um alcance maior de clientes em novas regiões geográficas ou sem precisar implantar nos locais dos clientes, por exemplo.
- Entrega mais ágil de recursos a fim de conseguir responder rapidamente às demandas dos clientes.
- Melhor utilização de recursos para reduzir os custos.

Essas necessidades comerciais estão afetando o *modo* como criamos os aplicativos.

Para obter mais informações sobre a abordagem do Azure aos microsserviços, leia: [Microsserviços: uma revolução de aplicativo proporcionada pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)

## Abordagem de design monolítico versus microsserviços
Todos os aplicativos evoluem ao longo do tempo. Os aplicativos bem-sucedidos evoluem sendo úteis às pessoas. Aplicativos sem sucesso não evoluem e eventualmente tornam-se obsoletos. A pergunta principal é: o que você realmente sabe sobre suas necessidades atuais e quais serão essas necessidades no futuro? Por exemplo, se você estiver construindo um aplicativo de relatórios para um departamento e tiver certeza de que isso permanecerá dentro do escopo de sua empresa, e de que os relatórios terão vida curta, sua opção de abordagem será diferente de, vamos supor, construir um serviço para entrega de conteúdo em vídeo para dezenas de milhões de clientes. Às vezes, lançar algo como prova de conceito é o fator determinante, com a noção de que o aplicativo pode ser reprojetado posteriormente. Não há muito sentido em trabalhar muito em algo que nunca será usado. É a compensação comum da engenharia. Por outro lado, quando as empresas falam sobre criação para a nuvem, a expectativa é de crescimento e uso. O problema é que o crescimento e o dimensionamento são imprevisíveis. Gostaríamos de ser capazes de criar um protótipo rapidamente e saber, ao mesmo tempo, que estamos no caminho certo para lidar com o sucesso no futuro. Essa é a abordagem de inicialização simples: compilar, medir, aprender, iterar.

Durante a era cliente-servidor, nossa tendência era se concentrar na construção de aplicativos em camadas usando tecnologias específicas em cada camada. O termo “aplicativo monolítico" surgiu para essas abordagens. As interfaces tendiam a ser entre as camadas, e usava-se geralmente um design mais rigidamente acoplado entre os componentes em cada nível. Os desenvolvedores projetavam e fatoravam com classes compiladas em bibliotecas e as reuniam em alguns arquivos .exe e .dll. Há benefícios em tal abordagem de design monolítico. Pode ser mais simples de projetar e as chamadas ficam mais rápidas entre componentes, pois eles são normalmente sobre IPC. Além disso, todos testam um único produto e isso tende a ser mais eficiente em termos de recursos e pessoas. As desvantagens são que o aplicativo é acoplado rigidamente nas camadas e você não pode redimensionar os componentes individuais. Se você precisar executar correções ou atualizações, precisará esperar que os demais concluam o teste; é mais difícil ter agilidade.

Os microsserviços resolvem esses lados negativos e atendem de forma mais adequada às necessidades comerciais descritas acima. Porém, eles também têm seus prós e contras. Os benefícios dos microsserviços estão no fato de que cada um deles normalmente envolve uma funcionalidade comercial mais simples. Eles podem ser escalados vertical ou horizontalmente, testados, implantados e gerenciados independentemente. Uma vantagem importante de uma abordagem de microsserviço é que as equipes tendem a ser mais orientadas por cenários de negócios que por tecnologia, o que era encorajado pela abordagem em camadas. Na prática, isso significa que as equipes menores desenvolvem um microsserviço baseado em um cenário de cliente usando qualquer tecnologia que escolherem. Em outras palavras, a organização não precisa padronizar a tecnologia para manter monolitos. Além disso, as equipes individuais que possuem serviços podem fazer o que fizer sentido para elas com base na experiência da equipe ou no que é mais apropriado para o problema que o serviço está tentando resolver. Na prática, ter um conjunto recomendado de tecnologias, como determinado repositório NoSQL ou uma estrutura de aplicativo Web, é preferível, claro. A desvantagem dos microsserviços está em gerenciar um número cada vez maior de entidades separadas, lidar com implantações cada vez mais complexas e controlar as versões, tendo mais tráfego de rede entre os microsserviços, e as latências de rede correspondentes. Ter muitos serviços informais, muito granulares, é uma receita desastrosa para o desempenho. Sem ferramentas para ajudar a exibir essas dependências, é difícil "ver" todo o sistema. Em última análise, os padrões tornam a abordagem de microsserviço funcional, concordando sobre como se comunicar e sendo tolerante apenas às coisas que você precisa de um serviço, ao contrário de contratos rígidos. É importante definir esses contatos antecipadamente no design, pois os serviços serão atualizados de forma independente. Outra descrição cunhada durante a criação de uma abordagem de microsserviço é "SOA refinada".


***Em sua forma mais simples, a abordagem de design com microsserviços se resume a uma federação dissociada de serviços, com alterações independentes e padrões acordados para comunicação.***


Conforme mais aplicativos em nuvem são produzidos, mais pessoas estão descobrindo que essa decomposição do aplicativo geral em serviços independentes focados na situação é uma abordagem melhor de longo prazo.
## Comparação entre abordagens de desenvolvimento de aplicativos

![Desenvolvimento de aplicativos da plataforma Service Fabric][Image1]

1. Um aplicativo monolítico contém uma funcionalidade específica ao domínio e é normalmente dividido por camadas funcionais, como Web, negócios e dados.

2. Você dimensiona os aplicativos monolíticos clonando-os em vários servidores/VMs/contêineres.

3. Um aplicativo de microsserviço separa a funcionalidade em serviços menores separados.

4. Essa abordagem pode ser dimensionada por meio da implantação de cada serviço de forma independente, criando instâncias desses serviços entre servidores/VMs/contêineres.


Criar usando uma abordagem de microsserviço não é uma solução para todos os projetos, mas ela se alinha mais aos objetivos comerciais descritos anteriormente. Além disso, começar com uma abordagem monolítica pode ser aceitável se você souber que posteriormente terá a oportunidade de refazer o código em um design de microsserviço, se isso for necessário. Normalmente, você começa com um aplicativo monolítico e o desmembra em etapas, começando com as áreas funcionais que precisam ser mais escalonáveis ou ágeis.

Para resumir, a abordagem de microsserviço é compor o aplicativo de muitos serviços menores em execução em contêineres implantados em um cluster de computadores. Cada serviço é desenvolvido por uma equipe menor que se concentra em um cenário, e cada serviço é testado, implantado, dimensionado e tem a versão controlada para que o aplicativo possa evoluir como um todo.

## O que é um microsserviço?

Há definições diferentes de microsserviços, e uma pesquisa na Internet proporciona bons recursos que fornecem seus próprios pontos de vista e definições. No entanto, a maioria das características dos microsserviços descritas a seguir é aceita:

- Eles abrangem um cenário de cliente ou comercial. Qual é o problema que você está solucionando?
- Eles são desenvolvidos por uma pequena equipe de engenharia.
- Eles podem ser escritos em qualquer linguagem de programação e usar qualquer estrutura.
- Eles são formados por código e, opcionalmente, estado com versão, implantação e dimensionamento independentes.
- Eles interagem com outros microsserviços em protocolos e interfaces bem definidos
- Eles têm URLs (nomes exclusivos) que podem ser usados para resolver seus locais.
- Eles permanecem consistentes e disponíveis na presença de falhas.

Você pode resumir essa introdução da seguinte maneira:

***Os aplicativos de microsserviços são compostos por serviços pequenos e focados no cliente, com controle de versão independente e escalonáveis que se comunicam entre si por meio de protocolos padrão com interfaces bem definidas.***


Abordamos os primeiros dois pontos indicados acima na seção anterior e agora esclareceremos os outros.

### Podem ser escritos em qualquer linguagem de programação e usar qualquer estrutura
Como desenvolvedores, deveríamos ser livres para escolher qualquer linguagem ou estrutura que quisermos dependendo de nossas habilidades ou das necessidades do serviço. Em alguns serviços, você pode valorizar os benefícios de desempenho do C++ acima de tudo, enquanto em outros, a facilidade do desenvolvimento gerenciado em C# ou Java pode ser mais importante. Em alguns casos, talvez seja necessário usar um uma biblioteca de terceiros, uma tecnologia de armazenamento de dados ou meios de expor o serviço aos clientes.

Após a escolha de uma tecnologia, chegamos ao gerenciamento operacional ou de ciclo de vida e ao dimensionamento do serviço.

### Permite que o código e o estado tenham versão, implantação e dimensionamento independentes  

Não importa o modo como você escolhe escrever seus microsserviços, o código e, opcionalmente, o estado de cada um devem ser implantados, atualizados e dimensionados de forma independente. Na verdade, esse é um dos problemas mais difíceis de resolver, pois tem base em sua escolha de tecnologias e, para o dimensionamento, na compreensão de como particionar (ou fragmentar) o código e o estado. Quando o código e o estado usam tecnologias separadas (que, atualmente, é o que costuma ser feito), os scripts de implantação de seu microsserviço precisam ser capazes de lidar com o dimensionamento de ambos. Isso está relacionado também à agilidade e à flexibilidade; portanto, você pode atualizar alguns microsserviços sem precisar atualizar todos de uma vez. Retornando à questão abordagem monolítica contra microsserviço por um momento, o diagrama a seguir mostra as diferenças das abordagens para o armazenamento do estado.

#### Armazenamento de estado entre os estilos de aplicativo
![Armazenamento de estado da plataforma Service Fabric][Image2]

***À esquerda está a abordagem monolítica com um banco de dados individual e camadas de tecnologias específicas.***

***À direita está a abordagem de microsserviços, um gráfico de microsserviços interconectados, no qual o estado normalmente tem como escopo o microsserviço e várias tecnologias são usadas.***

Em uma abordagem monolítica, normalmente há um único banco de dados usado pelo aplicativo. A vantagem é que se trata de um único local, facilitando a implantação. Cada componente pode ter uma única tabela para armazenar seu estado. A parte difícil é que as equipes precisam ser rigorosas na separação do estado e, inevitavelmente, há a tentação de simplesmente adicionar uma nova coluna a uma tabela de clientes existente, realizar uma associação entre tabelas e criar dependências na camada de armazenamento. Quando isso acontecer, você não poderá dimensionar os componentes individuais. Na abordagem de microsserviços, cada serviço gerencia e armazena seu próprio estado, ou seja, ele é responsável por dimensionar o código e o estado para atender às demandas do serviço. A desvantagem dessa abordagem aparece quando há a necessidade de criar qualquer exibição, ou consulta, dos dados de seus aplicativos, pois agora você precisará consultar entre esses armazenamentos de estado diferentes. Normalmente, isso é resolvido com um microsserviço separado que cria um modo de exibição em uma coleção de microsserviços. Se você precisar executar várias consultas ad hoc nos dados, cada microsserviço deverá considerar a gravação de seus dados em um serviço de data warehouse para análise offline.


O controle de versão é específico para a versão implantada de um microsserviço. Ele é necessário para que várias versões diferentes possam ser distribuídas e executadas lado a lado. O controle de versão aborda os cenários nos quais uma versão mais recente de um microsserviço falha durante a atualização e precisa ser revertida para uma versão anterior. O outro cenário para controle de versão é a realização de um teste ao estilo A/B, no qual usuários diferentes experimentam versões diferentes do serviço. Por exemplo, é comum atualizar um microsserviço para um conjunto específico de clientes testar a nova funcionalidade, antes de fazê-lo mais amplamente. Após o gerenciamento do ciclo de vida de microsserviço, isso nos leva à comunicação entre eles.


### Interage com outros microservices em protocolos e interfaces bem-definidos

Há pouco a ser dito sobre este tópico além de ler a extensa literatura sobre arquitetura orientada a serviços publicada nos últimos 10 anos, já que grande parte disso foi direcionada aos padrões de comunicação. Em geral, isso agora se resume ao uso de uma abordagem REST com protocolos HTTP e TCP, e XML ou JSON como formato de serialização. Do ponto de vista da interface, isso se resume à adoção da abordagem de design da Web. Mas não há nada que impeça você de usar protocolos binários ou seus próprios formatos de dados. Apenas esteja preparado para a possibilidade de as pessoas terem mais dificuldade em usar seus microsserviços se estiverem abertamente disponíveis.

### Tenha um nome exclusivo (URL) que possa ser usado para resolver seu local.

Lembra-se de como continuamos afirmando que a abordagem de microsserviço é como a Web? Assim como a Web, seu microsserviço precisa ser endereçável em qualquer lugar que esteja sendo executado. Se você ficar pensando em máquinas, e qual delas está executando um determinado microsserviço, as coisas sairão dos trilhos rapidamente. Da mesma forma que o DNS resolve uma URL específica para um determinado computador, seu microservice deve ter um nome exclusivo para descobrir onde ele está atualmente. Os microsserviços precisam de nomes endereçáveis que os tornem independentes da infraestrutura na qual estão executando. Obviamente, isso significa que há uma interação entre como seu serviço é implantado e como ele é descoberto, já que deve haver um registro do serviço. Igualmente, precisa haver uma interação entre o momento em que ocorre falha na máquina e o que acontece com o microsserviço para que o serviço de registro possa avisar você onde ele está em execução. Isso nos leva ao próximo tópico: resiliência e consistência.

### Permanece consistente e disponível na presença de falhas

Lidar com falhas inesperadas é um dos problemas mais difíceis de se resolver, especialmente em um sistema distribuído. Boa parte do código que escrevemos, como desenvolvedores, serve para lidar exceções, e também é nisso que a maior parte do tempo é gasto durante o teste. Mas o problema é mais complicado do que escrever código para tratar de falhas. O que acontece quando o computador onde o microserviço está em execução falha? Você não precisará apenas detectar essa falha do microsserviço (um problema difícil por si só), mas também precisará de algo para reiniciar o microsserviço. Um microsserviço precisa ser resiliente a falhas e ter a capacidade de reiniciar com frequência em outra máquina por motivos de disponibilidade. Com isso, também há a pergunta: qual estado foi salvo em nome do microsserviço, de onde ele pode recuperar esse estado, e se ele é capaz de reiniciar com êxito. Em outras palavras, é necessário haver resiliência na computação (ou seja, o processo é reiniciado) e no estado ou nos dados (não houve perda de dados e os dados permanecem consistentes).

Os problemas de resiliência são agravados durante outros cenários, por exemplo, quando falhas ocorrem durante uma atualização do aplicativo. O microsserviço, trabalhando em conjunto com o sistema de implantação, não precisa somente fazer a recuperação. Ele também precisa decidir se pode continuar a seguir com a versão mais recente ou se reverte para uma versão anterior a fim de manter um estado consistente. Perguntas como se há máquinas suficientes para continuar avançando e como recuperar versões anteriores do microsserviço precisam ser levadas em consideração. Isso exige que o microsserviço emita informações de integridade para poder tomar essas decisões.

### Relatórios de integridade e diagnóstico

Pode parecer óbvio, mas muitas vezes é ignorado e é essencial que um microsserviço relate sua integridade e seu diagnóstico. Caso contrário, há pouca percepção de uma perspectiva de operações. O desafio está em ver a correlação de eventos de diagnóstico em um conjunto de serviços independentes, com cada um registrando de forma independente, e lidar com a defasagem do relógio do computador a fim de dar sentido à ordem de eventos. Da mesma forma que você interage com um microsserviço sobre os formatos de dados e protocolos acordados, surge a necessidade de padronização de como registrar os eventos de integridade e de diagnóstico que, em última análise, terminam em um repositório de eventos que pode ser consultado e exibido. Em uma abordagem de microsserviços é fundamental que as equipes diferentes concordem em um único formato de registro em log, pois há a necessidade de uma abordagem consistente para a exibição de eventos de diagnóstico no aplicativo como um todo.

Integridade é diferente de diagnóstico. Integridade significa o microsserviço reportar seu estado atual para que as ações possam ser executadas. O mais óbvio é trabalhar com mecanismos de atualização e implantação para manter a disponibilidade. Por exemplo, um serviço pode não estar íntegro no momento devido a uma falha do processo ou reinicialização da máquina, mas estar ainda operacional. A última coisa de que você precisa é piorar a situação executando uma atualização. A melhor abordagem é fazer uma investigação primeiro ou aguardar até que o microsserviço se recupere. Os eventos de integridade de um microsserviço nos permite tomar decisões informadas e ajudar realmente a criar serviços que se recuperam automaticamente.

## Service Fabric como uma plataforma de microsserviço

O Service Fabric do Azure nasceu da transição da Microsoft do fornecimento de produtos de caixa, que eram normalmente monolíticos em estilo, para o fornecimento de serviços. O Service Fabric foi gerado basicamente pela experiência de criar e operar grandes serviços, como bancos de dados Azure SQL, Banco de Dados de Documentos e outros serviços principais do Azure. Abordamos as necessidades totais com escalabilidade, agilidade e equipes independentes e deixamos a plataforma evoluir ao longo do tempo conforme mais serviços a adotaram. Acima de tudo, o Service Fabric precisava ser executado em qualquer lugar, não apenas no Azure, mas também em implantações autônomas do Windows Server.

***O objetivo do Service Fabric é resolver os problemas difíceis de criação e execução de um serviço, como falhas, atualizações e utilização de recursos de infraestrutura com eficiência para que as equipes possam resolver problemas comerciais negócios usando uma abordagem de microsserviços.***

O Service Fabric fornece duas áreas amplas para ajudar você com a criação de aplicativos usando uma abordagem de microsserviço:

- Uma plataforma composta por um conjunto de serviços do sistema que cuidam das implantações, atualizações, detecção e reinicialização de serviços com falhas, descoberta de onde os serviços estão em execução no momento, gerenciamento de estado, monitoramento de integridade, etc. Esses serviços de sistema permitem muitas características de microsserviços descritas acima.

-  APIs de programação, ou estruturas, para ajudar você a criar aplicativos como microsserviços. As APIs de programação fornecidas são chamadas de [reliable actors e reliable services](service-fabric-choose-framework.md). Obviamente, você pode usar qualquer código de sua escolha para criar seu microsserviço. Mas essas APIs facilitam o trabalho e se integram com a plataforma em um nível mais profundo. Dessa forma, por exemplo, você pode obter informações de integridade e de diagnóstico ou pode tirar proveito da alta disponibilidade interna.

***O Service Fabric não se importa com o modo de criação de seu serviço, e você pode usar qualquer tecnologia. No entanto, ele fornece APIs de programação internas que facilitam muito a criação de microsserviços***

### Os microsserviços são ideais para meu aplicativo?

Talvez. Em nosso caso, com o aumento do número de equipes da Microsoft recebendo instruções para criar para a nuvem por motivos comerciais, muitos delas perceberam os benefícios de usar um método de microsserviços. O Bing, por exemplo, utiliza os microsserviços em pesquisa há anos. Para outras equipes, isso era muito novo. Eles descobriram que havia problemas difíceis que precisavam ser solucionados e que estavam além das suas competências. É por isso que o Service Fabric ganhou adeptos como a tecnologia preferida para a criação de serviços.

O objetivo do Service Fabric é reduzir a complexidade da criação de aplicativos com uma abordagem de microsserviço para que você não precise passar por diversas reformulações dispendiosas. Comece pequeno, expanda quando necessário, substitua serviços, adicione serviços novos e evolua de acordo com o uso do cliente. Essa é a abordagem. Também sabemos que, na realidade, há muitos outros problemas que ainda precisam ser resolvidos para tornar os microsserviços mais acessíveis para a maioria dos desenvolvedores. Os contêineres e o modelo de programação de ator são exemplos de pequenas etapas nessa direção, e temos certeza de que outras inovações surgirão para tornar isso mais fácil.
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

* Para mais informações:
	* [Visão geral da Malha do Serviço](service-fabric-overview.md)
    * [Microsserviços: uma revolução de aplicativo proporcionada pela nuvem](https://azure.microsoft.com/pt-BR/blog/microservices-an-application-revolution-powered-by-the-cloud/)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png

<!---HONumber=AcomDC_0330_2016-->