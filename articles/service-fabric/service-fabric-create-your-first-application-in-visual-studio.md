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
   ms.date="03/27/2016"
   ms.author="seanmck"/>

# Criar seu primeiro aplicativo do Azure Service Fabric no Visual Studio

O SDK do Service Fabric inclui um suplemento do Visual Studio que fornece modelos e ferramentas para criar, implantar e depurar aplicativos do Service Fabric. Este tópico orienta você pelo processo de criação de seu primeiro aplicativo no Visual Studio.

## Pré-requisitos

Antes de começar, verifique se você [configurou o ambiente de desenvolvimento](service-fabric-get-started.md).

## Passo a passo em vídeo

O vídeo a seguir descreve as etapas deste tutorial:

>[AZURE.VIDEO creating-your-first-service-fabric-application-in-visual-studio]

## Criar o aplicativo

Um aplicativo do Service Fabric pode conter um ou mais serviços, cada um com uma função específica no fornecimento de funcionalidade do aplicativo. O assistente de Novo Projeto permite criar um projeto de aplicativo, junto com seu primeiro projeto de serviço. Você poderá adicionar outros serviços mais tarde.

1. Inicie o Visual Studio como um administrador.

2. Clique em **Arquivo > Novo Projeto > Nuvem > Aplicativo do Service Fabric**.

3. De um nome ao aplicativo e clique em **OK**.

	![Caixa de diálogo Novo projeto no Visual Studio][1]

4. Na próxima página, será solicitado que você escolha o primeiro tipo de serviço a ser incluído em seu aplicativo. Para os fins deste tutorial, escolheremos **Serviço com Estado**. Nomeie-o e clique em **OK**.

	![Caixa de diálogo Novo serviço no Visual Studio][2]

	>[AZURE.NOTE] Para saber mais sobre as opções, veja [Escolhendo uma estrutura](service-fabric-choose-framework.md).

	O Visual Studio cria o projeto de aplicativo e o projeto de serviço com estado e os exibe no Gerenciador de Soluções.

	![Gerenciador de Soluções seguindo a criação de aplicativo com serviço com estado][3]

	O projeto de aplicativo não contém qualquer código diretamente. Em vez disso, ele faz referência a um conjunto de projetos de serviço. Além disso, ele contém três outros tipos de conteúdo:

	- **Perfis de publicação**: usados para gerenciar preferências de ferramentas para ambientes diferentes.

	- **Scripts**: inclui um script do PowerShell para a implantação/atualização de seu aplicativo. Esse script é usado em segundo plano pelo Visual Studio e pode ser chamado diretamente na linha de comando.

	- **Definição de aplicativo**: inclui o manifesto de aplicativo em *ApplicationPackageRoot* e arquivos de parâmetro de aplicativo associados em *ApplicationParameters* que definem o aplicativo e permitem que você o configure especificamente para um determinado ambiente.

    Para obter uma visão geral do conteúdo do projeto de serviço, confira [Introdução aos Reliable Services](service-fabric-reliable-services-quick-start.md).

## Implantar e depurar o aplicativo

Agora que você tem um aplicativo, poderá tentar executá-lo.

1. Pressione F5 no Visual Studio para implantar o aplicativo para depuração.

	>[AZURE.NOTE] Isso levará algum tempo na primeira vez em que o Visual Studio estiver criando um cluster local para desenvolvimento. Um cluster local é executado no mesmo código de plataforma que você criará em um cluster com vários computadores, só que em um único computador. Você verá o status de criação do cluster na janela de saída do Visual Studio.

	Quando o cluster estiver pronto, você receberá uma notificação do aplicativo gerenciador da bandeja do sistema do cluster local incluído com o SDK.

	![Notificação de bandeja do sistema de cluster local][4]

2. Assim que o aplicativo for iniciado, o Visual Studio abrirá automaticamente o Visualizador de Eventos de Diagnóstico, onde você poderá ver a saída de rastreamento do serviço.

	![Visualizador de eventos de diagnóstico][5]

	No caso do modelo de serviço com estado, as mensagens simplesmente mostram o valor do contador sendo incrementado no método `RunAsync` de MyStatefulService.cs.

