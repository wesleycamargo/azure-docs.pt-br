<properties linkid="develop-mobile-how-to-guides-register-for-facebook-authentication" urlDisplayName="Register for Facebook Authentication" pageTitle="Register for Facebook authentication - Mobile Services" metaKeywords="Azure Facebook, Azure Facebook, Azure authenticate Mobile Services" description="Learn how to use Facebook authentication in your Azure Mobile Services app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Facebook authentication with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Registrar seus aplicativos para a autenticação do Facebook com os Serviços Móveis

Este tópico mostra como registrar seus aplicativos para poder usar o Facebook para autenticar com os Serviços Móveis do Azure.

> [WACOM.NOTE] Este tutorial é sobre o [Serviços Móveis do Azure][Serviços Móveis do Azure], uma solução para ajudar você a desenvolver aplicativos móveis escaláveis para qualquer plataforma. Os Serviços Móveis facilita a sincronização de dados, autentica usuários e envia notificações por push. Esta página oferece suporte ao tutorial [Introdução à autenticação][Introdução à autenticação] que mostra como fazer o logon de usuários no seu aplicativo. Se esta for sua primeira experiência com os Serviços Móveis, conclua o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

Para concluir o procedimento neste tópico, você deve ter uma conta do Facebook com um endereço de email verificado e um número de telefone celular. Para criar uma nova conta do Facebook, vá para [facebook.com][facebook.com].

1.  Navegue até o site de [Desenvolvedores do Facebook][Desenvolvedores do Facebook] e entre com suas credenciais de conta do Facebook.

2.  (Opcional) Se você ainda não foi registrado, clique em **Aplicativos** e em **Registrar como um desenvolvedor**, aceite a política e siga as etapas de registro.

    ![][0]

3.  Clique em **Aplicativos** e clique em **Criar um Novo Aplicativo**.

    ![][1]

4.  Escolha um nome exclusivo para seu aplicativo, selecione **Aplicativos para Páginas**, clique em **Criar Aplicativo** e conclua a pergunta do desafio.

    ![][2]

    Isso registra o aplicativo com o Facebook

5.  Clique em **Configurações**, digite o domínio do seu serviço móvel em **Domínios de Aplicativo**. Além disso, insira um **Email de contato** e clique em **Adicionar Plataforma** e selecione **Site**.

    ![][3]

6.  Digite a URL do seu serviço móvel em **URL do Site** e clique em **Salvar Alterações**.

    ![][4]

7.  Clique em **Mostrar**, forneça sua senha se solicitado e anote os valores de **ID do Aplicativo** e **Segredo do Aplicativo**.

    ![][5]

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o de Seguran&ccedil;a</b>
<p>O segredo do aplicativo &eacute; uma credencial de seguran&ccedil;a importante. N&atilde;o compartilhe esse segredo com ningu&eacute;m nem o distribua com seu aplicativo.</p>
</div>

8.  Clique na guia **Avançado**, digite a URL de seu serviço móvel acrescentada ao caminho */login/facebook* em **URIs de redirecionamento do OAuth válido** e clique em **Salvar alterações**.

    > [WACOM.NOTE]Para um serviço móvel de back-end do .NET publicado no Azure usando o Visual Studio, a URL de redirecionamento é a URL do seu serviço móvel acrescentada ao caminho *signin-facebook* e o seu serviço móvel é um serviço .NET, por exemplo, . <code>https://todolist.azure-mobile.net/signin-facebook</code>.

    ![][6]

9.  A conta do Facebook, para o qual você definiu o novo aplicativo, é um administrador do aplicativo e tem acesso ao aplicativo como administrador. Para autenticar outras contas do Facebook, eles precisam ter acesso ao aplicativo. Esta etapa concede acesso ao público geral para que o aplicativo possa autenticar outras contas do Facebook. Clique em **Status & Examine**. Depois clique em **Sim** para habilitar o acesso ao público geral.

    ![][7]

Agora você está pronto para usar um logon do Facebook para autenticação em seu aplicativo, fornecendo os valores de ID do Aplicativo e Segredo do Aplicativo para os Serviços Móveis.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Serviços Móveis do Azure]: http://azure.microsoft.com/pt-br/services/mobile-services/
  [Introdução à autenticação]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-ios-get-started-users/
  [Introdução aos Serviços Móveis]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-ios-get-started/
  [facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
  [Desenvolvedores do Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
  [0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
  [1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
  [2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
  [3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
  [4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
  [5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png
  [6]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-3.png
  [7]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-general-public.png
