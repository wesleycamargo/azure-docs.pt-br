<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Register for Twitter Authentication" pageTitle="Register for Twitter authentication - Mobile Services" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Twitter login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Registrar seus aplicativos para logon no Twitter com os Serviços Móveis

Este tópico mostra a você como registrar seus aplicativos para poder usar o Twitter para se autenticar com os Serviços Móveis do Azure.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Para concluir o procedimento deste t&oacute;pico, voc&ecirc; deve ter uma conta do Twitter com um endere&ccedil;o de email verificado. Para criar uma nova conta do Twitter, v&aacute; para <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.</p>
</div>

1.  Navegue até o site [Desenvolvedores do Twitter][Desenvolvedores do Twitter], entre com suas credenciais de conta do Twitter e clique em **Criar um novo aplicativo**.

    ![][0]

2.  Digite os valores **Nome**, **Descrição** e **Site** para seu aplicativo, depois digite a URL para seu serviço acrescentado ao caminho */login/twitter* em **URL de Retorno de Chamada**.

    > [WACOM.NOTE]Para um serviço móvel de back-end do .NET publicado no Azure usando o Visual Studio, a URL de redirecionamento é a URL do seu serviço móvel acrescentada ao caminho *signin-twitter* e o seu serviço móvel é um serviço .NET, por exemplo, . <code>https://todolist.azure-mobile.net/signin-twitter</code>.

    ![][1]

3.  Na parte inferior da página, leia e aceite os termos, digite as palavras corretas de CAPTCHA e, em seguida, clique em **Criar seu aplicativo Twitter**.

    ![][2]

    Isso registrará o aplicativo e exibirá os detalhes do aplicativo.

4.  Anote os valores de **Chave do consumidor** e **Segredo do consumidor**.

    ![][3]

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o de Seguran&ccedil;a</b>
<p>O segredo do consumidor &eacute; uma credencial de seguran&ccedil;a importante. N&atilde;o compartilhe esse segredo com ningu&eacute;m nem o distribua com seu aplicativo.</p>
</div>

5.  Clique na guia **Configurações**, role para baixo, marque **Permitir que este aplicativo seja usado para entrar com o Twitter** e, em seguida, clique em **Atualizar as configurações deste aplicativo Twitter**.

    ![][4]

Agora você está pronto para usar um logon do Twitter para autenticação no seu aplicativo, fornecendo os valores de chave do consumidor e de segredo do consumidor para os Serviços Móveis.

 
 


  [Desenvolvedores do Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
  [0]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
  [1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
  [2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
  [3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
  [4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png
