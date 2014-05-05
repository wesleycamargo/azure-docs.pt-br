<properties linkid="develop-dotnet-website-with-webmatrix" urlDisplayName="Site da Web com o WebMatrix" pageTitle="site do .NET com o WebMatrix - tutoriais Azure" metaKeywords="Azure do WebMatrix, o WebMatrix Azure, Azure site do WebMatrix, site Azure WebMatrix, Web Matrix Azure, WebMatrix Azure" description="aprender a desenvolver e implantar um site da web Azure com o WebMatrix." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Desenvolver e implantar um site da web com o Microsoft WebMatrix" authors="" solutions="" manager="" editor="" />





#Criar e implantar um site com o Microsoft WebMatrix
Este guia descreve como usar o Microsoft WebMatrix para criar e implantar um site da web para o Azure.  Você usará um aplicativo de exemplo de um modelo de site do WebMatrix.

Você irá aprender:

* Como se inscrever no Azure do WebMatrix
* Como criar um site usando um modelo com o WebMatrix 
* Como implantar o site personalizado diretamente do WebMatrix para o Azure.


[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Entrar no Azure

1. Inicie o WebMatrix.
2. Se essa for a primeira vez em que tive usado o WebMatrix 3, você receberá uma solicitação iniciar sessão no Azure.  Caso contrário, você pode clicar na **Sign In** botão e escolha **adicionar conta**  Optar por **entrar** usando sua conta da Microsoft.

	![Adicionar conta][addaccount]

3. Se você se inscrever para uma conta do Azure, você pode efetuar login usando sua conta da Microsoft:

	![Entrar no Azure][signin]	


## Criar um site usando um modelo criado no Azure

1. Na tela inicial, clique o **novo** botão e escolha **Galeria de modelos de** para criar um novo site da Galeria de modelos:

	![Novo site da Galeria de modelos][sitefromtemplate]

2. A Galeria de modelos mostrará uma lista de modelos disponíveis que podem ser executados localmente ou no Azure.  Selecione **confeitaria** da lista de modelos, digite **bakerysample** para o **nome do Site**e clique em **próximo**

	![Criar site a partir de modelo][sitefromtemplatedetails]

3. Se você tiver entrado no Azure, agora você tem a opção de criar um Site do Azure para seu site local.  Escolha um nome exclusivo e selecione o Center dados onde deseja que o seu site a ser criado: 

	![Criar um site no Azure][sitefromtemplateazure]

	Após o WebMatrix termina de criar o site, o WebMatrix IDE é exibido:

	![O WebMatrix IDE][howtowebmatrixide] 

## Testar o site.

O exemplo de confeitaria inclui um formulário de pedido simulado que envia uma mensagem de email com o item encomendado para uma conta do Windows Live Hotmail que você fornecer.

1. No painel de navegação esquerdo do WebMatrix, expanda o **bakerysample** pasta.

	![][modify1]

2. Abrir o *Order.cshtml* página clicando duas vezes no nome do arquivo.

	![][modify2]

3. Localize o comentário que diz //SMTP configuração do Hotmail.

	![][modify3]

4. Altere os valores nas seguintes linhas para coincidir com seu próprio provedor de email:

		WebMail.SmtpServer = "smtp.live.com";
		WebMail.SmtpPort  = 25;
		WebMail.EnableSsl = true; 

		//Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

	Altere o valor de WebMail.SmtpServer para o nome do servidor de e-mail que você normalmente usa para enviar email. Forneça valores para o nome de usuário e senha. Defina a propriedade From como seu endereço de e-mail.

4. Na faixa de opções do WebMatrix clique **executar** para testar o site.

	![][modify4]

5. Clique em **pedido agora** em um dos produtos e enviar um pedido para si mesmo.

6. Verifique o seu e-mail e verificar se que você conseguiu a confirmação do pedido. Se você tiver dificuldades para enviar e-mail, consulte [problemas com o envio de E-mail][sendmailissues] no guia de solução de problemas de páginas de Web do ASP.NET (Razor).
 

## Implantar o site personalizado do WebMatrix para o Azure.

1. No WebMatrix, clique em **Publicar** da faixa de opções **Início** para exibir a caixa de diálogo **Publicar visualização** para o site.

	![Visualizar Publicação do WebMatrix][howtopublishpreview]

2. Clique para selecionar a caixa de seleção ao lado de bakery.sdf e, em seguida, clique em **continuar**  Quando a publicação for concluída a URL para o site atualizado no Azure é exibida na parte inferior do IDE WebMatrix.  

	![Publicação concluída][publishcomplete]

3. Clique no link para abrir o site no seu navegador:

	![Site de exemplo de confeitaria][bakerysample]

	A URL do site da web também pode ser encontrada no portal do Azure clicando em **Sites da Web** para exibir todos os sites da web para sua assinatura. A URL de cada site da web é exibida na coluna URL na página sites da web.

## Modificar o site e publicá-lo novamente para o site do Azure

Você pode usar o WebMatrix para modificar o site e publicá-lo novamente ao web site do Azure. O procedimento a seguir, você adicionará uma caixa de seleção para indicar que a ordem é um vale-presente.

1. Abrir o *Order.cshtml* página.

2. Localize a definição de formulário de classe "shiping". Inserir o código a seguir logo após o &lt;li&gt; bloco.
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. Localizar "remessa var = solicitação["orderShipping"];" linha no arquivo e insira a seguinte linha de código logo a seguir.

		var gift = Request["isGift"];

4. Logo após o bloco de código que valida que o endereço de remessa não está vazio no trecho de código a seguir.

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	O *order.cshtml* arquivo deve ser semelhante a esta imagem.

	![][modify6]

5. Salve o arquivo e executar o site localmente e enviar uma ordem de teste. Certifique-se de testar a nova caixa de seleção.

	![][modify7]

6. Republicar o site clicando em **publicar** sobre o **Home** faixa de opções.

7. Na caixa de diálogo **Visualização de Publicação**, verifique se os dois arquivos Order.cshtml estão marcados e clique em continuar.

8. Clique no link para abrir o site no navegador e testar a atualização em seu site do Azure.

# Próximas etapas

Você viu como criar e implantar um site a partir do WebMatrix para o Azure. Para saber mais sobre o WebMatrix, verifique estes recursos:

* [WebMatrix para Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Site do WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[howtowebmatrixide]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide.png
[howtopublishpreview]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png

[publishcomplete]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
[bakerysample]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
[addaccount]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
[sitefromtemplatedetails]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
[sitefromtemplateazure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png

[modify1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
[modify2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
[modify3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
[modify4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
[modify5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
[modify6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
[modify7]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png



[sendmailissues]: http://go.microsoft.com/fwlink/?LinkId=253001#email

