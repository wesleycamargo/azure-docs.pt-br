<properties linkid="develop-mobile-tutorials-get-started-with-users-ios" urlDisplayName="Introdução à autenticação (iOS)" pageTitle="Introdução à autenticação (iOS) | Mobile Dev Center" metaKeywords="aplicativo de registro do Azure, autenticação do Azure, autenticação do aplicativo, autenticar os serviços móveis, Mobile Services iOS" description="Saiba como usar os Serviços Móveis para autenticar os usuários do seu aplicativo iOS por meio de vários provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Introdução à autenticação nos Serviços Móveis" authors="" solutions="" manager="" editor="" />

# Introdução à autenticação dos Serviços Móveis
<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-users" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-users" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-get-started-users" title="iOS" class="current">iOS</a><a href="/pt-br/documentation/articles/mobile-services-android-get-started-users" title="Android">Android</a><a href="/pt-br/documentation/articles/mobile-services-html-get-started-users" title="HTML">HTML</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-ios-get-started-users/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure do seu aplicativo iOS.  Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.  

Este tutorial o orientará por estas etapas básicas para habilitar a autenticação no seu aplicativo:

1. [Registrar seu aplicativo para autenticação e configurar os Serviços Móveis]
2. [Restringir as permissões de tabela aos usuários autenticados]
3. [Adicionar autenticação ao aplicativo]

Este tutorial baseia-se no início rápido dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis]. 

Para concluir este tutorial, é necessário ter XCode 4.5 e iOS 5.0 ou versões posteriores. 

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a> Restringir as permissões aos usuários autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

<ol start="3">
<li><p>No Xcode, abra o projeto criado quando você concluiu o tutorial <a href="/pt-br/documentation/articles/mobile-services-ios-get-started">Introdução aos Serviços Móveis</a>.</p></li> 
<li><p>Pressione o botão <strong>Executar</strong> para compilar o projeto e iniciar o aplicativo no emulador do iPhone; verifique se uma exceção não tratada com código de status 401 (Não autorizado) é gerada depois que o aplicativo é iniciado.<p> 
   
   	<p>Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</p></li>
</ol>

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

## <a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Autorização do lado do servidor dos usuários dos Serviços Móveis][Authorize users with scripts], você usará o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado para filtrar os dados retornados pelos Serviços Móveis. 

<!-- Anchors. -->
[Registrar seu aplicativo para autenticação e configurar os Serviços Móveis]: #register
[Restringir as permissões de tabela aos usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Próximas etapas]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Enviar página de aplicativo:]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Logon único para aplicativos da Windows Store usando o Live Connect]: /pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-ios
[Começar com os dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-ios
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-ios
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-ios
[Authorize users with scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-ios

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

