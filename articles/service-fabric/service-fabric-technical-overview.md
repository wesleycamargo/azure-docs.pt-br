<properties
   pageTitle="Visão geral técnica"
   description="Uma visão geral técnica da Malha do Serviço. Discute os principais conceitos e visão geral da arquitetura"
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
   ms.date="04/14/2015"
   ms.author="mfussell"/>

# Visão geral técnica da Malha do Serviço

Malha do Serviço é uma plataforma de sistemas distribuídos que facilita a criação de aplicativos escalonáveis, confiáveis, de baixa latência e facilmente gerenciados para a nuvem. Isso significa que você pode se concentrar em suas necessidades de negócios e permite que a Malha do Serviço garanta que seu aplicativo esteja sempre disponível e possa ser expandido.

## Principais conceitos

**Cluster** - um conjunto de máquinas físicas ou virtuais conectadas em rede, nas quais ocorre a implantação de instâncias de aplicativos. Os clusters podem ser dimensionados para milhares de máquinas.

**Nó** - uma unidade endereçável em um cluster. Os nós têm características como propriedades de posicionamento e IDs exclusivas. Os nós podem ingressar em um cluster e correlacionar a uma instância do sistema operacional com Fabric.exe em execução.

**Aplicativo/Tipo de Aplicativo** - Uma coleção de micro(serviços). Pense em um tipo de aplicativo como um contêiner de um ou mais tipos de serviço. Consulte o artigo [Modelo de aplicativo](service-fabric-application-model.md) para entender como um cluster (que é composto por vários nós) pode ser formado por vários ApplicationTypes.

**Serviço/Tipo de Serviço** - código e configuração que executam uma função autônoma (pode iniciar e executar de forma independente), por exemplo, um serviço de fila ou um serviço de banco de dados. Um ApplicationType pode ser formado por um ou mais ServiceTypes. Há dois tipos de tipos de serviço:

- Serviço sem estado: um serviço com um estado mantido no armazenamento externo, como o armazenamento de Bancos de Dados do Azure ou Tabela do Azure. Se ocorrer a falha de um nó no qual uma instância do serviço esteja ativa, outra instância será iniciada automaticamente em outro nó.

- Serviço com estado: um serviço que tem estado e obtém a confiabilidade por meio da replicação entre réplicas em outros nós no cluster. Os serviços com estado têm uma réplica primária e várias réplicas secundárias. Se ocorrer a falha de um nó no qual uma réplica desse serviço esteja ativa, uma nova réplica será iniciada em outro nó e, se for a réplica primária, uma réplica secundária será promovida automaticamente como a nova primária.

**Instância do Aplicativo** - em um cluster, é possível criar várias instâncias do aplicativo de um tipo de aplicativo, cada uma delas com um nome específico. Cada instância do aplicativo pode ser gerenciada de forma independente e apresentar controle de versão de outras instâncias do mesmo tipo ou de outro tipo de aplicativo. Além disso, elas definem o isolamento de recursos e da segurança.

**Instância do Serviço** - código instanciado para um tipo de serviço. Cada instância de serviço tem um nome exclusivo que começa com `fabric:/` e está associada uma instância de aplicativo com nome exclusivo.

**Pacote de Aplicativos** - a coleção de pacotes de código de serviço e os arquivos de configuração combinados para um aplicativo específico. Estes são os arquivos físicos implantados, e é simplesmente um layout de formato de arquivo e pasta. Por exemplo, um pacote de aplicativos para um aplicativo de email pode conter um pacote de serviços em fila, um pacote de serviços front-end e um pacote de serviços de banco de dados.

**Modelos de Programação** - há dois modelos de programação disponíveis na Malha do Serviço para criar aplicativos:

- Serviços Confiáveis - uma API para criar serviços com e sem estado com base nas classes `StatelessService` e `StatefulService` do .NET, armazenando o estado em coleções .NET confiáveis (Dicionário e Fila) e com a capacidade de conectar-se a uma variedade de pilhas de comunicação, como a API Web e o WCF. Este modelo de programação é adequado para aplicativos nos quais você precisa realizar a computação em várias unidades de estado.

- Atores Confiáveis - uma API para criar objetos com e sem estado por meio do modelo de programação virtual de Ator, que é adequado a aplicativos com várias unidades independentes de estado e computação.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
Para saber mais sobre a Malha do Serviço, consulte:

- [Modelo de Aplicativo](service-fabric-application-model.md)
- [Cenários de Aplicativo](service-fabric-application-scenarios.md)
 

<!---HONumber=July15_HO4-->