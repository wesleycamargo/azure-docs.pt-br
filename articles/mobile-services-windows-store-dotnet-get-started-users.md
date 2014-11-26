<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn="" Gailey" />

# Introdução à autenticação dos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este t&oacute;pico mostra como autenticar usu&aacute;rios nos Servi&ccedil;os M&oacute;veis do Azure em seu aplicativo. Neste tutorial, voc&ecirc; pode adicionar autentica&ccedil;&atilde;o ao projeto de in&iacute;cio r&aacute;pido usando um provedor de identidade suportado pelos Servi&ccedil;os M&oacute;veis. Ap&oacute;s ser autenticado e autorizado com &ecirc;xito pelos Servi&ccedil;os M&oacute;veis, o valor da ID do usu&aacute;rio &eacute; exibido.</p>
<p>Voc&ecirc; pode assistir a uma vers&atilde;o do v&iacute;deo deste tutorial clicando no clipe &agrave; direita.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-users-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o v&iacute;deo</span></a> <span class="time">10:04:00</span></div>

</div>

> [WACOM.NOTE]A autenticação não oferece suporte no momento para os aplicativos do Windows Phone Store 8.1 ao usar a biblioteca cliente dos JavaScript dos Serviços Móveis. Se você tiver um projeto do aplicativo universal do Windows que usa o cliente JavaScript, você não poderá autenticar os usuários no Windows Phone neste momento.

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1.  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis][Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]
2.  [Restringir permissões de tabela para usuários autenticados][Restringir permissões de tabela para usuários autenticados]
3.  [Adicionar autenticação ao aplicativo][Adicionar autenticação ao aplicativo]
4.  [Armazenar os tokens de autenticação no cliente][Armazenar os tokens de autenticação no cliente]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

> [WACOM.NOTE]Este tutorial demonstra o fluxo de autenticação gerenciado pelos Serviços Móveis utilizando uma variedade de provedores de identidade. Esse método é fácil de configurar e oferece suporte a vários provedores. Em vez de usar o Live Connect com autenticação gerenciada pelo cliente e forneça uma experiência de logon único em seu aplicativo da Windows Phone, consulte o tópico [Logon único para aplicativos da Windows Store usando o Live Connect][Logon único para aplicativos da Windows Store usando o Live Connect]. Ao usar a autenticação gerenciada pelo cliente, seu aplicativo tem acesso aos dados adicionais do usuário mantidos pelo provedor de identidade. Você pode obter os mesmos dados do usuário em seu serviço móvel ao chamar a função **user.getIdentities()** nos scripts do servidor. Para obter mais informações, consulte [essa postagem][essa postagem].

## <a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (Opcional) Conclua as etapas em [Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft][Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft].

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>Esta etapa &eacute; opcional, pois s&oacute; &eacute; aplicada ao provedor de login da conta da Microsoft. Ao registrar as informa&ccedil;&otilde;es do pacote de aplicativos da Windows Store com Servi&ccedil;os M&oacute;veis, o cliente &eacute; capaz de reutilizar as credenciais de login da conta da Microsoft para obter uma experi&ecirc;ncia de logon &uacute;nica. Se voc&ecirc; n&atilde;o fizer isso, os usu&aacute;rios de login da conta da Microsoft ser&atilde;o apresentados com uma solicita&ccedil;&atilde;o de logon toda vez que o m&eacute;todo de logon for chamado. Conclua esta etapa ao planejar usar o provedor de identidade da conta da Microsoft.</p>
    </div>

## <a name="permissions"></a>Restringir as permissões aos usuários autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  No Visual Studio 2012 Express para Windows 8, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis][1].

2.  Pressione a tecla F5 para executar este aplicativo baseado em inicialização rápida; verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado.

    Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app](../includes/mobile-services-windows-dotnet-authenticate-app.md)]

## <a name="tokens"></a>Armazene o token de autorização no cliente

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorização do lado do serviço dos usuários dos Serviços Móveis][Autorização do lado do serviço dos usuários dos Serviços Móveis], você usará o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado para filtrar os dados retornados pelos Serviços Móveis. Saiba mais sobre como usar os Serviços Móveis com .NET no [Referência conceitual do tutorial dos Serviços Móveis .NET][Referência conceitual do tutorial dos Serviços Móveis .NET]



  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]: #register
  [Restringir permissões de tabela para usuários autenticados]: #permissions
  [Adicionar autenticação ao aplicativo]: #add-authentication
  [Armazenar os tokens de autenticação no cliente]: #tokens
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-get-started/
  [Logon único para aplicativos da Windows Store usando o Live Connect]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
  [essa postagem]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft]: /pt-br/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
  [1]: /pt-br/documentation/articles/mobile-services-windows-store-get-started
  [Autorização do lado do serviço dos usuários dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
  [Referência conceitual do tutorial dos Serviços Móveis .NET]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library
