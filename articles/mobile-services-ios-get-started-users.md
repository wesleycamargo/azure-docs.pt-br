<properties linkid="develop-mobile-tutorials-get-started-with-users-ios" urlDisplayName="Get Started with Authentication (iOS)" pageTitle="Get started with authentication (iOS) | Mobile Dev Center" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services iOS" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/23/2014" ms.author="krisragh" />

# Introdução à autenticação dos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure do seu aplicativo iOS. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1.  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis][Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]
2.  [Restringir permissões de tabela para usuários autenticados][Restringir permissões de tabela para usuários autenticados]
3.  [Adicionar autenticação ao aplicativo][Adicionar autenticação ao aplicativo]
4.  [Armazenando tokens de autenticação no seu aplicativo][Armazenando tokens de autenticação no seu aplicativo]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

Para concluir este tutorial, é necessário ter XCode 4.5 e iOS 5.0 ou versões posteriores.

## <a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a>Restringir as permissões aos usuários autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  No Xcode, abra o projeto criado quando você concluiu o tutorial [Introdução aos Serviços Móveis][1].

2.  Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo no emulador do iPhone; verifique se uma exceção não tratada com código de status 401 (Não autorizado) é gerada depois que o aplicativo é iniciado.

    Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

## <a name="store-authentication"></a>Armazenando tokens de autenticação no seu aplicativo

[WACOM.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Autorização do lado do serviço dos usuários dos Serviços Móveis][Autorização do lado do serviço dos usuários dos Serviços Móveis], você usará o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado para filtrar os dados retornados pelos Serviços Móveis.

 
 


  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]: #register
  [Restringir permissões de tabela para usuários autenticados]: #permissions
  [Adicionar autenticação ao aplicativo]: #add-authentication
  [Armazenando tokens de autenticação no seu aplicativo]: #store-authentication
  [Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-ios
  [1]: /pt-br/documentation/articles/mobile-services-ios-get-started
  [Autorização do lado do serviço dos usuários dos Serviços Móveis]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-ios
