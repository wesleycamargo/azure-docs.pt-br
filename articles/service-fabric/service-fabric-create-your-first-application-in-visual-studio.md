<properties
   pageTitle="Criar seu primeiro aplicativo do Service Fabric no Visual Studio | Microsoft Azure"
   description="Criar, implantar e depurar um aplicativo do Service Fabric usando o Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="seanmck"/>

# Criar seu primeiro aplicativo do Service Fabric no Visual Studio

O SDK do Service Fabric inclui um suplemento do Visual Studio que fornece modelos e ferramentas para criar, depurar e implantar aplicativos do Service Fabric. Este tópico orienta você pelo processo de criação de seu primeiro aplicativo no Visual Studio.

## Pré-requisitos

Antes de começar, verifique se você [configurou seu ambiente de desenvolvimento](service-fabric-get-started.md).

## Criar o aplicativo

Um aplicativo do Service Fabric pode conter um ou mais serviços, cada um com uma função específica no fornecimento de funcionalidade do aplicativo. O assistente de Novo Projeto permite criar um projeto de aplicativo junto com seu primeiro projeto de serviço. Você poderá adicionar outros serviços mais tarde.

1. Inicie o Visual Studio como um Administrador.

2. Clique em **Arquivo > Novo Projeto > Nuvem > Aplicativo do Service Fabric**.

3. Nomeie o aplicativo e clique em OK.

	![Caixa de diálogo Novo projeto no Visual Studio][1]

4. Na próxima caixa de diálogo, será solicitado que você escolha o primeiro tipo de serviço a ser incluído em seu aplicativo. Para os fins deste tutorial, vamos escolher "Serviço com Estado". Nomeie-o e clique em OK.

	![Caixa de diálogo Novo serviço no Visual Studio][2]

	>[AZURE.NOTE]Para saber mais sobre as opções, consulte [Escolhendo uma estrutura](service-fabric-choose-framework.md).

	O Visual Studio cria o projeto de aplicativo e o projeto de serviço com estado e os exibe no Gerenciador de Soluções.

	![Gerenciador de Soluções seguindo a criação de aplicativo com serviço com estado][3]

	O projeto de Aplicativo não contém nenhum código diretamente; em vez disso, ele faz referência a um conjunto de projetos de serviço. Além disso, ele contém três outros tipos de conteúdo:

	- **Perfis de Publicação**: usado para gerenciar preferências de ferramentas para ambientes diferentes.

	- **Scripts**: um script do PowerShell para implantação/atualização de seu aplicativo. Esse script é usado em segundo plano pelo Visual Studio e pode ser chamado diretamente na linha de comando.

	- **Definição de aplicativo**: o manifesto de aplicativo e os arquivos de parâmetros de aplicativo associados definem o aplicativo e permitem que você o configure especificamente para um determinado ambiente.

  Para obter uma visão geral do conteúdo do projeto de serviço, consulte [Introdução aos serviços confiáveis](service-fabric-reliable-services-quick-start.md).

## Implantar e depurar o aplicativo

Agora que temos um aplicativo, vamos tentar executá-lo.

1. Pressione F5 no Visual Studio para implantar o aplicativo para depuração.

	>[AZURE.NOTE]Isso levará algum tempo na primeira vez que o Visual Studio estiver criando um cluster local para desenvolvimento. Um cluster local é executado no mesmo código de plataforma que você criará em um cluster com vários computadores, só que em um único computador. Você verá o status de criação do cluster na janela de saída do Visual Studio.

	Quando o cluster estiver pronto, você receberá uma notificação do aplicativo gerenciador da bandeja do sistema do cluster local incluído com o SDK.

	![Notificação de bandeja do sistema de cluster local][4]

2. Assim que o aplicativo for iniciado, o Visual Studio abrirá automaticamente o Visualizador de Eventos de Diagnóstico, onde você poderá ver a saída de rastreamento do serviço.

	![Visualizador de eventos de diagnóstico][5]

	No caso do modelo de serviço com estado, as mensagens simplesmente mostram o valor do contador sendo incrementado no método `RunAsync` de MyStatefulService.cs.

3. Expanda um dos eventos para ver mais detalhes, inclusive o nó onde o código está sendo executado - nesse caso, é o nó 2, embora isso possa ser diferente em seu computador.

	![Detalhe do visualizador de eventos de diagnóstico][6]

	O cluster local é composto de cinco nós hospedados em uma única máquina, imitando um cluster de cinco nós onde os nós estão em computadores diferentes. Vamos desativar um dos nós no cluster local para simular a perda de uma máquina e experimentar o depurador do Visual Studio ao mesmo tempo.

    >[AZURE.NOTE]Os eventos de diagnóstico de aplicativo emitidos pelo modelo de projeto usam a classe `ServiceEventSource` incluída. Para saber mais, consulte [Como monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally)

4. Localize a classe em seu projeto de serviço que derive de StatefulService (por exemplo, MyStatefulService) e defina um ponto de interrupção na primeira linha do método `RunAsync`.

	![Ponto de interrupção no método RunAsync de serviço com estado][7]

5. Clique com o botão direito do mouse no aplicativo de bandeja do sistema do Gerenciador de Cluster Local de atalho e escolha Gerenciar Cluster Local para iniciar o serviço Service Fabric Explorer.

  ![Iniciar o Service Fabric Explorer do Gerenciador de Cluster Local][systray-launch-sfx]

  O Service Fabric Explorer oferece uma representação visual de um cluster, incluindo o conjunto de aplicativos implantados nele e o conjunto de nós físicos que o constituem.

6. No painel esquerdo, expanda **Cluster > Nós** e localize o nó onde o código está sendo executado.

7. Clique em **Ações > Desativar (Reiniciar)** para simular uma reinicialização do computador.

	![Parar um nó no Service Fabric Explorer][sfx-stop-node]

	Momentaneamente, você deverá ver o ponto de interrupção atingido no Visual Studio, já que a computação que você estava fazendo diretamente em um nó falha em outro.

8. Retorne ao Visualizador de Eventos de Diagnóstico e observe as mensagens. Observe que o contador continua aumentando, mesmo que os eventos estejam, na verdade, vindo de um nó diferente.

  ![Visualizador de eventos de diagnóstico após o failover][diagnostic-events-viewer-detail-post-failover]


## Próximas etapas

- [Saiba como criar um cluster no Azure](service-fabric-cluster-creation-via-portal.md)
- [Saiba mais sobre a criação de Serviços Confiáveis](service-fabric-reliable-services-quick-start.md)
- [Veja como você pode expor seus serviços na Internet com a WebAPI](service-fabric-add-a-web-frontend.md)
- [Tente criar um serviço usando o modelo de programação Ator Confiável](service-fabric-reliable-actors-get-started.md)

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

<!---HONumber=Nov15_HO4-->