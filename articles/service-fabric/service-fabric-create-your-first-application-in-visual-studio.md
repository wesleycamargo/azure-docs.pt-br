---
title: Criar seu primeiro aplicativo do Service Fabric no Visual Studio | Microsoft Docs
description: Criar, implantar e depurar um aplicativo do Service Fabric usando o Visual Studio
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2016
ms.author: ryanwi

---
# <a name="create-your-first-azure-service-fabric-application"></a>Criar seu primeiro aplicativo do Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
> 
> 

O SDK do Service Fabric inclui um suplemento do Visual Studio que fornece modelos e ferramentas para criar, implantar e depurar aplicativos do Service Fabric. Este tópico orienta você no processo de criação de seu primeiro aplicativo no Visual Studio.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, verifique se você [configurou o ambiente de desenvolvimento](service-fabric-get-started.md).

## <a name="video-walkthrough"></a>Passo a passo em vídeo
O vídeo a seguir descreve as etapas deste tutorial:

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio/player]
> 
> 

## <a name="create-the-application"></a>Criar o aplicativo
Um aplicativo do Service Fabric pode conter um ou mais serviços, cada um com uma função específica no fornecimento de funcionalidade do aplicativo. O assistente de Novo Projeto permite criar um projeto de aplicativo, junto com seu primeiro projeto de serviço. Você poderá adicionar outros serviços mais tarde.

1. Inicie o Visual Studio como um administrador.
2. Clique em **Arquivo > Novo Projeto > Nuvem > Aplicativo do Service Fabric**.
3. De um nome ao aplicativo e clique em **OK**.
   
    ![Caixa de diálogo Novo projeto no Visual Studio][1]
