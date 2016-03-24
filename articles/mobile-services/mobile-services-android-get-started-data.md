<properties
	pageTitle="Introdução aos dados no Android (back-end do JavaScript) | Microsoft Azure"
	description="Saiba como começar a usar os Serviços Móveis para aproveitar os dados em seu aplicativo do Android (back-end do JavaScript)."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="ricksal"/>

# Adicionar os Serviços Móveis a um aplicativo do Android existente (back-end do JavaScript)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para a versão de aplicativos móveis equivalente deste tópico, consulte [Como usar a biblioteca de cliente Android para aplicativos móveis](../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md).

## Resumo

Este tópico mostra como usar Serviços Móveis do Azure para adicionar dados persistentes a um aplicativo Android. Neste tutorial, você baixará um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o aplicativo ao serviço móvel de forma que ele armazene e atualize dados nos Serviços Móveis do Azure em vez de localmente e, em seguida, usará o portal clássico do Azure para exibir as alterações aos dados que foram feitas, executando o aplicativo.

Este tutorial ajuda a entender em mais detalhes como os Serviços Móveis do Azure podem armazenar e recuperar dados de um aplicativo Android. Portanto, ele explica muitas das etapas que já foram concluídas para você no tutorial de início rápido dos Serviços Móveis. Se esta for sua primeira experiência com os Serviços Móveis, primeiro conclua o tutorial [Introdução aos Serviços Móveis](mobile-services-android-get-started.md).

## Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

- uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AED8DE357).


- o [SDK do Android dos Serviços Móveis do Azure];
- o [ambiente de desenvolvimento integrado do Android Studio](https://developer.android.com/sdk/index.html), que inclui o SDK do Android; e o Android 4.2 ou uma versão posterior. O projeto GetStartedWithData baixado requer o Android 4.2 ou uma versão posterior. No entanto, o SDK dos Serviços Móveis requer apenas o Android 2.2 ou uma versão mais recente.

## Exemplo de código

Para ver o código-fonte completo, clique [aqui](https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/AndroidStudio).

## Baixar o projeto GetStartedWithData

###Obter o código de amostra

[AZURE.INCLUDE [download-android-sample-code](../../includes/download-android-sample-code.md)]

### Inspecionar e executar o código de amostra

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code.md)]

## Criar um novo serviço móvel no portal clássico do Azure

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## Adicionar uma nova tabela ao serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

## Atualizar o aplicativo para usar o serviço móvel para acesso a dados

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data.md)]


## Testar o aplicativo com seu novo serviço móvel

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, você pode testá-lo nos Serviços Móveis usando o emulador do Android ou um telefone Android.

1. No menu **Executar**, clique em **Executar aplicativo** para iniciar o projeto.

	Isso executa seu aplicativo criado com o SDK do Android que usa a biblioteca cliente para enviar uma consulta que retorna os itens de seu serviço móvel.

5. Como antes, digite texto significativo e clique em **Adicionar**.

   	Isso envia um novo item como uma inserção no serviço móvel.

3. No [Portal clássico do Azure], clique em **Serviços Móveis** e, em seguida, clique no seu serviço móvel.

4. Clique na guia **Dados** e, em seguida, clique em **Procurar**.

   	![][9]

   	Observe que a tabela **TodoItem** agora contém dados com alguns valores gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

Isso conclui o tutorial **Introdução aos dados** para Android.

## Solucionar problemas

### Verificar Versão de SDK do Android

[AZURE.INCLUDE [Verifique o SDK](../../includes/mobile-services-verify-android-sdk-version.md)]



## Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo Android para trabalhar com dados nos Serviços Móveis. Tente um destes outros tutoriais do Android:

* [Introdução à autenticação](mobile-services-android-get-started-users.md) <br/>Saiba como autenticar usuários de seu aplicativo.

* [Introdução às notificações por push](mobile-services-javascript-backend-android-get-started-push.md) <br/>Saiba como enviar uma notificação por push bastante básica ao seu aplicativo com os Serviços Móveis.

<!-- Anchors. -->
[Download the Android app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->

[Portal clássico do Azure]: https://manage.windowsazure.com/
[SDK do Android dos Serviços Móveis do Azure]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!---HONumber=AcomDC_0309_2016-->