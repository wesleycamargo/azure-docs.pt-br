<properties linkid="develop-mobile-tutorials-get-started-with-users-html" urlDisplayName="Get Started with Authentication (HTML5)" pageTitle="Get started with authentication (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your HTML app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Introdução à autenticação dos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo HTML ou PhoneGap. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1.  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis][Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]
2.  [Restringir permissões de tabela para usuários autenticados][Restringir permissões de tabela para usuários autenticados]
3.  [Adicionar autenticação ao aplicativo][Adicionar autenticação ao aplicativo]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

## <a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a>Restringir as permissões aos usuários autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  No diretório do aplicativo, inicie um dos seguintes arquivos de comando da subpasta **server**.

    -   **launch-windows** (computadores com Windows)
    -   **launch-mac.command** (computadores com Mac OS X)
    -   **launch-linux.sh** (computadores com Linux)

    > [WACOM.NOTE]Em um computador Windows, digite `R` quando o PowerShell solicitar que você confirme se deseja executar o script. Seu navegador da web poderá avisá-lo para não executar o script por ele ter sido baixado da internet. Quando isso acontecer, você deverá solicitar que o navegador continue para carregar o script.

    Isso iniciará um servidor web no computador local para hospedar o novo aplicativo.

2.  Abra a URL <http://localhost:8000/> em um navegador da web para iniciar o aplicativo.

    Os dados não são carregados. Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

3.  (Opcional) Abra o depurador de scripts de seu navegador da web e recarregue a página. Verifique se ocorre um erro de acesso negado.

Em seguida, você irá atualizar o aplicativo para permitir autenticação antes de solicitar recursos do serviço móvel.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo

> [WACOM.NOTE]Como o logon é realizado em um pop-up, você deve chamar o método **login** de um evento de clique em botão. Caso contrário, muitos navegadores suprimirão a janela de logon.

1.  Abra o arquivo de projeto index.html, localize o elemento H1 e, sob ele, adicione o seguinte trecho de código:

        <div id="logged-in">
            You are logged in as <span id="login-name"></span>.
            <button id="log-out">Log out</button>
        </div>
        <div id="logged-out">
            You are not logged in.
            <button>Log in</button>
        </div>

    Isso permite que você faça logon nos Serviços Móveis a partir da página.

2.  No arquivo app.js, localize a linha de código na parte inferior do arquivo que chama a função refreshTodoItems e substitua-o pelo código a seguir:

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

    Isso cria um conjunto de funções para manipular o processo de autenticação. O usuário é autenticado usando um logon do Facebook. Se estiver usando um provedor de identidade diferente do Facebook, altere o valor passado para o método **login** acima para um dos seguintes: *microsoftaccount*, *facebook*, *twitter*, *google* ou *aad*.

    > [WACOM.NOTE]Em um aplicativo do PhoneGap, você deve também adicionar os seguintes plugins ao projeto:
    >
    > -   `phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git`
    > -   `phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git`
    >
    > </p>

3.  Volte para o navegador onde seu aplicativo está em execução e atualize a página.

    Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.

    > [WACOM.NOTE]Ao usar o Internet Explorer, você pode receber o erro após o logon: `Cannot reach window opener. It may be on a different Internet Explorer zone`. Isso ocorre porque o pop-up é executado em uma zona de segurança diferente (internet) do host local (intranet). Isso afeta apenas aplicativos durante o desenvolvimento usando localhost. Para solucionar esse problema, abra a guia **Segurança** de **Opções da Internet**, clique em **Intranet Local**, clique em **Sites**e desative **Detectar automaticamente a rede intranet**. Lembre-se de alterar essa configuração novamente quando concluir o teste.

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts][Autorizar usuários com scripts], você irá obter o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis. Saiba mais sobre como usar os Serviços Móveis com HTML/JavaScript em [Referência conceitual do tutorial HTML/JavaScript nos Serviços Móveis][Referência conceitual do tutorial HTML/JavaScript nos Serviços Móveis]

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]: #register
  [Restringir permissões de tabela para usuários autenticados]: #permissions
  [Adicionar autenticação ao aplicativo]: #add-authentication
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-html-get-started
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [Autorizar usuários com scripts]: /pt-br/documentation/articles/mobile-services-html-authorize-users-in-scripts
  [Referência conceitual do tutorial HTML/JavaScript nos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-html-how-to-use-client-library
