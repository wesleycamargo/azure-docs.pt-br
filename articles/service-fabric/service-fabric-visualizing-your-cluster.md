<properties
   pageTitle="Visualizar o seu cluster usando o Explorador do Service Fabric | Microsoft Azure"
   description="O Gerenciador do Service Fabric é uma ferramenta de GUI útil para inspecionar e gerenciar aplicativos em nuvem e nós em um cluster do Service Fabric do Microsoft Azure."
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
   ms.date="10/30/2015"
   ms.author="jesseb"/>

# Visualizando o cluster usando o Explorador do Service Fabric

O Explorador do Service Fabric é uma ferramenta baseada na Web para inspecionar e gerenciar aplicativos e nós em um cluster do Service Fabric. O Explorador do Service Fabric é hospedado diretamente dentro do cluster para que esteja sempre disponível, independentemente de onde o cluster esteja sendo executado.

## Conectar ao Explorador do Service Fabric

Se você seguiu as instruções para [preparar o seu ambiente de desenvolvimento](service-fabric-get-started.md), pode iniciar o Explorador do Service Fabric no cluster local navegando para http://localhost:19080/Explorer.

>[AZURE.NOTE]Se estiver usando o Internet Explorer (IE) com o Explorador do Service Fabric para gerenciar um cluster remoto, você precisará definir algumas configurações do IE. Vá para **Ferramentas -> Configurações de Exibição de Compatibilidade** e desmarque **Exibir sites da intranet na Exibição de Compatibilidade** para garantir que todas as informações são carregadas corretamente.

## Entender o layout do Explorador do Service Fabric

Você pode navegar pelo Explorador do Service Fabric usando a árvore à esquerda. Na raiz da árvore, o painel do cluster fornece uma visão geral do cluster, incluindo um resumo do aplicativo e a integridade do nó.

![Painel de clusters do Explorador do Service Fabric][sfx-cluster-dashboard]

O cluster contém duas subárvores: uma para aplicativos e outra para nós.

### Exibindo seus aplicativos e serviços

A exibição de aplicativos permite navegar pela hierarquia lógica do Service Fabric: aplicativos, serviços, partições e réplicas.

No exemplo abaixo, o aplicativo **MyApp** é composto de dois serviços, **MyStatefulService** e **WebSvcService**. Como **MyStatefulService** tem monitoração de estado, ele inclui uma partição com uma réplica primária e duas réplicas secundárias. Por outro lado, o WebSvcService não tem monitoração de estado e contém uma única instância.

![Exibição do aplicativo do Explorador do Service Fabric][sfx-application-tree]

Em cada nível da árvore, o painel principal mostra informações pertinentes sobre o item. Por exemplo, você pode ver o status de integridade e a versão de um serviço específico.

![Painel essencial do Explorador do Service Fabric][sfx-service-essentials]

### Exibir os nós do cluster

A exibição de Nós mostra o layout físico do cluster. Em cada nó, você pode inspecionar quais aplicativos têm código implantado nele e, mais especificamente, que réplicas estão sendo executadas atualmente no nó.

## Executar ações usando o Explorador do Service Fabric

O Explorador do Service Fabric oferece uma maneira rápida de invocar ações em nós, aplicativos e serviços no cluster.

Por exemplo, para excluir uma instância do aplicativo, basta escolher o aplicativo na árvore à esquerda e escolher Ações > Excluir o Aplicativo.

![Excluir um aplicativo no Explorador do Service Fabric][sfx-delete-application]

Como muitas ações são destrutivas, você será solicitado a confirmar a sua intenção antes que a ação seja concluída.

>[AZURE.NOTE]Todas as ações que podem ser executadas usando o Explorador do Service Fabric também podem ser executadas usando o PowerShell ou uma API REST e habilitando a automação.



## Conectando-se a um cluster remoto do Service Fabric

Como o Explorador do Service Fabric é baseado na Web e executado dentro do cluster, ele é acessível em qualquer navegador desde que você conheça o ponto de extremidade do cluster e tenha permissões suficientes para acessá-lo.

### Descobrir o ponto de extremidade do Explorador do Service Fabric para um Cluster remoto

Você pode descobrir o ponto de extremidade de cluster no portal do Service Fabric. Para acessar o Explorador do Service Fabric para um determinado cluster, basta conectar ao ponto de extremidade na porta 19007:

http://&lt;your-cluster-endpoint&gt;:19007

### Conectar a um cluster seguro

Você pode controlar o acesso ao seu cluster do Service Fabric exigindo que os clientes apresentem um certificado para se conectarem a ele.

Se você tentar conectar ao Explorador do Service Fabric em um cluster seguro, o seu navegador solicitará a apresentação de um certificado para obter acesso.

## Próximas etapas

- [Visão geral da capacidade de teste](service-fabric-testability-overview.md).
- [Gerenciar os seus aplicativos do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Implantação de aplicativos do Service Fabric usando o PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[applicationview]: ./media/service-fabric-visualizing-your-cluster/applicationview.png
[clustermap]: ./media/service-fabric-visualizing-your-cluster/clustermap.png
[connecttocluster]: ./media/service-fabric-visualizing-your-cluster/connecttocluster.png
[replicadetails]: ./media/service-fabric-visualizing-your-cluster/replicadetails.png
[servicefabricexplorer]: ./media/service-fabric-visualizing-your-cluster/servicefabricexplorer.png
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png

<!---HONumber=Nov15_HO4-->