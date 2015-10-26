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
	ms.date="09/25/2015" 
	ms.author="glenga"/>

# Registrar seus aplicativos para a autenticação do Facebook com os Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

Este tópico mostra como registrar seus aplicativos para poder usar o Facebook para autenticar com os Serviços Móveis do Azure.

>[AZURE.NOTE]Este tutorial é sobre os [Serviços Móveis do Azure], uma solução para ajudá-lo a criar aplicativos móveis escalonáveis para qualquer plataforma. Os Serviços Móveis facilita a sincronização de dados, autentica usuários e envia notificações por push. Esta página oferece suporte ao tutorial [Introdução à autenticação](mobile-services-ios-get-started-users.md) que mostra como fazer o logon de usuários no seu aplicativo. Se esta for sua primeira experiência com os Serviços Móveis, conclua o tutorial [Introdução aos Serviços Móveis](mobile-services-ios-get-started.md).
	
Para concluir o procedimento neste tópico, você deve ter uma conta do Facebook com um endereço de email verificado e um número de telefone celular. Para criar uma nova conta do Facebook, vá para [facebook.com](http://go.microsoft.com/fwlink/p/?LinkId=268285).

1. Navegue até o site de [Desenvolvedores do Facebook](http://go.microsoft.com/fwlink/p/?LinkId=268285) e entre com suas credenciais de conta do Facebook.

2. (Opcional) Se você ainda não foi registrado, clique em **Meus Aplicativos** e em **Registrar como um Desenvolvedor**, aceite a política e siga as etapas de registro.

3. Clique em **Meus aplicativos** > **Adicionar um Novo Aplicativo** > **Configuração avançada**.

4. Digite um **Nome de exibição** exclusivo para seu aplicativo, escolha **Aplicativos para Páginas** em **Categoria**, clique em **Criar ID de Aplicativo** e conclua o exercício de segurança.

	Isso cria uma nova ID para o aplicativo Facebook.

5. Clique em **Configurações**, digite o domínio do seu serviço móvel em **Domínios de Aplicativo**, insira um **Email de Contato** opcional, clique em **Adicionar Plataforma** e selecione **Site**.

   	![][3]

6. Digite a URL do seu serviço móvel em **URL do Site** e clique em **Salvar Alterações**.

7. Clique em **Mostrar**, forneça sua senha se solicitado e anote os valores de **ID do Aplicativo** e **Segredo do Aplicativo**.

   	![][5] &nbsp;
	
    >[AZURE.IMPORTANT]O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo. &nbsp;

8. Clique na guia **Avançado**, digite a URL de seu serviço móvel acrescentada ao caminho _/login/facebook_ em **URIs de redirecionamento do OAuth válido** e clique em** Salvar Alterações**. &nbsp;

     >[AZURE.NOTE]Para um serviço móvel de back-end do .NET publicado no Azure usando o Visual Studio, a URL de redirecionamento é a URL do seu serviço móvel acrescentada ao caminho _signin-facebook_ e o seu serviço móvel é um serviço .NET, como `https://todolist.azure-mobile.net/signin-facebook`.
       

9. Clique em **Status e Revisão** > **Sim** para habilitar o acesso do público geral ao seu aplicativo.

	A conta do Facebook que você usou para registrar o novo aplicativo é um administrador do aplicativo e tem acesso ao aplicativo como administrador. Esta etapa concede acesso ao público geral para que o aplicativo possa autenticar usando outras contas do Facebook.


Agora você está pronto para usar um logon do Facebook para autenticação em seu aplicativo, fornecendo os valores de ID do Aplicativo e Segredo do Aplicativo para os Serviços Móveis.

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Management Portal]: https://manage.windowsazure.com/
[Serviços Móveis do Azure]: http://azure.microsoft.com/services/mobile-services/
 

<!---HONumber=Oct15_HO3-->