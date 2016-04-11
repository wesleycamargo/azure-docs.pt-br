<properties
   pageTitle="Introdução ao Serviço do Contêiner do Azure | Microsoft Azure"
   description="O ACS (Serviço de Contêiner do Azure) fornece uma maneira de simplificar a criação, configuração e gerenciamento de um cluster de máquinas virtuais que são pré-configuradas para executar aplicativos contidos."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contêineres, Microsserviços, Mesos, Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>

# Introdução ao Serviço de Contêiner do Azure

O ACS (Serviço de Contêiner do Azure) fornece uma maneira de simplificar a criação, configuração e gerenciamento de um cluster de máquinas virtuais que são pré-configuradas para executar aplicativos contidos. Usando uma configuração otimizada de ferramentas populares de agendamento e orquestração de código-fonte aberto, o ACS permite que você use suas habilidades existentes ou explore uma experiência cada vez maior da comunidade para implantar e gerenciar aplicativos baseados em contêiner no Microsoft Azure.

<br /> ![O ACS fornece um meio para gerenciar os aplicativos contidos em vários hosts no Azure.](./media/acs-intro/acs-cluster.png) <br /><br />

O ACS utiliza o formato de contêiner do Docker para garantir que os contêineres de seu aplicativo são totalmente portáteis. Ele também dá suporte à opção de selecionar o Marathon, Apache Mesos ou Docker Swarm para assegurar que esses aplicativos podem ser escalados para milhares, e até dezenas de milhares de contêineres.

O Serviço de Contêiner do Azure permite que você aproveite os recursos de nível empresarial do Azure e ainda manter a portabilidade do aplicativo, inclusive nas camadas de orquestração.

Usando o Serviço de Contêiner do Azure
-----------------------------

Nosso objetivo com o Serviço de Contêiner do Azure é fornecer um ambiente de hospedagem de contêineres usando ferramentas e tecnologias de código-fonte aberto, que são comuns entre os nossos clientes hoje. Para essa finalidade, expomos os pontos de extremidade de API padrão para seu orquestrador escolhido. Usando esses pontos de extremidade, é possível utilizar qualquer software que possa se comunicar com os pontos de extremidade. Por exemplo, no caso do ponto de extremidade do Docker Swarm, talvez você opte por usar a CLI do Docker, enquanto para o Apache Mesos, você opte por usar a CLI do DCOS.

Criando um Cluster do Docker usando o Serviço de Contêiner do Azure
-------------------------------------------------------

Para começar a usar o Serviço de Contêiner do Azure, um cluster ACS será implantado usando um modelo do Gerenciador de Recursos do Azure. Essa implantação pode ser configurada com diferentes opções de tamanho e de disponibilidade e será configurada com o Apache Mesos ou o Docker Swarm. Os Modelos do Azure Resource Manager podem ser implantados por meio do portal do Azure, usando a CLI do Azure ou com o PowerShell. Os modelos também podem ser modificados para incluir uma configuração do Azure avançada ou adicional. Para saber mais sobre implantação e o cluster ACS, confira [Implantar um cluster do Serviço de Contêiner do Azure](./container-service-deployment.md).

Implantando um aplicativo
------------------------

Durante a visualização, fornecemos uma opção do Docker Swarm ou do Apache Mesos (com estruturas do DCOS Marathon e DCOS Chronos) para a orquestração.

### Usando o Apache Mesos

Apache Mesos é um projeto de código-fonte aberto hospedado no Apache Software Foundation. Ele lista alguns dos [principais nomes em TI](http://mesos.apache.org/documentation/latest/powered-by-mesos/) como usuários e colaboradores.

![ACS configurado para o Swarm, mostrando agentes e mestres.](media/acs-intro/acs-mesos.png)

O Mesos traz um conjunto de recursos impressionantes.

-   Escalabilidade para 10.000 nós

-   Mestre e subordinados replicados com tolerância a falhas usando o ZooKeeper

-   Suporte para contêineres formatados do Docker

-   Isolamento nativo entre tarefas com contêineres do Linux

-   Agendamento de vários recursos (memória, CPU, disco e portas)

-   APIs do Java, Python e C++ para desenvolver novos aplicativos paralelos

-   Interface do usuário da Web para exibir o estado do cluster

No Mesos, há suporte para um grande número de [estruturas](http://mesos.apache.org/documentation/latest/frameworks/) que pode ser usado para o agendamento de cargas de trabalho no Serviço de Contêiner do Azure. Por padrão, o ACS inclui as estruturas Marathon e Chronos.

#### Usando o Marathon e o Chronos

O Marathon é um sistema de inicialização e controle de todo o cluster para serviços em cgroups ou, no caso do ACS, contêineres de formato do Docker. É um parceiro ideal para o Chronos, um agendador de trabalhos tolerante a falhas para o Mesos que manipula as dependências e agendamentos com hora.

O Marathon e o Chronos fornecem uma interface do usuário da Web da qual você pode implantar seus aplicativos. Você acessará isso em uma URL semelhante a `http://DNS\_PREFIX.REGION.cloudapp.azure.com`, em que DNS\_PREFIX e REGION são definidos no momento da implantação. É claro, você também pode fornecer seu próprio nome DNS. Para saber mais sobre como executar um contêiner usando a interface de usuário da Web do Marathon, confira [Gerenciamento de contêiner pela interface de usuário da Web](./container-service-mesos-marathon-ui.md).

Você também pode usar as APIs REST para se comunicar com o Marathon e Chronos. Há várias bibliotecas de cliente disponíveis para cada ferramenta, que abrangem uma variedade de idiomas e, claro, você pode usar o protocolo HTTP em qualquer idioma. Além disso, muitas ferramentas populares de DevOps dão suporte para esses agendadores. Isso fornece uma flexibilidade máxima para a equipe de operações quando estiver trabalhando com um cluster do ACS. Para saber mais sobre como executar um contêiner usando a API REST do Marathon, confira [Gerenciamento de contêiner com a API REST](./container-service-mesos-marathon-rest.md).

### Usando o Docker Swarm

O Docker Swarm fornece o clustering nativo para o Docker. Como o Docker Swarm atende à API padrão do Docker, qualquer ferramenta que já se comunica com um daemon do Docker pode usar o Swarm escalar transparentemente para vários hosts no Serviço de Contêiner do Azure.

![ACS configurado para usar o Apache Mesos, mostrando jumpbox, agentes e mestres.](media/acs-intro/acs-swarm.png)

As ferramentas com suporte para gerenciar contêineres em um cluster do Swarm, incluem, entre outras:

-   Dokku

-   CLI do Docker e Docker Compose

-   Krane

-   Jenkins

Vídeos
------
Comunicado sobre o AzureCon:

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

Introdução ao ACS:

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]

<!---HONumber=AcomDC_0330_2016-->