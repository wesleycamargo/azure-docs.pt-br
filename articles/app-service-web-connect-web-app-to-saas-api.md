<properties 
   pageTitle="Conectar aplicativos Web e móveis a APIs SaaS (O365/SharePoint/Salesforce) no Serviço de Aplicativo do Azure" 
   description="Este tutorial mostra como consumir um aplicativo de API SaaS de um ASP.NET hospedado em aplicativos Web do Serviço de Aplicativo do Azure." 
   services="app-service\web" 
   documentationCenter="" 
   authors="syntaxc4" 
   manager="yochayk" 
   editor=""/>

<tags
   ms.service="app-service-web"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="web" 
   ms.date="03/24/2015"
   ms.author="cfowler"/>

# Conectar aplicativos Web e móveis a APIs SaaS (O365/SharePoint/Salesforce) no Serviço de Aplicativo do Azure

Este tutorial mostra como consumir um Aplicativo de API de um ASP.NET hospedado em [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714).

## Visão geral

Você aprenderá:

- Como consumir um aplicativo de API do ASP.NET hospedado em Aplicativos Web.

## Pré-requisitos

Este tutorial se baseia na série de tutoriais de Aplicativos de API:

1. [Criar um Aplicativo de API do Azure](app-service-dotnet-create-api-app)
2. [Publicar um Aplicativo de API do Azure](app-service-dotnet-publish-api-app)
3. [Implantar um Aplicativo de API do Azure](app-service-dotnet-deploy-api-app)
4. [Depurar um Aplicativo de API do Azure](app-service-dotnet-remotely-debug-api-app)

## Tornar o aplicativo de API publicamente acessível

No [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715), selecione o Aplicativo de API. Clique no botão **Configurações** na barra de comandos. Na folha **Configurações Básicas**, altere o **Nível de Acesso** para **Público (anônimo)**.

![](./media/app-service-web-connect-web-app-to-saas-api/4-5-Change-Access-Level-To-Public.png)

## Criar um Aplicativo MVC ASP.NET no Visual Studio

1. Abra o Visual Studio. Use a caixa de diálogo **Novo Projeto** para adicionar um novo **Aplicativo Web ASP.NET**. Clique em **OK**.

	![File > New > Web > ASP.NET Web Application](./media/app-service-web-connect-web-app-to-saas-api/1-Create-New-MVC-App-For-Consumption.png)

1. Selecione o modelo **MVC**. Clique em **Alterar Autenticação**, selecione **Sem Autenticação** e, em seguida, clique em **OK** duas vezes.

	![New ASP.NET Application](./media/app-service-web-connect-web-app-to-saas-api/2-Change-Auth-To-No-Auth.png)

1. No Gerenciador de Soluções, clique com botão direito do mouse no projeto de Aplicativo Web recém-criado e selecione **Adicionar Referência de Aplicativo do Azure**.

	![Add Azure API App Reference...](./media/app-service-web-connect-web-app-to-saas-api/3-Add-Azure-API-App-SDK.png)

1. Na lista suspensa **Aplicativos de API existentes**, selecione o Aplicativo de API ao qual você deseja se conectar.

	![Select Existing API App](./media/app-service-web-connect-web-app-to-saas-api/4-Add-Azure-API-App-SDK-Dialog.png)

	>[AZURE.NOTE] O código do cliente para se conectar ao aplicativo de API será gerado automaticamente de um ponto de extremidade de API Swagger.

1. Para aproveitar o código de API gerado, abra o arquivo HomeController.cs e substitua a ação `Contact` pelo seguinte:

	    public async Task<ActionResult> Contact()
	    {
	        ViewBag.Message = "Your contact page.";
	
	        var contacts = new ContactsList();
	        var response = await contacts.Contacts.GetAsync();
	        var contactList = response.Body;
	
	        return View(contactList);
	    }

	![HomeController.cs Code Updates](./media/app-service-web-connect-web-app-to-saas-api/5-Write-Code-Which-Leverages-Swagger-Generated-Code.png)

1. Atualize a exibição `Contact` para refletir a lista dinâmica de contatos com o seguinte código:  
	<pre>// Add to the very top of the view file
	@model IList&lt;MyContactsList.Web.Models.Contact&gt;
	
	// Replace the default email addresses with the following
    &lt;h3&gt;Public Contacts&lt;/h3&gt;
    &lt;ul&gt;
        @foreach (var contact in Model)
        {
            &lt;li&gt;&lt;a href=&quot;mailto:@contact.EmailAddress&quot;&gt;@contact.Name &amp;lt;@contact.EmailAddress&amp;gt;&lt;/a&gt;&lt;/li&gt;
        }
    &lt;/ul&gt; 
	</pre>

	![Contact.cshtml Code Updates](./media/app-service-web-connect-web-app-to-saas-api/6-Update-View-To-Reflect-Changes.png)

## Implantar o aplicativo Web para Aplicativos Web no Serviço de Aplicativo

Siga as instruções disponíveis em [Como Implantar um Aplicativo Web do Azure](web-sites-deploy.md).

>[AZURE.NOTE] Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever para obter uma conta do Azure, vá para [Experimentar Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você poderá criar imediatamente um aplicativo Web starter de curta duração no Serviço de Aplicativo. Não é exigido um cartão de crédito; sem compromisso.

## O que mudou
* Para obter um guia para a alteração de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e Seu Impacto sobre os Serviços Existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a alteração do portal antigo para o novo portal, consulte: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->