<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, FAcebook, GOogle, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn="" Gailey" />

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

## <a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

## <a name="permissions"></a>Restringir as permissões aos usuários autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

<ol start="6">
<li><p>No Visual Studio, clique com o botão direito do mouse no projeto do Windows Store para o aplicativo TodoList e clique em <b>Definir como Projeto de Inicialização</b>.</p></li>

<li><p>No projeto compartilhado, abra o arquivo de projeto App.xaml.cs, localize a definição para o <a href="http://msdn.microsoft.com/pt-br/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a>, e certifique-se de que está configurado para conectar ao serviço móvel em execução no Azure.</p>

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong><p>Ao usar as ferramentas do Visual Studio para conectar seu aplicativo a um Servi&ccedil;o M&oacute;vel, a ferramenta gera dois conjuntos de defini&ccedil;&otilde;es <strong>MobileServiceClient</strong>, um para cada plataforma de cliente. Este &eacute; um bom momento para simplificar o c&oacute;digo gerado, ao unificar as defini&ccedil;&otilde;es ajustadas do <code data-inline="1">#if...#endif</code> do <strong>MobileServiceClient</strong> em uma &uacute;nica defini&ccedil;&atilde;o n&atilde;o ajustada usada por ambas as vers&otilde;es do aplicativo.</p></div></li>

<li><p>Pressione a tecla F5 para executar o aplicativo Windows Store e verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado.</p>

    <p>Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</p></li>
</ol>

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)]

<!-- Currently not supported [WACOM.NOTE]If you registered your Windows Store app package information with Mobile Services, you should call the <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> method by supplying a value of <strong>true</strong> for the <em>useSingleSignOn</em> parameter. If you do not do this, your users will still be    ` presented with a login prompt every time that the login method is called. -->

## <a name="tokens"></a>Armazene os tokens de autorização no cliente

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorização do lado do serviço dos usuários dos Serviços Móveis][Autorização do lado do serviço dos usuários dos Serviços Móveis], você usará o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado para filtrar os dados retornados pelos Serviços Móveis. Saiba mais sobre como usar os Serviços Móveis com .NET no [Referência conceitual do tutorial dos Serviços Móveis .NET][Referência conceitual do tutorial dos Serviços Móveis .NET]



  [Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]: #register
  [Restringir permissões de tabela para usuários autenticados]: #permissions
  [Adicionar autenticação ao aplicativo]: #add-authentication
  [Armazenar os tokens de autenticação no cliente]: #tokens
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Introdução à autenticação dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
  [Autorização do lado do serviço dos usuários dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
  [Referência conceitual do tutorial dos Serviços Móveis .NET]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