3. Expanda um dos eventos para ver mais detalhes, inclusive o nó onde o código estiver em execução. Nesse caso, é o \_Node\_2, que pode ser diferente em seu computador.

	![Detalhe do visualizador de eventos de diagnóstico][6]

	O cluster local contém cinco nós hospedados em um único computador. Ele simula um cluster de cinco nós, onde os nós estão em computadores diferentes. Vamos desativar um dos nós no cluster local para simular a perda de uma máquina e experimentar o depurador do Visual Studio ao mesmo tempo.

    >[AZURE.NOTE] Os eventos de diagnóstico de aplicativo emitidos pelo modelo de projeto usam a classe `ServiceEventSource` incluída. Para saber mais, confira [Como monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

4. Localize a classe em seu projeto de serviço que derive de StatefulService (por exemplo, MyStatefulService) e defina um ponto de interrupção na primeira linha do método `RunAsync`.

	![Ponto de interrupção no método RunAsync de serviço com estado][7]

5. Clique com o botão direito do mouse no aplicativo de bandeja do sistema do Gerenciador de Cluster Local de atalho e escolha **Gerenciar Cluster Local** para iniciar o Service Fabric Explorer.

    ![Iniciar o Service Fabric Explorer do Gerenciador de Cluster Local][systray-launch-sfx]

    O Service Fabric Explorer oferece uma representação visual de um cluster, incluindo o conjunto de aplicativos implantados nele e o conjunto de nós físicos que o constituem. Para saber mais sobre o Service Fabric Explorer, confira [Visualizando seu cluster](service-fabric-visualizing-your-cluster.md).

6. No painel esquerdo, expanda **Cluster > Nós** e localize o nó onde o código está sendo executado.

7. Clique em **Ações > Desativar (Reiniciar)** para simular uma reinicialização do computador. (Observe que você também pode fazer isso em um menu de contexto na exibição de lista de nós no painel esquerdo selecionando o ícone de três pontos.)

	![Parar um nó no Service Fabric Explorer][sfx-stop-node]

	Momentaneamente, você deverá ver o ponto de interrupção atingido no Visual Studio, já que a computação que você estava fazendo diretamente em um nó falha em outro.

8. Retorne ao Visualizador de Eventos de Diagnóstico e observe as mensagens. Observe que o contador continua aumentando, mesmo que os eventos estejam, na verdade, vindo de um nó diferente.

    ![Visualizador de eventos de diagnóstico após o failover][diagnostic-events-viewer-detail-post-failover]

### Limpando

  Antes da conclusão, é importante lembrar que o cluster local é muito real. Mesmo após a interrupção do depurador e do fechamento do Visual Studio, seus aplicativos continuarão a ser executados em segundo plano. Dependendo da natureza de seus aplicativos, essa atividade em segundo plano poderá ocupar recursos significativos em seu computador. Você tem várias opções para gerenciar isto:

  1. Para remover um aplicativo individual e todos os seus dados, use a ação **Excluir aplicativo** no Service Fabric Explorer, seja com o menu **AÇÕES** ou com o menu de contexto no modo de exibição de lista de aplicativos no painel esquerdo.
  
    ![Excluir um aplicativo no Service Fabric Explorer][sfe-delete-application]
    
  2. Depois de excluir o aplicativo do cluster, em seguida, você pode selecionar o **Tipo de Desprovisionamento** para o aplicativo, o que remove o pacote de aplicativos, incluindo o código e a configuração, do repositório de imagem do cluster.
  3. Para desligar o cluster, mas manter os dados e os rastreamentos do aplicativo, clique em **Parar Cluster Local** no aplicativo da bandeja do sistema.

  4. Para excluir totalmente o cluster, clique em **Remover Cluster Local** no aplicativo da bandeja do sistema. Observe que essa opção resultará em outra implantação lenta na próxima vez que você pressionar F5 no Visual Studio. Use isso somente se você não pretender usar o cluster local por algum tempo ou se precisar recuperar recursos.



## Próximas etapas

<!--
Temporarily removing this link because we have removed the ASP.NET template.

 - [See how you can expose your services to the Internet with a web service front end](service-fabric-add-a-web-frontend.md)
-->
- [Saiba como criar um cluster no Azure](service-fabric-cluster-creation-via-portal.md)
- [Saiba mais sobre os Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Tente criar um serviço usando o modelo de programação Reliable Actors](service-fabric-reliable-actors-get-started.md)

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

<!---HONumber=AcomDC_0406_2016-->