4. Na próxima página, escolha **Com Estado** como o primeiro tipo de serviço a ser incluído em seu aplicativo. Nomeie-o e clique em **OK**.
   
    ![Caixa de diálogo Novo serviço no Visual Studio][2]
   
   > [!NOTE]
   > Para obter mais informações sobre as opções, confira [Visão geral do modelo de programação do Service Fabric](service-fabric-choose-framework.md).
   > 
   > 
   
    O Visual Studio cria o projeto de aplicativo e o projeto de serviço com estado e os exibe no Gerenciador de Soluções.
   
    ![Gerenciador de Soluções seguindo a criação de aplicativo com serviço com estado][3]
   
    O projeto de aplicativo não contém qualquer código diretamente. Em vez disso, ele faz referência a um conjunto de projetos de serviço. Além disso, ele contém três outros tipos de conteúdo:
   
   * **Perfis de publicação**: usados para gerenciar preferências de ferramentas para ambientes diferentes.
   * **Scripts**: inclui um script do PowerShell para a implantação/atualização de seu aplicativo. O Visual Studio usa o script de bastidores do Visual Studio. O script também pode ser chamado diretamente na linha de comando.
   * **Definição de aplicativo**: inclui o manifesto do aplicativo em *ApplicationPackageRoot*. Os arquivos de parâmetros do aplicativo associados estão em *ApplicationParameters*, que definem o aplicativo e permitem que você o configure especificamente para determinado ambiente.
     
     Para obter uma visão geral do conteúdo do projeto de serviço, confira [Introdução aos Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Implantar e depurar o aplicativo
Agora que você tem um aplicativo, tente executá-lo.

1. Pressione F5 no Visual Studio para implantar o aplicativo para depuração.
   
   > [!NOTE]
   > A implantação leva algum tempo na primeira vez em que o Visual Studio está criando um cluster local para desenvolvimento. Um cluster local é executado no mesmo código de plataforma que você criará em um cluster com vários computadores, só que em um único computador. O status de criação do cluster aparece na janela de saída do Visual Studio.
   > 
   > 
   
    Quando o cluster estiver pronto, você receberá uma notificação do aplicativo gerenciador da bandeja do sistema do cluster local incluído com o SDK.
   
    ![Notificação de bandeja do sistema de cluster local][4]
2. Assim que o aplicativo for iniciado, o Visual Studio abre automaticamente o Visualizador de Eventos de Diagnóstico, onde você poderá ver a saída de rastreamento do serviço.
   
    ![Visualizador de eventos de diagnóstico][5]
   
    No caso do modelo de serviço com estado, as mensagens simplesmente mostram o valor do contador aumentando no método `RunAsync` de MyStatefulService.cs.
3. Expanda um dos eventos para ver mais detalhes, inclusive o nó onde o código estiver em execução. Nesse caso, é o _Node_2, que pode ser diferente em seu computador.
   
    ![Detalhe do visualizador de eventos de diagnóstico][6]
   
    O cluster local contém cinco nós hospedados em um único computador. Ele simula um cluster de cinco nós, onde os nós estão em computadores diferentes. Vamos desativar um dos nós no cluster local para simular a perda de uma máquina enquanto experimentamos o depurador do Visual Studio.
   
   > [!NOTE]
   > Os eventos de diagnóstico de aplicativo emitidos pelo modelo de projeto usam a classe `ServiceEventSource` incluída. Para saber mais, confira [Como monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
   > 
   > 
4. Localize a classe em seu projeto de serviço que derive de StatefulService (por exemplo, MyStatefulService) e defina um ponto de interrupção na primeira linha do método `RunAsync` .
   
    ![Ponto de interrupção no método RunAsync de serviço com estado][7]
5. Clique com o botão direito do mouse no aplicativo de bandeja do sistema do Gerenciador de Cluster Local de atalho e escolha **Gerenciar Cluster Local** para iniciar o Service Fabric Explorer.
   
    ![Iniciar o Service Fabric Explorer do Gerenciador de Cluster Local][systray-launch-sfx]
   
    O Service Fabric Explorer oferece uma representação visual de um cluster, incluindo o conjunto de aplicativos implantados nele e o conjunto de nós físicos que o constituem. Para saber mais sobre o Service Fabric Explorer, confira [Visualizando seu cluster](service-fabric-visualizing-your-cluster.md).
6. No painel esquerdo, expanda **Cluster > Nós** e localize o nó onde o código está sendo executado.
7. Clique em **Ações > Desativar (Reiniciar)** para simular uma reinicialização do computador. (Observe que você também pode desativar o menu de contexto no modo de exibição de lista de nós no painel esquerdo.)
   
    ![Parar um nó no Service Fabric Explorer][sfx-stop-node]
   
    Momentaneamente, você deverá ver o ponto de interrupção atingido no Visual Studio, já que a computação que você estava fazendo diretamente em um nó falha em outro.
8. Retorne ao Visualizador de Eventos de Diagnóstico e observe as mensagens. Observe que o contador continua aumentando, mesmo que os eventos estejam, na verdade, vindo de um nó diferente.
   
    ![Visualizador de eventos de diagnóstico após o failover][diagnostic-events-viewer-detail-post-failover]

## <a name="switch-cluster-mode"></a>Alternar o modo de cluster
Por padrão, o cluster de desenvolvimento local é configurado para ser executado como um cluster de cinco nós, o que é útil para depuração de serviços implantados em vários nós. A implantação de um aplicativo no cluster de desenvolvimento de cinco nós pode demorar algum tempo, no entanto. Se você quiser iterar as alterações de código rapidamente, sem executar seu aplicativo em cinco nós, você poderá alternar o cluster de desenvolvimento para o modo de Um nó. Para executar o código em um cluster com um nó, clique com botão direito no Gerenciador de Cluster Local na bandeja do sistema e selecione **Alternar Modo de Cluster -> 1 Nó**.  

![Alternar o modo de cluster][switch-cluster-mode]

Quando você altera o modo de cluster, o cluster de desenvolvimento é redefinido e todos os aplicativos provisionados, ou em execução, no cluster são removidos.

## <a name="cleaning-up"></a>Limpando
  Antes da conclusão, é importante lembrar que o cluster local é muito real. Parar o depurador remove a instância do aplicativo e cancela o registro do tipo de aplicativo. Porém, o cluster continua em execução em segundo plano. Você tem várias opções para gerenciar o cluster:

1. Para desativar o cluster, mas manter os dados e os rastreamentos do aplicativo, clique em **Parar Cluster Local** no aplicativo da bandeja do sistema.
2. Para excluir totalmente o cluster, clique em **Remover Cluster Local** no aplicativo da bandeja do sistema. Observe que essa opção resultará em outra implantação lenta na próxima vez que você pressionar F5 no Visual Studio. Somente exclua o cluster se você não pretende usar o cluster local por algum tempo ou se precisa recuperar recursos.

## <a name="next-steps"></a>Próximas etapas
* Saiba como criar um [cluster no Azure](service-fabric-cluster-creation-via-portal.md) ou um [cluster autônomo no Windows](service-fabric-cluster-creation-for-windows-server.md).
* Tente criar um serviço usando os modelos de programação [Reliable Services](service-fabric-reliable-services-quick-start.md) ou [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Saiba como você pode expor os serviços na Internet com um [front-end de serviço Web](service-fabric-add-a-web-frontend.md).
* Veja um [laboratório prático](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) e crie um serviço sem estado, configure o monitoramento e relatórios de integridade, e realize uma atualização do aplicativo.

<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png



<!--HONumber=Oct16_HO2-->


