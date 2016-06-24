<properties 
	pageTitle="Criar uma aplicativo Web Hello World para o Azure no IntelliJ | Microsoft Azure" 
	description="Este tutorial mostra como usar o Kit de Ferramentas do Azure para IntelliJ para criar um aplicativo Web Hello World para o Azure." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="selvasingh" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/07/2016" 
	ms.author="asirveda;robmcm"/>

# Criar um aplicativo Web Hello World para o Azure no IntelliJ

Este tutorial mostra como criar e implantar um aplicativo Hello World básico para o Azure como um aplicativo Web usando o [Kit de Ferramentas do Azure para IntelliJ]. Um exemplo básico de JSP é exibido para manter a simplicidade, mas etapas muito semelhantes podem ser apropriadas para um servlet Java quando o assunto é a implantação do Azure.

Após a conclusão deste tutorial, seu aplicativo será semelhante à ilustração a seguir quando exibido em um navegador da Web:

![][01]
 
## Pré-requisitos

* Um JDK (Java Developer Kit) versão 1.7 ou posterior.
* IntelliJ IDEA Ultimate Edition. Isso pode ser baixado em <https://www.jetbrains.com/idea/download/index.html>.
* Uma distribuição de um servidor Web ou de um servidor de aplicativo baseado em Java, como o Apache Tomcat ou o Jetty.
* Uma assinatura do Azure, que pode ser adquirida de <https://azure.microsoft.com/free/> ou de <http://azure.microsoft.com/pricing/purchase-options/>.
* O Kit de Ferramentas do Azure para IntelliJ. Para saber mais, confira [Instalação do Kit de Ferramentas do Azure para IntelliJ].

## Para criar um aplicativo Hello World

Primeiro, vamos começar com a criação de um projeto Java.

1. Inicie o IntelliJ e, no menu, clique em **Arquivo**, então em **novo** e clique em **Projeto**.

   ![][02]

1. Na caixa de diálogo Novo Projeto, selecione **Java**, **Aplicativo Web** e clique em **Avançar**.

   ![][03a]

   Se for solicitado a continuar sem SDK atribuído, clique em **Sim**.

   ![][03b]

1. Para fins deste tutorial, nomeie o projeto como **Java-Web-App-On-Azure** e clique em **Concluir**.

   ![][04]

1. Na exibição do Explorador de Projetos do IntelliJ, expanda **Java-Web-App-On-Azure**, expanda **web** e clique duas vezes em **index.jsp**.

   ![][05]

1. Quando o arquivo index.jsp for aberto no IntelliJ, adicione o texto para exibir dinamicamente **Hello World!** dentro do elemento `<body>` existente. Seu conteúdo do `<body>` atualizado deve ser parecido com o exemplo a seguir:

   `<body><b><% out.println("Hello World!"); %></b></body>`

1. Salve o index.jsp.

## Para implantar seu aplicativo em um contêiner de aplicativos Web do Azure

Há várias maneiras pelas quais você pode implantar um aplicativo Web Java no Azure. Este tutorial descreve uma das mais simples: o aplicativo será implantado em um contêiner de aplicativos Web do Azure, e não há a necessidade de qualquer tipo de projeto especial nem de ferramentas adicionais. O JDK e o software do contêiner da Web serão fornecidos a você pelo Azure, portanto, não é necessário carregar seu próprio; tudo o que você precisa é de seu aplicativo Web Java. Como resultado, o processo de publicação de seu aplicativo demorará segundos, e não minutos.

1. No Explorador de Projetos do IntelliJ, clique com o botão direito do mouse no projeto **Java-Web-App-On-Azure**. Quando o menu de contexto for exibido, selecione **Azure** e clique em **Publicar como Aplicativo Web do Azure...**

   ![][06]

1. Se você ainda não tiver entrado no Azure por meio do IntelliJ, será solicitada a entrada em sua conta do Azure:

   ![][07]

   Observação: se você tiver várias contas do Azure, alguns dos avisos mostrados durante o processo de entrada poderão ser exibidos mais de uma vez, mesmo se forem, aparentemente, os mesmos. Quando isso acontecer, continue seguindo as instruções de entrada.

