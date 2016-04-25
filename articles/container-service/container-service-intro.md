<properties
   pageTitle="Introdução ao Serviço do Contêiner do Azure | Microsoft Azure"
   description="O Serviço de Contêiner do Azure fornece uma maneira de simplificar a criação, configuração e gerenciamento de um cluster de máquinas virtuais que são pré-configuradas para executar aplicativos contidos."
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

O Serviço de Contêiner do Azure simplifica a criação, a configuração e o gerenciamento de um cluster de máquinas virtuais que são pré-configuradas para executar os aplicativos contidos. Ele usa uma configuração otimizada de ferramentas populares de agendamento de software livre e de orquestração. Isso permite que você use suas habilidades existentes ou explore uma experiência cada vez maior da comunidade para implantar e gerenciar aplicativos baseados em contêiner no Microsoft Azure.

<br /> ![O Serviço de Contêiner do Azure fornece um meio para gerenciar os aplicativos contidos em vários hosts no Azure.](./media/acs-intro/acs-cluster.png) <br /><br />

O Serviço de Contêiner do Azure utiliza o formato de contêiner do Docker para garantir que os contêineres de seu aplicativo são totalmente portáteis. Ele também dá suporte à opção de selecionar o Marathon, Apache Mesos ou Docker Swarm para que você possa dimensionar esses aplicativos para milhares de contêineres ou até mesmo dezenas de milhares.

Usando o Serviço de Contêiner do Azure, você pode aproveitar as vantagens dos recursos de nível empresarial do Azure e ainda manter a portabilidade do aplicativo, incluindo a portabilidade nas camadas de orquestração.

Usando o Serviço de Contêiner do Azure
-----------------------------

Nosso objetivo com o Serviço de Contêiner do Azure é fornecer um ambiente de hospedagem de contêineres usando ferramentas e tecnologias de código-fonte aberto, que são comuns entre os nossos clientes hoje. Para essa finalidade, expomos os pontos de extremidade de API padrão para seu orquestrador escolhido. Usando esses pontos de extremidade, é possível utilizar qualquer software que possa se comunicar com os pontos de extremidade. Por exemplo, no caso do ponto de extremidade Docker Swarm, você pode optar por usar a interface de linha de comando do Docker (CLI). Para o Apache Mesos, você pode optar por usar a CLI do DCOS.

Criação de um cluster do Docker usando o Serviço de Contêiner do Azure
-------------------------------------------------------

Para começar a usar o Serviço de Contêiner do Azure, implante um cluster do Serviço de Contêiner do Azure usando um modelo do Azure Resource Manager. Você pode configurar essa implantação com opções diferentes de tamanho e de disponibilidade, usando o Apache Mesos ou o Docker Swarm. Você pode implantar os modelos do Azure Resource Manager por meio do portal do Azure usando a CLI do Azure ou com o PowerShell. Os modelos também podem ser modificados para incluir uma configuração do Azure avançada ou adicional. Para saber mais sobre a implantação de um cluster do Serviço de Contêiner do Azure, veja [Implantar um cluster do Serviço de Contêiner do Azure](./container-service-deployment.md).

Implantação de um aplicativo
------------------------

Durante a visualização, fornecemos uma opção do Docker Swarm ou do Apache Mesos (com estruturas do DCOS Marathon e DCOS Chronos) para a orquestração.

### Usando o Apache Mesos

O Apache Mesos é um projeto de software livre hospedado no Apache Software Foundation. Ele lista alguns dos [principais nomes em TI](http://mesos.apache.org/documentation/latest/powered-by-mesos/) como usuários e colaboradores.

![Serviço de Contêiner do Azure configurado para o Swarm, mostrando agentes e mestres.](media/acs-intro/acs-mesos.png)

O Mesos inclui um conjunto de recursos impressionante:

-   Escalabilidade para 10.000 nós

-   Mestre e subordinados replicados com tolerância a falhas que usam o ZooKeeper

-   Suporte para contêineres formatados pelo Docker

-   Isolamento nativo entre tarefas com contêineres do Linux

-   Agendamento de vários recursos (memória, CPU, disco e portas)

-   APIs do Java, Python e C++ para desenvolver novos aplicativos paralelos

-   Uma interface do usuário da Web para exibir o estado do cluster

O Mesos dá suporte a um grande número de [estruturas](http://mesos.apache.org/documentation/latest/frameworks/) que você pode usar para o agendamento de cargas de trabalho no Serviço de Contêiner do Azure. Por padrão, o Serviço de Contêiner do Azure inclui as estruturas Marathon e Chronos.

#### Usando o Marathon e o Chronos

O Marathon é um sistema de inicialização e controle de todo o cluster para serviços em cgroups ou, no caso do Serviço de Contêiner do Azure, em contêineres formatados pelo Docker. É um parceiro ideal para o Chronos, um agendador de trabalhos tolerante a falhas para o Mesos que manipula as dependências e agendamentos com hora.

O Marathon e o Chronos fornecem uma interface do usuário da Web da qual você pode implantar seus aplicativos. Você pode acessar isso em uma URL semelhante a `http://DNS\_PREFIX.REGION.cloudapp.azure.com`, em que DNS\_PREFIX e REGION são definidos no momento da implantação. É claro, você também pode fornecer seu próprio nome DNS. Para saber mais sobre como executar um contêiner usando a interface de usuário da Web do Marathon, confira [Gerenciamento de contêiner pela interface de usuário da Web](./container-service-mesos-marathon-ui.md).

Você também pode usar as APIs REST para se comunicar com o Marathon e Chronos. Há uma série de bibliotecas de cliente que estão disponíveis para cada ferramenta. Elas abrangem uma variedade de linguagens e, claro, você pode usar o protocolo HTTP em qualquer linguagem. Além disso, muitas ferramentas populares de DevOps dão suporte para esses agendadores. Isso oferece uma flexibilidade máxima para a equipe de operações quando estiver trabalhando com um cluster do Serviço de Contêiner do Azure. Para saber mais sobre como executar um contêiner usando a API REST do Marathon, confira [Gerenciamento de contêiner com a API REST](./container-service-mesos-marathon-rest.md).

### Usando o Docker Swarm

O Docker Swarm fornece o clustering nativo para o Docker. Como o Docker Swarm atende à API padrão do Docker, qualquer ferramenta que já se comunica com um daemon do Docker pode usar o Swarm escalar transparentemente para vários hosts no Serviço de Contêiner do Azure.

![Serviço de Contêiner do azure configurado para usar o Apache Mesos, mostrando jumpbox, agentes e mestres.](media/acs-intro/acs-swarm.png)

As ferramentas com suporte para gerenciar contêineres em um cluster do Swarm, incluem, entre outras:

-   Dokku

-   CLI do Docker e Docker Compose

-   Krane

-   Jenkins

Vídeos
------
Comunicado sobre o AzureCon:

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

Introdução ao Serviço de Contêiner do Azure:

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]

<!---HONumber=AcomDC_0413_2016-->