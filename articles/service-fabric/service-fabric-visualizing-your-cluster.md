<properties
   pageTitle="Visualizando o cluster usando o Gerenciador de Malha do Serviço"
   description="O Gerenciador de Malha do Serviço é uma ferramenta de GUI útil para inspecionar e gerenciar aplicativos em nuvem e nós em um cluster de Malha do Serviço do Microsoft Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/05/2015"
   ms.author="jesseb"/>

# Visualizando o cluster usando o Gerenciador de Malha do Serviço

O Gerenciador de Malha do Serviço é uma ferramenta visual para inspecionar e gerenciar aplicativos em nuvem e nós em um cluster de Malha do Serviço do Microsoft Azure. O Gerenciador de Malha do Serviço pode se conectar a clusters de desenvolvimento locais e clusters do Azure. Para saber mais sobre os cmdlets do PowerShell de Malha do Serviço, confira as **próximas etapas**.

> [AZURE.NOTE]A criação de clusters de Malha do Serviço no Azure ainda não está disponível.

## Introdução ao Gerenciador de Malha do Serviço

Verifique se o ambiente de desenvolvimento local está configurado seguindo as instruções em [configurar seu ambiente de desenvolvimento de Malha do Serviço](service-fabric-get-started.md).

Execute o Gerenciador de Malha do Serviço de seu caminho de instalação local (%Program Files%\\Microsoft SDKs\\Malha do Serviço\\Tools\\ServiceFabricExplorer\\ServiceFabricExplorer.exe). A ferramenta vai se conectar automaticamente a um cluster de desenvolvimento local, se houver. Ela exibe informações sobre o cluster, como:

- Aplicativos em execução no cluster
- Informações sobre os nós do cluster
- Eventos de integridade de aplicativos e nós
- Carregar os aplicativos no cluster
- Monitoramento do status de atualização do aplicativo

![Representação visual do cluster da Malha do Serviço e aplicativos implantados][servicefabricexplorer]

Uma das visualizações importantes é o mapa de clusters, visível no painel para o cluster (por exemplo, clicar em **Onebox/Local cluster**). O mapa de cluster mostra o conjunto de domínios de atualização e domínios de falha e quais nós são mapeados para quais domínios. Consulte a [Visão geral técnica da Malha do Serviço](service-fabric-technical-overview.md) para se familiarizar com os principais conceitos de Malha do Serviço.

![O mapa de cluster mostra a quais domínios de atualização e domínios de falha cada nó pertence.][clustermap]


## Exibindo seus aplicativos e serviços

O Gerenciador de Malha do Serviço permite explorar os aplicativos em execução no cluster. Expanda o **Modo de exibição de aplicativo** para exibir informações detalhadas sobre seus aplicativos, serviços, partições e réplicas.

O diagrama a seguir mostra que o aplicativo chamado **"fabric:/Stateful1Application"** tem um serviço sem monitoração de estado **"fabric:/Stateful1Application/MyFrontEnd"** e um serviço com estado **"fabric:/Stateful1Application/Stateful1"**. O serviço sem monitoração de estado tem uma partição com uma réplica em execução em **Node.4**. O serviço com estado tem duas partições, cada uma com 3 réplicas, executadas em vários nós diferentes.

![Exibição dos aplicativos em execução no cluster de Malha do Serviço][applicationview]

Clicar em um aplicativo, serviço, partição ou réplica fornece informações detalhadas sobre essa entidade. O diagrama a seguir mostra o painel de integridade de réplica do serviço para uma das réplicas primárias do serviço com estado. Isso inclui sua função, o nó em que está sendo executada, o endereço em que está escutando, o local dos arquivos no disco e os eventos de integridade.

![Informações detalhadas sobre uma réplica de Malha do Serviço][replicadetails]


## Conectando-se a um cluster de Malha do Serviço remoto

Para exibir um cluster de Malha do Serviço remoto, clique em **Conectar** para abrir a caixa de diálogo **Conectar ao Cluster de Malha do Serviço**. Insira o **Ponto de extremidade de MalhadoServiço** para seu cluster e clique em **Conectar**. O ponto de extremidade de Malha do Serviço geralmente é o nome público do serviço de cluster escutando na porta 19000.

![Configurar uma conexão com o cluster de Malha do Serviço remoto][connecttocluster]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

- [Visão geral da capacidade de teste](service-fabric-testability-overview.md).
- [Gerenciando seus aplicativos de Malha do Serviço no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Implantação de aplicativos de Malha do Serviço usando o PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[applicationview]: ./media/service-fabric-visualizing-your-cluster/applicationview.png
[clustermap]: ./media/service-fabric-visualizing-your-cluster/clustermap.png
[connecttocluster]: ./media/service-fabric-visualizing-your-cluster/connecttocluster.png
[replicadetails]: ./media/service-fabric-visualizing-your-cluster/replicadetails.png
[servicefabricexplorer]: ./media/service-fabric-visualizing-your-cluster/servicefabricexplorer.png

<!---HONumber=Oct15_HO3-->