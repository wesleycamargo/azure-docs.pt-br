<properties pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial mostra como adicionar um servi&ccedil;o de back-end baseado na nuvem a um aplicativo Windows Phone 8 usando os Servi&ccedil;os M&oacute;veis do Azure. Neste tutorial, voc&ecirc; criar&aacute; um novo servi&ccedil;o m&oacute;vel e um aplicativo simples <em>To do list</em> que armazena dados do aplicativo no novo servi&ccedil;o m&oacute;vel.</p>
<p>Se voc&ecirc; preferir assistir a um v&iacute;deo, o clipe &agrave; direita segue as mesmas etapas deste tutorial. No v&iacute;deo, Nick Harris fornece uma introdu&ccedil;&atilde;o aos Servi&ccedil;os M&oacute;veis e explica como criar seu primeiro servi&ccedil;o m&oacute;vel e conectar-se a ele em um aplicativo da Windows Store.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o v&iacute;deo</span></a> <span class="time">13:18:00</span></div>

</div>

Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa JavaScript para a lógica de negócios do lado do servidor. Para criar um serviço móvel que permite que você grave sua lógica de negócios do lado do servidor nos idiomas com suporte de .NET usando o Visual Studio, consulte [Versão de back-end do .NET][Versão de back-end do .NET] neste tópico.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][]

> [WACOM.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure]. Para criar um novo aplicativo do Windows Phone 8.1, você deve ter a Atualização 2 do Visual Studio 2013 ou uma versão superior.

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service][mobile-services-create-new-service]]

## 

## <span class="short-header">Criar um novo aplicativo</span>Criar um novo aplicativo para Windows Phone

</h2>
Depois de criar seu serviço móvel, você poderá seguir um quickstart fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção você criará um novo aplicativo do Windows Phone 8 que estará conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Na guia de início rápido, clique em **Windows Phone 8** em **Escolher plataforma** e expanda **Criar um novo aplicativo do Windows Phone 8**.

    ![][1]

    Isso exibe as três etapas fáceis para criar um aplicativo do Windows Phone conectado ao seu serviço móvel.

    ![][2]

3.  Se ainda não o fez, baixe e instale o [Visual Studio 2012 Express para Windows Phone][Visual Studio 2012 Express para Windows Phone] em seu computador local.

    > [WACOM.NOTE]Para criar um aplicativo do Windows Phone 8.1, você deve ter a Atualização 2 do Visual Studio 2013 instalada.

4.  Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.

5.  Em **Baixe e execute o aplicativo**, clique em **Baixar**.

    Isso baixa o projeto para o aplicativo de exemplo *To do list* que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu aplicativo do Windows Phone

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1.  Navegue até o local onde você salvou os arquivos compactados do projeto, expanda os arquivos em seu computador e abra o arquivo da solução no Visual Studio.

2.  (Opcional) Se você estiver criando um aplicativo do Windows Phone 8.1, clique com o botão direito do mouse no projeto no Gerenciador de Soluções, clique em **Propriedades**, defina a **Versão do SO do Windows Phone de destino** para **Windows Phone 8.1**, depois clique em **Sim** para atualizar o projeto.

3.  Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

4.  No aplicativo, digite um texto significativo, como *Concluir o tutorial* e clique em **Salvar**.

    ![][3]

    Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

    > [WACOM.NOTE]Você pode revisar o código que acessa seu serviço móvel para consultar e inserir dados, ele é encontrado no arquivo MainPage.xaml.cs.

5.  No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

    ![][4]

    Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

    ![][5]

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

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [assista ao tutorial]: http://go.microsoft.com/fwlink/?LinkId=290816
  [Versão de back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
  []: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2FFpt-br%2Fdocumentation%2Farticles%2Fmobile-services-windows-phone-get-started%2F
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [3]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [4]: ./media/mobile-services-windows-phone-get-started/mobile-data-tab.png
  [5]: ./media/mobile-services-windows-phone-get-started/mobile-data-browse.png
  [Começar a trabalhar com dados]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-data/
  [Introdução à sincronização de dados offline]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-users/
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
