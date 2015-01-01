<properties pageTitle="Introdução à autenticação (Appcelerator) | Centro de desenvolvimento dos Serviços Móveis" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="11/24/2014" ms.author="Appcelerator team;mahender" />

# Introdução à autenticação dos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Este tópico mostra como autenticar usuários nos Serviços Móveis do Microsoft Azure do seu aplicativo. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade aos quais os Serviços Móveis dão suporte. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID de usuário é exibido.

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1.  [Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]
2.  [Restringir permissões de tabela para usuários autenticados]
3.  [Adicionar autenticação ao aplicativo]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis].

Para concluir este tutorial, é preciso ter o Appcelerator Titanium Studio 3.2.1 ou versões posteriores e o iOS 7.0 e/ou versões posteriores e o Android 4.3 ou versões posteriores.

##<a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a> Restringir as permissões aos usuários autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

3.	No Appcelerator Titanium Studio, abra o projeto que você criou ao concluir o tutorial [Introdução aos Serviços Móveis].

4.	Pressione o botão Executar para criar o projeto e iniciar o aplicativo no emulador do iPhone. Verifique se uma exceção sem tratamento com um código de status 401 (Não autorizado) é gerada após o aplicativo ser iniciado.
    
    Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela TodoItem agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

1.	Abra o arquivo de projeto index.js e no método Ouvinte de eventos da tabela, procure por `case 2:`

    Em seu aplicativo, você pode informar ao usuário os provedores de identidade disponíveis ou fornecer automaticamente um dos provedores de identidade.

2.	Para fornecer todos os provedores de identidade disponíveis, use o código a seguir:

        var azureMobileServiceModule = require( 'com.winwire.azuremobileservices');
        var azureMobileServices = new azureMobileServiceModule.AzureMobileServices();
        var appName = 'appctest';
        azureMobileServices.setAppName(appName);
        var authenticationClients = ['Google', 'Facebook', 'Twitter', 'Microsoft Account', 'Active Directory', 'Cancel'];
        var dialog = Ti.UI.createOptionDialog({
            options : authenticationClients,
            title : 'Select a client'
        });
        dialog.addEventListener('click', function(evt) {
            if (evt.index == 0 || evt.index == 1 || evt.index == 2 || evt.index == 3 || evt.index == 4) {
                var str = authenticationClients[evt.index];
                str = str.replace(/ /g, '');
                var authorizeClient = str.toLowerCase();
                if (authorizeClient == 'activedirectory') authorizeClient = 'aad';
                azureMobileServices.authorizeClient(authorizeClient, function(result) {
                    if (result == 'true') {
                        Alloy.createController('TableData');
                    }
                });
            } else {
                dialog.hide();
            }
        });
        dialog.show();

3.	Para fornecer um provedor de identidade específico, use o código a seguir:

        var azureMobileServiceModule = require( 'com.winwire.azuremobileservices');
        var azureMobileServices = new azureMobileServiceModule.AzureMobileServices();
        var authorizeClient = "Google"; //Replace "Google" with identity provider.
        authorizeClient = authorizeClient.toLowerCase();
        azureMobileServices.authorizeClient(authorizeClient, function(result) {
            if (result == 'true') {
                Alloy.createController('TableData');
            }
        });

>[WACOM.NOTE] Se você estiver usando um provedor de identidade diferente do Google, altere o valor passado para **authorizeClient** para um dos seguintes:*microsoftaccount*, *facebook*, *twitter* ou *windowsazureactivedirectory*.

4.	Pressione o botão Executar para criar o projeto, inicie o aplicativo no simulador do iPhone ou em um emulador do Android e clique na opção Com Logon para fazer logon no provedor de identidade.

    Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.


<!-- Anchors. -->

[Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication

<!-- Images. -->

<!-- URLs. -->
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started

<!--HONumber=35_1-->
