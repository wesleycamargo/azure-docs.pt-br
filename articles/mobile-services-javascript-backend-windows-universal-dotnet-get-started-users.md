<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/18/2014" ms.author="Glenn="" Gailey" />

# Introdução à autenticação dos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo Windows universal. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1.  [Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis][Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]
2.  [Restringir permissões de tabela para usuários autenticados][Restringir permissões de tabela para usuários autenticados]
3.  [Adicionar autenticação ao aplicativo][Adicionar autenticação ao aplicativo]
4.  [Armazenar os tokens de autenticação no cliente][Armazenar os tokens de autenticação no cliente]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

> [WACOM.NOTE]Esse tutorial mostra como autenticar usuários nos aplicativos Windows Store e Windows Phone Store 8.1. Para um aplicativo Windows Phone 8.0 ou Windows Phone Silverlight 8.1, consulte esta versão de [Introdução à autenticação dos Serviços Móveis][Introdução à autenticação dos Serviços Móveis].

> Este tutorial demonstra o fluxo de autnticação gerenciado pelos Serviços Móveis utilizando uma variedade de provedores de identidade. Esse método é fácil de configurar e oferece suporte a vários provedores. Em vez de usar o Live Connect com autenticação gerenciada pelo cliente e forneça uma experiência de logon único em seu aplicativo da Windows Phone, consulte o tópico [Logon único para aplicativos da Windows Store usando o Live Connect][Logon único para aplicativos da Windows Store usando o Live Connect]. Ao usar a autenticação gerenciada pelo cliente, seu aplicativo tem acesso aos dados adicionais do usuário mantidos pelo provedor de identidade. Você pode obter os mesmos dados do usuário em seu serviço móvel ao chamar a função **user.getIdentities()** nos scripts do servidor. Para obter mais informações, consulte [essa postagem][essa postagem].

## <a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (Opcional) Conclua as etapas em [Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft][Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft].

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>Esta etapa &eacute; opcional, pois s&oacute; &eacute; aplicada ao provedor de login da conta da Microsoft. Ao registrar as informa&ccedil;&otilde;es do pacote de aplicativos da Windows Store com Servi&ccedil;os M&oacute;veis, o cliente &eacute; capaz de reutilizar as credenciais de login da conta da Microsoft para obter uma experi&ecirc;ncia de logon &uacute;nica. Se voc&ecirc; n&atilde;o fizer isso, os usu&aacute;rios de login da conta da Microsoft ser&atilde;o apresentados com uma solicita&ccedil;&atilde;o de logon toda vez que o m&eacute;todo de logon for chamado. Conclua esta etapa ao planejar usar o provedor de identidade da conta da Microsoft.</p>
    </div>

## <a name="permissions"></a>Restringir as permissões aos usuários autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  No Visual Studio, clique com o botão direito do mouse no projeto do Windows Store para o aplicativo TodoList e clique em **Definir como Projeto de Inicialização**.

2.  No projeto compartilhado, abra o arquivo de projeto App.xaml.cs, localize a definição para o [MobileServiceClient][MobileServiceClient], e certifique-se de que está configurado para conectar ao serviço móvel em execução no Azure.

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong><p>Ao usar as ferramentas do Visual Studio para conectar seu aplicativo a um Servi&ccedil;o M&oacute;vel, a ferramenta gera dois conjuntos de defini&ccedil;&otilde;es <strong>MobileServiceClient</strong>, um para cada plataforma de cliente. Este &eacute; um bom momento para simplificar o c&oacute;digo gerado, ao unificar as defini&ccedil;&otilde;es ajustadas do <code data-inline="1">#if...#endif</code> do <strong>MobileServiceClient</strong> em uma &uacute;nica defini&ccedil;&atilde;o n&atilde;o ajustada usada por ambas as vers&otilde;es do aplicativo.</p></div>

3.  Pressione a tecla F5 para executar o aplicativo Windows Store e verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado.

    Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)]

## <a name="tokens"></a>Armazene o token de autorização no cliente

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorização do lado do serviço dos usuários dos Serviços Móveis][Autorização do lado do serviço dos usuários dos Serviços Móveis], você usará o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado para filtrar os dados retornados pelos Serviços Móveis. Saiba mais sobre como usar os Serviços Móveis com o .NET na [Referência Conceitual do tutorial dos Serviços Móveis .NET][Referência Conceitual do tutorial dos Serviços Móveis .NET]



  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]: #register
  [Restringir permissões de tabela para usuários autenticados]: #permissions
  [Adicionar autenticação ao aplicativo]: #add-authentication
  [Armazenar os tokens de autenticação no cliente]: #tokens
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
  [Introdução à autenticação dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-users
  [Logon único para aplicativos da Windows Store usando o Live Connect]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
  [essa postagem]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft]: /pt-br/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [MobileServiceClient]: http://msdn.microsoft.com/pt-br/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [mobile-services-windows-universal-dotnet-authenticate-app]: ../includes/mobile-services-windows-universal-dotnet-authenticate-app.md
  [mobile-services-windows-store-dotnet-authenticate-app-with-token]: ../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md
  [Autorização do lado do serviço dos usuários dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
  [Referência Conceitual do tutorial dos Serviços Móveis .NET]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
