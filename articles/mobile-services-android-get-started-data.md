<properties linkid="develop-mobile-tutorials-get-started-with-data-android" urlDisplayName="Get Started with Data - Android" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="Azure android data, Azure mobile services data, windows droid, windows android, microsoft droid, microsoft android" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal,glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/29/2014" ms.author="ricksal,glenga"></tags>

# Introdução aos dados nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-data][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este t&oacute;pico mostra como usar os Servi&ccedil;os M&oacute;veis do Azure para utilizar dados em um aplicativo Android. Neste tutorial, voc&ecirc; baixar&aacute; um aplicativo que armazena dados na mem&oacute;ria, criar&aacute; um novo servi&ccedil;o m&oacute;vel, integrar&aacute; o servi&ccedil;o m&oacute;vel ao aplicativo e far&aacute; logon no Portal de Gerenciamento do Azure para exibir as altera&ccedil;&otilde;es nos dados feitas durante a execu&ccedil;&atilde;o do aplicativo.</p>

</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o v&iacute;deo</span></a><span class="time">15:32:00</span></div>

</div>

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>O objetivo deste tutorial &eacute; ajudar voc&ecirc; a compreender melhor como os Servi&ccedil;os M&oacute;veis permitem usar o Azure para armazenar e recuperar dados de um aplicativo Android. Desse modo, este t&oacute;pico explica muitas das etapas que s&atilde;o conclu&iacute;das para voc&ecirc; no Guia de in&iacute;cio r&aacute;pido dos Servi&ccedil;os M&oacute;veis. Se esta for sua primeira experi&ecirc;ncia com os Servi&ccedil;os M&oacute;veis, primeiro conclua o tutorial <a href="/pt-br/develop/mobile/tutorials/get-started-android">Introdu&ccedil;&atilde;o aos Servi&ccedil;os M&oacute;veis</a>.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto do aplicativo Android][]
2.  [Criar o serviço móvel][]
3.  [Adicionar uma tabela de dados para armazenamento][]
4.  [Atualizar o aplicativo para usar Serviços Móveis][]
5.  [Testar o aplicativo com os Serviços Móveis][]

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

Este tutorial requer o [SDK para Android de Serviços Móveis][]; o [SDK para Android][] que inclui o IDE (ambiente de desenvolvimento integrado) Eclipse e o plug-in Android Developer Tools (ADT); e o Android 4.2 ou a versão mais recente.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Este tutorial fornece instru&ccedil;&otilde;es para instalar o SKD para Android e o SDK para Android de Servi&ccedil;os M&oacute;veis. O projeto GetStartedWithData baixado requer o Android 4.2 ou uma vers&atilde;o posterior. No entanto, o SDK dos Servi&ccedil;os M&oacute;veis requer apenas o Android 2.2 ou uma vers&atilde;o mais recente.</p>
</div>

## <a name="download-app"></a><span class="short-header">Baixar o projeto</span>Baixe o projeto GetStartedWithData

### Obter o código de amostra

[WACOM.INCLUDE [download-android-sample-code][]]

### Verificar Versão de SDK do Android

[WACOM.INCLUDE [Verificar SDK][]]

### Inspecionar e executar o código de amostra

[WACOM.INCLUDE [mobile-services-android-run-sample-code][]]

## <a name="create-service"></a><span class="short-header">Criar um novo serviço móvel</span>Crie um novo serviço móvel no Portal de Gerenciamento

[WACOM.INCLUDE [mobile-services-create-new-service-data][]]

## <a name="add-table"></a><span class="short-header">Adicionar uma nova tabela</span>Adicione uma nova tabela para o serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service-data-2][]]

## <a name="update-app"></a><span class="short-header">Atualizar o aplicativo</span>Atualizar o aplicativo para usar o serviço móvel para acesso a dados

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data][]]

## <a name="test-app"></a><span class="short-header">Testar o aplicativo</span>Teste o aplicativo com o novo serviço móvel

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, você pode testá-lo nos Serviços Móveis usando o emulador do Android ou um telefone Android.

1.  No menu **Executar**, clique em **Executar** para iniciar o projeto.

    Isso executa seu aplicativo criado com o SDK do Android que usa a biblioteca cliente para enviar uma consulta que retorna os itens de seu serviço móvel.

2.  Como antes, digite texto significativo e clique em **Adicionar**.

    Isso envia um novo item como uma inserção no serviço móvel.

3.  No [Portal de Gerenciamento][], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4.  Clique na guia **Dados** e, em seguida, clique em **Procurar**.

    ![][]

    Observe que a tabela **TodoItem** agora contém dados com alguns valores gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

Isso conclui o tutorial **Introdução aos dados** para Android.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo Android para trabalhar com dados nos Serviços Móveis.

Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

-   [Validar e modificar dados com scripts][]
    Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

-   [Refinar consultas com paginação][]
    Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente um destes outros tutoriais do Android:

-   [Introdução à autenticação][]
    Saiba como autenticar usuários de seu aplicativo.

-   [Introdução às notificações por push][]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo com os Serviços Móveis.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [assista ao tutorial]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services
  [Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-android
  [Baixar o projeto do aplicativo Android]: #download-app
  [Criar o serviço móvel]: #create-service
  [Adicionar uma tabela de dados para armazenamento]: #add-table
  [Atualizar o aplicativo para usar Serviços Móveis]: #update-app
  [Testar o aplicativo com os Serviços Móveis]: #test-app
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AED8DE357
  [SDK para Android de Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  [SDK para Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [download-android-sample-code]: ../includes/download-android-sample-code.md
  [Verificar SDK]: ../includes/mobile-services-verify-android-sdk-version.md
  [mobile-services-android-run-sample-code]: ../includes/mobile-services-android-run-sample-code.md
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [mobile-services-android-getting-started-with-data]: ../includes/mobile-services-android-getting-started-with-data.md
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
  [Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-android
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-android
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-android
