---
title: "Criar um serviço confiável do Azure Service Fabric em C#"
description: Crie, implante e depure um aplicativo Reliable Services baseado no Azure Service Fabric com o Visual Studio.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 43f77a1a2e1bbe28bb646aa23c28c253c20e8dda
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2018
---
# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>Criar seu primeiro aplicativo Reliable Services com estado do Service Fabric em C#

Saiba como implantar seu primeiro aplicativo do Azure Service Fabric para .NET no Windows em apenas alguns minutos. Quando você terminar, terá um cluster local com um aplicativo Reliable Services.

## <a name="prerequisites"></a>pré-requisitos

Antes de começar, verifique se você [configurou o ambiente de desenvolvimento](service-fabric-get-started.md). Esse processo inclui a instalação do SDK do Service Fabric e do Visual Studio 2015 ou 2017.

## <a name="create-the-application"></a>Criar o aplicativo

1. Inicie o Visual Studio como administrador.

2. Crie um projeto selecionando Ctrl+Shift+N.

3. Na caixa de diálogo **Novo Projeto**, selecione **Nuvem** > **Aplicativo do Service Fabric**.

4. Nomeie o aplicativo **MyApplication**. Depois, selecione **OK**.

   ![Caixa de diálogo Novo projeto no Visual Studio][1]

5. Você pode criar qualquer tipo de aplicativo do Service Fabric na próxima caixa de diálogo. Para este guia de início rápido, escolha **Serviço com Estado**.

6. Nomeie o serviço **MyStatefulService**. Depois, selecione **OK**.

    ![Caixa de diálogo Novo serviço no Visual Studio][2]

    O Visual Studio cria o projeto de aplicativo e o projeto de serviço com estado. Em seguida, ele os exibe no Gerenciador de Soluções.

    ![Gerenciador de Soluções seguindo a criação de aplicativo com serviço com estado][3]

    O projeto de aplicativo (**MyApplication**) não contém nenhum código. Em vez disso, ele faz referência a um conjunto de projetos de serviço. Ele também tem três outros tipos de conteúdo:

    * **Perfis de publicação**  
    Perfis para a implantação em ambientes diferentes.

    * **Scripts**  
    Scripts do PowerShell para implantar ou atualizar seu aplicativo.

    * **Definição de aplicativo**  
Inclui o arquivo ApplicationManifest.xml em *ApplicationPackageRoot*, que descreve a composição do aplicativo. Os arquivos de parâmetro de aplicativo associados estão em *ApplicationParameters*, que pode ser usado para definir parâmetros específicos do ambiente. O Visual Studio seleciona um arquivo de parâmetro de aplicativo que é especificado no perfil de publicação associado.
    
