<properties
	pageTitle="Introdução aos Serviços Móveis do Azure para aplicativos iOS"
	description="Siga este tutorial para começar a usar os serviços móveis do Azure para desenvolvimento do iOS."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="04/24/2015"
	ms.author="krisragh"/>

# <a name="getting-started"></a>Introdução aos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo iOS usando os Serviços Móveis do Azure.

Neste tutorial, você criará um novo serviço móvel e um aplicativo simples _To do list_ que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa JavaScript para a lógica de negócios do lado do servidor. Para criar um serviço móvel com lógica de negócios do servidor no .NET, consulte a [versão back-end .NET] neste tópico.

> [AZURE.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se não tiver uma conta, você poderá se inscrever para uma avaliação do Azure e obter [serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação](http://azure.microsoft.com/pricing/details/mobile-services/). Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20 target="_blank").

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Criar um novo aplicativo iOS

Você pode consultar um Início Rápido simples no Portal de Gerenciamento para criar um novo aplicativo conectado a seu serviço móvel:

1. No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2. Na guia Início Rápido, clique em **iOS**, em **Escolher uma plataforma** e expanda **Criar um novo aplicativo iOS**. Isso exibe as etapas para criar um aplicativo iOS conectado a seu serviço móvel.

3. Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.

4. Em **Baixe e execute o aplicativo**, clique em **Baixar**. Isso baixa o projeto para o aplicativo de exemplo _To do list_ que está conectado ao serviço móvel, juntamente com o SDK do iOS dos Serviços Móveis. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu novo aplicativo iOS

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]

<ol start="4"> <li><p>De volta ao Portal de Gerenciamento, clique na guia <strong>DADOS</strong> e na tabela <strong>TodoItem</strong>. Isso permite que você procure os dados inseridos pelo aplicativo na tabela.<p></li></ol></p>

## <a name="next-steps"> </a>Próximas etapas
Saiba como executar tarefas adicionais importantes nos Serviços Móveis:

* [Adicionar serviços móveis a um aplicativo existente] <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à sincronização de dados offline] <br/>Saiba como usar a sincronização de dados offline para tornar o seu aplicativo responsivo e robusto.

* [Adicionar autenticação a um aplicativo existente] <br/>Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

* [Adicionar notificações por push a um aplicativo existente] <br/>Saiba como enviar uma notificação por push bem básica ao seu aplicativo.


<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Adicionar serviços móveis a um aplicativo existente]: mobile-services-dotnet-backend-ios-get-started-data.md
[Introdução à sincronização de dados offline]: mobile-services-ios-get-started-offline-data.md
[Adicionar autenticação a um aplicativo existente]: mobile-services-dotnet-backend-ios-get-started-users.md
[Adicionar notificações por push a um aplicativo existente]: mobile-services-dotnet-backend-ios-get-started-push.md


[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[versão back-end .NET]: mobile-services-dotnet-backend-ios-get-started.md
 

<!---HONumber=July15_HO2-->