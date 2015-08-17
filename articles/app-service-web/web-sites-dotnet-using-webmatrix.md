<properties 
	pageTitle="Desenvolver e implantar um aplicativo Web com o Microsoft WebMatrix" 
	description="Aprenda a desenvolver e a implantar um aplicativo web ASP.NET para Aplicativos Web do Serviço de Aplicativo do Azure com o Microsoft WebMatrix." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="tomfitz"/>


# Desenvolver e implantar um aplicativo Web com o Microsoft WebMatrix

## Visão geral

Este guia descreve como utilizar o Microsoft WebMatrix para criar e implantar um site .NET para os Aplicativos Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Você usará um aplicativo de exemplo de um modelo de site do WebMatrix.

Você aprenderá a:

* Como se inscrever no Azure do WebMatrix
* Como criar um site usando um modelo com o WebMatrix 
* Como implantar o site personalizado diretamente do WebMatrix para o Azure.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Entrar no Azure

1. Inicie o WebMatrix.
2. Se essa for a primeira vez em que tive usado o WebMatrix 3, você receberá uma solicitação iniciar sessão no Azure. Caso contrário, você pode clicar na **Sign In** botão e escolha **adicionar conta** Optar por **entrar** usando sua conta da Microsoft.

	![Adicionar conta][addaccount]

3. Se você se inscrever para uma conta do Azure, você pode efetuar login usando sua conta da Microsoft:

	![Entrar no Azure][signin]


## Criar um site usando um modelo criado no Azure

1. Na tela inicial, clique o **novo** botão e escolha **Galeria de modelos de** para criar um novo site da Galeria de modelos:

	![Novo site da Galeria de modelos][sitefromtemplate]

2. A Galeria de modelos mostrará uma lista de modelos disponíveis que podem ser executados localmente ou no Azure. Selecione **confeitaria** da lista de modelos, digite **bakerysample** para o **nome do Site**e clique em **próximo**

	![Criar site a partir de modelo][sitefromtemplatedetails]

3. Se você tiver entrado no Azure, agora terá a opção de criar uma instância dos Aplicativos Web do Serviço de Aplicativo do Azure para seu site local. Escolha um nome exclusivo e selecione o data center onde você deseja que sua instância de Aplicativos Web seja criada:

	![Criar um site no Azure][sitefromtemplateazure]

	Após o WebMatrix terminar de criar o site local e a instância dos Aplicativos Web no Azure, o WebMatrix IDE é exibido.

	![O WebMatrix IDE][howtowebmatrixide]

## Configurar email

O exemplo de confeitaria inclui um formulário de pedido simulado que envia uma mensagem de email com o item solicitado. Você usará o serviço de email SendGrid no Azure para enviar emails do seu site.

1. Siga as etapas no tutorial [Como enviar emails utilizando o Sendgrid com o Azure][sendgridexample] para configurar uma conta SendGrid e recuperar as informações de conexão. Você não precisa ler todo o tutorial - apenas até a parte de informações de conexão.

2. Adicione o pacote NuGet do SendGrid ao seu projeto do WebMatrix. Primeiro, clique no botão do NuGet.

    ![Adicionar SendGrid][addsendgrid]

    Pesquise SendGrid e instale-o.

    ![Instalar SendGrid][installsendgrid]

    Após a conclusão da instalação do pacote, observe que os assemblies de SendGrid foram adicionados ao compartimento.

    ![SendGrid adicionado][binsendgrid]

3. Abrir o *Order.cshtml* página clicando duas vezes no nome do arquivo.

	![][modify2]

4. Na parte superior do arquivo, adicione o seguinte código:

        @using SendGrid;
        @using System.Net.Mail;

4. Localize o comentário que diz //SMTP configuração do Hotmail e exclua ou comente todo o código para o uso do WebMail.

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


5. Adicione o código para usar o SendGrid para enviar emails em vez do WebMail. Adicione o seguinte código no lugar do código que você excluiu na etapa anterior. Adicione seu nome de usuário do SendGrid e sua senha ao criar a NetworkCredential.

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
            var credentials = new NetworkCredential("[your user name]", "[your password]");

            // Create an Web transport for sending email.
            var transportWeb = new Web(credentials);

            // Send the email.
            try {
                transportWeb.DeliverAsync(myMessage);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }


6. Na faixa de opções do WebMatrix clique **executar** para testar o site.

	![][modify4]

7. Clique em **pedido agora** em um dos produtos e enviar um pedido para si mesmo.

8. Verifique o seu e-mail e verificar se que você conseguiu a confirmação do pedido. Se você tiver dificuldades para enviar e-mail, consulte [problemas com o envio de E-mail][sendmailissues] no guia de solução de problemas de páginas de Web do ASP.NET (Razor).
 

## Implantar o site personalizado do WebMatrix para o Azure.

1. No WebMatrix, clique em **Publicar** da faixa de opções **Início** para exibir a caixa de diálogo **Publicar visualização** para o site.

	![Visualizar Publicação do WebMatrix][howtopublishpreview]

2. Clique para selecionar a caixa de seleção ao lado de bakery.sdf e, em seguida, clique em **Continuar**. Quando a publicação é concluída, a URL para o aplicativo Web atualizado no Serviço de Aplicativo do Azure é exibida na parte inferior do IDE WebMatrix.

	![Publicação concluída][publishcomplete]

3. Clique no link para abrir o site (uma instância dos Aplicativos Web no Azure) no seu navegador:

	![Site de exemplo de confeitaria][bakerysample]

	A URL para a instância dos Aplicativos Web também pode ser encontrada no [Portal do Azure](https://portal.azure.com) clicando em **Procurar** > **Aplicativos Web** para exibir todas as instâncias de Aplicativos Web de sua assinatura e, em seguida, escolha um aplicativo Web. A URL para o aplicativo Web é exibida na folha do aplicativo Web.

## Modificar o site e publicá-lo novamente nos Aplicativos Web

Você pode usar o WebMatrix para modificar o site e publicá-lo novamente na instância dos Aplicativos Web. O procedimento a seguir, você adicionará uma caixa de seleção para indicar que a ordem é um vale-presente.

1. Abrir o *Order.cshtml* página.

2. Localize a definição de formulário de classe "shiping". Insira o código a seguir logo após o bloco &lt;li&gt;.
		
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

8. Clique no link para abrir o site no navegador e testar a atualização em sua instância dos Aplicativos Web.

## Próximas etapas

* [Site do WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, confira: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)




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
 

<!---HONumber=August15_HO6-->