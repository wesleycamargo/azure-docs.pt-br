<properties
	pageTitle="Como configurar a autenticação do Facebook para seu aplicativo de Serviços de Aplicativos"
	description="Saiba como configurar a autenticação do Facebook para seu aplicativo de Serviços de Aplicativos."
	services="app-service"
	documentationCenter=""
	authors="mattchenderson"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahender"/>

# Como configurar seu aplicativo do Serviço do Aplicativo para usar o logon do Facebook

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o Serviço de Aplicativo do Azure para usar o Facebook como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Facebook com um endereço de email verificado e um número de telefone celular. Para criar uma nova conta do Facebook, vá para [facebook.com].

## <a name="register"> </a>Registrar seu aplicativo com o Facebook

1. Faça logon no [portal do Azure] e navegue até o seu aplicativo. Copie a **URL**. Você a usará para configurar o seu aplicativo do Facebook.

2. Em outra janela do navegador, navegue até o site de [Desenvolvedores do Facebook] e entre com suas credenciais de conta do Facebook.

3. (Opcional) Se você ainda não se registrou, clique em **Aplicativos** > **Registrar-se como desenvolvedor**, aceite a política e siga as etapas de registro.

4. Clique em **Meus Aplicativos** > **Adicionar um Novo Aplicativo** > **Site** > **Ignorar e Criar ID do Aplicativo**.

5. Em **Nome de Exibição**, insira um nome exclusivo para seu aplicativo, escolha uma **Categoria** para ele, clique em **Criar ID do Aplicativo** e conclua a verificação de segurança. Isso o levará ao painel do desenvolvedor de seu novo aplicativo do Facebook.

6. Em "Logon no Facebook", clique em **Introdução**. Adicione o **URI de redirecionamento** do seu aplicativo aos **URIs de redirecionamento OAuth válidos** e, então, clique em **Salvar alterações**.

	> [AZURE.NOTE] O URI de redirecionamento é a URL do seu aplicativo adicionada ao caminho _/.auth/login/facebook/callback_. Por exemplo: `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Certifique-se de que você está usando o esquema HTTPS.

6. No painel de navegação à esquerda, clique em **Configurações**. Clique no campo **Segredo do Aplicativo**, clique em **Mostrar**, forneça sua senha se solicitado e anote os valores de **ID do Aplicativo** e **Segredo do Aplicativo**. Você os utilizará mais tarde para configurar seu aplicativo no Azure.

	> [AZURE.IMPORTANT] O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.

7. A conta do Facebook usada para registrar o aplicativo é um administrador do aplicativo. Neste ponto, apenas os administradores podem entrar neste aplicativo. Para autenticar outras contas do Facebook, clique em **Análise do Aplicativo** e habilite a opção **Tornar <your-app-name> público** para habilitar o acesso ao público geral usando a autenticação do Facebook.

## <a name="secrets"> </a>Adicionar informações do Facebook ao seu aplicativo

1. De volta ao [portal do Azure], navegue até o seu aplicativo. Clique em **Configurações** > **Autenticação/Autorização** e certifique-se de que a **Autenticação do Serviço de Aplicativo** está **Ativada**.

2. Clique em **Facebook**, cole os valores de ID do aplicativo e de Segredo do aplicativo obtidos anteriormente e, opcionalmente, habilite os escopos exigidos por seu aplicativo, e clique em **OK**.

    ![][0]

	Por padrão, o Serviço de Aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do site e às APIs. Você deve autorizar os usuários no código do aplicativo.

3. (Opcional) Para restringir o acesso ao seu site somente para usuários autenticados pelo Facebook, defina **Ação a ser executada quando a solicitação não for autenticada** como o **Facebook**. Isso exige que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas ao Facebook para autenticação.

4. Ao concluir a configuração de autenticação, clique em **Salvar**.

Agora você está pronto para usar o Facebook para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Desenvolvedores do Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /pt-BR/develop/mobile/tutorials/get-started-with-users-dotnet/
[portal do Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0511_2016-->