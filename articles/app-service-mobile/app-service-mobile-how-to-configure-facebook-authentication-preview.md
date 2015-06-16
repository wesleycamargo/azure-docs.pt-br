<properties 
	pageTitle="Como configurar a autenticação do Facebook para seu aplicativo de Serviços de Aplicativos"
	description="Saiba como configurar a autenticação do Facebook para seu aplicativo de Serviços de Aplicativos." 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson,ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="mahender"/>

# Como configurar seu aplicativo para usar o logon do Facebook

Este tópico mostra como configurar os Serviços de Aplicativos do Azure para usar o Facebook como um provedor de autenticação.
	
Para concluir o procedimento neste tópico, você deve ter uma conta do Facebook com um endereço de email verificado e um número de telefone celular. Para criar uma nova conta do Facebook, vá para [facebook.com].

## <a name="register"> </a>Registrar seu aplicativo com o Facebook

1. Navegue até o site de [Desenvolvedores do Facebook] e entre com suas credenciais de conta do Facebook.

2. (Opcional) Se você ainda não foi registrado, clique em **Aplicativos** e em **Registrar como um desenvolvedor**, aceite a política e siga as etapas de registro.

3. Clique em **Aplicativos** e clique em **Criar um Novo Aplicativo**.

4. Selecione **Site** como sua plataforma. Escolha um nome exclusivo para seu aplicativo e, em seguida, clique em **Criar nova ID do Aplicativo do Facebook**.

5. Selecione uma categoria para o seu aplicativo no menu suspenso. Em seguida, clique em **Criar ID do Aplicativo**.

6. Na página seguinte, selecione **Ignorar Início Rápido** no canto superior direito. Isso o levará ao painel do desenvolvedor para o seu aplicativo.

7. Clique no campo **Segredo do Aplicativo**, clique em **Mostrar**, forneça sua senha se solicitado e anote os valores de **ID do Aplicativo** e **Segredo do Aplicativo**.

	> [AZURE.NOTE]**Observação de segurança** O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.

8. Na barra de navegação à esquerda, clique em **Configurações**. Digite a URL do gateway em **Domínios de Aplicativo** e insira um **Email de Contato**.

    ![][0]

9. Se você não vir uma seção do site abaixo, clique em **Adicionar Plataforma** e selecione **Site**. Digite a URL do seu gateway de Serviço de Aplicativo em **URL do Site** e, em seguida, clique em **Salvar Alterações**.

10. Clique na guia **Avançado** e adicione o URI de redirecionamento a **URIs de redirecionamento OAuth válidos**. Em seguida, clique em **Salvar Alterações**. O URI redirecionamento é a URL do seu gateway anexada ao caminho, _/signin-facebook_. Por exemplo: `https://contosogateway.azurewebsites.net/signin-facebook`. Certifique-se de que você está usando o esquema HTTPS.

11. A conta do Facebook usada para registrar o aplicativo é um administrador do aplicativo. Neste ponto, apenas os administradores podem entrar neste aplicativo. Para autenticar outras contas do Facebook, clique em **Status e Análise** no painel de navegação esquerdo. Depois clique em **Sim** para habilitar o acesso ao público geral.


## <a name="secrets"> </a>Adicionar informações do Facebook ao seu aplicativo móvel


12. Faça logon no [Portal de Gerenciamento do Azure] e navegue para o gateway de Serviços de Aplicativos.

13. Em **Configurações**, escolha **Identidade** e, em seguida, selecione **Facebook**. Cole os valores de ID do aplicativo e segredo do aplicativo obtidos anteriormente. Em seguida, clique em **Salvar**.

    ![][1]

Agora você está pronto para usar o Facebook para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/app-service-facebook-settings.png

<!-- URLs. -->
[Desenvolvedores do Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal de Gerenciamento do Azure]: https://portal.azure.com/
<!--HONumber=54--> 