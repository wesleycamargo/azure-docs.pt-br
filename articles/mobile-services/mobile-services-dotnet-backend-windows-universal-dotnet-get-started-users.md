<properties
	pageTitle="Adicionar autenticação ao seu aplicativo universal para Windows 8.1 | Microsoft Azure"
	description="Saiba como usar os Serviços Móveis para autenticar usuários do seu aplicativo universal para Windows 8.1 usando vários provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/06/2016"
	ms.author="glenga"/>

# Adicionar autenticação ao aplicativo de Serviços Móveis
[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para a versão de aplicativos móveis equivalente deste tópico, consulte [Adicionar autenticação ao seu aplicativo Windows](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md).

## Visão geral

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo Windows universal. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.

Este tutorial baseia-se no início rápido dos Serviços Móveis. Você também deve primeiro concluir os tutoriais [Introdução aos Serviços Móveis] ou [Adicionar os Serviços Móveis a um aplicativo existente](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md).

>[AZURE.NOTE]Este tutorial mostra como usar a autenticação para usuários direcionada pelo servidor nos aplicativos Windows Store e Windows Phone Store 8.1. Para obter informações sobre autenticação direcionada pelo cliente, veja [Fazendo logon com SDKs do Google, Microsoft e Facebook nos Serviços Móveis do Azure](https://azure.microsoft.com/blog/2014/10/27/logging-in-with-google-microsoft-and-facebook-sdks-to-azure-mobile-services/).

##<a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;6. No Visual Studio, clique com o botão direito do mouse no projeto do Windows Store para o aplicativo TodoList e clique em **Definir como Projeto de Inicialização**.

&nbsp;&nbsp;7. No projeto compartilhado, abra o arquivo de projeto App.xaml.cs, localize a definição para o [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx), e certifique-se de que está configurado para conectar ao serviço móvel em execução no Azure.

>[AZURE.NOTE]Ao usar as ferramentas do Visual Studio para conectar seu aplicativo a um Serviço Móvel, a ferramenta gera dois conjuntos de definições **MobileServiceClient**, um para cada plataforma de cliente. Este é um bom momento para simplificar o código gerado, ao unificar as definições ajustadas do `#if...#endif` do **MobileServiceClient** em uma única definição não ajustada usada por ambas as versões do aplicativo. Você não precisará fazer isso depois de baixar o aplicativo de início rápido no [portal clássico do Azure].

&nbsp;&nbsp;8. Pressione a tecla F5 para executar o aplicativo da Windows Store e verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado.

&nbsp;&nbsp;Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]

>[AZURE.NOTE]Se você registrou as informações do pacote de aplicativos da Windows Store nos Serviços Móveis, deverá chamar o método <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> fornecendo um valor **true** para o parâmetro *useSingleSignOn*. Se você não fizer isso, uma solicitação de logon ainda será apresentada aos usuários toda vez que o método de logon for chamado.

##<a name="tokens"></a>Armazenar os tokens de autorização no cliente

O exemplo anterior mostrou uma entrada padrão, que requer que o cliente contate o provedor de identidade e o serviço móvel sempre que o aplicativo for iniciado. Além de esse método ser ineficiente, você pode se deparar com problemas relacionados ao uso caso muitos consumidores tentem iniciar o aplicativo ao mesmo tempo. Uma melhor abordagem é armazenar em cache o token de autorização retornado pelos Serviços Móveis e tentar usá-lo antes de usar a entrada baseada no provedor.

>[AZURE.NOTE]Você pode armazenar em cache o token emitido pelos Serviços Móveis usando tanto a autenticação gerenciada pelo cliente quanto a autenticação gerenciada pelo serviço. Este tutorial usa a autenticação gerenciada pelo serviço.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]


## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorização do lado do serviço dos usuários dos Serviços Móveis][Authorize users with scripts], você usará o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado para filtrar os dados retornados pelos Serviços Móveis.

##Consulte também

+ [Recurso avançado de usuários](https://azure.microsoft.com/blog/2014/10/02/custom-login-scopes-single-sign-on-new-asp-net-web-api-updates-to-the-azure-mobile-services-net-backend/)<br/> Você pode obter dados de usuário adicionais mantidos pelo provedor de identidade em seu serviço móvel chamando o método **ServiceUser.GetIdentitiesAsync()** em um back-end do .NET.

+ [Referência conceitual do tutorial dos Serviços Móveis em .NET] <br/>Saiba mais sobre como usar os Serviços Móveis com um cliente .NET.


<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: mobile-services-dotnet-backend-service-side-authorization.md
[JavaScript and HTML]: mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[portal clássico do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial dos Serviços Móveis em .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=AcomDC_0518_2016-->