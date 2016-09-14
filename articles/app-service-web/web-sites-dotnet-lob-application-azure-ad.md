<properties 
	pageTitle="Criar um aplicativo de linha de negócios do Azure com a autenticação do Azure Active Directory | Microsoft Azure" 
	description="Saiba como criar um aplicativo de linha de negócios ASP.NET MVC no Serviço de Aplicativo do Azure que realiza a autenticação com o Azure Active Directory" 
	services="app-service\web, active-directory" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="08/31/2016" 
	ms.author="cephalin"/>

# Criar um aplicativo de linha de negócios do Azure com a autenticação do Azure Active Directory #

Este artigo mostra como criar um aplicativo de linha de negócios do .NET nos [Aplicativos Web do Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) usando o recurso de [Autenticação/Autorização](../app-service/app-service-authentication-overview.md). Também mostra como usar a [API do Graph do Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) para consultar dados de diretório no aplicativo.

O locatário do Azure Active Directory que você usa pode ser um diretório somente do Azure. Ou, pode ser [sincronizado com o Active Directory local](../active-directory/active-directory-aadconnect.md) para criar uma experiência de logon único para trabalhadores locais e remotos. Este artigo usa o diretório padrão para sua conta do Azure.

<a name="bkmk_build"></a>
## O que você compilará ##

Você compilará um aplicativo simples de linha de negócios CRUD (Create-Read-Update-Delete) nos Aplicativos Web do Serviço de Aplicativo que acompanha itens de trabalho com os seguintes recursos:

- Autentica usuários no Active Directory do Azure
- Consulta usuários de diretório e grupos usando a [API do Graph do Azure Active Directory](http://msdn.microsoft.com/library/azure/hh974476.aspx)

Se você precisar de controle de acesso baseado em função (RBAC) para seu aplicativo de linha de negócios no Azure, confira a [Próxima etapa](#next).

<a name="bkmk_need"></a>
## O que você precisa ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

É necessário o seguinte para concluir este tutorial:

- Um locatário do Active Directory do Azure com usuários em vários grupos
- Permissões para criar aplicativos no locatário do Active Directory do Azure
- Visual Studio 2013 Atualização 4 ou posterior
- [SDK 2.8.1 do Azure ou posterior](https://azure.microsoft.com/downloads/)

<a name="bkmk_deploy"></a>
## Crie e implante um aplicativo Web no Azure ##

1. No Visual Studio, clique em **Arquivo** > **Novo** > **Projeto**.

2. Selecione **Aplicativo Web ASP.NET**, nomeie seu projeto e clique em **OK**.

3. Selecione o modelo **MVC** e altere a autenticação para **Sem Autenticação**. Selecione **Host na Nuvem** e clique em **OK**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/1-create-mvc-no-authentication.png)

4. Na caixa de diálogo **Criar Serviço de Aplicativo**, clique em **Adicionar uma conta** (e, depois, **Adicionar uma conta** na lista suspensa) para fazer logon em sua conta do Azure.

5. Depois de conectado, configure seu aplicativo Web. Crie um grupo de recursos e um novo Plano do Serviço de Aplicativo clicando no respectivo botão **Novo**. Clique em **Explorar serviços adicionais** para continuar.

	![](./media/web-sites-dotnet-lob-application-azure-ad/2-create-app-service.png)

6. Na guia **Serviços**, clique em **+** para adicionar um Banco de Dados SQL para seu aplicativo.

	![](./media/web-sites-dotnet-lob-application-azure-ad/3-add-sql-database.png)

7. Em **Configurar Banco de Dados SQL**, clique em **Novo** para criar uma instância do SQL Server.

8. Em **Configurar SQL Server**, configure sua instância do SQL Server. Em seguida, clique em **OK**, **OK** e em **Criar** para iniciar a criação do aplicativo no Azure.

9. Em **Atividade do Serviço de Aplicativo do Azure**, você pode ver quando a criação do aplicativo terminar. Clique em **Publicar &lt;*appname*> para este Aplicativo Web agora** e depois clique em **Publicar**.

	Após a conclusão do Visual Studio, ele abrirá o aplicativo de publicação no navegador.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-published-shown-in-browser.png)

<a name="bkmk_auth"></a>
## Configurar a autenticação e acesso ao diretório

1. Faça logon no [Portal do Azure](https://portal.azure.com).

2. No menu à esquerda, clique em **Serviços de Aplicativos** > **&lt;*appname*>** > **Autenticação/Autorização**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-app-service-authentication.png)

3. Ative a autenticação do Azure Active Directory clicando em **Ativar** > **Azure Active Directory** > **Express** > **OK**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-authentication-express.png)

