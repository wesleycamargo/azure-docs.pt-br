<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Registrar-se para a autenticação do Google" pageTitle="Registrar-se para a autenticação do Google - Serviços Móveis" metaKeywords="aplicativo de registro do Azure, autenticação do Azure, autenticação de aplicativo do Google, autenticar serviços móveis" description="Saiba como registrar seus aplicativos para usar o Google para se autenticar com os Serviços Móveis do Azure." metaCanonical="" services="" documentationCenter="" title="Registrar seus aplicativos para logon do Google com os Serviços Móveis" authors="" solutions="" manager="" editor="" />

# Registrar seus aplicativos para logon no Google com os Serviços Móveis

Este tópico mostra a você como registrar seus aplicativos para poder usar o Google para se autenticar com os Serviços Móveis do Azure.

<div class="dev-callout"><b>Observação</b>
<p>Para concluir o procedimento neste tópico, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div> 

1. Vá até o site <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Apis do Google</a>, faça logon com suas credenciais de conta do Google e clique em **Criar projeto...**.

   	![][1]   

2. Clique em **Acesso à API** e, em seguida, clique em **Criar uma ID de cliente OAuth 2.0...**.

   	![][2]

3. Em **Informações de Identificação Visual**, digite o seu **Nome do produto** e, em seguida, clique em **Avançar**.  

   	![][3]

4. Em **Configurações de ID de Cliente**, selecione **Aplicativo Web**, digite a URL do seu serviço móvel em **Seu site ou nome de host**, clique em **mais opções**, substitua a URL gerada em **URIs de Redirecionamento Autorizados** pela URL do seu serviço móvel acrescentado ao caminho _/login/google_ e, em seguida, clique em **Criar ID do cliente**.

   	![][4]

	>[WACOM.NOTE]Para um serviço móvel de back-end do .NET publicado no Azure usando o Visual Studio, a URL de redirecionamento é a URL do seu serviço móvel acrescentada ao caminho __Hlk384692799signin-google__Hlk384692799 e o seu serviço móvel é um serviço .NET, por exemplo, <code>https://todolist.azure-mobile.net/signin-google</code>.

6. Em **ID do Cliente para aplicativos web**, anote os valores da **ID do Cliente** e do **Segredo do cliente**. 

   	![][5]

    <div class="dev-callout"><b>Observação de segurança</b>
	<p>O segredo do cliente é uma credencial de segurança importante. Não o compartilhe com ninguém nem o distribua com seu aplicativo.</p>
    </div>

Agora você está pronto para usar um logon do Google para autenticação no seu aplicativo, fornecendo os valores de ID do cliente e de segredo do cliente para os Serviços Móveis.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-developers.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Apis do Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

