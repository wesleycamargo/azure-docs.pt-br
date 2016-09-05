<properties
   pageTitle="Visualizar o seu cluster usando o Explorador do Service Fabric | Microsoft Azure"
   description="O Explorador do Service Fabric é uma ferramenta baseada na Web para inspecionar e gerenciar aplicativos em nuvem e nós em um cluster do Service Fabric do Microsoft Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/22/2016"
   ms.author="seanmck"/>

# Visualizando o cluster com o Explorador do Service Fabric

O Explorador do Service Fabric é uma ferramenta baseada na Web para inspecionar e gerenciar aplicativos e nós em um cluster do Service Fabric do Azure. O Explorador do Service Fabric é hospedado diretamente dentro do cluster para estar sempre disponível, independentemente de onde o cluster estiver sendo executado.

## Conectar ao Explorador do Service Fabric

Se você seguiu as instruções para [preparar o seu ambiente de desenvolvimento](service-fabric-get-started.md), pode iniciar o Explorador do Service Fabric no cluster local navegando para http://localhost:19080/Explorer.

>[AZURE.NOTE] Se estiver usando o Internet Explorer com o Explorador do Service Fabric para gerenciar um cluster remoto, você precisará definir algumas configurações do Internet Explorer. Vá para **Ferramentas** > **Configurações do Modo de Exibição de Compatibilidade** e desmarque **Exibir sites da intranet no Modo de Exibição de Compatibilidade** para garantir que todas as informações sejam carregadas corretamente.

## Entender o layout do Explorador do Service Fabric

Você pode navegar pelo Explorador do Service Fabric usando a árvore à esquerda. Na raiz da árvore, o painel do cluster fornece uma visão geral do cluster, incluindo um resumo do aplicativo e a integridade do nó.

![Painel de clusters do Explorador do Service Fabric][sfx-cluster-dashboard]

### Exibir o layout do cluster

Nós em um cluster do Service Fabric são colocados em uma grade bidimensional de domínios de falha e domínios de atualização. Esse posicionamento garante que seus aplicativos permaneçam disponíveis na presença de falhas de hardware e atualizações de aplicativos. Você pode exibir o layout do cluster atual usando o mapa de clusters.

![Mapa de clusters do Explorador do Service Fabric][sfx-cluster-map]

### Exibir aplicativos e serviços

O cluster contém duas subárvores: uma para aplicativos e outra para nós.

Você pode usar a exibição de aplicativos para navegar pela hierarquia lógica do Service Fabric: aplicativos, serviços, partições e réplicas.

No exemplo abaixo, o aplicativo **MyApp** é composto de dois serviços, **MyStatefulService** e **WebService**. Como o **MyStatefulService** tem monitoração de estado, ele inclui uma partição com uma réplica principal e duas secundárias. Por outro lado, o WebSvcService é sem monitoração de estado e contém uma única instância.

![Exibição do aplicativo do Explorador do Service Fabric][sfx-application-tree]

Em cada nível da árvore, o painel principal mostra informações pertinentes sobre o item. Por exemplo, você pode ver o status de integridade e a versão de um serviço específico.

![Painel essencial do Explorador do Service Fabric][sfx-service-essentials]

### Exibir os nós do cluster

A exibição de nós mostra o layout físico do cluster. Para um nó específico, você pode inspecionar quais aplicativos têm código implantado naquele nó. Mais especificamente, você pode ver quais réplicas estão sendo executadas lá atualmente.

## Ações

O Explorador do Service Fabric oferece uma maneira rápida de invocar ações em nós, aplicativos e serviços no cluster.

Por exemplo, para excluir uma instância do aplicativo, basta escolher o aplicativo na árvore à esquerda e escolher **Ações** > **Excluir Aplicativo**.

![Excluir um aplicativo no Explorador do Service Fabric][sfx-delete-application]

>[AZURE.TIP] Você pode executar as mesmas ações clicando nas reticências ao lado de cada elemento.

A tabela a seguir lista as ações disponíveis para cada entidade:

| **Entidade** | **Ação** | **Descrição** |
| ------ | ------ | ----------- |
| Tipo de aplicativo | Remover o provisionamento de tipo | Remover o pacote de aplicativos do repositório de imagens do cluster. Requer que todos os aplicativos desse tipo sejam removidos em primeiro lugar. |
| Aplicativo | Excluir aplicativo | Exclua o aplicativo, incluindo todos os seus serviços e o estado (se houver). |
| O Barramento de | Excluir serviço | Exclua o serviço e seu estado (se houver). |
| Nó | Ativar | Ative o nó. |
|| Desativar (pausa) | Pause o nó em seu estado atual. Os serviços continuam sendo executados, mas o Service Fabric não move nada dele ou para ele proativamente, exceto se for necessário para impedir uma interrupção ou inconsistência de dados. Essa ação normalmente é usada para habilitar serviços de depuração em um nó específico a fim de garantir que não sejam movidos durante a inspeção. |
|| Desativar (reiniciar) | Mova todos os serviços na memória de um nó com segurança e feche os serviços persistentes. Normalmente usado quando os processos de host ou o computador precisam ser reiniciados. |
|| Desativar (remover dados) | Feche com segurança todos os serviços em execução no nó após a criação de réplicas de reposição suficientes. Normalmente usado quando um nó (ou pelo menos seu armazenamento) é desabilitado permanentemente. |
|| Remover o estado do nó | Remova o conhecimento das réplicas do nó do cluster. Normalmente usado quando um nó com falha já é considerado irrecuperável. |

Como muitas ações são destrutivas, talvez seja necessário confirmar sua intenção antes que a ação seja concluída.

>[AZURE.TIP] Todas as ações que podem ser executadas usando o Explorador do Service Fabric também podem ser executadas usando o PowerShell ou uma API REST para habilitar a automação.

Você também pode usar o Service Fabric Explorer para criar novas instâncias de aplicativo para um determinado tipo e versão de aplicativo. Escolha o tipo de aplicativo no modo de exibição de árvore, clique no link **Create app instance (Criar instância de aplicativo)** ao lado da versão desejada no painel da direita.

![Criar uma instância de aplicativo no Service Fabric Explorer][sfx-create-app-instance]

>[AZURE.NOTE] Instâncias de aplicativos criadas por meio do Service Fabric Explorer no momento não podem ser parametrizadas. Elas são criadas usando os valores de parâmetro padrão.

## Conectar a um cluster remoto do Service Fabric

Como o Explorador do Service Fabric é baseado na Web e executado dentro do cluster, ele é acessível em qualquer navegador desde que você conheça o ponto de extremidade do cluster e tenha permissões suficientes para acessá-lo.

### Descobrir o ponto de extremidade do Explorador do Service Fabric para um cluster remoto

Para acessar o Explorador do Service Fabric para um determinado cluster, basta apontar seu navegador para:

http://&lt;your-cluster-endpoint&gt;:19080/Explorer

A URL completa também está disponível no painel essencial do cluster do portal do Azure.

### Conectar a um cluster seguro

Você pode controlar o acesso do cliente ao cluster do Service Fabric com certificados ou usando o AAD (Azure Active Directory).

Se você tentar se conectar ao Service Fabric Explorer em um cluster seguro, precisará apresentar um certificado do cliente ou fazer logon usando o AAD, dependendo da configuração do cluster.

## Próximas etapas

- [Visão geral da Possibilidade de Teste](service-fabric-testability-overview.md)
- [Gerenciando aplicativos da Malha do Serviço no Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Implantação de aplicativos de Malha do Serviço usando o PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png

<!---HONumber=AcomDC_0824_2016-->