4. Clique em **Salvar** na barra de comandos.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-authentication-save.png)

    Após a gravação das configurações de autenticação, tente navegar até seu aplicativo novamente no navegador. As configurações padrão impõem a autenticação em todo o aplicativo. Se você ainda não estiver conectado, será redirecionado para uma tela de logon. Depois de conectado, você verá seu aplicativo protegido por HTTPS. Em seguida, será necessário habilitar o acesso aos dados do diretório.

5. Navegue até o [Portal Clássico](https://manage.windowsazure.com).

6. No menu à esquerda, clique em **Active Directory** > **Diretório Padrão** > **Aplicativos** > **&lt;*appname*>**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-find-aad-application.png)

	Este é o aplicativo do Azure Active Directory criado pelo Serviço de Aplicativo para você a fim de habilitar o recurso de Autorização/Autenticação.

7. Clique em **Usuários** e **Grupos** para certificar-se de que você tenha alguns usuários e grupos no diretório. Caso contrário, crie alguns usuários e grupos de teste.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-users-groups.png)

7. Clique em **Configurar** para configurar esse aplicativo.

8. Role para baixo até a seção **Chaves** e adicione uma chave selecionando uma duração. Em seguida, clique em **Permissões Delegadas** e selecione **Ler dados do diretório**. Clique em **Salvar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-configure-aad-application.png)

8. Após a gravação das configurações, role para cima até a seção **Chaves** e clique no botão **Cópia** para copiar a chave de cliente.

	![](./media/web-sites-dotnet-lob-application-azure-ad/11-get-app-key.png)

	>[AZURE.IMPORTANT] Se você sair desta página agora, não poderá acessar essa chave de cliente novamente.

9. Em seguida, você precisará configurar seu aplicativo Web com essa chave. Faça logon no [Gerenciador de Recursos do Azure](https://resources.azure.com) com sua conta do Azure.

10. Na parte superior da página, clique em **Leitura/Gravação** para fazer alterações no Gerenciador de Recursos do Azure.

	![](./media/web-sites-dotnet-lob-application-azure-ad/12-resource-manager-writable.png)

11. Encontre as configurações de autenticação de seu aplicativo, localizadas em assinaturas > **&lt;*subscriptionname*>** > **resourceGroups** > **&lt;*resourcegroupname*>** > **provedores** > **Microsoft.Web** > **sites** > **&lt;*appname*>** > **config** > **authsettings**.

12. Clique em **Editar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/13-edit-authsettings.png)

13. No painel de edição, defina as propriedades `clientSecret` e `additionalLoginParams` da seguinte maneira.

		...
		"clientSecret": "<client key from the Azure Active Directory application>",
		...
		"additionalLoginParams": ["response_type=code id_token", "resource=https://graph.windows.net"],
		...

14. Clique em **Inserir** na parte superior para enviar suas alterações.

	![](./media/web-sites-dotnet-lob-application-azure-ad/14-edit-parameters.png)

14. Agora, para testar se você tem o token de autorização para acessar a API do Graph do Azure Active Directory, altere ~\\Controllers\\HomeController.cs para usar o seguinte método de ação `Index()`:
	<pre class="prettyprint">
	public ActionResult Index()
	{
		return <mark>Content(Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);</mark>
	}
	</pre>

15. Publique suas mudanças clicando com o botão direito do mouse em seu projeto e clicando em **Publicar**. Clique em **Publicar** novamente na caixa de diálogo.

	![](./media/web-sites-dotnet-lob-application-azure-ad/15-publish-token-code.png)

	Se a página inicial do aplicativo mostrar agora um token de acesso, seu aplicativo poderá acessar a API do Graph do Azure Active Directory. Fique à vontade para desfazer as alterações em ~\\Controllers\\HomeController.cs.

Em seguida, você fará algo útil com os dados do diretório.

<a name="bkmk_crud"></a>
## Adicionar uma funcionalidade de linha de negócios ao seu aplicativo

Agora, crie um rastreador de itens de trabalho CRUD simples.

5.	Na pasta ~\\Modelos, crie um arquivo de classe chamado WorkItem.cs e substitua `public class WorkItem {...}`pelo código a seguir:

		using System.ComponentModel.DataAnnotations;

		public class WorkItem
		{
			[Key]
			public int ItemID { get; set; }
			public string AssignedToID { get; set; }
			public string AssignedToName { get; set; }
			public string Description { get; set; }
			public WorkItemStatus Status { get; set; }
		}

		public enum WorkItemStatus
		{
			Open,
			Investigating,
			Resolved,
			Closed
		}

7.	Compile o projeto para disponibilizar o novo modelo para a lógica de scaffolding no Visual Studio.

8.	Adicione um novo item com scaffold `WorkItemsController` à pasta ~\\Controllers (clique com o botão direito do mouse em **Controladores**, aponte para **Adicionar** e selecione **Novo item com scaffold**).

