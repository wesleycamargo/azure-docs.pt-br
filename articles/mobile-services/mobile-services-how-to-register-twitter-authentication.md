<properties
	pageTitle="Registrar-se para a autenticação do Twitter | Microsoft Azure"
	description="Aprenda a usar a autenticação do Twitter com o aplicativo de serviços móveis do Azure."
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
	ms.date="02/07/2016" 
	ms.author="glenga"/>

#Registrar seus aplicativos para logon do Twitter com os Serviços Móveis

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

Este tópico mostra a você como registrar seus aplicativos para usar o Twitter e se autenticar com os Serviços Móveis do Azure.

>[AZURE.NOTE] Este tutorial é sobre os [Serviços Móveis do Azure](https://azure.microsoft.com/services/mobile-services/), uma solução para ajudá-lo a criar aplicativos móveis escalonáveis para qualquer plataforma. Os Serviços Móveis facilita a sincronização de dados, autentica usuários e envia notificações por push. Esta página dá suporte ao tutorial [Adicionar autenticação ao seu aplicativo](mobile-services-ios-get-started-users.md), que mostra como fazer o logon de usuários em seu aplicativo. Se esta for sua primeira experiência com os Serviços Móveis, conclua o tutorial [Introdução aos Serviços Móveis](mobile-services-ios-get-started.md).

Para concluir o procedimento deste tópico, você deve ter uma conta do Twitter com um endereço de email verificado. Para criar uma nova conta do Twitter, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

1. Navegue até o site de [Desenvolvedores do Twitter,](http://go.microsoft.com/fwlink/p/?LinkId=268300) entre com suas credenciais da conta do Twitter e clique em **Criar novo aplicativo**.

2. Digite os valores de **Nome**, **Descrição**, e **Site** de seu aplicativo e digite um dos formatos de URL a seguir em **URL de Retorno de Chamada**.

	+ **Back-end do .NET**: `https://<mobile_service>.azure-mobile.net/signin-twitter`
	+ **Back-end do JavaScript**: `https://<mobile_service>.azure-mobile.net/login/twitter`

	 >[AZURE.NOTE]Certifique-se de que você use o formato de caminho da URL de redirecionamento correto para seu tipo de back-end de Serviços Móveis. Quando isso estiver incorreto, a autenticação não terá êxito. &nbsp;

   	![][2]

3.  Na parte inferior da página, leia e aceite os termos e, em seguida, clique em **Criar seu aplicativo Twitter**.

   	Isso registrará o aplicativo e exibirá os detalhes do aplicativo.

6. Clique na guia **Chaves e Tokens de Acesso** no painel do seu aplicativo e anote os valores de **Chave do Consumidor** e **Segredo do Consumidor**.

    > [AZURE.NOTE] O segredo do consumidor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.

7. Clique na guia **Configurações**, role para baixo e certifique-se de que a opção **Permitir que este aplicativo seja usado para entrar com o Twitter** está marcada; em seguida, clique em **Atualizar Configurações**.

Agora você está pronto para usar um logon do Twitter para autenticação no seu aplicativo, fornecendo os valores de chave do consumidor e de segredo do consumidor para os Serviços Móveis.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

<!---HONumber=AcomDC_0211_2016-->