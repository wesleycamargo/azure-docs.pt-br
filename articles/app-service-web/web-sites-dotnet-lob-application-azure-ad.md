<properties 
	pageTitle="Criar um aplicativo Web .NET MVC no Serviço de Aplicativo do Azure com a autenticação do Active Directory do Azure" 
	description="Saiba como criar um aplicativo de linha de negócios ASP.NET MVC no Serviço de Aplicativo do Azure que realiza a autenticação com o Active Directory do Azure" 
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
	ms.date="10/14/2015" 
	ms.author="cephalin"/>

# Criar um aplicativo Web .NET MVC no Serviço de Aplicativo do Azure com a autenticação do Active Directory do Azure #

Neste artigo, você aprenderá a criar um aplicativo de linha de negócios ASP.NET MVC nos [Aplicativos Web do Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) usando o [Active Directory do Azure](/services/active-directory/) como o provedor de identidade. Você também aprenderá como usar a [Biblioteca de cliente do Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam/archive/2014/06/02/azure-active-directory-graph-client-library-1-0-publish.aspx) para consultar dados de diretório no aplicativo.

O locatário do Active Directory do Azure que você usa pode ter um diretório somente no Azure ou pode ser sincronizado no diretório com seu AD (Active Directory) local para criar uma experiência de logon único para trabalhadores locais ou remotos.

>[AZURE.NOTE] Para os Aplicativos Web do Serviço de Aplicativo do Azure, você pode configurar a autenticação simples em relação a um locatário do Active Directory do Azure com alguns cliques de um botão. Para obter mais informações, consulte [Usar o Active Directory para autenticação no Serviço de Aplicativo do Azure](web-sites-authentication-authorization.md).

<a name="bkmk_build"></a>
## O que você compilará ##

Você compilará um aplicativo simples de linha de negócios CRUD (Create-Read-Update-Delete) nos Aplicativos Web do Serviço de Aplicativo que acompanha itens de trabalho com os seguintes recursos:

