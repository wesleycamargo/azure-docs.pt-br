<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, FAcebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/18/2014" ms.author="glenga"></tags>

# Introdução à autenticação dos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-users][mobile-services-selector-get-started-users]]

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo da Windows Store. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.

> [WACOM.NOTE]A autenticação não oferece suporte no momento para os aplicativos do Windows Phone Store 8.1 ao usar a biblioteca cliente dos JavaScript dos Serviços Móveis. Se você tiver um projeto do aplicativo universal do Windows que usa o cliente JavaScript, você não poderá autenticar os usuários no Windows Phone neste momento.

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1.  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis][Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]
2.  [Restringir permissões de tabela para usuários autenticados][Restringir permissões de tabela para usuários autenticados]
3.  [Adicionar autenticação ao aplicativo][Adicionar autenticação ao aplicativo]
4.  [Armazenar os tokens de autenticação no cliente][Armazenar os tokens de autenticação no cliente]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

## <a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension][mobile-services-dotnet-backend-aad-server-extension]]

## <a name="permissions"></a>Restringir as permissões aos usuários autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

1.  No Visual Studio 2013, abra o projeto criado quando você concluiu o tutorial [Introdução aos Serviços Móveis][1].

2.  Pressione a tecla F5 para executar este aplicativo baseado em inicialização rápida; verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado.

    Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app][mobile-services-windows-store-javascript-authenticate-app]]

## <a name="tokens"></a>Armazene os tokens de autorização no cliente

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token][mobile-services-windows-store-javascript-authenticate-app-with-token]]

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorização do lado do serviço dos usuários dos Serviços Móveis][Autorização do lado do serviço dos usuários dos Serviços Móveis], você usará o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado para filtrar os dados retornados pelos Serviços Móveis. Saiba mais sobre como usar os Serviços Móveis com .NET no [Referência conceitual do tutorial dos Serviços Móveis .NET][Referência conceitual do tutorial dos Serviços Móveis .NET]

<!-- Anchors. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]: #register
  [Restringir permissões de tabela para usuários autenticados]: #permissions
  [Adicionar autenticação ao aplicativo]: #add-authentication
  [Armazenar os tokens de autenticação no cliente]: #tokens
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [1]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [mobile-services-windows-store-javascript-authenticate-app]: ../includes/mobile-services-windows-store-javascript-authenticate-app.md
  [mobile-services-windows-store-javascript-authenticate-app-with-token]: ../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md
  [Autorização do lado do serviço dos usuários dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts
  [Referência conceitual do tutorial dos Serviços Móveis .NET]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library
