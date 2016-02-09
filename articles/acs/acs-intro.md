<properties
   pageTitle="Introdução ao Serviço do Contêiner do Azure | Microsoft Azure"
   description="O ACS (Serviço de Contêiner do Azure) fornece uma maneira de simplificar a criação, configuração e gerenciamento de um cluster de máquinas virtuais que são pré-configuradas para executar aplicativos contidos."
   services="virtual-machines"
   documentationCenter=""
   authors="rgardler"
   manager="nepeters"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contêineres, Microsserviços, Mesos, Azure"/>
   
<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="home-page"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="rogardle"/>

# Introdução ao Serviço de Contêiner do Azure

O ACS (Serviço de Contêiner do Azure) fornece uma maneira de simplificar a criação, configuração e gerenciamento de um cluster de máquinas virtuais que são pré-configuradas para executar aplicativos contidos. Usando uma configuração otimizada de ferramentas populares de agendamento e orquestração de código-fonte aberto, o ACS permite que você use suas habilidades existentes ou explore uma experiência cada vez maior da comunidade para implantar e gerenciar aplicativos baseados em contêiner no Microsoft Azure.

<br /> ![O ACS fornece um meio para gerenciar os aplicativos contidos em vários hosts no Azure.](./media/acs-intro/acs-cluster.png) <br /><br />

O ACS utiliza o Docker para garantir que os contêineres do seu aplicativo sejam totalmente portáteis. Ele também dá suporte à opção de selecionar o Marathon, Chronos e Apache Mesos ou Docker Swarm para garantir que esses aplicativos possam ser escalados para milhares, e até dezenas de milhares de contêineres.

O Serviço de Contêiner do Azure permite que você aproveite os recursos de nível empresarial do Azure e ainda manter a portabilidade do aplicativo, inclusive nas camadas de orquestração.

Enquanto o serviço está no modo de visualização, pedimos que aqueles que estão interessados em testar o serviço [se autodesignem](http://aka.ms/acspreview). Depois que o acesso de visualização tiver sido fornecido, um email será enviado com mais detalhes, incluindo modelos de implantação e instruções de introdução. Para usar o serviço, você precisará de uma assinatura do Azure; se não tiver uma, por que não se inscrever para obter uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)?

Usando o Serviço de Contêiner do Azure
-----------------------------

Nosso objetivo com o Serviço de Contêiner do Azure é fornecer um ambiente de hospedagem de contêineres usando ferramentas e tecnologias de código-fonte aberto, que são comuns entre os nossos clientes hoje. Para essa finalidade, vamos expor os pontos de extremidade padrão de API para o Docker e para o seu orquestrador escolhido. Usando esses pontos de extremidade, é possível utilizar qualquer software que possa se comunicar com os pontos de extremidade. Por exemplo, no caso do ponto de extremidade do Docker Swarm, você pode optar por usar o Docker Compose, enquanto para o Apache Mesos, você pode optar por usar a CLI do DCOS.

Criando um Cluster do Docker usando o Serviço de Contêiner do Azure
-------------------------------------------------------

Depois de [solicitar](http://aka.ms/acspreview) e receber permissão de acesso à visualização, você pode usar um dos vários modelos do Gerenciador de Recursos do Azure que permitem que você implante seu primeiro cluster por meio do Portal do Azure. Com esses modelos, você pode criar um serviço com rapidez e iniciar a implantação de aplicativos nele imediatamente. Para começar, basta escolher o tamanho do seu cluster e se você deseja usar o Docker Swarm ou o Apache Mesos para gerenciar seus aplicativos.

Você também pode [usar a linha de comando](/documentation/articles/resource-group-template-deploy/) para criar Serviços do Contêiner do Azure usando esses mesmos modelos. Quando estiver familiarizado com a estrutura desses modelos, você poderá escrever seu próprio modelo e automatizar por completo a criação de um cluster do Serviço de Contêiner do Azure.

A documentação completa e o suporte serão fornecidos aos participantes da visualização e serão publicados aqui depois que o serviço estiver aberto para o público.

Implantando um aplicativo
------------------------

Durante a visualização, fornecemos uma opção do Docker Swarm ou do Apache Mesos (com estruturas do DCOS Marathon e DCOS Chronos) para a orquestração.

### Usando o Apache Mesos

Apache Mesos é um projeto de código-fonte aberto hospedado no Apache Software Foundation. Ele lista alguns dos [principais nomes em TI](http://mesos.apache.org/documentation/latest/powered-by-mesos/) como usuários e colaboradores.

![ACS configurado para o Swarm, mostrando agentes e mestres.](media/acs-intro/acs-mesos.png)

O Mesos traz um conjunto de recursos impressionantes.

-   Escalabilidade para 10.000 nós

-   Mestre e subordinados replicados com tolerância a falhas usando o ZooKeeper

-   Suporte para os contêineres do Docker

-   Isolamento nativo entre tarefas com contêineres do Linux

-   Agendamento de vários recursos (memória, CPU, disco e portas)

-   APIs do Java, Python e C++ para desenvolver novos aplicativos paralelos

-   Interface do usuário da Web para exibir o estado do cluster

No Mesos, há suporte para um grande número de [estruturas](http://mesos.apache.org/documentation/latest/frameworks/) que pode ser usado para o agendamento de cargas de trabalho no Serviço de Contêiner do Azure. Por padrão, o ACS inclui as estruturas Marathon e Chronos.

#### Usando o Marathon e o Chronos

O Marathon é um sistema de inicialização e controle de todo o cluster para serviços em cgroups ou, no caso do ACS, contêineres do Docker. É um parceiro ideal para o Chronos, um agendador de trabalhos tolerante a falhas para o Mesos que manipula as dependências e agendamentos com hora.

O Marathon e o Chronos fornecem uma interface do usuário da Web da qual você pode implantar seus aplicativos. Você acessará isso em uma URL semelhante a `http://DNS\_PREFIX.REGION.cloudapp.azure.com`, em que DNS\_PREFIX e REGION são definidos no momento da implantação. É claro, você também pode fornecer seu próprio nome DNS.

Você também pode usar as APIs REST para se comunicar com o Marathon e Chronos. Há várias bibliotecas de cliente disponíveis para cada ferramenta, que abrangem uma variedade de idiomas e, claro, você pode usar o protocolo HTTP em qualquer idioma. Além disso, muitas ferramentas populares de DevOps dão suporte para esses agendadores. Isso fornece uma flexibilidade máxima para a equipe de operações quando estiver trabalhando com um cluster do ACS.

A documentação completa e o suporte serão fornecidos aos participantes da visualização e serão publicados aqui depois que o serviço estiver aberto para o público.

### Usando o Docker Swarm

O Docker Swarm fornece o clustering nativo para o Docker. Como o Docker Swarm atende à API padrão do Docker, qualquer ferramenta que já se comunica com um daemon do Docker pode usar o Swarm escalar transparentemente para vários hosts no Serviço de Contêiner do Azure.

![ACS configurado para usar o Apache Mesos, mostrando jumpbox, agentes e mestres.](media/acs-intro/acs-swarm.png)

As ferramentas com suporte para gerenciar contêineres em um cluster do Swarm, incluem, entre outras:

-   Dokku

-   CLI do Docker e Docker Compose

-   Krane

-   Jenkins

A documentação completa e o suporte serão fornecidos aos participantes da visualização e serão publicados aqui depois que o serviço estiver aberto para o público.

Obtendo acesso
--------------

Enquanto o serviço está no modo de visualização, pedimos que aqueles que estão interessados em testar o serviço [se autodesignem](http://aka.ms/acspreview). Primeiro, será necessário uma Assinatura do Azure; se você não tiver uma, porque não se inscrever para obter uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)?

Vídeos
------
Comunicado sobre o AzureCon:

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

Introdução ao ACS:

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]

<!---HONumber=AcomDC_0128_2016--->