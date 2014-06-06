<properties linkid="develop-mobile-tutorials-get-started-with-users-html" urlDisplayName="Introdução à autenticação (HTML5)" pageTitle="Introdução à autenticação (HTML 5) | Mobile Dev Center" metaKeywords="" description="Saiba como usar os Serviços Móveis para autenticar usuários de seu aplicativo HTML por meio de vários provedores de identidade, incluindo o Google, o Facebook, o Twitter e a Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Introdução à autenticação em Serviços Móveis" authors=""  solutions="" writer="glenga" manager="" editor=""  />




# Introdução à autenticação dos Serviços Móveis
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/develop/mobile/tutorials/get-started-with-users-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-html" title="HTML" class="current">HTML</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>


Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo HTML.  Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.  

Este tutorial orienta você pelas seguintes etapas básicas para autenticação no seu aplicativo:

1. [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]

Este tutorial baseia-se no início rápido dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis]. 

<h2><a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo para autenticação e configurar os Serviços Móveis</h2>

Para ser capaz de autenticar usuários, você deve registrar seu aplicativo com um provedor de identidade. Você deve registrar o segredo do cliente gerado pelo provedor com os Serviços Móveis.

1. Faça o logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu serviço móvel.

   	![][4]

2. Clique na guia **Painel** e anote o valor da **URL do Serviço Móvel**.

   	![][5]

    Talvez seja necessário fornecer este valor para o provedor de identidade ao registrar seu aplicativo.

3. Escolha um provedor de identidade suportado na lista abaixo e siga as etapas para registrar seu aplicativo com o provedor:

 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-microsoft-authentication/" target="_blank">Conta da Microsoft</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-facebook-authentication/" target="_blank">Logon no Facebook</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-twitter-authentication/" target="_blank">Logon no Twitter</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-google-authentication/" target="_blank">Logon no Google</a>
 - <a href="/pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    Lembre-se de anotar os valores da identidade do cliente e segredo gerados pelo provedor.

    <div class="dev-callout"><b>Observação de Segurança</b>
	<p>O segredo gerado pelo provedor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.</p>
    </div>

4. No Portal de Gerenciamento, clique na guia **Identidade**, digite os valores do identificador do aplicativo e do segredo compartilhados obtidos do provedor de identidade e clique em **Salvar**.

   	![][13]

O serviço móvel e seu aplicativo agora estão configurados para trabalhar com o provedor de autenticação escolhido.

<h2><a name="permissions"></a><span class="short-header">Restringir permissões</span>Restringir permissões para usuários autenticados</h2>

1. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**. 

   	![][14]

2. Clique na guia **Permissões**, defina todas as permissões para **Somente usuários autenticados** e clique em **Salvar**. Isso garantirá que todas as operações em relação a tabela **TodoItem** exigem um usuário autenticado. Isso também simplifica os scripts no próximo tutorial porque eles não terão que permitir a possibilidade de usuários anônimos.

   	![][15]

3. No diretório do aplicativo, inicie um dos seguintes arquivos de comando da subpasta **server**.

	+ **launch-windows** (computadores com Windows) 
	+ **launch-mac.command** (computadores com Mac OS X)
	+ **launch-linux.sh** (computadores com Linux)

	<div class="dev-callout"><b>Observação</b>
		<p>Em um computador Windows, digite `R` quando o PowerShell solicitar que você confirme se deseja executar o script. Seu navegador da web poderá avisá-lo para não executar o script por ele ter sido baixado da internet. Quando isso acontecer, você deverá solicitar que o navegador continue para carregar o script.</p>
	</div>

	Isso iniciará um servidor web no computador local para hospedar o novo aplicativo.

2. Abra a URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> em um navegador da web para iniciar o aplicativo. 

	Os dados não são carregados. Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela _TodoItem_ agora exige autenticação.

3. (Opcional) Abra o depurador de scripts de seu navegador da web e recarregue a página. Verifique se ocorre um erro de acesso negado. 

Em seguida, você irá atualizar o aplicativo para permitir autenticação antes de solicitar recursos do serviço móvel.

<h2><a name="add-authentication"></a><span class="short-header">Adicionar autenticação</span>Adicionar autenticação ao aplicativo</h2>

<div class="dev-callout"><b>Observação</b>
		<p>Como o logon é realizado em um pop-up, você deve chamar o método <strong>login</strong> a partir de um evento de cliente em botão. Caso contrário, muitos navegadores suprimirão a janela de logon.</p>
</div>

1. Abra o arquivo de projeto index.html, localize o elemento H1 e, sob ele, adicione o seguinte trecho de código:

	    <div id="logged-in">
            Você está conectado como <span id="login-name"></span>.
            <button id="log-out">Fazer logoff</button>
        </div>
        <div id="logged-out">
            Você não está conectado.
            <button>Fazer Logon</button>
        </div>

	Isso permite que você faça logon nos Serviços Móveis a partir da página.

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

    Isso cria um conjunto de funções para manipular o processo de autenticação. O usuário é autenticado usando um logon do Facebook.

    <div class="dev-callout"><b>Observação</b>
	<p>Se você estiver usando um provedor de identidade diferente do Facebook, altere o valor passado para o método <strong>login</strong> acima para um dos seguintes: <em>microsoftaccount</em>, <em>facebook</em>, <em>twitter</em> ou <em>google</em>.</p>
    </div>

9. Volte para o navegador onde seu aplicativo está em execução e atualize a página. 

   Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.

	<div class="dev-callout"><b>Observação</b>
		<p>Ao usar o Internet Explorer, você pode receber o erro após o logon: <code>Cannot reach window opener. It may be on a different Internet Explorer zone</code> Isso ocorre porque o pop-up é executado em uma zona de segurança diferente (internet) do host local (intranet). Isso afeta apenas aplicativos durante o desenvolvimento usando localhost. Para solucionar esse problema, abra a guia <strong>Segurança</strong> de <strong>Opções da Internet</strong>, clique em <strong>Intranet Local</strong>, clique em <strong>Sites</strong>e desative <strong>Detectar automaticamente a rede intranet</strong>. Lembre-se de alterar essa configuração novamente quando concluir o teste.</p>
	</div>

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts], você irá obter o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis. Saiba mais sobre como usar os Serviços Móveis com HTML/JavaScript em [Referência conceitual do tutorial HTML/JavaScript nos Serviços Móveis]

<!-- Anchors. -->
[Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]: #register
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
[Logon na conta da Microsoft]: /pt-br/develop/mobile/how-to-guides/register-for-microsoft-authentication
[Logon no Facebook]: /pt-br/develop/mobile/how-to-guides/register-for-facebook-authentication
[Logon no Twitter]: /pt-br/develop/mobile/how-to-guides/register-for-twitter-authentication
[Logon no Google]: /pt-br/develop/mobile/how-to-guides/register-for-google-authentication
[Começar com os Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-html
[Começar com os dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-html
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-html

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial HTML/JavaScript nos Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client

