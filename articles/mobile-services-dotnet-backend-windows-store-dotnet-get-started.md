<properties 
	pageTitle="Introdução aos Serviços Móveis para aplicativos da Windows Store | Centro de Desenvolvimento de Serviços Móveis" 
	description="Siga este tutorial para começar a usar os serviços móveis do Azure para desenvolvimento da Windows Store em C#, VB ou JavaScript." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="02/10/2015" 
	ms.author="glenga"/>


# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a um aplicativo universal do Windows usando os Serviços Móveis do Azure. Soluções de aplicativos universais do Windows incluem projetos para a Windows Store 8.1 e a Loja do Windows Phone 8.1, e um projeto compartilhado comum. Para obter mais informações, consulte [Criar aplicativos universais do Windows voltados para Windows e Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx).

Neste tutorial, você criará um novo serviço móvel e um aplicativo simples de *Lista de tarefas pendentes* que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa as linguagens do .NET com suporte usando o Visual Studio para a lógica de negócios do lado do servidor e para gerenciar o serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte Versão de back-end do JavaScript neste tópico.

>[AZURE.NOTE] Este tópico mostra como criar um novo projeto de serviço móvel e o aplicativo universal do Windows usando o Portal de Gerenciamento do Azure. Usando o Visual Studio 2013 Update 3, você também pode adicionar um novo projeto de serviço móvel em uma solução existente do Visual Studio. Para obter mais informações, consulte o tutorial [Adicionar Serviços Móveis a um aplicativo existente](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md).

>Para adicionar um serviço móvel a um projeto de aplicativo para Windows Phone 8.0 ou Windows Phone Silverlight 8.1, consulte [Adicionar Serviços Móveis a um aplicativo do Windows Phone existente](mobile-services-dotnet-backend-windows-phone-get-started-data.md).

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. 

## Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Criar um novo aplicativo universal do Windows

Depois de criar seu serviço móvel, você poderá seguir um guia de início rápido fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel. 

Nesta seção, você criará um novo aplicativo universal do Windows conectado ao seu serviço móvel.

1. No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.
   
2. Na guia de início rápido, clique em **Windows**, em **Escolher a plataforma** e expanda **Criar um novo aplicativo da Windows Store**.

   	Isso exibe as três etapas fáceis para criar um aplicativo da Windows Store conectado ao seu serviço móvel.

  	![Etapas de início rápido de Serviços Móveis](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. Se você ainda não o fez, baixe e instale o <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> no computador local ou na máquina virtual.

4. Em **Baixar e executar seu aplicativo e serviço localmente**, selecione um idioma para seu aplicativo da Windows Store e clique em **Baixar**. 

  	Isso baixa uma solução que contém projetos para o serviço móvel e para o aplicativo de exemplo To do list que está conectado ao seu serviço móvel. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

## Testar o aplicativo no serviço móvel local

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[AZURE.NOTE] Você pode rever o código que acessa o serviço móvel para consultar e inserir dados, que está localizado no arquivo MainPage.xaml.cs.


## Publicar o serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


<ol start="4">
<li><p>No projeto do código compartilhado, abra o arquivo App.xaml.cs, localize o código que cria uma instância de <a href="http://msdn.microsoft.com/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a>, comente o código que cria esse cliente usando <em>localhost</em> e remova o comentário do código que cria o cliente usando a URL de serviço móvel remoto, de maneira semelhante a:</p>

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXX-APPLICATION-KEY-XXXXX");</code></pre>

	<p>Agora, o cliente acessará o serviço móvel publicado no Azure.</p></li>
</ol>

## Testar o aplicativo no serviço móvel hospedado no Azure

Agora que o serviço móvel está publicado e o cliente está conectado a um serviço móvel hospedado no Azure, podemos executar o aplicativo usando o Azure para armazenar itens.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]


## Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

* [Adicionar Serviços Móveis a um aplicativo existente][Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à sincronização de dados offline]
  <br/>Saiba como usar a sincronização de dados offline para tornar seu aplicativo ágil e robusto.

* [Adicionar autenticação a seu aplicativo dos Serviços Móveis][Introdução à autenticação]
  <br/>Aprenda a autenticar usuários do aplicativo com um provedor de identidade.

* [Adicionar notificações por push ao seu aplicativo][Introdução às notificações por push]
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

* [Solucionar um problema de back-end do .NET dos Serviços Móveis]
  <br/> Saiba como diagnosticar e corrigir problemas que podem surgir com um back-end do .NET dos serviços móveis. 

Para obter mais informações sobre aplicativos Windows universais, consulte [Suporte a várias plataformas de dispositivo de um único serviço móvel](mobile-services-how-to-use-multiple-clients-single-service.md#shared-vs).

<!-- Anchors. -->
[Introdução aos Serviços Móveis]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Próximas etapas]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[Introdução aos dados]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md
[Introdução aos dados]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Introdução à sincronização de dados offline]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[Introdução à autenticação]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Introdução às notificações por push]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript e HTML]: mobile-services-win8-javascript/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Versão de back-end do JavaScript]: mobile-services-windows-store-get-started.md
[Introdução aos dados nos Serviços Móveis usando o Visual Studio 2012]: mobile-services-windows-store-dotnet-get-started-data-vs2012.md
[Solucionar um problema de back-end do .NET dos Serviços Móveis]: mobile-services-dotnet-backend-how-to-troubleshoot.md

<!--HONumber=52-->