- Autentica usuários no Active Directory do Azure
- Implementa Funcionalidade de entrada e saída
- Usa `[Authorize]` para autorizar usuários para ações CRUD diferentes
- Consulta dados do Active Directory do Azure usando a [API do Azure Active Directory Graph](http://msdn.microsoft.com/library/azure/hh974476.aspx)
- Usa [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (em vez do Windows Identity Foundation, ou seja, WIF), que é o futuro do ASP.NET e é muito mais simples de configurar para autenticação e autorização do que o WIF

<a name="bkmk_need"></a>
## O que será necessário ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

>[AZURE.NOTE] Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

É necessário o seguinte para concluir este tutorial:

- Um locatário do Active Directory do Azure com usuários em vários grupos
- Permissões para criar aplicativos no locatário do Active Directory do Azure
- Visual Studio 2013
- [SDK do Azure 2.5.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) ou posterior

<a name="bkmk_sample"></a>
## Usar o aplicativo de exemplo para modelo de linha de negócios ##

O aplicativo de exemplo neste tutorial [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims), é criado pela equipe do Active Directory do Azure e pode ser usado como um modelo para criar novos aplicativos de linha de negócios com facilidade. Ele tem os seguintes recursos integrados:

- Usa [OpenID Connect](http://openid.net/connect/) para realizar a autenticação no Active Directory do Azure
- Contém um controlador `TaskTracker` de exemplo que demonstra como é possível autorizar funções diferentes para ações específicas em um aplicativo, incluindo o uso padrão de `[Authorize]`. 
- É um aplicativo multilocatário com funções predefinidas que você pode atribuir imediatamente aos usuários e grupos. 

<a name="bkmk_run" />
## Executar o aplicativo de exemplo ##

1.	Clone ou baixe a solução de exemplo em [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) em seu diretório local.

2.	Siga as instruções em [Como executar o exemplo como um aplicativo de locatário único](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#how-to-run-the-sample-as-a-single-tenant-app) para configurar o aplicativo do Active Directory do Azure e o projeto.
Certifique-se de seguir todas as instruções para converter o aplicativo de multilocatário em único locatário.

3.	Na exibição do [portal do Azure](https://manage.windowsazure.com) do aplicativo do Active Directory do Azure que você acabou de criar, clique na guia **USUÁRIOS**. Em seguida, atribua os usuários desejados às funções desejadas.

	>[AZURE.NOTE] Se desejar atribuir funções a grupos, além de usuários, você deverá atualizar seu locatário do Active Directory do Azure para o [Azure Active Directory Premium](/pricing/details/active-directory/). Na interface do usuário do portal do seu aplicativo, se você vir a guia **USUÁRIOS** em vez da guia **USUÁRIOS E GRUPOS, você poderá experimentar o Azure Active Directory Premium acessando a guia **LICENÇAS** do seu locatário do Active Directory do Azure.

3.	Quando terminar de configurar o aplicativo, digite `F5` no Visual Studio para executar o aplicativo do ASP.NET.

4.	Depois que o aplicativo for carregado, clique em **Entrar** e entre com um usuário que tem a função de Administrador no portal do Azure.

5.	Se tiver configurado o aplicativo do Active Directory do Azure corretamente e definir as configurações correspondentes no Web.config, você deverá ser redirecionado para o logon. Simplesmente faça logon com a conta usada para criar o aplicativo do Active Directory do Azure no portal do Azure, pois ela é a proprietária padrão do aplicativo do Active Directory do Azure.
	
<a name="bkmk_deploy"></a>
## Implantar o aplicativo de exemplo para os Aplicativos Web do Serviço de Aplicativo

Aqui, você publicará o aplicativo para um Aplicativo Web no Serviço de Aplicativo do Azure. Já existem instruções em [README.md](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims/blob/master/README.md) para implantar em Aplicativos Web do Serviço de Aplicativo do Azure, mas essas etapas também anulam a configuração para o seu ambiente de depuração local. Mostrarei a você como implantar enquanto preserva a configuração de depuração.

1. Clique duas vezes com o botão direito em seu projeto e selecione **Publicar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/publish-app.png)

2. Selecione **Aplicativos Web do Microsoft Azure**.

3. Se não tiver entrado no Azure, clique em **Adicionar uma conta** e use a conta da Microsoft de sua assinatura do Azure para entrar.

4. Depois de conectado, clique em **Novo** para criar um novo aplicativo Web no Azure.

5. Preencha todos os campos obrigatórios. Você precisará de uma conexão de banco de dados para esse aplicativo para armazenar os dados de aplicativo, tokens em cache e mapeamentos de função.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-website.png)

6. Clique em **Criar**. Depois que o aplicativo Web for criado, a caixa de diálogo **Publicar Web** será aberta.

7. Em **URL de destino**, altere **http** para **https**. Copie a URL inteira em um editor de texto. Você precisará dela mais tarde. Em seguida, clique em **Avançar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-change-to-https.png)

8. Desmarque a caixa de seleção **Habilitar Autenticação Organizacional**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-enable-code-first-migrations.png)

8. Expanda **RoleClaimContext** e selecione **Executar Migrações do Code First (executado na inicialização do aplicativo)**. As [Migrações do Code First](https://msdn.microsoft.com/data/jj591621.aspx) ajudam a atualizar o esquema de banco de dados do aplicativo no Azure ao definir modelos de dados adicionais do Code First posteriormente.

9. Em vez de clicar em **Publicar** para continuar com a publicação da web, clique em **Fechar**. Clique em **Sim** para salvar as alterações no perfil de publicação.

2. No [Portal de gerenciamento do Azure](https://manage.windowsazure.com), vá para seu locatário do Active Directory do Azure e clique na guia **Aplicativos**.

2. Clique em **Adicionar** na parte inferior da página.

2. Clique em **Adicionar um aplicativo que a minha organização está desenvolvendo**.

3. Selecione **Aplicativo Web e/ou API**.

4. Nomeie o aplicativo e clique em **Avançar**.

5. Em Propriedades do Aplicativo, defina **URL de logon** para a URL do aplicativo Web que você salvou anteriormente (por exemplo, `https://<site-name>.azurewebsites.net/`) e o **URI de ID do aplicativo** para `https://<aad-tenanet-name>/<app-name>`. Em seguida, clique em **Concluir**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-app-properties.png)

2.	Depois que o aplicativo for criado, atualize o manifesto do aplicativo da mesma maneira que você fazia anteriormente, usando as instruções em [Definir as funções do seu aplicativo](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#step-2-define-your-application-roles).

3.	Na exibição do [portal do Azure](https://manage.windowsazure.com) do aplicativo do Active Directory do Azure que você acabou de criar, clique na guia **USUÁRIOS**. Em seguida, atribua os usuários desejados às funções desejadas.

6. Clique na guia **CONFIGURAR**.

7. Em **Chaves**, crie uma nova chave selecionando **1 ano** na lista suspensa.

8. Em **Permissões para outros aplicativos**, na entrada **Active Directory do Azure**, selecione **Entrar e ler o perfil do usuário** e **Ler dados do diretório** na lista suspensa **Permissões Delegadas**.

	> [AZURE.NOTE] As permissões exatas necessárias dependem da funcionalidade desejada do seu aplicativo. Algumas permissões exigem a função **Administrador Global** definida, mas as permissões exigidas por este tutorial requerem apenas a função **Usuário**.

9.  Clique em **Salvar**.  

10.  Antes de navegar para fora da página de configuração salva, copie as informações a seguir em um editor de texto.

	-	ID do cliente
	-	Chave (se você navegar para fora da página, você não poderá ver a chave novamente)

11. No Visual Studio, abra **Web.Release.config** em seu projeto. Insira o seguinte XML na marca `<configuration>` e substitua o valor de cada chave pelas informações salvas para seu novo aplicativo do Active Directory do Azure.
	<pre class="prettyprint">
&lt;appSettings>
   &lt;add key="ida:ClientId" value="<mark>[e.g. 82692da5-a86f-44c9-9d53-2f88d52b478b]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
   &lt;add key="ida:AppKey" value="<mark>[e.g. rZJJ9bHSi/cYnYwmQFxLYDn/6EfnrnIfKoNzv9NKgbo=]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
   &lt;add key="ida:PostLogoutRedirectUri" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
&lt;/appSettings></pre>

	Certifique-se de que o valor de ida: PostLogoutRedirectUri termine com uma barra "/".

1. Clique duas vezes com o botão direito em seu projeto e selecione **Publicar**.

2. Clique em **Publicar** para publicar Aplicativos Web do Serviço de Aplicativo do Azure.

Quando terminar, você terá dois aplicativos do Active Directory do Azure configurados no portal de gerenciamento do Azure, um para o seu ambiente de depuração no Visual Studio e um para o aplicativo Web publicado no Azure. Durante a depuração, as configurações do aplicativo em Web.config são usadas para fazer sua configuração **Depurar** funcionar com o Active Directory do Azure, e quando ele é publicado (por padrão, a configuração **Versão** é publicada), um Web.config transformado é carregado e incorpora as alterações de configuração do aplicativo em Web.Release.config.

Se você deseja anexar o aplicativo Web publicado ao depurador (você deve carregar símbolos de depuração do seu código no aplicativo Web publicado), você pode criar um clone da configuração de depuração para depuração do Azure, mas com sua própria transformação de Web.config (por exemplo, Web.AzureDebug.config) personalizada que usa as configurações do Active Directory do Azure de Web.Release.config. Isso permite que você mantenha uma configuração estática em diferentes ambientes.

<a name="bkmk_crud"></a>
## Adicionar funcionalidade de linha de negócios no aplicativo de exemplo

Nesta parte do tutorial, você aprenderá a criar a funcionalidade de linha de negócios desejada com base no aplicativo de exemplo. Você criará um rastreador de itens de trabalho CRUD simples, semelhante ao controlador TaskTracker, mas usando o scaffolding de CRUD padrão e o padrão de design. Você também usará o Scripts\\AadPickerLibrary.js incluído para enriquecer seu aplicativo com dados da API do Azure Active Directory Graph.

5.	Na pasta Modelos, crie um novo modelo do [Code First](http://www.asp.net/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) chamado WorkItem.cs e substitua o código pelo código abaixo:

		using System.ComponentModel.DataAnnotations;
		
		namespace WebApp_RoleClaims_DotNet.Models
		{
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
		}

6.	Abra DAL\\RoleClaimContext.cs e adicione o código realçado:
	<pre class="prettyprint">
    public class RoleClaimContext : DbContext
    {
        public RoleClaimContext() : base("RoleClaimContext") { }

        public DbSet&lt;Task&gt; Tasks { get; set; }
        <mark>public DbSet&lt;WorkItem&gt; WorkItems { get; set; }</mark>
        public DbSet&lt;TokenCacheEntry&gt; TokenCacheEntries { get; set; }
    }</pre>

7.	Compile o projeto para disponibilizar o novo modelo para a lógica de scaffolding no Visual Studio.

8.	Adicione um novo item `WorkItemsController` de scaffolding na pasta Controladores. Para fazer isso, clique com botão direito do mouse em **Controladores**, aponte para **Adicionar** e selecione **Novo item gerado automaticamente**.

9.	Selecione **Controlador MVC 5 com modos de exibição, usando o Entity Framework** e clique em **Adicionar**.

10.	Selecione o modelo que você acabou de criar e clique em **Adicionar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-add-scaffolded-controller.png)

9.	Abra Controllers\\WorkItemsController.cs

11. Adicione as decorações [Authorize] destacadas nas respectivas ações a seguir.
	<pre class="prettyprint">
	...

    <mark>[Authorize(Roles = "Admin, Observer, Writer, Approver")]</mark>
    public class WorkItemsController : Controller
    {
		...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public ActionResult Create()
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Create([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Edit(int? id)
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Edit([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
        ...

        <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
        public async Task&lt;ActionResult&gt; Delete(int? id)
        ...

        <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
        public async Task&lt;ActionResult&gt; DeleteConfirmed(int id)
        ...
	}</pre>
	
	Como você cuida dos mapeamentos de função na interface do usuário do portal do Azure, basta garantir que cada ação autorize as funções certas.

	> [AZURE.NOTE] Você talvez tenha observado a decoração <code>[ValidateAntiForgeryToken]</code> em algumas das ações. Devido ao comportamento descrito por [Brock Allen](https://twitter.com/BrockLAllen) em [MVC 4, AntiForgeryToken e declarações](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/), seu HTTP POST poderá ser reprovado na validação de token antifalsificação porque:
	> + o Active Directory do Azure não envia o http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, que é necessário por padrão para o token antifalsificação.
	> + Se o Active Directory do Azure for o diretório sincronizado com o AD FS, a relação de confiança do AD FS, por padrão, também não enviará a declaração http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, embora você possa configurar manualmente o AD FS para enviar essa declaração.
	> Você cuidará disso na próxima etapa.

12.  Em App\_Start\\Startup.Auth.cs, adicione a linha de código a seguir no método `ConfigureAuth`. Clique com o botão direito do mouse em cada erro de resolução de nomes para corrigi-lo.

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	`ClaimTypes.NameIdentifies` especifica a declaração `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, que o Active Directory do Azure fornece. Agora que você cuidou da parte de autorização (é sério, não demorou muito), você pode dedicar seu tempo à funcionalidade real das ações.

13.	Em Create() e Edit(), adicione o código a seguir para disponibilizar algumas variáveis para o JavaScript mais tarde. Clique com o botão direito do mouse em cada erro de resolução de nomes para corrigi-lo.

        ViewData["token"] = AcquireToken(ClaimsPrincipal.Current.FindFirst(Globals.ObjectIdClaimType).Value);
        ViewData["tenant"] = ConfigHelper.Tenant;

13.	O método `AcquireToken()` ainda não foi definido, portanto defina-o na classe `WorkItemsController` agora. Clique com o botão direito do mouse em cada erro de resolução de nomes para corrigi-lo.

        static string AcquireToken(string userObjectId)
        {
            ClientCredential cred = new ClientCredential(ConfigHelper.ClientId, ConfigHelper.AppKey);
            Claim tenantIdClaim = ClaimsPrincipal.Current.FindFirst(Globals.TenantIdClaimType);
            AuthenticationContext authContext = new AuthenticationContext(String.Format(CultureInfo.InvariantCulture, ConfigHelper.AadInstance, tenantIdClaim.Value), new TokenDbCache(userObjectId));
            AuthenticationResult result = authContext.AcquireTokenSilent(ConfigHelper.GraphResourceId, cred, new UserIdentifier(userObjectId, UserIdentifierType.UniqueId));
            return result.AccessToken;
        }
		
14.	Em Views\\WorkItems\\Create.cshtml (um item automaticamente submetido a scaffolding), localize o método auxiliar `Html.BeginForm` e modifique-o da seguinte maneira:
	<pre class="prettyprint">@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>))
	{
	    @Html.AntiForgeryToken()
	    
	    &lt;div class="form-horizontal"&gt;
	        &lt;h4&gt;WorkItem&lt;/h4&gt;
	        &lt;hr /&gt;
	        @Html.ValidationSummary(true, "", new { @class = "text-danger" })
	
	        &lt;div class="form-group"&gt;
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type=&quot;hidden&quot;</mark> } })
	                @Html.ValidationMessageFor(model =&gt; model.AssignedToID, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
	                @Html.ValidationMessageFor(model =&gt; model.AssignedToName, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.Description, new { htmlAttributes = new { @class = "form-control" } })
	                @Html.ValidationMessageFor(model =&gt; model.Description, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EnumDropDownListFor(model =&gt; model.Status, htmlAttributes: new { @class = "form-control" })
	                @Html.ValidationMessageFor(model =&gt; model.Status, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            &lt;div class="col-md-offset-2 col-md-10"&gt;
	                &lt;input type="submit" value="Create" class="btn btn-default" <mark>id="submit-button"</mark> /&gt;
	            &lt;/div&gt;
	        &lt;/div&gt;
	    &lt;/div&gt;
	
	    <mark>&lt;script&gt;
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
	    &lt;/script&gt;</mark>
	
	}</pre>
	
	No script, o objeto AadPicker chama a [Graph API do Active Directory do Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) para pesquisar usuários e grupos que correspondem à entrada.

15. Abra o [Console do Gerenciador de Pacotes](http://docs.nuget.org/Consume/Package-Manager-Console) e execute **Enable-Migrations –EnableAutomaticMigrations**. Semelhante à opção selecionada quando você publicou o aplicativo no Azure, este comando ajuda a atualizar o esquema de banco de dados do aplicativo em [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) quando você o depura no Visual Studio.

15. Agora execute o aplicativo no depurador do Visual Studio ou publique-o novamente nos Aplicativos Web do Serviço de Aplicativo. Faça logon como o proprietário do aplicativo e navegue até `https://<webappname>.azurewebsites.net/WorkItems/Create`. Você verá agora que pode selecionar um usuário ou grupo do Active Directory do Azure na lista suspensa ou digitar algo para filtrar a lista.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-workitem.png)

16. Preencha o restante do formulário e clique em **Criar**. A página ~/WorkItems/Index agora mostra o item de trabalho criado recentemente. Você também observará na captura de tela abaixo que removi a coluna `AssignedToID` em Views\\WorkItems\\Index.cshtml.

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-workitem-index.png)

11.	Agora, faça alterações semelhantes na exibição **Editar**. Em Views\\WorkItems\\Edit.cshtml, faça as alterações no método auxiliar `Html.BeginForm` que são idênticas às de Views\\WorkItems\\Create.cshtml na etapa anterior (substitua "Criar" por "Editar" no código realçado acima).

É isso!

Agora que você configurou as autorizações e a funcionalidade de linha de negócios para as diferentes ações no controlador WorkItems, você pode tentar fazer logon como usuários de funções de aplicativo diferentes para ver como o aplicativo responde.

![](./media/web-sites-dotnet-lob-application-azure-ad/11-edit-unauthorized.png)

<a name="bkmk_resources"></a>
## Outros recursos

- [Proteger o aplicativo com SSL e o atributo Authorize](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Usar o Active Directory para autenticação no Serviço de Aplicativo do Azure](web-sites-authentication-authorization.md)
- [Criar um aplicativo Web .NET MVC no Serviço de Aplicativo do Azure com autenticação do AD FS](web-sites-dotnet-lob-application-adfs.md)
- [Exemplos e documentação do Active Directory do Microsoft Azure](https://github.com/AzureADSamples)
- [Blog de Vittorio Bertocci](http://blogs.msdn.com/b/vbertocci/)
- [Migrar um projeto Web VS2013 do WIF para Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Novas conexões híbridas do Azure, não seu #hybridCloud pai](/documentation/videos/new-hybrid-connections-not-your-fathers-hybridcloud/)
- [Semelhanças entre o Active Directory e o Active Directory do Azure](http://technet.microsoft.com/library/dn518177.aspx)
- [Sincronização de diretórios com cenário de logon único](http://technet.microsoft.com/library/dn441213.aspx)
- [Tipos de declaração e token com suporte no Active Directory do Azure](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-----HONumber=Oct15_HO4-->