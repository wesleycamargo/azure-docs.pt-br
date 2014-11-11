<properties linkid="develop-mobile-tutorials-get-started-with-users-js" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store JavaScript app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Introdução à autenticação dos Serviços Móveis

> [AZURE.SELECTOR-LIST (Plataforma | Back-end )]
- [(iOS | .NET)](/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/)
- [(iOS | JavaScript)](/pt-br/documentation/articles/mobile-services-ios-get-started-users/)
- [(Windows C# | .NET)](/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/)
- [(Windows C# | Javascript)](/pt-br/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users/)
- [(Windows JavaScript | .NET)](/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/)
- [(Windows JavaScript | Javascript)](/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-users/)
- [(Windows Phone | .NET)](/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/)
- [(Windows Phone | Javascript)](/pt-br/documentation/articles/mobile-services-windows-phone-get-started-users/)
- [(Android | .NET)](/pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/)
- [(Android | Javascript)](/pt-br/documentation/articles/mobile-services-android-get-started-users/)
- [(Xamarin iOS | .NET)](/pt-br/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/)
- [(Xamarin iOS | Javascript)](/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/)
- [(HTML | Javascript)](/pt-br/documentation/articles/mobile-services-html-get-started-users/)
- [(Xamarin Android | .NET)](/pt-br/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/)
- [(Xamarin Android | Javascript)](/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/)
- [(Appcelerator | Javascript)](/pt-br/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users/)

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1.  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]
2.  [Restringir permissões de tabela para usuários autenticados]
3.  [Adicionar autenticação ao aplicativo]
4.  [Armazenar os tokens de autenticação no cliente]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis].

> [WACOM.NOTE]Este tutorial demonstra o fluxo de autnticação gerenciado pelos Serviços Móveis utilizando uma variedade de provedores de identidade. Esse método é fácil de configurar e oferece suporte a vários provedores. Em vez de usar o Live Connect com autenticação gerenciada pelo cliente e forneça uma experiência de logon único em seu aplicativo da Windows Phone, consulte o tópico [Logon único para aplicativos da Windows Store usando o Live Connect]. Ao usar a autenticação gerenciada pelo cliente, seu aplicativo tem acesso aos dados adicionais do usuário mantidos pelo provedor de identidade. Você pode obter os mesmos dados do usuário em seu serviço móvel ao chamar a função **user.getIdentities()** nos scripts do servidor. Para obter mais informações, consulte [essa postagem](http://go.microsoft.com/fwlink/p/?LinkId=506605).

## <a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

1.  (Opcional) Conclua as etapas em <a href="/pt-br/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft</a>.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>Esta etapa &eacute; opcional, pois s&oacute; &eacute; aplicada ao provedor de login da conta da Microsoft. Ao registrar as informa&ccedil;&otilde;es do pacote de aplicativos da Windows Store com Servi&ccedil;os M&oacute;veis, o cliente &eacute; capaz de reutilizar as credenciais de login da conta da Microsoft para obter uma experi&ecirc;ncia de logon &uacute;nica. Se voc&ecirc; n&atilde;o fizer isso, os usu&aacute;rios de login da conta da Microsoft ser&atilde;o apresentados com uma solicita&ccedil;&atilde;o de logon toda vez que o m&eacute;todo de logon for chamado. Conclua esta etapa ao planejar usar o provedor de identidade da conta da Microsoft.</p>
    </div>

O serviço móvel e seu aplicativo agora estão configurados para funcionar com o provedor de autenticação escolhido.

## <a name="permissions"></a>Restringir as permissões aos usuários autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  No Visual Studio 2012 Express para Windows 8, abra o projeto que você criou quando concluiu o tutorial <a href="/pt-br/develop/mobile/tutorials/get-started/">Introdução aos Serviços Móveis</a>.

2.  Pressione a tecla F5 para executar este aplicativo baseado em inicialização rápida; verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado.

    Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)] 

## <a name="tokens"></a>Armazene os tokens de autorização no cliente

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorização do lado do serviço dos usuários dos Serviços Móveis][Autorização do lado do serviço dos usuários dos Serviços Móveis], você usará o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado para filtrar os dados retornados pelos Serviços Móveis.

<!-- Anchors. -->
<!-- URLs. -->

  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]: #register
  [Restringir permissões de tabela para usuários autenticados]: #permissions
  [Adicionar autenticação ao aplicativo]: #add-authentication
  [Armazenar os tokens de autenticação no cliente]: #tokens
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-get-started/
  [Logon único para aplicativos da Windows Store usando o Live Connect]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
  [essa postagem]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [mobile-services-windows-store-javascript-authenticate-app]: ../includes/mobile-services-windows-store-javascript-authenticate-app.md
  [mobile-services-windows-store-javascript-authenticate-app-with-token]: ../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md
  [Autorização do lado do serviço dos usuários dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts
