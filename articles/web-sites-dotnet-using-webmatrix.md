<properties 
	pageTitle="Site do .NET com o WebMatrix - tutoriais do Azure" 
	description="Aprenda a desenvolver e implantar um site do Azure com o WebMatrix." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/27/2014" 
	ms.author="tomfitz"/>





#Criar e implantar um site com o Microsoft WebMatrix
Este guia descreve como usar o Microsoft WebMatrix para criar e implantar um site da web para o Azure.  Você usará um aplicativo de exemplo de um modelo de site do WebMatrix.

Você aprenderá:

* Como se inscrever no Azure do WebMatrix
* Como criar um site usando um modelo com o WebMatrix 
* Como implantar o site personalizado diretamente do WebMatrix para o Azure.

> [AZURE.NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Você pode <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">ativar os benefícios de assinante do MSDN</a> ou <a href="http://azure.microsoft.com/pricing/free-trial/">se inscrever para fazer uma avaliação gratuita</a>.
> Se você quiser iniciar com Websites do Azure antes de assinar uma conta, acesse <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, em que é possível criar imediatamente um site de iniciante ASP.NET de vida curta nos Websites do Azure gratuitamente. Nenhum cartão de crédito é exigido, sem compromissos.

## Entrar no Azure

1. Inicie o WebMatrix.
2. Se essa for a primeira vez em que tive usado o WebMatrix 3, você receberá uma solicitação iniciar sessão no Azure.  Caso contrário, você pode clicar na **Entrar** botão e escolha **Adicionar Conta**.  Optar por **Entrar** usando sua conta da Microsoft.

	![Add Account][addaccount]

3. Se você se inscrever para uma conta do Azure, você pode efetuar login usando sua conta da Microsoft:

	![Sign into Azure][signin]	


## Criar um site usando um modelo criado no Azure

1. Na tela inicial, clique no botão **novo** e escolha **Galeria de modelos** para criar um novo site a partir da Galeria de Modelos:

	![New site from Template Gallery][sitefromtemplate]

2. A Galeria de modelos mostrará uma lista de modelos disponíveis que podem ser executados localmente ou no Azure.  Selecione **confeitaria** da lista de modelos, digite **bakerysample** para o **nome do Site** e clique em **próximo**.

	![Create Site from Template][sitefromtemplatedetails]

3. Se você tiver entrado no Azure, agora você tem a opção de criar um Site do Azure para seu site local.  Escolha um nome exclusivo e selecione o data center onde quer que seu site seja criado: 

	![Create site on Azure][sitefromtemplateazure]

	Após o WebMatrix termina de criar o site, o WebMatrix IDE é exibido:

	![WebMatrix IDE][howtowebmatrixide] 

## Configurar o email

O exemplo de confeitaria inclui um formulário de pedido simulado que envia uma mensagem de email com o item solicitado. Você usará o serviço de email SendGrid no Azure para enviar emails do seu site.

1. Siga as etapas no tutorial [Como enviar emails usando o SendGrid com o Azure][sendgridexample] para configurar uma conta do SendGrid e recuperar as informações de conexão. Você não precisa ler todo o tutorial - apenas até a parte de informações de conexão.

2. Adicione o pacote NuGet do SendGrid ao seu projeto do WebMatrix. Primeiro, clique no botão do NuGet.

    ![Add SendGrid][addsendgrid]

    Procure pelo SendGrid e instale-o.

    ![Install SendGrid][installsendgrid]

    Após a instalação do pacote, observe se os assemblies do SendGrid foram adicionados ao compartimento.

    ![SendGrid added][binsendgrid]

3. Abra a página *Order.cshtml* clicando duas vezes no nome do arquivo.

	![][modify2]

4. Na parte superior do arquivo, adicione o seguinte código:

        @using SendGrid;
        @using System.Net.Mail;	

4. Localize o comentário que diz //SMTP Configuration for Hotmail e exclua ou comente todos os códigos para o uso do WebMail.

        /*
        //SMTP Configuration for Hotmail
        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort = 25;
        WebMail.EnableSsl = true;

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

        if (WebMail.UserName.IsEmpty() || WebMail.Password.IsEmpty() || WebMail.From.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        } 
        else {
            try {
                WebMail.Send(to: customerEmail, subject: "Fourth Coffee - New Order", body: body);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }*/


5. Adicione o código para usar o SendGrid para enviar emails em vez do WebMail. Adicione o seguinte código no lugar do código que você excluiu na etapa anterior.

		 if (email.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        }
        else {
            // Create the email object first, then add the properties.
            SendGridMessage myMessage = new SendGridMessage();
            myMessage.AddTo(email);
            myMessage.From = new MailAddress("FourthCoffee@example.com", "Fourth Coffee");
            myMessage.Subject = "Fourth Coffee - New Order";
            myMessage.Text = body;

            // Create credentials, specifying your user name and password.
            var credentials = new NetworkCredential("[your user name", "[your password]");

            // Create an Web transport for sending email.
            var transportWeb = new Web(credentials);

            // Send the email.
            try {
                transportWeb.Deliver(myMessage);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }


6. Na faixa de opções do WebMatrix clique em **executar** para testar o site.

	![][modify4]

7. Clique em **Pedir agora** em um dos produtos e envie um pedido para si mesmo.

8. Verifique o seu e-mail e verificar se que você conseguiu a confirmação do pedido. Se você tiver dificuldades para enviar e-mail, consulte [problemas com o envio de E-mail][sendmailissues] no guia de solução de problemas de páginas de Web do ASP.NET (Razor).
 

## Implantar o site personalizado do WebMatrix para o Azure.

1. No WebMatrix, clique em **Publicar** da faixa de opções **Início** para exibir a caixa de diálogo **Publicar visualização** para o site.

	![WebMatrix Publish Preview][howtopublishpreview]

2. Clique para selecionar a caixa de seleção ao lado de bakery.sdf e, em seguida, clique em **Continuar**.  Quando a publicação for concluída a URL para o site atualizado no Azure é exibida na parte inferior do IDE WebMatrix.  

	![Publishing Complete][publishcomplete]

3. Clique no link para abrir o site no seu navegador:

	![Bakery Sample Site][bakerysample]

	A URL do site da web também pode ser encontrada no portal do Azure clicando em **Sites da Web** para exibir todos os sites da web para sua assinatura. A URL de cada site da web é exibida na coluna URL na página sites da web.

## Modificar o site e publicá-lo novamente no site do Azure

Você pode usar o WebMatrix para modificar o site e publicá-lo novamente ao web site do Azure. O procedimento a seguir, você adicionará uma caixa de seleção para indicar que a ordem é um vale-presente.

1. Abra a página *Order.cshtml*.

2. Localize a definição de formulário de classe "shiping". Insira o código a seguir logo após o bloco &lt;li&gt;.
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. Localize a linha "var shipping = Request["orderShipping"];"no arquivo e insira a seguinte linha de código logo a seguir.

		var gift = Request["isGift"];

4. Logo após o bloco de código que valida que o endereço de remessa não está vazio no trecho de código a seguir.

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	O seu arquivo *order.cshtml* deve ser semelhante à imagem a seguir.

	![][modify6]

5. Salve o arquivo e executar o site localmente e enviar uma ordem de teste. Certifique-se de testar a nova caixa de seleção.

	![][modify7]

6. Republique o site clicando em **Publicar** sobre a faixa de opções **Início**.

7. Na caixa de diálogo **Visualização de Publicação**, verifique se os dois arquivos Order.cshtml estão marcados e clique em continuar.

8. Clique no link para abrir o site no navegador e testar a atualização em seu site do Azure.

# Próximas etapas

Você viu como criar e implantar um site por meio do WebMatrix para o Azure. Para saber mais sobre o WebMatrix, veja estes recursos:

* [WebMatrix para Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Site do WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[howtowebmatrixide]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide2.png
[howtopublishpreview]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
[addsendgrid]: ./media/web-sites-dotnet-using-webmatrix/addsendgridpackage.png
[installsendgrid]: ./media/web-sites-dotnet-using-webmatrix/installsendgrid.png
[binsendgrid]: ./media/web-sites-dotnet-using-webmatrix/sendgridbin.png

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
[sendgridexample]: http://azure.microsoft.com/documentation/articles/sendgrid-dotnet-how-to-send-email/



<!--HONumber=42-->