1. Após o logon bem-sucedido em sua conta do Azure, a caixa de diálogo **Gerenciar Assinaturas** exibirá uma lista de assinaturas associadas às suas credenciais. Se houver várias assinaturas listadas e se você quiser trabalhar com apenas um subconjunto específico delas, poderá, opcionalmente, desmarcar as que deseja usar. Depois de selecionar suas assinaturas, clique em **Fechar**.

   ![][08]

1. Quando a caixa de diálogo **Implantar no Contêiner do Aplicativo Web do Azure** for mostrada, ela exibirá todos os contêineres do Aplicativo Web criados anteriormente por você; se você não tiver criado nenhum contêiner, a lista estará vazia.   

   ![][09]

1. Se você nunca tiver criado um contêiner de aplicativos Web do Azure, ou se quiser publicar seu aplicativo em um novo contêiner, use as etapas a seguir. Caso contrário, selecione um Contêiner de Aplicativo Web existente e pule para a etapa 6 abaixo.

  1. Clique em **+**.

        ![][10]

  1. A caixa de diálogo **Novo Contêiner de Aplicativo Web** será exibida e será usada nas próximas etapas.

        ![][11]

  1. Insira um **Rótulo DNS** para seu Contêiner do Aplicativo Web; isso formará o rótulo DNS folha da URL do host de seu aplicativo Web no Azure. Observação: o nome deve estar disponível e de acordo com os requisitos de nomenclatura de aplicativo Web do Azure.

  1. No menu suspenso **Contêiner da Web**, selecione o software apropriado ao seu aplicativo.

        No momento, você pode escolher entre o Tomcat 8, Tomcat 7 ou Jetty 9. Uma distribuição recente do software selecionado será fornecida pelo Azure e ele será executado em uma distribuição recente do JDK 8 criado pela Oracle e fornecido pelo Azure.

  1. No menu suspenso **Assinatura**, selecione a assinatura que deseja usar para essa implantação.

  1. No menu suspenso **Grupo de Recursos**, selecione o Grupo de Recursos com o qual deseja associar seu Aplicativo Web.

        Observação: os Grupos de Recursos do Azure lhe permitem agrupar recursos relacionados para que, por exemplo, possam ser excluídos juntos.

        Você pode selecionar um Grupo de Recursos existente (se houver) e ignorar a etapa g abaixo ou usar as seguintes etapas para criar um novo Grupo de Recursos:

      * Clique em **Novo...**

      * A caixa de diálogo **Novo Grupo de Recursos** será exibida:

            ![][12]

      * Na caixa de texto **Nome**, especifique um nome para o novo Grupo de Recursos.

      * No menu suspenso **Região**, selecione o local do data center do Azure apropriado ao Grupo de Recursos.

      * Clique em **OK**.

  1. O menu suspenso **Plano do Serviço de Aplicativo** lista os planos do serviço de aplicativo associados ao Grupo de Recursos selecionado.

        Observação: um Plano do Serviço de Aplicativo especifica informações como o local de seu Aplicativo Web, o tipo de preço e o tamanho da instância de computação. Um único Plano do Serviço de Aplicativo pode ser usado para vários Aplicativos Web, por isso, ele é mantido separadamente de uma implantação de Aplicativo Web específica.

        Você pode selecionar um Plano do Serviço de Aplicativo existente (se houver) e ignorar a etapa h abaixo ou usar as seguintes etapas para criar um novo Plano do Serviço de Aplicativo:

      * Clique em **Novo...**

      * A caixa de diálogo **Novo Plano do Serviço de Aplicativo** será exibida:

            ![][13]

      * Na caixa de texto **Nome**, especifique um nome para o novo Plano do Serviço de Aplicativo.

      * No menu suspenso **Local**, selecione o local do data center do Azure apropriado ao plano.

      * No menu suspenso **Tipo de Preço**, selecione o preço apropriado ao plano. Para fins de teste, é possível escolher **Gratuito**.

      * No menu suspenso **Tamanho da Instância**, selecione o tamanho de instância apropriado ao plano. Para fins de teste, é possível escolher **Pequeno**.

  1. Depois de concluir todas as etapas acima, a caixa de diálogo New Web App Container (Novo Contêiner de Aplicativos Web) deve ser semelhante à ilustração a seguir:

        ![][14]

  1. Clique em **OK** para concluir a criação do novo contêiner do Aplicativo Web.

        Aguarde alguns segundos para a lista de contêineres de Aplicativo Web ser atualizada. O contêiner do aplicativo Web recém-criado agora deve ser selecionado na lista.

