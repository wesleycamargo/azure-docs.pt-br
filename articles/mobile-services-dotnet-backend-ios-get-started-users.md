<properties pageTitle="Introdução à autenticação (Windows Store) | Mobile Dev Center" metaKeywords="autenticação, Facebook, Google, Twitter, Conta da Microsoft, logon" description="Saiba como usar os Serviços Móveis para autenticar os usuários do seu aplicativo da Windows Store por meio de vários provedores de identidade, incluindo o Google, o Facebook, o Twitter e a Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Introdução à autenticação nos Serviços Móveis" authors="Glenn Gailey" solutions="" manager="" editor="" />

# Introdução à autenticação dos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users" title="iOS" class="current">iOS</a><!--<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/" title="Back-end do .NET" class="current">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-ios-get-started-users/"  title="Back-end do JavaScript">Back-end do JavaScript</a></div>

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.

Este tutorial orienta você pelas seguintes etapas básicas para autenticação no seu aplicativo:

1. [Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis]. 

##<a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>No Visual Studio, abra o arquivo Web.config do projeto de serviço móvel e, na seção appSettings, defina os valores do identificador do aplicativo e do segredo compartilhado obtidos do provedor de identidade.</p>
<p>Essas configurações são usadas durante o desenvolvimento local. Após a publicação do seu projeto de serviço móvel no Azure, essas configurações serão substituídas pelos valores definidos no portal.</p></li>
</ol>

##<a name="permissions"></a>Restringir as permissões aos usuários autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li><p>No Xcode, abra o projeto criado quando você concluiu o tutorial <a href="/pt-br/documentation/articles/mobile-services-ios-get-started">Introdução aos Serviços Móveis</a>.</p></li> 
<li><p>Pressione o botão <strong>Executar</strong> para compilar o projeto e iniciar o aplicativo no emulador do iPhone; verifique se uma exceção não tratada com código de status 401 (Não autorizado) é gerada depois que o aplicativo é iniciado.<p> 
   
   	<p>Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</p></li>
</ol>

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Autorização do lado do serviço dos usuários dos Serviços Móveis][Authorize users with scripts], você usará o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado para filtrar os dados retornados pelos Serviços Móveis. 

<!-- Anchors. -->
[Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Próximas etapas]:#next-steps


<!-- URLs. -->
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/
[Autorizar usuários com scripts]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library
[Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft]: /pt-br/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication

