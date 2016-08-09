<properties
	pageTitle="Registrar-se para autenticação do Google | Microsoft Azure"
	description="Saiba como registrar seus aplicativos para usar o Google para autenticar com os Serviços Móveis do Azure."
	services="mobile-services"
	documentationCenter="android"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>


<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/21/2016" 
	ms.author="glenga"/>

# Registrar seus aplicativos para logon no Google com os Serviços Móveis

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

Este tópico mostra a você como registrar seus aplicativos para poder usar o Google para se autenticar com os Serviços Móveis do Azure.

>[AZURE.NOTE] Este tutorial é sobre os [Serviços Móveis do Azure](https://azure.microsoft.com/services/mobile-services/), uma solução para ajudá-lo a criar aplicativos móveis escalonáveis para qualquer plataforma. Os Serviços Móveis facilita a sincronização de dados, autentica usuários e envia notificações por push. Esta página dá suporte ao tutorial [Introdução à autenticação](mobile-services-ios-get-started-users.md), que mostra como conectar usuários ao seu aplicativo. <br/>Se essa for sua primeira experiência com os Serviços Móveis, conclua o tutorial [Introdução aos Serviços Móveis](mobile-services-ios-get-started.md).

Para concluir o procedimento neste tópico, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

3. Vá até o site [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) faça logon com suas credenciais de conta do Google, clique em **Criar projeto**, forneça um **Nome do projeto** e clique em **Criar**.

4. No **Produtos e serviços** menu suspenso, clique em **Gerencaidor de API**, em seguida, em **APIs Sociais**, clique em **API do Google+** > **Habilitar API**.

5. Clique em **Credenciais** > **Tela de consentimento do OAuth**, então, selecione seu **Endereço de email**, insira um **Nome do Produto** e clique em **Salvar**.

6. Na guia **Credenciais**, clique em **Adicionar credenciais** > **ID do cliente do OAuth 2.0** e selecione **Aplicativo Web**.


7. Digite a URL do serviço móvel em **Origens de JavaScript Autorizadas**, substitua a URL gerada em **URI de Redirecionamento Autorizado** por um dos seguintes formatos de URL e clique em **Criar**:
 

	+ **Back-end do .NET**: `https://<mobile_service>.azure-mobile.net/signin-google`
	+ **Back-end do JavaScript**: `https://<mobile_service>.azure-mobile.net/login/google`

	 >[AZURE.NOTE]Certifique-se de que você use o formato de caminho da URL de redirecionamento correto para seu tipo de back-end de Serviços Móveis. Quando isso estiver incorreto, a autenticação não terá êxito.

8. Na próxima tela, anote os valores de ID do cliente e de segredo do cliente.

    > [AZURE.IMPORTANT] O segredo do cliente é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.

Agora você está pronto para configurar seu serviço móvel para usar o logon do Google para autenticação em seu aplicativo.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

<!---HONumber=AcomDC_0727_2016-->