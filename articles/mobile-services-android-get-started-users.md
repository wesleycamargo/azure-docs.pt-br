<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/23/2014" ms.author="ricksal"></tags>

# Introdução à autenticação dos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-users][mobile-services-selector-get-started-users]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este t&oacute;pico mostra como autenticar usu&aacute;rios nos Servi&ccedil;os M&oacute;veis do Azure em seu aplicativo. Neste tutorial, voc&ecirc; pode adicionar autentica&ccedil;&atilde;o ao projeto de in&iacute;cio r&aacute;pido usando um provedor de identidade suportado pelos Servi&ccedil;os M&oacute;veis. Ap&oacute;s ser autenticado e autorizado com &ecirc;xito pelos Servi&ccedil;os M&oacute;veis, o valor da ID do usu&aacute;rio &eacute; exibido.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o v&iacute;deo</span></a><span class="time">10:42</span></div>

</div>

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1.  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis][Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]
2.  [Restringir permissões de tabela para usuários autenticados][Restringir permissões de tabela para usuários autenticados]
3.  [Adicionar autenticação ao aplicativo][Adicionar autenticação ao aplicativo]
4.  [Armazenar os tokens de autenticação no cliente][Armazenar os tokens de autenticação no cliente]
5.  [Atualizar tokens expirados][Atualizar tokens expirados]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

Para concluir este tutorial, é necessário ter o Eclipse e o Android 4.2 ou uma versão posterior.

## <a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo para autenticação e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

## <a name="permissions"></a><span class="short-header">Restringir permissões</span>Restringir permissões para usuários autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][mobile-services-restrict-permissions-javascript-backend]]

1.  No Eclipse, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

2.  No menu **Executar**, clique em **Executar** para iniciar o aplicativo. Verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é acionada depois que o aplicativo é iniciado.

    Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

## <a name="add-authentication"></a><span class="short-header">Adicionar autenticação</span>Adicionar autenticação ao aplicativo

[WACOM.INCLUDE [mobile-services-android-authenticate-app][mobile-services-android-authenticate-app]]

## <a name="cache-tokens"></a>Armazenar tokens de autenticação em cache no cliente

[WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token][mobile-services-android-authenticate-app-with-token]]

## <a name="refresh-tokens"></a>Atualizar o token armazenado em cache

[WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token][mobile-services-android-authenticate-app-refresh-token]]

## <a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts][Autorizar usuários com scripts], você irá obter o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [assista ao tutorial]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services
  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]: #register
  [Restringir permissões de tabela para usuários autenticados]: #permissions
  [Adicionar autenticação ao aplicativo]: #add-authentication
  [Armazenar os tokens de autenticação no cliente]: #cache-tokens
  [Atualizar tokens expirados]: #refresh-tokens
  [Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-android
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [mobile-services-android-authenticate-app]: ../includes/mobile-services-android-authenticate-app.md
  [mobile-services-android-authenticate-app-with-token]: ../includes/mobile-services-android-authenticate-app-with-token.md
  [mobile-services-android-authenticate-app-refresh-token]: ../includes/mobile-services-android-authenticate-app-refresh-token.md
  [Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-android
