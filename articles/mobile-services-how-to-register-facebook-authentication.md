<properties linkid="develop-mobile-how-to-guides-register-for-facebook-authentication" urlDisplayName="Registre-se para a autenticação do Facebook" pageTitle="Registre-se para a autenticação do Facebook - Serviços Móveis" metaKeywords="Facebook no Azure, Facebook do Azure, Serviços Móveis de autenticação do Azure" description="Saiba como usar a autenticação do Facebook em seu aplicativo de Serviços Móveis do Azure" metaCanonical="" services="" documentationCenter="" title="Registre seus aplicativos para autenticação do Facebook com os Serviços Móveis" authors=""  solutions="" writer="glenga" manager="" editor=""  />

# Registrar seus aplicativos para a autenticação do Facebook com os Serviços Móveis

Este tópico mostra como registrar seus aplicativos para poder usar o Facebook para autenticar com os Serviços Móveis do Azure. 

<div class="dev-callout"><b>Observação</b>
<p>Para concluir o procedimento neste tópico, você deve ter uma conta do Facebook com um endereço de email verificado e um número de telefone celular. Para criar uma nova conta do Facebook, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a>.</p>
</div> 

1. Navegue até o site de <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">Desenvolvedores do Facebook</a> e entre com suas credenciais de conta do Facebook.

2. (Opcional) Se você ainda não foi registrado, clique em **Aplicativos** e em **Registrar como um desenvolvedor**, aceite a política e siga as etapas de registro. 

   	![][0]

3. Clique em **Aplicativos** e clique em **Criar um Novo Aplicativo**.

   	![][1]

4. Escolha um nome exclusivo para seu aplicativo, selecione **Aplicativos para Páginas**, clique em **Criar Aplicativo** e conclua a pergunta do desafio.

   	![][2]

	Isso registra o aplicativo com o Facebook 

5. Clique em **Configurações**, digite o domínio do seu serviço móvel em **Domínios de Aplicativo**, clique em **Adicionar Plataforma** e selecione **Site**.

   	![][3]

6. Digite a URL do seu serviço móvel em **URL do Site** e clique em **Salvar Alterações**.

	![][4]

7. Clique em **Mostrar**, forneça sua senha se solicitado e anote os valores de **ID do Aplicativo** e **Segredo do Aplicativo**. 

   	![][5]

    <div class="dev-callout"><b>Observação de Segurança</b>
	<p>O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.</p>
    </div>

Agora você está pronto para usar um logon do Facebook para autenticação em seu aplicativo, fornecendo os valores de ID do Aplicativo e Segredo do Aplicativo para os Serviços Móveis.  

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
[1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
[2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Desenvolvedores do Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

