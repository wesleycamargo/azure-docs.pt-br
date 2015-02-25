<properties 
	pageTitle="Introdução à autenticação (HTML 5) | Centro de Desenvolvimento de Serviços Móveis" 
	description="Aprenda a usar os serviços móveis para autenticar usuários de seu aplicativo HTML por meio de uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="glenga"/>

# Adicionar autenticação ao seu aplicativo de Serviços Móveis 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo HTML ou PhoneGap.  Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade aos quais os Serviços Móveis dão suporte. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID de usuário é exibido.  

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1. [Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]

Este tutorial baseia-se no guia de início rápido dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis]. 

##<a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. No diretório do aplicativo, inicie um dos seguintes arquivos de comando da subpasta **server**.

	+ **launch-windows** (Computadores Windows) 
	+ **launch-mac.command** (Computadores Mac OS X)
	+ **launch-linux.sh** (Computadores Linux)
	
	>[AZURE.NOTE]Em um computador Windows, digite `R` quando o PowerShell solicitar que você confirme se deseja executar o script. Seu navegador da Web poderá avisá-lo para não executar o script por ele ter sido baixado da internet. Quando isso acontecer, você deverá solicitar que o navegador continue para carregar o script.

	Isso iniciará um servidor Web no computador local para hospedar o novo aplicativo.

2. Abra a URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> em um navegador Web para iniciar o aplicativo. 

	Os dados não são carregados. Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela _TodoItem_ agora exige autenticação.

3. (Opcional) Abra o depurador de scripts de seu navegador da Web e recarregue a página. Verifique se ocorre um erro de acesso negado. 

Em seguida, você irá atualizar o aplicativo para permitir autenticação antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

>[AZURE.NOTE]Como o logon é executado em um pop-up, você deve chamar o método <strong>login</strong> de um evento de clique do botão. Caso contrário, muitos navegadores suprimirão a janela de logon.

1. Abra o arquivo do projeto index.html, localize o elemento H1 e, sob ele, adicione o seguinte trecho de código:

	    <div id="logged-in">
            You are logged in as <span id="login-name"></span>.
            <button id="log-out">Log out</button>
        </div>
        <div id="logged-out">
            You are not logged in.
            <button>Log in</button>
        </div>

	Isso permite que você faça logon nos Serviços Móveis por meio da página.

2. No arquivo app.js, localize a linha de código na parte inferior do arquivo que chama a função refreshTodoItems e substitua-o pelo código a seguir: 
	
		function refreshAuthDisplay() {
			var isLoggedIn = client.currentUser !== null;
			$("#logged-in").toggle(isLoggedIn);
			$("#logged-out").toggle(!isLoggedIn);

			if (isLoggedIn) {
				$("#login-name").text(client.currentUser.userId);
				refreshTodoItems();
			}
		}

		function logIn() {
			client.login("facebook").then(refreshAuthDisplay, function(error){
				alert(error);
			});
		}

		function logOut() {
			client.logout();
			refreshAuthDisplay();
			$('#summary').html('<strong>You must login to access data.</strong>');
		}

		// On page init, fetch the data and set up event handlers
		$(function () {
			refreshAuthDisplay();
			$('#summary').html('<strong>You must login to access data.</strong>');		    
			$("#logged-out button").click(logIn);
			$("#logged-in button").click(logOut);
		});

    Isso cria um conjunto de funções para manipular o processo de autenticação. O usuário é autenticado usando um logon do Facebook. Se você estiver usando um provedor de identidade diferente do Google, altere o valor passado para o método <strong>login</strong> acima para um dos seguintes: <em>microsoftaccount</em>, <em>facebook</em>, <em>twitter</em>, <em>google</em>, ou <em>aad</em>.

	>[AZURE.IMPORTANT]Em um aplicativo do PhoneGap, você deve também adicionar os seguintes plugins ao projeto:
	><ul><li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git</code></li>
	><li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git</code></li></ul>

9. Volte para o navegador onde seu aplicativo está em execução e atualize a página. 

	   Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.

	>[AZURE.NOTE]Ao usar o Internet Explorer, você pode receber o erro após o logon: <code>Cannot reach window opener. It may be on a different Internet Explorer zone</code>. Isso ocorre porque o pop-up é executado em uma zona de segurança diferente (internet) do host local (intranet). Isso afeta apenas aplicativos durante o desenvolvimento usando localhost. Como alternativa, abra a guia <strong>Segurança</strong> em <strong>Opções da Internet</strong>, clique em <strong>Intranet Local</strong>, clique em <strong>Sites</strong>, e desabilite <strong>Detectar automaticamente a rede intranet</strong>. Lembre-se de alterar essa configuração novamente quando concluir o teste.

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts], você vai obter o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis. Saiba mais sobre como usar os Serviços Móveis com HTML/JavaScript em [Referência conceitual do tutorial HTML/JavaScript nos Serviços Móveis]

<!-- Anchors. -->
[Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Próximas etapas]:#next-steps

<!-- Images. -->

[4]: ./media/mobile-services-html-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-html-get-started-users/mobile-service-uri.png
[13]: ./media/mobile-services-html-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-html-get-started
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-html-get-started-data
[Autorizar usuários com scripts]: /en-us/documentation/articles/mobile-services-html-authorize-users-in-scripts

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]: /en-us/documentation/articles/mobile-services-html-how-to-use-client-library
\n<!--HONumber=42-->
