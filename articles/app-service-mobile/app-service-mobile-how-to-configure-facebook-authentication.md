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
	ms.date="10/29/2015"
	ms.author="mahender"/>

# Como configurar seu aplicativo do Serviço do Aplicativo para usar o logon do Facebook

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Este tópico mostra como configurar o Serviço de Aplicativo do Azure para usar o Facebook como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Facebook com um endereço de email verificado e um número de telefone celular. Para criar uma nova conta do Facebook, vá para [facebook.com].


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Registrar seu aplicativo com o Facebook

1. Faça logon no [Portal de Gerenciamento do Azure] e navegue até o seu aplicativo. Copie a **URL**. Você a usará para configurar o seu aplicativo do Facebook.
 
2. Em outra janela do navegador, navegue até o site de [Desenvolvedores do Facebook] e entre com suas credenciais de conta do Facebook.

3. (Opcional) Se você ainda não foi registrado, clique em **Aplicativos** e em **Registrar como um desenvolvedor**, aceite a política e siga as etapas de registro.

4. Clique em **Meus aplicativos** e clique em **Adicionar um Novo Aplicativo**.

5. Selecione **Site** como sua plataforma. Escolha um nome exclusivo para seu aplicativo e, em seguida, clique em **Criar nova ID do Aplicativo do Facebook**.

6. Selecione uma categoria para o seu aplicativo no menu suspenso. Em seguida, clique em **Criar ID do Aplicativo**.

7. Na página seguinte, selecione **Ignorar Início Rápido** no canto superior direito. Isso o levará ao painel do desenvolvedor para o seu aplicativo.

8. Clique no campo **Segredo do Aplicativo**, clique em **Mostrar**, forneça sua senha se solicitado e anote os valores de **ID do Aplicativo** e **Segredo do Aplicativo**. Você irá configurar seu aplicativo para usar mais tarde.

	> [AZURE.NOTE]**Observação de segurança** O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.

9. Na barra de navegação à esquerda, clique em **Configurações**. Digite a **URL** do seu Aplicativo móvel em **Domínios de Aplicativo** e insira um **Email de Contato**.

    ![][0]

10. Se você não vir uma seção do site abaixo, clique em **Adicionar Plataforma** e selecione **Site**. Insira a **URL** do seu Aplicativo móvel no campo da **URL do Site** e clique em **Salvar Alterações**.

11. Clique na guia **Avançado** e adicione o **URI de redirecionamento** do seu aplicativo a **URIs de redirecionamento válidos do OAuth**. Em seguida, clique em **Salvar Alterações**. O URI de redirecionamento é a URL do seu aplicativo adicionada ao caminho _/.auth/login/facebook/callback_. Por exemplo: `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Certifique-se de que você está usando o esquema HTTPS.


	> [AZURE.NOTE]Se você estiver usando o Gateway do Serviço de Aplicativo em vez do recurso de Autenticação/Autorização do Serviço de Aplicativo, a URL de redirecionamento usará a URL de gateway com o caminho _/signin-facebook_.


12. A conta do Facebook usada para registrar o aplicativo é um administrador do aplicativo. Neste ponto, apenas os administradores podem entrar neste aplicativo. Para autenticar outras contas do Facebook, clique em **Status e Análise** na barra de navegação à esquerda. Depois clique em **Sim** para habilitar o acesso ao público geral.


## <a name="secrets"> </a>Adicionar informações do facebook ao seu aplicativo


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Facebook**. Paste in the values you obtained earlier and click **Save**.


13. De volta ao [Portal de Gerenciamento do Azure], navegue até o aplicativo. Clique em **Configurações** e em **Autenticação/Autorização**.

14. Se o recurso Autenticação/Autorização não estiver habilitado, mude a opção para **Ativado**.

15. Clique em **Facebook**. Cole os valores de ID do Aplicativo e de Segredo do Aplicativo que você obteve anteriormente e, opcionalmente, habilite os escopos exigidos pelo seu aplicativo. Em seguida, clique em **OK**.

    ![][1]
	
16. Por padrão, o Serviço de Aplicativo fornece logon, mas não restringe o acesso ao conteúdo do site e às APIs - isso é responsabilidade do código do aplicativo. Se você quiser que o site seja totalmente protegido pelo logon do Facebook, altere a lista suspensa **Ação a ser tomada quando a solicitação não for autenticada** para usar a opção **Facebook**. Isso exigirá que todas as solicitações sejam autenticadas; as solicitações não autenticadas serão redirecionadas para fazer logon com o Facebook.

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
[Portal de Gerenciamento do Azure]: https://portal.azure.com/

<!---HONumber=Nov15_HO4-->