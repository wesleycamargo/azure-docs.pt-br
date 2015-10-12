<properties
	pageTitle="Adicionar Serviços Móveis a um aplicativo existente (iOS) | Microsoft Azure"
	description="Saiba como começar a usar os serviços móveis para utilizar dados em seu aplicativo iOS."
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
	ms.date="10/01/2015"
	ms.author="krisragh"/>

# Adicionar Serviços Móveis a um aplicativo existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

Neste tutorial, você baixará um aplicativo existente que armazena dados na memória e irá alterá-lo para trabalhar com um serviço móvel do Azure.

Concluir o [Início Rápido] é um pré-requisito para de iniciar este tutorial. Você usará o serviço móvel criado no Início Rápido.

##<a name="download-app"></a>Baixar o projeto GetStartedWithData

Este tutorial é baseado no [aplicativo iOS GetStartedWithData]. O aplicativo é idêntico ao [Início Rápido], exceto que os itens são armazenados na memória.

Baixe o [aplicativo GetStartedWithData]. No Xcode, abra o projeto e examine o **Todoservice.m**. Há oito comentários **/ / TODO** que especificam as etapas para fazer com que esse aplicativo funcione.

##<a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel para acesso a dados

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Testar o aplicativo

1. No Xcode, clique em **Executar** para iniciar o aplicativo. Adicione itens à lista de tarefas pendentes digitando texto e clicando em **+**.

2. No Portal de Gerenciamento do Azure, clique em **Serviços Móveis** e no serviço móvel. Clique na guia **Dados** e, em seguida, clique em **Procurar**. Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

<!-- Anchors. -->
[Download the iOS app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png







[8]: ./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784


[Início Rápido]: ../mobile-services-javascript-backend-ios-get-started.md
[aplicativo GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[aplicativo iOS GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622

<!---HONumber=Oct15_HO1-->