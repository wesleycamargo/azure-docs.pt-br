<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Google login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Registrar seus aplicativos para logon no Google com os Serviços Móveis

Este tópico mostra a você como registrar seus aplicativos para poder usar o Google para se autenticar com os Serviços Móveis do Azure.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Para concluir o procedimento neste t&oacute;pico, voc&ecirc; deve ter uma conta do Google com um endere&ccedil;o de email verificado. Para criar uma nova conta do Google, v&aacute; para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div>

1.  Vá até o site [Google apis][Google apis] faça logon com suas credenciais de conta do Google, clique em **Criar projeto**, forneça um **Nome do projeto** e clique em **Criar**.

    ![][0]

2.  Clique em **API & Auth**, clique em **Credenciais** e clique em **Criar nova ID de cliente**.

    ![][1]

3.  Selecione **Aplicativo Web**, digite a URL do seu Serviço Móvel em **Authorized JavaScript Origins**, substitua a URL gerada em **URI de Redirecionamento Autorizados** com a URL do seu serviço móvel acrescentado ao caminho */login/google* e clique em **Criar ID do cliente**.

    > [WACOM.NOTE]Para um serviço móvel de back-end do .NET publicado no Azure usando o Visual Studio, a URL de redirecionamento é a URL do seu serviço móvel acrescentada ao caminho *signin-google* e o seu serviço móvel é um serviço .NET, por exemplo, . <code>https://todolist.azure-mobile.net/signin-google</code>.

    ![][2]

4.  Em **ID do Cliente para aplicativos web**, anote os valores da **ID do Cliente** e do **Segredo do cliente**.

    ![][3]

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o de Seguran&ccedil;a</b>
<p>O segredo do cliente &eacute; uma credencial de seguran&ccedil;a importante. N&atilde;o compartilhe esse segredo com ningu&eacute;m nem o distribua com seu aplicativo.</p>
</div>

Agora você está pronto para usar um logon do Google para autenticação no seu aplicativo, fornecendo os valores de ID do cliente e de segredo do cliente para os Serviços Móveis.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  [0]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
  [1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
  [2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
  [3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