1. Agora você está pronto para concluir a implantação inicial do seu aplicativo Web no Azure. Clique em **OK** para implantar seu aplicativo Java no contêiner do aplicativo Web selecionado.

    ![][15]

    Observação: por padrão, o aplicativo será implantado como um subdiretório do servidor de aplicativos. Se desejar implantá-lo como o aplicativo raiz, marque a caixa de seleção **Implantar na raiz** antes de clicar em **OK**.

1. Em seguida, você deverá ver o modo de exibição do **Log de Atividades do Azure**, que indicará o status da implantação de seu Aplicativo Web.

    ![][16]

    O processo de implantação de seu aplicativo Web no Azure deve demorar apenas alguns segundos para ser concluído. Quando seu aplicativo estiver pronto, você verá um link chamado **Publicado** na coluna **Status**. Quando você clicar no link, ele o levará à página inicial do seu aplicativo Web implantado, ou você pode usar as etapas da seção a seguir para navegar até o seu aplicativo Web.

## Navegando até seu aplicativo Web no Azure

Para navegar até o aplicativo Web no Azure, você poderá usar o modo de exibição do **Azure Explorer**.

Se o modo de exibição do **Azure Explorer** ainda não estiver aberto, você poderá abri-lo clicando no menu **View** no IntelliJ, clicando em **Tool Windows** e em **Service Explorer**. Se você não tiver feito logon anteriormente, receberá uma solicitação para fazê-lo.

Quando o modo de exibição do **Azure Explorer** for exibido, use estas etapas para interromper o Aplicativo Web:

1. Expanda o nó **Azure**.

1. Expanda o nó **Aplicativos Web**.

1. Clique com botão direito do mouse no Aplicativo Web desejado.

1. Quando o menu de contexto for exibido, clique em **Abrir no Navegador**.

    ![][17]

## Atualização de seu aplicativo Web

A atualização de um aplicativo Web do Azure em execução é um processo rápido e fácil, e você tem duas opções de atualização:

* Você pode atualizar a implantação de um aplicativo Web Java existente.
* Você pode publicar um aplicativo Java adicional no mesmo contêiner de aplicativo Web.

Em ambos os casos, o processo é idêntico e demora apenas alguns segundos:

1. No explorador de projetos do IntelliJ, clique com o botão direito do mouse no aplicativo Java que você deseja atualizar ou adicionar a um contêiner de aplicativos Web existente.

1. Quando o menu de contexto for exibido, selecione **Azure** e, em seguida, **Publicar como Aplicativo Web do Azure...**

1. Como você já fez logon anteriormente, verá uma lista com seus contêineres de aplicativo Web existentes. Selecione aquele no qual deseja publicar ou publicar novamente o aplicativo Java e clique em **OK**.

Após alguns segundos, o modo de exibição do **Log de Atividades do Azure** mostrará a implantação atualizada como **Publicada** e será possível verificar o aplicativo atualizado em um navegador da Web.

## Iniciar ou parar aplicativo Web existente

Para iniciar ou parar um contêiner do Aplicativo Web do Azure existente, (incluindo todos os aplicativos Java implantados nele), é possível usar o modo de exibição do **Azure Explorer**.

