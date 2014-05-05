<properties linkid="develop-mobile-tutorials-get-started-with-users-wp8" urlDisplayName="Introdução à autenticação" pageTitle="Introdução à autenticação (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Saiba como usar os Serviços Móveis para autenticar os usuários do seu aplicativo do Windows Phone por meio de vários provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Introdução à autenticação nos Serviços Móveis" authors="glenga" solutions="" manager="" editor="" />

# Introdução à autenticação dos Serviços Móveis
<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-users" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-users" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-users" title="Windows Phone" class="current">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-get-started-users" title="iOS">iOS</a><a href="/pt-br/documentation/articles/mobile-services-android-get-started-users" title="Android">Android</a><a href="/pt-br/documentation/articles/mobile-services-html-get-started-users" title="HTML">HTML</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-users/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure do seu aplicativo. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="dev-onpage-video"><span class="icon">Executar o vídeo</span></a> <span class="time">10:50</span></div>
</div>  

Este tutorial o orientará por estas etapas básicas para habilitar a autenticação no seu aplicativo:

1. [Registrar seu aplicativo para autenticação e configurar os Serviços Móveis]
2. [Restringir as permissões de tabela aos usuários autenticados]
3. [Adicionar autenticação ao aplicativo]

Este tutorial baseia-se no início rápido dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis]. 

<h2><a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo para autenticação e configurar os Serviços Móveis</h2>

Para poder autenticar usuários, você deve registrar seu aplicativo com um provedor de identidade. Você deve registrar o segredo do cliente gerado pelo provedor com os Serviços Móveis.

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique no seu serviço móvel.

   	![][1]

2. Clique na guia **Painel** e anote o valor da **URL do Serviço Móvel**.

   	![][2]

    Você talvez precise fornecer esse valor para o provedor de identidade ao registrar seu aplicativo.

3. Na lista abaixo, escolha um provedor de identidade com suporte e siga as etapas para registrar seu aplicativo com esse provedor:

 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-microsoft-authentication/" target="_blank">Conta da Microsoft</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-facebook-authentication/" target="_blank">Logon no Facebook</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-twitter-authentication/" target="_blank">Logon no Twitter</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-google-authentication/" target="_blank">Logon no Google</a>
 - <a href="/pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    Lembre-se de anotar os valores da identidade do cliente e de segredo gerados pelo provedor.

    <div class="dev-callout"><b>Observação de segurança</b>
	<p>O segredo gerado pelo provedor é uma credencial de segurança importante. Não o compartilhe com ninguém nem o distribua com seu aplicativo.</p>
    </div>

4. De volta ao Portal de Gerenciamento, clique na guia **Identidade**, insira os valores do identificador do aplicativo e do segredo compartilhado obtidos do seu provedor de identidade e clique em **Salvar**.

   	![][3]

Seu serviço móvel e seu aplicativo agora estão configurados para trabalhar com o provedor de autenticação escolhido.

##<a name="permissions"></a>Restringir as permissões aos usuários autenticados

1. No Portal de Gerenciamento, clique na guia **Dados** e, em seguida, clique na tabela **TodoItem**. 

   	![][4]

2. Clique na guia **Permissões**, defina todas as permissões para **Somente usuários autenticados** e clique em **Salvar**. Isso garantirá que todas as operações em relação a tabela **TodoItem** exigem um usuário autenticado. Isso também simplificará os scripts no próximo tutorial, pois eles não precisarão permitir possíveis usuários anônimos.

   	![][5]

3. No Visual Studio 2012 Express para Windows Phone, abra o projeto criado quando você concluiu o tutorial [Introdução aos Serviços Móveis]. 

4. Pressione a tecla F5 para executar este aplicativo baseado no início rápido; verifique se uma exceção não tratada com código de status 401 (Não autorizado) será gerada depois que o aplicativo for iniciado. 
   
   	Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela _TodoItem_ agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorização do lado do servidor dos usuários dos Serviços Móveis][Authorize users with scripts], você usará o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado para filtrar os dados retornados pelos Serviços Móveis. 

<!-- Anchors. -->
[Registrar seu aplicativo para autenticação e configurar os Serviços Móveis]: #register
[Restringir as permissões de tabela aos usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Próximas etapas]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Enviar página de aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-wp8
[Começar com os dados]: /pt-br/develop/mobile/tutorials/started-with-data-wp8
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-wp8
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-wp8
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

