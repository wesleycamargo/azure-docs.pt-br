<properties
	pageTitle="Como configurar a autenticação de conta da Microsoft para o seu aplicativo de Serviços de Aplicativos"
	description="Saiba como configurar a autenticação de conta da Microsoft para o seu aplicativo de Serviços de Aplicativos."
	authors="mattchenderson"
	services="app-service"
	documentationCenter=""
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/24/2016"
	ms.author="mahender"/>

# Como configurar seu aplicativo do Serviço de Aplicativo para usar o logon da Conta da Microsoft

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o Serviço de Aplicativo do Azure para usar a conta da Microsoft como um provedor de autenticação.

## <a name="register-windows-dev-center"> </a>Registrar seu aplicativo do Windows no Centro de Desenvolvimento do Windows

Aplicativos Universais do Windows e aplicativos da Windows Store devem ser registrados por meio do Centro de Desenvolvimento do Windows. Isso permite que você configure com mais facilidade as notificações por push para seu aplicativo no futuro.

>[AZURE.NOTE]Ignore esta seção para Windows Phone 8, Windows Phone 8.1 Silverlight e todos os outros aplicativos não Windows. Se já tiver configurado notificações por push para seu aplicativo do Windows, também ignore esta seção.

1. Faça logon no [portal do Azure] e navegue até o seu aplicativo. Copie a **URL**. Você a usará para configurar seu aplicativo com a Conta da Microsoft.

2. Se você ainda não registrou seu aplicativo, navegue até o [Centro de Desenvolvimento do Windows](https://dev.windows.com/dashboard/Application/New), faça logon com sua conta da Microsoft, digite um nome para seu aplicativo, verifique sua disponibilidade e clique em **Reservar o nome do aplicativo**.

3. Abra seu projeto de aplicativo do Windows no Visual Studio. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto de aplicativo da Windows Store e clique em **Loja** > **Associar aplicativo à Loja...**.

  	![](./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-windows-store-association.png)

4. No assistente, clique em **Entrar** e entre com sua conta da Microsoft, selecione o nome do aplicativo que você reservou e clique em **Avançar** > **Associar**. Para um aplicativo universal do Windows 8.1, é necessário repetir as etapas 4 e 5 para o projeto da Windows Phone Store.

6. De volta à página do Centro de Desenvolvimento do Windows do seu novo aplicativo, clique em **Serviços** > **Notificações por push**.

7. Na página **Notificações por push**, clique em **site dos Live Services** em **WNS (Serviços de Notificação por Push do Windows) e Serviços Móveis do Microsoft Azure**.

Em seguida, você vai configurar a autenticação da conta da Microsoft para aplicativos do Windows.


## <a name="register-microsoft-account"> </a>Registrar seu aplicativo na Conta da Microsoft

Se já tiver registrado seu aplicativo do Windows na seção anterior, ignore esta etapa e vá para a etapa 4.

1. Faça logon no [portal do Azure] e navegue até o seu aplicativo. Copie a **URL**. Você a usará para configurar seu aplicativo com a Conta da Microsoft.

2. Navegue até a página [Meus Aplicativos] no Centro de Desenvolvedoras da conta da Microsoft e faça logon com sua conta da Microsoft, se necessário.

3. Clique em **Criar aplicativo**, digite um **Nome de aplicativo** e clique em **Aceito**.

4. Clique em **Configurações de API**, selecione **Sim** para **Aplicativo de cliente móvel ou de desktop**, forneça a **URL de Redirecionamento** de seu aplicativo e clique em **Salvar**.
 
	![][0]

	>[AZURE.NOTE]O URI de redirecionamento é a URL do seu aplicativo acrescentada com o caminho _/.auth/login/microsoftaccount/callback_. Por exemplo: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Certifique-se de que você está usando o esquema HTTPS.

6. Clique em **Configurações da API** e anote os valores da **ID do cliente** e **Segredo do cliente**.

    > [AZURE.IMPORTANT] O segredo do cliente é uma credencial de segurança importante. Não compartilhe o segredo do cliente com ninguém nem distribua-o em um aplicativo cliente.

## <a name="secrets"> </a>Adicionar informações da Conta da Microsoft ao aplicativo do Serviço de Aplicativo

1. De volta ao [portal do Azure], navegue até o aplicativo, clique em **Configurações** > **Autenticação/Autorização**.

2. Se o recurso Autenticação/Autorização não estiver habilitado, mude-o para **Ativado**.

3. Clique em **Conta da Microsoft**. Cole os valores de ID do Aplicativo e de Segredo do Aplicativo que você obteve anteriormente e, opcionalmente, habilite os escopos exigidos pelo seu aplicativo. Em seguida, clique em **OK**.

    ![][1]

	Por padrão, o Serviço de Aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do site e às APIs. Você deve autorizar os usuários no código do aplicativo.

4. (Opcional) Para restringir o acesso ao seu site somente para usuários autenticados pela conta da Microsoft, defina **Ação a ser executada quando a solicitação não for autenticada** como **Conta da Microsoft**. Isso exige que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas à conta da Microsoft para autenticação.

5. Clique em **Salvar**.

Agora você está pronto para usar uma conta da Microsoft para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[portal do Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0302_2016-->