<properties
   pageTitle="Visão geral técnica do Service Fabric | Microsoft Azure"
   description="Uma visão geral técnica da Malha do Serviço. Aborda os conceitos principais e a arquitetura."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="chackdan;subramar"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/09/2016"
   ms.author="mfussell"/>

# Visão geral técnica da Malha do Serviço

O Service Fabric do Azure é uma plataforma de sistemas distribuídos que facilita a criação de aplicativos escalonáveis, confiáveis, de baixa latência e facilmente gerenciados para a nuvem. Isso significa que você pode se concentrar em suas necessidades de negócios e permite que o Service Fabric garanta que seu aplicativo esteja sempre disponível e possa ser expandido.

## Principais conceitos

**Cluster**: um conjunto de máquinas físicas ou virtuais conectadas em rede, nas quais as instâncias de aplicativos são implantadas. Os clusters podem ser dimensionados para milhares de máquinas.

**Nó**: uma unidade endereçável em um cluster. Os nós têm características como propriedades de posicionamento e IDs exclusivas. Os nós podem ingressar em um cluster e correlacionar a uma instância do sistema operacional com Fabric.exe em execução.

**Aplicativo/tipo de aplicativo**: uma coleção de micro(serviços). Pense em um tipo de aplicativo como um contêiner de um ou mais tipos de serviço. Veja o artigo [Modelo de aplicativo](service-fabric-application-model.md) para entender como um cluster (que é composto por vários nós) pode ser formado por vários tipos de aplicativo.

**Serviço/tipo de serviço**: código e configuração que executam uma função autônoma (pode ser iniciada e executada de forma independente), por exemplo, um serviço de fila ou de banco de dados. Um tipo de aplicativo pode ser composto por um ou mais tipos de serviço. Há dois tipos de tipos de serviço:

- Serviço sem estado: um serviço com um estado mantido no armazenamento externo, como o armazenamento de bancos de dados do Azure ou de Tabela do Azure. Se ocorrer a falha de um nó no qual uma instância do serviço esteja ativa, outra instância será iniciada automaticamente em outro nó.

- Serviço com estado: um serviço que tem estado e obtém a confiabilidade por meio da replicação entre réplicas em outros nós no cluster. Os serviços com estado têm uma réplica primária e várias réplicas secundárias. Se um nó, no qual uma réplica desse serviço estiver ativa, falhar, uma nova réplica será iniciada em outro nó. Se o nó que falhar for a réplica primária, uma réplica secundária será promovida automaticamente para uma nova primária.

**Instância do aplicativo**: em um cluster, é possível criar muitas instâncias de um tipo de aplicativo, cada uma delas com um nome específico. Cada instância do aplicativo pode ser gerenciada de forma independente e apresentar controle de versão de outras instâncias do mesmo tipo ou de outro tipo de aplicativo. Além disso, elas definem o isolamento de recursos e da segurança.

**Instância do serviço**:-código instanciado para um tipo de serviço. Cada instância de serviço tem um nome exclusivo que começa com `fabric:/` e está associada uma instância de aplicativo com um nome exclusivo.

**Pacote de aplicativos**: a coleção de pacotes de código de serviço e os arquivos de configuração combinados para um aplicativo específico. Estes são os arquivos físicos implantados e eles estão apenas em um layout de formato de pasta e arquivo. Por exemplo, um pacote de aplicativos para um aplicativo de email pode conter um pacote de serviços de fila, um pacote de serviços de front-end e um pacote de serviços de banco de dados.

**Modelos de programação**: há dois modelos de programação disponíveis no Service Fabric para criar aplicativos:

- Reliable Services: uma API para criar serviços com e sem estado com base nas classes .NET `StatelessService` e `StatefulService`, que armazenam o estado nas coleções confiáveis do .NET (dicionário e fila). Eles também têm a capacidade de se conectar a uma variedade de pilhas de comunicação, como a API Web e o Windows Communication Foundation. Este modelo de programação é adequado para aplicativos nos quais você precisa realizar a computação em várias unidades de estado.

- Reliable Actors: uma API para criar objetos com e sem estado por meio do modelo de programação virtual de ator, que é adequado a aplicativos com várias unidades independentes de estado e computação.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
Para saber mais sobre a Malha do Serviço, consulte:

- [Modelo de aplicativo](service-fabric-application-model.md)
- [Cenários de aplicativos](service-fabric-application-scenarios.md)

<!---HONumber=AcomDC_0211_2016-->