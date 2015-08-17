<properties 
	pageTitle="Introdução aos dados (Android) | Mobile Dev Center" 
	description="Saiba como começar a usar os serviços móveis para utilizar dados em seu aplicativo Android." 
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
	ms.date="05/06/2015" 
	ms.author="ricksal"/>

# Adicionar Serviços Móveis a um aplicativo existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data-EC.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este tópico mostra como usar os Serviços Móveis do Azure para utilizar dados em um aplicativo Android. Neste tutorial, você baixará um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o serviço móvel ao aplicativo e fará logon no Portal de Gerenciamento do Azure para exibir as alterações nos dados feitas durante a execução do aplicativo.</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o vídeo</span></a><span class="time">15:32:00</span></div>
</div>

> [AZURE.NOTE]O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados de um aplicativo Android. Desse modo, este tópico explica muitas das etapas que são concluídas para você no Guia de início rápido dos Serviços Móveis. Se esta for sua primeira experiência com os Serviços Móveis, primeiro conclua o tutorial [Introdução aos Serviços Móveis](../get-started-android-EC.md).
> 
> Se você gostaria de ver o código-fonte do aplicativo concluído, clique [aqui](https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData).

Para concluir este tutorial, você precisará do seguinte:

+ Uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdocumentation%2Farticles%2Fmobile-services-android-get-started-data-EC%2F). 

+ O [SDK de Serviços Móveis para Android]; o <a href="https://go.microsoft.com/fwLink/p/?LinkID=280125" target="_blank">SDK para Android</a> que inclui o IDE (ambiente de desenvolvimento integrado) Eclipse e o plug-in Android Developer Tools (ADT); e o Android 4.2 ou uma versão mais recente.

> [AZURE.NOTE]Este tutorial fornece instruções para instalar o SKD para Android e o SDK para Android de Serviços Móveis. O projeto GetStartedWithData baixado requer o Android 4.2 ou uma versão posterior. No entanto, o SDK dos Serviços Móveis requer apenas o Android 2.2 ou uma versão mais recente.

<!-- -->

> [AZURE.NOTE]Este tutorial usa a versão mais recente do SDK de Serviços Móveis. Você pode encontrar uma versão anterior dele <a href="http://go.microsoft.com/fwlink/p/?LinkID=280126"> aqui</a> para fins de compatibilidade com versões anteriores, mas o código contido nesses tutoriais não funciona com ele.

##<a name="download-app"></a>Baixar o projeto GetStartedWithData

###Obter o código de amostra

[AZURE.INCLUDE [download-android-sample-code](../../includes/download-android-sample-code-EC.md)]

###Verificar Versão de SDK do Android

[AZURE.INCLUDE [Verifique o SDK](../../includes/mobile-services-verify-android-sdk-version-EC.md)]


###Inspecionar e executar o código de amostra

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code-EC.md)]

##<a name="create-service"></a>Criar um novo serviço móvel no Portal de Gerenciamento

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Adicionar uma nova tabela ao serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel para acesso a dados

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data-EC.md)]

##<a name="test-app"></a>Testar o aplicativo com seu novo serviço móvel

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, você pode testá-lo nos Serviços Móveis usando o emulador do Android ou um telefone Android.

1. No menu **Executar**, clique em **Executar** para iniciar o projeto.

	Isso executa seu aplicativo criado com o SDK do Android que usa a biblioteca cliente para enviar uma consulta que retorna os itens de seu serviço móvel.

5. Como antes, digite texto significativo e clique em **Adicionar**.

   	Isso envia um novo item como uma inserção no serviço móvel.

3. No [Portal de Gerenciamento], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4. Clique na guia **Dados** e, em seguida, clique em **Procurar**.

   	![][9]
  
   	Observe que a tabela **TodoItem** agora contém dados com alguns valores gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

Isso conclui o tutorial **Introdução aos dados** para Android.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo Android para trabalhar com dados nos Serviços Móveis.

Em seguida, tente um destes outros tutoriais do Android:

* [Introdução à autenticação] <br/>Saiba como autenticar usuários de seu aplicativo.

* [Introdução às notificações por push] <br/>Saiba como enviar uma notificação por push bastante básica ao seu aplicativo com os Serviços Móveis.

<!-- Anchors. -->
[Download the Android app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data-EC/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Introdução à autenticação]: mobile-services-android-get-started-users.md
[Introdução às notificações por push]: mobile-services-javascript-backend-android-get-started-push-EC.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[SDK de Serviços Móveis para Android]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
 

<!---HONumber=August15_HO6-->