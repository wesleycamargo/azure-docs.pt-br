<properties
	pageTitle="Registrar a autenticação do Facebook | Serviços Móveis do Azure"
	description="Aprenda a usar a autenticação do Facebook em seu aplicativo de Serviços Móveis do Azure."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/28/2016"
	ms.author="glenga"/>

# Registrar seus aplicativos para a autenticação do Facebook com os Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]&nbsp;


[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

Este tópico mostra como registrar seus aplicativos para poder usar o Facebook para autenticar com os Serviços Móveis do Azure. Esta página oferece suporte ao tutorial [Introdução à autenticação](mobile-services-ios-get-started-users.md) que mostra como fazer o logon de usuários no seu aplicativo. Se esta for sua primeira experiência com os Serviços Móveis, conclua o tutorial [Introdução aos Serviços Móveis](mobile-services-ios-get-started.md).

Para concluir o procedimento neste tópico, você deve ter uma conta do Facebook com um endereço de email verificado e um número de telefone celular. Para criar uma nova conta do Facebook, vá para [facebook.com](http://go.microsoft.com/fwlink/p/?LinkId=268285).

1. Navegue até o site de [Desenvolvedores do Facebook](http://go.microsoft.com/fwlink/p/?LinkId=268285) e entre com suas credenciais de conta do Facebook.

2. (Opcional) Se você ainda não foi registrado, clique em **Meus Aplicativos** e em **Registrar como um Desenvolvedor**, aceite a política e siga as etapas de registro.

3. Clique em **Meus Aplicativos** > **Adicionar um Novo Aplicativo** > **Site** > **Ignorar e Criar ID do Aplicativo**.

4. Em **Nome de Exibição**, insira um nome exclusivo para seu aplicativo, escolha uma **Categoria** para ele, clique em **Criar ID do Aplicativo** e conclua a verificação de segurança. Isso o levará ao painel do desenvolvedor de seu novo aplicativo do Facebook.

5. Clique no campo **Segredo do Aplicativo**, clique em **Mostrar**, forneça sua senha se solicitado e anote os valores de **ID do Aplicativo** e **Segredo do Aplicativo**. Você os utilizará mais tarde para configurar seu aplicativo no Azure.

	> [AZURE.NOTE] **Observação de segurança** O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.

5. Na barra de navegação à esquerda, clique em **Configurações**, digite o domínio de seu serviço móvel em **Domínios do Aplicativo**, insira um **Email de Contato** opcional, clique em **Adicionar Plataforma** e selecione **Site**.

   	![][3]

6. Digite a URL do seu serviço móvel em **URL do Site** e clique em **Salvar Alterações**.

7. Clique em **Mostrar**, forneça sua senha se solicitado e anote os valores de **ID do Aplicativo** e **Segredo do Aplicativo**.

   	![][5] &nbsp;

    >[AZURE.IMPORTANT] O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo. &nbsp;

8. Clique na guia **Avançado**, digite um dos seguintes formatos de URL em **URIs de redirecionamento válidos de OAuth** e clique em **Salvar Alterações**:

	+ **Back-end do .NET**: `https://<mobile_service>.azure-mobile.net/signin-facebook`
	+ **Back-end do JavaScript**: `https://<mobile_service>.azure-mobile.net/login/facebook`

	 >[AZURE.NOTE]Certifique-se de que você está usando o formato de caminho da URL de redirecionamento correto para seu tipo de back-end dos Serviços Móveis, usando o esquema *HTTPS*. Quando isso estiver incorreto, a autenticação não terá êxito.


12. A conta do Facebook usada para registrar o aplicativo é um administrador do aplicativo. Neste ponto, apenas os administradores podem entrar neste aplicativo. Para autenticar outras contas do Facebook, clique em **Análise do Aplicativo** e habilite **Tornar todolist-complete-nodejs público** para habilitar o acesso ao público geral usando a autenticação do Facebook.

Agora você está pronto para usar um logon do Facebook para autenticação em seu aplicativo, fornecendo os valores de ID do Aplicativo e Segredo do Aplicativo para os Serviços Móveis.

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Mobile Services]: http://azure.microsoft.com/services/mobile-services/

<!---HONumber=AcomDC_0302_2016-->