9.	Selecione **Controlador MVC 5 com modos de exibição, usando o Entity Framework** e clique em **Adicionar**.

10.	Selecione o modelo que você criou e clique em **+** e depois em **Adicionar** para adicionar um contexto de dados e, em seguida, clique em **Adicionar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/16-add-scaffolded-controller.png)

9.	Abra ~\\Controllers\\WorkItemsController.cs.

13.	No início dos métodos `Create()` e `Edit(int? id)`, adicione o seguinte código para tornar algumas variáveis disponíveis posteriormente para o JavaScript. `Ctrl`+`.` em cada erro de resolução de nomes para corrigi-lo.

		ViewData["token"] = Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"];
		ViewData["tenant"] =
			ClaimsPrincipal.Current.Claims
			.Where(c => c.Type == "http://schemas.microsoft.com/identity/claims/tenantid")
			.Select(c => c.Value).SingleOrDefault();

	> [AZURE.NOTE] Você talvez tenha observado a decoração <code>[ValidateAntiForgeryToken]</code> em algumas das ações. Devido ao comportamento descrito por [Brock Allen](https://twitter.com/BrockLAllen) em [MVC 4, AntiForgeryToken e declarações](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/), sua POSTAGEM HTTP poderá falhar na validação de token antifalsificação em virtude do seguinte:

	> - O Azure Active Directory não envia o http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, que é exigido por padrão pelo token antifalsificação.
	> - Se o Azure Active Directory for um diretório sincronizado com o AD FS, a relação de confiança do AD FS, por padrão, também não enviará a declaração http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, embora seja possível configurar o AD FS manualmente para enviar essa declaração.

	> Você cuidará desse problema na próxima etapa.

12.  Em ~\\Global.asax, adicione a seguinte linha de código ao método `Application_Start()`. `Ctrl`+`.` em cada erro de resolução de nomes para corrigi-lo.

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	`ClaimTypes.NameIdentifies` especifica a declaração `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, que o Active Directory do Azure fornece.

14.	Em ~\\Views\\WorkItems\\Create.cshtml (um item automaticamente submetido a scaffolding), localize o método auxiliar `Html.BeginForm` e faça as seguintes alterações destacadas:
	<pre class="prettyprint">
	@model WebApplication1.Models.WorkItem
	
	@{
		ViewBag.Title = "Create";
	}
	
	&lt;h2>Create&lt;/h2>
	
	@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>)) 
	{
		@Html.AntiForgeryToken()
	
		&lt;div class="form-horizontal">
			&lt;h4>WorkItem&lt;/h4>
			&lt;hr />
			@Html.ValidationSummary(true, "", new { @class = "text-danger" })
			&lt;div class="form-group">
				@Html.LabelFor(model => model.AssignedToID, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type = "hidden"</mark> } })
					@Html.ValidationMessageFor(model => model.AssignedToID, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				@Html.LabelFor(model => model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
					@Html.ValidationMessageFor(model => model.AssignedToName, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				@Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
					@Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				@Html.LabelFor(model => model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EnumDropDownListFor(model => model.Status, htmlAttributes: new { @class = "form-control" })
					@Html.ValidationMessageFor(model => model.Status, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				&lt;div class="col-md-offset-2 col-md-10">
					&lt;input type="submit" value="Create" class="btn btn-default"<mark> id="submit-button"</mark> />
				&lt;/div>
			&lt;/div>
		&lt;/div>
	}
	
	&lt;div>
		@Html.ActionLink("Back to List", "Index")
	&lt;/div>
	
	@section Scripts {
		@Scripts.Render("~/bundles/jqueryval")
		<mark>&lt;script>
			// People/Group Picker Code
			var maxResultsPerPage = 14;
			var input = document.getElementById("AssignedToName");
	
			var token = "@ViewData["token"]";
			var tenant = "@ViewData["tenant"]";
	
			var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
	
			// Submit the selected user/group to be asssigned.
			$("#submit-button").click({ picker: picker }, function () {
				if (!picker.Selected())
					return;
				$("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
			});
		&lt;/script></mark>
	}
	</pre>
	
	Observe que `token` e `tenant` são usados pelo objeto `AadPicker` para fazer chamadas da API do Graph do Azure Active Directory. Você adicionará `AadPicker` mais tarde.

15. Faça as mesmas alterações com ~\\Views\\WorkItems\\Edit.cshtml.

15. O objeto `AadPicker` é definido em um script ao qual você precisa adicionar ao seu projeto. Clique com o botão direito do mouse na pasta ~\\Scripts, aponte para **Adicionar** e clique em **arquivo JavaScript**. Digite `AadPickerLibrary` para o nome de arquivo e clique em **OK**.

16. Copie o conteúdo [deste local](https://raw.githubusercontent.com/cephalin/active-directory-dotnet-webapp-roleclaims/master/WebApp-RoleClaims-DotNet/Scripts/AadPickerLibrary.js) em ~\\Scripts\\AadPickerLibrary.js.

	No script, o objeto `AadPicker` chama a [API do Graph do Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) para pesquisar usuários e grupos que correspondam à entrada.

17. ~\\Scripts\\AadPickerLibrary.js também usa o [Preenchimento Automático do jQuery UI](https://jqueryui.com/autocomplete/). Portanto, você precisa adicionar o jQuery UI ao seu projeto. Clique com o botão direito do mouse em seu projeto e clique em **Gerenciar Pacotes NuGet**.

18. No Gerenciador de Pacotes do NuGet, clique em Procurar, digite **jquery-ui** na barra de pesquisa e clique em **jQuery.UI.Combined**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/17-add-jquery-ui-nuget.png)

19. No painel à direita, clique em **Instalar** e depois clique em **OK** para continuar.

19. Abra ~\\App\_Start\\BundleConfig.cs e faça as seguintes alterações realçadas:
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
		bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
					"~/Scripts/jquery-{version}.js"<mark>,
					"~/Scripts/jquery-ui-{version}.js",
					"~/Scripts/AadPickerLibrary.js"</mark>));
	
		bundles.Add(new ScriptBundle("~/bundles/jqueryval").Include(
					"~/Scripts/jquery.validate*"));
	
		// Use the development version of Modernizr to develop with and learn from. Em seguida, quando você estiver
		// ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
		bundles.Add(new ScriptBundle("~/bundles/modernizr").Include(
					"~/Scripts/modernizr-*"));
	
		bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
					"~/Scripts/bootstrap.js",
					"~/Scripts/respond.js"));
	
		bundles.Add(new StyleBundle("~/Content/css").Include(
					"~/Content/bootstrap.css",
					"~/Content/site.css"<mark>,
					"~/Content/themes/base/jquery-ui.css"</mark>));
	}
	</pre>

	Há outras maneiras de alto desempenho para gerenciar arquivos JavaScript e CSS em seu aplicativo. No entanto, para manter a simplicidade, apenas acumule os pacotes carregados com cada exibição.

20. Agora, publique suas alterações. Clique com o botão direito do mouse em seu projeto e clique em **Publicar**.

21. Clique em **Configurações**, verifique se há uma cadeia de conexão com seu Banco de Dados SQL, selecione **Atualizar Banco de Dados** para fazer as alterações de esquema no modelo e clique em **Publicar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/18-publish-crud-changes.png)

22. No navegador, navegue até https://&lt;*appname*>.azurewebsites.net/workitems e clique em **Criar Novo**.

23. Clique na caixa **AssignedToName**. Agora você verá os usuários e grupos do seu locatário do Azure Active Directory em uma lista suspensa. Você pode digitar para filtrar ou usar a tecla `Up` ou `Down` ou clicar para selecionar o usuário ou grupo.

	![](./media/web-sites-dotnet-lob-application-azure-ad/19-use-aadpicker.png)

24. Clique em **Criar** para salvar as alterações. Em seguida, clique em **Editar** no item de trabalho criado para observar o mesmo comportamento.

Parabéns, você está executando um aplicativo de linha de negócios no Azure com acesso ao diretório! Você pode fazer muito mais com a API do Graph. Consulte a [referência da API do Graph do Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog).

<a name="next"></a>
## Próxima etapa

Se você precisar de controle de acesso baseado em função (RBAC) para o aplicativo de linha de negócios no Azure, confira [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) para obter um exemplo da equipe do Azure Active Directory. Ele mostra como habilitar funções para seu aplicativo do Azure Active Directory e, em seguida, autorizar usuários com a decoração `[Authorize]`.

Se o seu aplicativo de linha de negócios precisar acessar dados locais, confira [Acesso a recursos locais usando conexões híbridas no Serviço de Aplicativo do Azure](web-sites-hybrid-connection-get-started.md).

<a name="bkmk_resources"></a>
## Outros recursos

- [Autenticação e autorização no Serviço de Aplicativo do Azure](../app-service/app-service-authentication-overview.md)
- [Autenticar com o Active Directory local em seu aplicativo do Azure](web-sites-authentication-authorization.md)
- [Criar um aplicativo de linha de negócios no Azure com autenticação do AD FS](web-sites-dotnet-lob-application-adfs.md)
- [Autenticação do Serviço de Aplicativo e a API do Graph do Azure AD](https://cgillum.tech/2016/03/25/app-service-auth-aad-graph-api/)
- [Exemplos e documentação do Active Directory do Microsoft Azure](https://github.com/AzureADSamples)
- [Tipos de declaração e token com suporte no Active Directory do Azure](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[Protect the Application with SSL and the Authorize Attribute]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute

<!---HONumber=AcomDC_0831_2016-->