<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Registrar-se para a autenticação do Twitter" pageTitle="Registrar-se para a autenticação do Twitter - Serviços Móveis" metaKeywords="aplicativo de registro do Azure, autenticação do Twitter no Azure, autenticação de aplicativo, autenticar serviços móveis, Twitter com Serviços Móveis" description="Saiba como usar a autenticação do Twitter com seu aplicativo de Serviços Móveis do Azure." metaCanonical="" services="" documentationCenter="" title="Registrar seus aplicativos para logon do Twitter com os Serviços Móveis" authors="" solutions="" manager="" editor="" />

#Registrar seus aplicativos para logon do Twitter com os Serviços Móveis

Este tópico mostra a você como registrar seus aplicativos para poder usar o Twitter para se autenticar com os Serviços Móveis do Azure.

<div class="dev-callout"><b>Observação</b>
<p>Para concluir o procedimento deste tópico, você deve ter uma conta do Twitter com um endereço de email verificado. Para criar uma nova conta do Twitter, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.</p>
</div> 

1. Navegue até o site <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Desenvolvedores do Twitter</a>, entre com suas credenciais de conta do Twitter e clique em **Criar um novo aplicativo**.

   	![][1]

2. Digite os valores de **Nome**, **Descrição** e **Site** para o seu aplicativo e digite a URL do serviço móvel em **URL de Retorno de Chamada**.

   	![][2]

    
	>[WACOM.NOTE]Para um serviço móvel de back-end do .NET publicado no Azure usando o Visual Studio, a URL de redirecionamento é a URL do seu serviço móvel acrescentada ao caminho _signin-google_ e o seu serviço móvel é um serviço .NET, por exemplo, <code>https://todolist.azure-mobile.net/signin-twitter</code>. <br />O valor do <strong>Site</strong> é necessário, mas não é usado.

3.  Na parte inferior da página, leia e aceite os termos, digite as palavras corretas de CAPTCHA e, em seguida, clique em **Criar seu aplicativo Twitter**. 

   	![][3]

   	Isso registrará o aplicativo e exibirá os detalhes do aplicativo.

6. Anote os valores de **Chave do consumidor** e **Segredo do consumidor**. 

   	![][4]

    <div class="dev-callout"><b>Observação de segurança</b>
	<p>O segredo do consumidor é uma credencial de segurança importante. Não o compartilhe com ninguém nem o distribua com seu aplicativo.</p>
    </div>

7. Clique na guia **Configurações**, role para baixo, marque **Permitir que este aplicativo seja usado para entrar com o Twitter** e, em seguida, clique em **Atualizar as configurações deste aplicativo Twitter**.

	![][5]

Agora você está pronto para usar um logon do Twitter para autenticação no seu aplicativo, fornecendo os valores de chave do consumidor e de segredo do consumidor para os Serviços Móveis.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png

<!-- URLs. -->

[Desenvolvedores do Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

