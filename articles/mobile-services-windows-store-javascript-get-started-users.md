<properties linkid="develop-mobile-tutorials-get-started-with-users-js" urlDisplayName="Introdução à autenticação" pageTitle="Introdução à autenticação (JavaScript) | Mobile Dev Center" metaKeywords="" description="Saiba como usar os Serviços Móveis para autenticar os usuários do seu aplicativo JavaScript da Windows Store por meio de vários provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft." metaCanonical="http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-users-dotnet/" services="" documentationCenter="Mobile" title="Introdução à autenticação nos Serviços Móveis" authors="" solutions="" manager="" editor="" />

# Introdução à autenticação nos Serviços Móveis
<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-users" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-users" title="JavaScript da Windows Store" class="current">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-get-started-users" title="iOS">iOS</a><a href="/pt-br/documentation/articles/mobile-services-android-get-started-users" title="Android">Android</a><a href="/pt-br/documentation/articles/mobile-services-html-get-started-users" title="HTML">HTML</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-users/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo.  Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.  

Este tutorial orienta você pelas seguintes etapas básicas para autenticação no seu aplicativo:

1. [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis]. 

<div class="dev-callout"><b>Observação</b>
	<p>Este tutorial demonstra o método básico fornecido pelos Serviços Móveis para autenticar os usuários utilizando uma variedade de provedores de identidade. Esse método é fácil de configurar e oferece suporte a vários provedores. No entanto, esse método também exige que os usuários efetuem o login sempre que seu aplicativo for iniciado. Em vez de usar o Live Connect para fornecer uma experiência de logon único em seu aplicativo da Windows Store, consulte o tópico <a href="/pt-br/develop/mobile/tutorials/single-sign-on-windows-8-js">Logon único para aplicativos da Windows Store usando o Live Connect</a>.</p>
</div>

##<a name="register"></a> Registrar seu aplicativo para autenticação e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(Opcional) Conclua as etapas em <a href="/pt-br/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft</a>.</p>

    <div class="dev-callout"><b>Observação</b>
	<p>Esta etapa é opcional, pois só é aplicada ao provedor de login da conta da Microsoft. Ao registrar as informações do pacote de aplicativos do Windows Store com Serviços Móveis, o cliente é capaz de reutilizar as credenciais de login da conta da Microsoft para obter uma experiência de logon única. Se você não fizer isso, os usuários de login da conta da Microsoft serão apresentados com uma solicitação de logon toda vez que o método de logon for chamado. Conclua esta etapa ao planejar usar o provedor de identidade da conta da Microsoft.</p>
    </div>
</li>
</ol>
O serviço móvel e seu aplicativo agora estão configurados para trabalhar com o provedor de autenticação escolhido.

##<a name="permissions"></a> Restringir permissões aos usuários autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>No Visual Studio 2012 Express para Windows 8, abra o projeto que você criou quando concluiu o tutorial <a href="/pt-br/develop/mobile/tutorials/get-started/">Introdução aos Serviços Móveis</a>.</p></li> 
<li><p>Pressione a tecla F5 para executar este aplicativo baseado em inicialização rápida; verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado.</p>
   
   	<p>Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</p></li>
</ol>

Em seguida, você irá atualizar o aplicativo para autenticar usuários antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a> Adicionar autenticação ao aplicativo

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)] 

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorização do lado do serviço dos usuários dos Serviços Móveis][Authorize users with scripts], você usará o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado para filtrar os dados retornados pelos Serviços Móveis. 


<!-- Anchors. -->
[Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Próximas etapas]:#next-steps


<!-- URLs. -->
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Logon único para aplicativos da Windows Store usando o Connect]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-get-started/
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-push/
[Autorizar usuários com scripts]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Registrar seu pacote de aplicativos da Windows Store para autenticação da Microsoft]: /pt-br/develop/mobile/how-to-guides/register-windows-store-app-package