Se o modo de exibição do **Azure Explorer** ainda não estiver aberto, você poderá abri-lo clicando no menu **View** no IntelliJ, clicando em **Tool Windows** e em **Service Explorer**. Se você não tiver feito logon anteriormente, receberá uma solicitação para fazê-lo.

Quando o modo de exibição do **Azure Explorer** for exibido, use estas etapas para iniciar ou parar o Aplicativo Web:

1. Expanda o nó **Azure**.

1. Expanda o nó **Aplicativos Web**.

1. Clique com botão direito do mouse no Aplicativo Web desejado.

1. Quando o menu de contexto for exibido, clique em **Iniciar** ou **Parar**. Observe que as opções de menu são sensíveis ao contexto, para que você só possa parar um aplicativo Web que esteja em execução ou iniciar um aplicativo Web que não esteja em execução no momento.

    ![][18]

## Próximas etapas

Para obter mais informações sobre os kits de ferramentas do Azure para Java IDEs, confira os links a seguir:

- [Kit de ferramentas do Azure para Eclipse]
  - [Instalação do Kit de Ferramentas do Azure para o Eclipse]
  - [Criar um aplicativo Web Hello World para o Azure no Eclipse]
  - [Novidades no Kit de Ferramentas do Azure para o Eclipse]
- [Kit de Ferramentas do Azure para IntelliJ]
  - [Instalação do Kit de Ferramentas do Azure para IntelliJ]
  - *Criar um aplicativo Web Hello World para o Azure no IntelliJ (este artigo)*

Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure].

Para obter mais informações sobre como criar aplicativos Web do Azure, consulte a [Visão geral de Aplicativos Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Kit de ferramentas do Azure para Eclipse]: ../azure-toolkit-for-eclipse.md
[Kit de Ferramentas do Azure para IntelliJ]: ../azure-toolkit-for-intellij.md
[Criar um aplicativo Web Hello World para o Azure no Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Instalação do Kit de Ferramentas do Azure para o Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Instalação do Kit de Ferramentas do Azure para IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Novidades no Kit de Ferramentas do Azure para o Eclipse]: ../azure-toolkit-for-eclipse-whats-new.md

[Central de desenvolvimento Java do Azure]: https://azure.microsoft.com/develop/java/
[Visão geral de Aplicativos Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-intellij-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-intellij-create-hello-world-web-app/02-File-New-Project.png
[03a]: ./media/app-service-web-intellij-create-hello-world-web-app/03-New-Project-Dialog.png
[03b]: ./media/app-service-web-intellij-create-hello-world-web-app/03-No-SDK-Specified.png
[04]: ./media/app-service-web-intellij-create-hello-world-web-app/04-New-Project-Dialog.png
[05]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Open-Index-Page.png
[06]: ./media/app-service-web-intellij-create-hello-world-web-app/06-Azure-Publish-Context-Menu.png
[07]: ./media/app-service-web-intellij-create-hello-world-web-app/07-Azure-Log-In-Dialog.png
[08]: ./media/app-service-web-intellij-create-hello-world-web-app/08-Manage-Subscriptions.png
[09]: ./media/app-service-web-intellij-create-hello-world-web-app/09-App-Containers.png
[10]: ./media/app-service-web-intellij-create-hello-world-web-app/10-Add-App-Container.png
[11]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container.png
[12]: ./media/app-service-web-intellij-create-hello-world-web-app/12-New-Resource-Group.png
[13]: ./media/app-service-web-intellij-create-hello-world-web-app/13-New-App-Service-Plan.png
[14]: ./media/app-service-web-intellij-create-hello-world-web-app/14-New-App-Container.png
[15]: ./media/app-service-web-intellij-create-hello-world-web-app/15-Deploy-To-Azure.png
[16]: ./media/app-service-web-intellij-create-hello-world-web-app/16-Progress-Indicator.png
[17]: ./media/app-service-web-intellij-create-hello-world-web-app/17-Browse-Web-App.png
[18]: ./media/app-service-web-intellij-create-hello-world-web-app/18-Stop-Web-App.png

<!---HONumber=AcomDC_0608_2016-->
