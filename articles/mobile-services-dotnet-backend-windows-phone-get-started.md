<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Get Started (WP8)" pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a um aplicativo Windows Phone 8 usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa as linguagens do .NET com suporte usando o Visual Studio para a lógica de negócios do lado do servidor e para gerenciar o serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript][Versão de back-end do JavaScript] neste tópico.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][]

> [WACOM.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].
> Este tutorial requer o [Visual Studio Professional 2013][Visual Studio Professional 2013]. Uma versão de avaliação gratuita está disponível. Para criar um novo aplicativo do Windows Phone 8.1, você deve ter a Atualização 2 do Visual Studio 2013 ou uma versão superior.

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## Criar um novo aplicativo Windows Phone

Depois de criar seu serviço móvel, você poderá seguir um quickstart fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção você criará um novo aplicativo do Windows Phone 8 que estará conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Na guia de início rápido, clique em **Windows Phone 8** em **Escolher plataforma** e expanda **Criar um novo aplicativo do Windows Phone 8**.

    ![][1]

    Isso exibe as três etapas fáceis para criar um aplicativo do Windows Phone conectado ao seu serviço móvel.

    ![][2]

3.  Se ainda não o fez, baixe e instale o [Visual Studio Professional 2013][Visual Studio Professional 2013] no computador local ou na máquina virtual.

4.  Em **Baixar e publicar o serviço na nuvem**, clique em **Baixar**.

    Isso baixa uma solução que contém projetos para o serviço móvel e para o aplicativo de exemplo *To do list* que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

5.  Em **Publicar o serviço na nuvem**, baixe seu perfil de publicação, salve o arquivo baixado no computador local e anote o local onde o salvou.

## Testar o serviço móvel no computador local

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

> [WACOM.NOTE]Há etapas de configuração adicionais necessárias para executar um aplicativo Windows Phone que se conecta a um serviço local. Não mostraremos como fazer isso neste tópico, mas você pode obter mais informações em [Como conectar-se a um serviço web local no emulador do Windows Phone 8][Como conectar-se a um serviço web local no emulador do Windows Phone 8]

## Publicar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

1.  No projeto do aplicativo Windows Phone, abra o arquivo App.xaml.cs, localize o código que cria uma instância de [MobileServiceClient][MobileServiceClient], comente o código que cria esse cliente usando *localhost* e remova o comentário do código que cria o cliente usando a URL de serviço móvel remoto, de maneira semelhante à seguinte:

        public static MobileServiceClient MobileService = new MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXX-APPLICATION-KEY-XXXXXXXX");

    Agora, o cliente acessará o serviço móvel publicado no Azure.

2.  (Opcional) Se você estiver criando um aplicativo do Windows Phone 8.1, clique com o botão direito do mouse no projeto no Gerenciador de Soluções, clique em **Propriedades**, defina a **Versão do SO do Windows Phone de destino** para **Windows Phone 8.1**, depois clique em **Sim** para atualizar o projeto.

3.  Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

4.  No aplicativo, digite um texto significativo, como *Concluir o tutorial* e clique em **Salvar**.

    Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

    <div class="dev-callout"> 
    <b>Observa&ccedil;&atilde;o</b> 
    <p>Voc&ecirc; pode rever o c&oacute;digo que acessa o servi&ccedil;o m&oacute;vel para consultar e inserir dados, que est&aacute; localizado no arquivo MainPage.xaml.cs.</p> 
    </div>

![][3]

Isso mostra como executar seu novo aplicativo cliente no serviço móvel em execução no Azure. Antes de testar o aplicativo Windows Phone com o serviço móvel em execução em um computador local, você deve configurar o servidor Web e o firewall para permitir acesso a partir de seu dispositivo ou emulador do Windows Phone. Para obter mais informações, consulte [Configurar o servidor web local para permitir conexões a um Serviço Móvel local][Configurar o servidor web local para permitir conexões a um Serviço Móvel local].

## <a name="next-steps"> </a>Próximas etapas

Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

-   [Começar a trabalhar com dados][Começar a trabalhar com dados]
    Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à sincronização de dados offline][Introdução à sincronização de dados offline]
    Saiba como usar a sincronização de dados offline para tornar o seu aplicativo responsivo e robusto.

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

-   [Introdução às notificações por push][Introdução às notificações por push]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

-   [Solucionar problemas de back-end do .NET dos Serviços Móveis][Solucionar problemas de back-end do .NET dos Serviços Móveis]
     Saiba como diagnosticar e corrigir problemas que podem surgir com o back-end do .NET dos Serviços Móveis.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Versão de back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started
  []: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [Como conectar-se a um serviço web local no emulador do Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkId=391930
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [MobileServiceClient]: http://msdn.microsoft.com/pt-br/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [Configurar o servidor web local para permitir conexões a um Serviço Móvel local]: /pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express
  [Começar a trabalhar com dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [Introdução à sincronização de dados offline]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [Solucionar problemas de back-end do .NET dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
