<properties
	pageTitle="Como configurar a autenticação do Facebook para seu aplicativo de Serviços de Aplicativos"
	description="Saiba como configurar a autenticação do Facebook para seu aplicativo de Serviços de Aplicativos."
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="mahender"/>

# Como configurar seu aplicativo do Serviço do Aplicativo para usar o logon do Facebook

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o Serviço de Aplicativo do Azure para usar o Facebook como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Facebook com um endereço de email verificado e um número de telefone celular. Para criar uma nova conta do Facebook, vá para [facebook.com].

> [AZURE.NOTE]
Este tópico demonstra o uso do recurso de Autenticação / Autorização do Serviço de Aplicativo. Isso substitui o gateway do Serviço de Aplicativo para a maioria dos aplicativos. As diferenças que se aplicam ao uso do gateway são destacadas nas notas ao longo desse tópico.


## <a name="register"> </a>Registrar seu aplicativo com o Facebook

1. Faça logon no [portal do Azure] e navegue até o seu aplicativo. Copie a **URL**. Você a usará para configurar o seu aplicativo do Facebook.

2. Em outra janela do navegador, navegue até o site de [Desenvolvedores do Facebook] e entre com suas credenciais de conta do Facebook.

3. (Opcional) Se você ainda não se registrou, clique em **Aplicativos** > **Registrar-se como desenvolvedor**, aceite a política e siga as etapas de registro.

4. Clique em **Meus aplicativos** > **Adicionar um novo aplicativo** > **site**, digite um nome exclusivo para seu aplicativo, clique em **Criar nova ID de aplicativo do Facebook**.

6. Selecione uma categoria para o seu aplicativo no menu suspenso, clique em **Criar ID de aplicativo** e, na próxima página, clique em **Ignorar início rápido**. Isso levará você ao painel do desenvolvedor de seu aplicativo.

8. Clique no campo **Segredo do Aplicativo**, clique em **Mostrar**, forneça sua senha se solicitado e anote os valores de **ID do Aplicativo** e **Segredo do Aplicativo**. Você irá configurar seu aplicativo para usar mais tarde.

	> [AZURE.NOTE] **Observação de segurança** O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.

9. Na barra de navegação à esquerda, clique em **Configurações**, digite a **URL** do aplicativo móvel em **Domínios de aplicativo** e insira um **Email de contato**.

    ![][0]

10. Se você não vir uma seção do site abaixo, clique em **Adicionar plataforma** > **Site**, insira a **URL** do aplicativo móvel no campo **URL do Site** e, em seguida, clique em **Salvar alterações**.

11. Clique na guia **Avançado**, adicione o **URI de redirecionamento** de seu aplicativo aos **URIs de redirecionamento OAuth válidos** e depois clique em **Salvar alterações**. O URI de redirecionamento é a URL do seu aplicativo adicionada ao caminho _/.auth/login/facebook/callback_. Por exemplo: `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Certifique-se de que você está usando o esquema HTTPS.


	> [AZURE.NOTE]
	Se você estiver usando o Gateway do Serviço de Aplicativo em vez do recurso de Autenticação / Autorização do Serviço de Aplicativo, a URL de redirecionamento usará a URL de gateway com o caminho _/signin-facebook_.


12. A conta do Facebook usada para registrar o aplicativo é um administrador do aplicativo. Neste ponto, apenas os administradores podem entrar neste aplicativo. Para autenticar outras contas do Facebook, clique em **Status e Análise** na barra de navegação à esquerda. Depois clique em **Sim** para habilitar o acesso ao público geral.


## <a name="secrets"> </a>Adicionar informações do Facebook ao seu aplicativo

> [AZURE.NOTE]
Se for usar o Gateway do Serviço de Aplicativo, ignore esta seção e navegue até o gateway no portal. Selecione **Configurações**, **Identidade**, e então **Facebook**. Cole os valores obtidos anteriormente e clique em **Salvar**.


13. De volta ao [portal do Azure], navegue até o seu aplicativo. Clique em **Configurações** > **Autenticação/Autorização** e certifique-se de que **Autenticação do serviço de aplicativo** esteja **Ativado**.

15. Clique em **Facebook**, cole os valores de ID do aplicativo e de Segredo do aplicativo obtidos anteriormente e, opcionalmente, habilite os escopos exigidos por seu aplicativo, e clique em **OK**.

    ![][1]

	Por padrão, o Serviço de Aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do site e às APIs. Você deve autorizar os usuários no código do aplicativo.

17. (Opcional) Para restringir o acesso ao seu site somente para usuários autenticados pelo Facebook, defina **Ação a ser executada quando a solicitação não for autenticada** como o **Facebook**. Isso exige que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas ao Facebook para autenticação.

17. Clique em **Salvar**.

Agora você está pronto para usar o Facebook para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Desenvolvedores do Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /pt-BR/develop/mobile/tutorials/get-started-with-users-dotnet/
[portal do Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0211_2016-->