Para obter uma visão geral do conteúdo do projeto de serviço, confira [Introdução aos Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Implantar e depurar o aplicativo

Agora que você tem um aplicativo, execute-o, implante-o e depure-o executando as etapas a seguir.

1. Pressione F5 no Visual Studio a fim de implantar o aplicativo para depuração.

    >[!NOTE]
    >Na primeira vez em que você executar e implantar o aplicativo localmente, o Visual Studio criará um cluster local para depuração. Isso pode levar algum tempo. O status de criação do cluster é exibido na janela de saída do Visual Studio.

    Quando o cluster estiver pronto, você receberá uma notificação do aplicativo gerenciador da bandeja do sistema do cluster local que vem incluído com o SDK.
    
    ![Notificação de bandeja do sistema de cluster local][4]

    Quando o aplicativo for iniciado, o Visual Studio abre automaticamente o Visualizador de Eventos de Diagnóstico, onde você poderá ver a saída de rastreamento dos seus serviços.
    
    ![Visualizador de eventos de diagnóstico][5]

    >[!NOTE]
    >Os eventos devem iniciar o rastreamento automaticamente no Visualizador de Eventos de Diagnóstico. Se você precisar configurar manualmente o Visualizador de Eventos de Diagnóstico, abra primeiro o arquivo `ServiceEventSource.cs` localizado no projeto **MyStatefulService**. Copie o valor do atributo `EventSource` na parte superior da classe `ServiceEventSource`. No exemplo a seguir, o evento de origem é chamado `"MyCompany-MyApplication-MyStatefulService"`, mas pode ser diferente na sua situação.
>
    >![Localizar o nome de origem do evento de serviço][service-event-source-name]


2. Em seguida, abra a caixa de diálogo **Provedores de ETW**. Em seguida, selecione o ícone de engrenagem localizado na guia **Eventos de Diagnóstico**. Cole o nome da origem de eventos que você copiou na caixa de entrada **Provedores de ETW**. Em seguida, selecione o botão **Aplicar**. Isso inicia automaticamente o rastreamento de eventos.

    ![Nome de origem do evento de diagnóstico de configuração][setting-event-source-name]

    Agora você deve ver os eventos aparecerem na janela Eventos de Diagnóstico.

    O modelo de serviço com estado mostra um incremento de valor de contador no método `RunAsync` de **MyStatefulService.cs**.

3. Expanda um dos eventos para ver mais detalhes, inclusive o nó onde o código estiver em execução. Nesse caso, é o **\_Node\_0,** embora possa ser diferente em seu computador.
   
    ![Detalhe do visualizador de eventos de diagnóstico][6]

4. O cluster local contém cinco nós que estão hospedados em um único computador. Em um ambiente de produção, cada nó está hospedado em uma máquina virtual ou física distinta. Para simular a perda de um computador enquanto você experimenta o depurador do Visual Studio, desative um dos nós no cluster local.

5. Na janela **Gerenciador de Soluções**, abra **MyStatefulService.cs**. 

6. Localize o método `RunAsync` e defina um ponto de interrupção na primeira linha do método.

    ![Ponto de interrupção no método RunAsync de serviço com estado][7]

7. Inicie a ferramenta Service Fabric Explorer clicando com o botão direito do mouse no aplicativo de bandeja do sistema **Gerenciador de Cluster Local** e selecionando **Gerenciar Cluster Local**.

    ![Iniciar o Service Fabric Explorer do gerenciador de cluster local][systray-launch-sfx]

    O [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) oferece uma representação visual de um cluster. Ele inclui o conjunto de aplicativos implantados nele e o conjunto de nós físicos que o compõem.

8. No painel esquerdo, expanda **Cluster** > **Nós** e localize o nó onde o código está sendo executado. Em seguida, para simular uma reinicialização do computador, selecione **Ações** > **Desativar (Reiniciar)**.

    ![Parar um nó no Service Fabric Explorer][sfx-stop-node]

    Momentaneamente, você deverá ver o ponto de interrupção atingido no Visual Studio, já que a computação que você estava fazendo diretamente em um nó falha em outro.

9. Em seguida, retorne ao Visualizador de Eventos de Diagnóstico e observe as mensagens. O contador continua aumentando, mesmo que os eventos estejam, na verdade, vindo de um nó diferente.

    ![Visualizador de eventos de diagnóstico após o failover][diagnostic-events-viewer-detail-post-failover]

## <a name="clean-up-the-local-cluster-optional"></a>Limpar o cluster local (opcional)

Lembre-se de que esse cluster local é real. Parar o depurador remove a instância do aplicativo e cancela o registro do tipo de aplicativo. No entanto, o cluster continua em execução em segundo plano. Quando você estiver pronto para parar o cluster local, terá duas opções.

### <a name="keep-application-and-trace-data"></a>Manter os dados de aplicativo e rastreamento

Desligue o cluster clicando com o botão direito do mouse no aplicativo de bandeja do sistema **Gerenciador de Cluster Local** e selecionando **Parar Cluster Local**.

### <a name="delete-the-cluster-and-all-data"></a>Excluir o cluster e todos os dados

Remova o cluster clicando com o botão direito do mouse no aplicativo de bandeja do sistema **Gerenciador de Cluster Local**. Em seguida, escolha **Remover Cluster Local**. 

Se você escolher essa opção, o Visual Studio reimplantará o cluster na próxima vez em que executar o aplicativo. Escolha essa opção se você não pretende usar o cluster local por algum tempo ou se precisa recuperar recursos.

## <a name="next-steps"></a>Próximas etapas
Leia mais sobre [Reliable Services](service-fabric-reliable-services-introduction.md).
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
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
