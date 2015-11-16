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

# Como configurar seu aplicativo para usar o logon do Facebook

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Este tópico mostra como configurar os aplicativos móveis do Azure para usar o Facebook como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Facebook com um endereço de email verificado e um número de telefone celular. Para criar uma nova conta do Facebook, vá para [facebook.com].

## <a name="register"> </a>Registrar seu aplicativo com o Facebook

1. Faça logon no [Portal de Gerenciamento do Azure] e navegue para o seu Aplicativo móvel. Copie a sua **URL**. Você a usará para configurar o seu aplicativo do Facebook.
 
2. Clique em **Configurações**, **Autenticação de usuário** e em **Facebook**. Em seguida, copie o **URI de redirecionamento** da folha do Facebook. Você a usará com seu aplicativo do Facebook.
 
3. Em outra janela do navegador, navegue até o site de [Desenvolvedores do Facebook] e entre com suas credenciais de conta do Facebook.

4. (Opcional) Se você ainda não foi registrado, clique em **Aplicativos** e em **Registrar como um desenvolvedor**, aceite a política e siga as etapas de registro.

5. Clique em **Meus aplicativos** e clique em **Adicionar um Novo Aplicativo**.

6. Selecione **Site** como sua plataforma. Escolha um nome exclusivo para seu aplicativo e, em seguida, clique em **Criar nova ID do Aplicativo do Facebook**.

7. Selecione uma categoria para o seu aplicativo no menu suspenso. Em seguida, clique em **Criar ID do Aplicativo**.

8. Na página seguinte, selecione **Ignorar Início Rápido** no canto superior direito. Isso o levará ao painel do desenvolvedor para o seu aplicativo.

9. Clique no campo **Segredo do Aplicativo**, clique em **Mostrar**, forneça sua senha se solicitado e anote os valores de **ID do Aplicativo** e **Segredo do Aplicativo**. Você irá defini-los na folha de configurações de autenticação de Facebook do seu Aplicativo móvel.

	> [AZURE.NOTE]**Observação de segurança** O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.

10. Na barra de navegação à esquerda, clique em **Configurações**. Digite a **URL** do seu Aplicativo móvel em **Domínios de Aplicativo** e insira um **Email de Contato**.

    ![][0]

11. Se você não vir uma seção do site abaixo, clique em **Adicionar Plataforma** e selecione **Site**. Insira a **URL** do seu Aplicativo móvel no campo da **URL do Site** e clique em **Salvar Alterações**.

12. Clique na guia **Avançado** e adicione o **URI de redirecionamento** do seu Aplicativo Móvel que copiou anteriormente aos **URIs de redirecionamento OAuth válidos**. Em seguida, clique em **Salvar Alterações**. O URI de redirecionamento é a URL do seu gateway do Aplicativo móvel anexada ao caminho, _/signin-facebook_. Por exemplo: `https://contosogateway.azurewebsites.net/signin-facebook`. Certifique-se de que você está usando o esquema HTTPS.

13. A conta do Facebook usada para registrar o aplicativo é um administrador do aplicativo. Neste ponto, apenas os administradores podem entrar neste aplicativo. Para autenticar outras contas do Facebook, clique em **Status e Análise** no painel de navegação à esquerda. Depois clique em **Sim** para habilitar o acesso ao público geral.


## <a name="secrets"> </a>Adicionar informações do Facebook ao seu aplicativo móvel


12. De volta no [Portal de gerenciamento do Azure], e navegue até a folha de configuração do Facebook para o seu Aplicativo móvel novamente. Cole os valores de ID do aplicativo e segredo do aplicativo obtidos anteriormente. Em seguida, clique em **Salvar**.

    ![][1]

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

<!---HONumber=Nov15_HO2-->