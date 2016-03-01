<properties
	pageTitle="Adicionar autenticação no Apache Cordova com os Aplicativos Móveis| Serviço de Aplicativo do Azure"
	description="Saiba como usar os Aplicativos Móveis no Serviço de Aplicativo do Azure para autenticar usuários de seu aplicativo Apache Cordova usando vários provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft."
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="ggailey777"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="adrianha"/>

# Adicionar autenticação ao aplicativo Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## Resumo

Neste tutorial, você adiciona a autenticação ao projeto de início rápido todolist no Apache Cordova usando um provedor de identidade com suporte. Este tutorial se baseia no tutorial [Introdução aos Aplicativos Móveis], que você deve concluir primeiro.

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ No Visual Studio, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Aplicativos Móveis], execute o aplicativo no **Emulador do Google Android** e verifique se uma Falha Inesperada de Conexão é exibida após o aplicativo ser iniciado.

    Isso acontece porque o aplicativo tenta acessar o back-end como um usuário não autenticado. O back-end redireciona o usuário para uma página de autenticação usando o OAuth. No entanto, o aplicativo não confia no ponto de extremidade OAuth.

Em seguida, você atualiza o aplicativo para autenticar os usuários antes de solicitar recursos do back-end do Aplicativo Móvel.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

1. Abra o projeto no **Visual Studio**, depois abra o arquivo <tt>www/index.html</tt> para edição.

2. Localize a marca meta `Content-Security-Policy` na seção de cabeçalho. Você precisará adicionar o host OAuth à lista de fontes permitidas.

    | Provedor | Nome do provedor do SDK | Host OAuth |
    | :--------------------- | :---------------- | :-------------------------- |
    | Active Directory do Azure | aad | https://login.windows.net |
    | Facebook | facebook | https://www.facebook.com |
    | Google | google | https://accounts.google.com |
    | Microsoft | microsoftaccount | https://login.live.com |
    | Twitter | twitter | https://api.twitter.com |

    Veja a seguir um exemplo de Política de segurança de conteúdo (implementada para o Active Directory do Azure):

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    Você deve substituir <tt>https://login.windows.net</tt> pelo host OAuth da tabela acima. Confira a [documentação sobre a Política de segurança de conteúdo] para saber mais sobre essa marca meta.

    Observe que alguns provedores de autenticação não exigem mudanças na Política de segurança de conteúdo quando usada em dispositivos móveis apropriados. Por exemplo, nenhuma mudança na Política de segurança de conteúdo será necessária ao usar a autenticação do Google em um dispositivo Android.

3. Abra o arquivo <tt>www/js/index.js</tt> para edição. Embora o projeto seja compilado e executado com apenas as alterações já feitas, considera-se uma Prática recomendada chamar explicitamente um método login() para iniciar uma ação de logon. Localize o método `onDeviceReady()`. No código de criação do cliente, adicione o seguinte:

        // Login to the service
        client.login('SDK Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Por exemplo, para o Active Directory do Azure, use:

        client.login('aad')

    O método login() é uma função assíncrona que retorna uma promessa de JavaScript. O restante da inicialização é colocado dentro da resposta de promessa para que não seja executado até a conclusão do método login().

4. Execute seu projeto. Após a conclusão da inicialização do projeto, o aplicativo mostrará a página de logon do OAuth para o provedor de autenticação escolhido.

##<a name="next-steps"></a>Próximas etapas

* Saiba mais [Sobre autenticação] com o Serviço de Aplicativo do Azure.
* Continue o tutorial adicionando [Notificações por push] ao seu aplicativo Apache Cordova.

<!-- URLs. -->
[Introdução aos Aplicativos Móveis]: app-service-mobile-cordova-get-started.md
[documentação sobre a Política de segurança de conteúdo]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Notificações por push]: app-service-mobile-cordova-get-started-push.md
[Sobre autenticação]: app-service-mobile-auth.md

<!---HONumber=AcomDC_0218_2016-->