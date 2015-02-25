<properties 
	pageTitle="Introdução aos Serviços Móveis para aplicativos da Windows Store | Centro de desenvolvimento dos Serviços Móveis" 
	description="Siga este tutorial para começar a usar os serviços móveis do Azure para desenvolvimento da Windows Store em C# ou JavaScript." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="08/18/2014" 
	ms.author="glenga"/>

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a um aplicativo universal do Windows usando os Serviços Móveis do Azure. Soluções de aplicativos universais do Windows incluem projetos para a Windows Store 8.1 e a Loja do Windows Phone 8.1, e um projeto compartilhado comum. Para obter mais informações, consulte [Criar aplicativos universais do Windows voltados para Windows e Windows Phone](http://msdn.microsoft.com/en-us/library/windows/apps/xaml/dn609832.aspx).

Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa JavaScript para a lógica de negócios do lado do servidor. Para criar um serviço móvel que permita escrever sua lógica de negócios do lado do servidor nos idiomas com suporte de .NET usando o Visual Studio, consulte Versão de back-end do .NET deste tópico.

>[AZURE.NOTE]Este tópico mostra como criar um novo projeto de serviço móvel e o aplicativo universal do Windows usando o Portal de Gerenciamento do Azure. Usando o Visual Studio 2013, você também pode adicionar um novo projeto de serviço móvel a uma solução existente do Visual Studio. Para saber mais, confira [Início rápido: Adicionar um serviço móvel (back-end JavaScript)](http://msdn.microsoft.com/en-us/library/windows/apps/xaml/dn263180.aspx).

>Para adicionar um serviço móvel a um projeto de aplicativo para Windows Phone 8.0 ou Windows Phone Store 8.1, consulte [Introdução aos dados para Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started-data).

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Visual Studio 2013 Express para Windows] 

## Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Criar um novo aplicativo universal do Windows

Depois de criar seu serviço móvel, você poderá seguir um fácil início rápido no Portal de Gerenciamento para criar um novo aplicativo universal do Windows ou modificar um projeto de aplicativo existente da Windows Store ou Windows Phone para se conectar ao seu serviço móvel. 

Nesta seção, você criará um novo aplicativo universal do Windows conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

   
2. Na guia de início rápido, clique em **Windows**, em **Escolher a plataforma** e expanda **Criar um novo aplicativo da Windows Store**.

   	![](./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-portal-quickstart.png)

   	Isso exibe as três etapas fáceis para criar um aplicativo da Windows Store conectado ao seu serviço móvel.

  	![](./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. Se você ainda não tiver feito isso, baixe e instale o [Visual Studio 2013 Express para Windows] no computador local ou máquina virtual.

4. Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.

5. Em **Baixar e executar seu aplicativo**, selecione um idioma para seu aplicativo e clique em **Baixar**. 

  	Isso baixa o projeto para o aplicativo de exemplo *To do list* que está conectado ao seu serviço móvel. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu aplicativo do Windows

[AZURE.INCLUDE [mobile-services-javascript-backend-run-app](../includes/mobile-services-javascript-backend-run-app.md)]

>[AZURE.NOTE]Você pode rever o código que acessa o serviço móvel para consultar e inserir dados, que está localizado no arquivo MainPage.xaml.cs.

## Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à sincronização de dados offline]
  <br/>Saiba como usar a sincronização de dados offline para tornar seu aplicativo ágil e robusto.

* [Introdução à autenticação]
  <br/>Aprenda a autenticar usuários do aplicativo com um provedor de identidade.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

Para obter mais informações sobre aplicativos Windows universais, consulte [Suporte a várias plataformas de dispositivo por meio de um único serviço móvel](/en-us/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs).

<!-- Anchors. -->
[Introdução aos Serviços Móveis]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Próximas etapas]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
[Introdução à sincronização de dados offline]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
[Introdução às notificações por push]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push
[Visual Studio 2013 Express para Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Introdução aos dados nos Serviços Móveis usando o Visual Studio 2012]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012
\n<!--HONumber=42-->
