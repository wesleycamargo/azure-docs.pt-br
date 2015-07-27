<properties 
	pageTitle="Criar um aplicativo ASP.NET MVC com autenticação e o banco de dados SQL e implantar no Serviço de Aplicativo do Azure" 
	description="Aprenda a desenvolver um aplicativo ASP.NET MVC 5 com um banco de dados SQL de back-end, adicionar autenticação e autorização e implantá-lo no Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="riande"/>



# Criar um aplicativo ASP.NET MVC com autenticação e o banco de dados SQL e implantar no Serviço de Aplicativo do Azure

Este tutorial mostra como criar um aplicativo Web ASP.NET MVC 5 seguro que permite que os usuários façam logon com credenciais do Google ou do Facebook. Você implantará o aplicativo no [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714).

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2013, o SDK instalará automaticamente o Visual Studio 2013 para o Web Express. É possível iniciar o desenvolvimento para o Azure de maneira gratuita.

Este tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure. Ao concluir este tutorial, você terá um aplicativo Web controlado por dados seguro e em funcionamento na nuvem usando um banco de dados em nuvem.

Você aprenderá a:

* Como criar um projeto ASP.NET MVC 5 seguro e publicá-lo em [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) no Serviço de Aplicativo do Azure.
* Como utilizar o [OAuth](http://oauth.net/ "http://oauth.net/") e o banco de dados de associação do ASP.NET para proteger seu aplicativo.
* Como usar um banco de dados SQL para armazenar dados no Azure.

Você criará um aplicativo Web de lista de contatos simples criado no ASP.NET MVC 5 e que usa o ADO.NET Entity Framework para acesso ao banco de dados. A seguinte ilustração mostra a página de logon do aplicativo concluído:

![página de logon][rxb]

>[AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Se não tem uma conta, você pode <a href="/pt-br/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">ativar os benefícios de assinante MSDN</a> ou <a href="/pt-br/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">inscrever-se em uma avaliação gratuita</a>.

>Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

Para configurar o ambiente de desenvolvimento, você deverá instalar o [Visual Studio 2013 Atualização 4](http://go.microsoft.com/fwlink/?LinkId=390521) ou superior e a versão mais recente do [SDK do Azure para Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409). Este artigo foi escrito para o Visual Studio Atualização 4 e para o SDK 2.5.1.

## Criar um aplicativo ASP.NET MVC 5

### Criar o projeto

1. No menu **Arquivo**, clique em **Novo Projeto**.

	![Novo projeto no menu Arquivo](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/gs13newproj.png)

1. Na caixa de diálogo **Novo Projeto**, expanda **C#**, selecione **Web** em **Modelos instalados** e, em seguida, selecione **Aplicativo Web ASP.NET**.

1. Nomeie o aplicativo **ContactManager** e clique em **OK**.

	![Caixa de diálogo Novo Projeto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13newprojdb.png)
 
	**Observação:** não deixe de inserir "ContactManager". Os blocos de código que você irá copiar posteriormente supõem que o nome do projeto seja ContactManager.

1. Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo **MVC**. Verifique se a **Autenticação** está definida como **Contas de Usuário Individuais**, se **Host na nuvem** está marcado e se a opção **Aplicativo Web** está selecionada.

	![Caixa de diálogo Novo Projeto ASP .NET](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newproject.png)

1. O assistente de configuração sugerirá um nome exclusivo com base em *ContactManager*. Você deve decidir se deseja criar um novo grupo de recursos e um plano do Serviço de Aplicativo. Para obter orientações para decidir se deseja criar um novo plano ou um grupo de recursos, consulte [Visão geral detalhada de planos do Serviço de Aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Para este tutorial, é pertinente criar pelo menos um novo grupo de recursos. Selecione uma região perto de você. Você pode utilizar o [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") para localizar o data center de latência mais baixa. Você configurará o banco de dados na próxima etapa, portanto, não clique em **OK** ainda.

   ![Novo plano e grupo de recursos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newplanandgroup.png)
 
2. Se você não criou um servidor de banco de dados antes, selecione **Criar novo servidor**, insira um nome de banco de dados, um nome de usuário e uma senha.

   ![Usar novo banco de dados](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newdb.png)

3. Se você tiver um servidor de banco de dados, use isso para criar um novo banco de dados. Os servidores de banco de dados são um recurso precioso e você geralmente deseja criar vários bancos de dados no mesmo servidor de teste e desenvolvimento em vez de criar um servidor de banco de dados por banco de dados. Verifique se o seu aplicativo Web e o banco de dados estão na mesma região.

    ![Usar banco de dados existente](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/useexistingdb.png)

### Configurar o cabeçalho e o rodapé da página


1. No **Gerenciador de Soluções**, abra o arquivo *Layout.cshtml* na pasta *Views\Shared*.

	![_Layout.cshtml no Gerenciador de Soluções][newapp004]

1. Substitua a marcação no arquivo *Layout.cshtml* pelo código a seguir. As alterações estão destacadas abaixo.

<pre>
			&lt;!DOCTYPE html>
			&lt;html>
			&lt;head>
			    &lt;meta charset="utf-8" />
			    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0">
			    &lt;title>@ViewBag.Title - <mark>Contact Manager</mark>&lt;/title>
			    @Styles.Render("~/Content/css")
			    @Scripts.Render("~/bundles/modernizr")
			
			&lt;/head>
			&lt;body>
			    &lt;div class="navbar navbar-inverse navbar-fixed-top">
			        &lt;div class="container">
			            &lt;div class="navbar-header">
			                &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
			                    &lt;span class="icon-bar">&lt;/span>
			                    &lt;span class="icon-bar">&lt;/span>
			                    &lt;span class="icon-bar">&lt;/span>
			                &lt;/button>
			                @Html.ActionLink("<mark>CM Demo</mark>", "Index", "<mark>Cm</mark>", new { area = "" }, new { @class = "navbar-brand" })
			            &lt;/div>
			            &lt;div class="navbar-collapse collapse">
			                &lt;ul class="nav navbar-nav">
			                    &lt;li>@Html.ActionLink("Home", "Index", "Home")&lt;/li>
			                    &lt;li>@Html.ActionLink("About", "About", "Home")&lt;/li>
			                    &lt;li>@Html.ActionLink("Contact", "Contact", "Home")&lt;/li>
			                &lt;/ul>
			                @Html.Partial("_LoginPartial")
			            &lt;/div>
			        &lt;/div>
			    &lt;/div>
			    &lt;div class="container body-content">
			        @RenderBody()
			        &lt;hr />
			        &lt;footer>
			            &lt;p>&amp;copy; @DateTime.Now.Year - <mark>Contact Manager</mark>&lt;/p>
			        &lt;/footer>
			    &lt;/div>
			
			    @Scripts.Render("~/bundles/jquery")
			    @Scripts.Render("~/bundles/bootstrap")
			    @RenderSection("scripts", required: false)
			&lt;/body>
			&lt;/html>
</pre>

### Executar o aplicativo localmente

1. Pressione CTRL+F5 para executar o aplicativo.

	A home page do aplicativo é exibida no navegador padrão.

	![Aplicativo Web em execução local](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr2.png)

Isso é tudo o que você precisa fazer por enquanto para criar o aplicativo que você implantará no Azure.

## Habilitar SSL para o projeto ##

1. Habilite o SSL. No Gerenciador de Soluções, clique no projeto **ContactManager** e clique em F4 para exibir a caixa de diálogo de propriedades. Altere **SSL Habilitado** para verdadeiro. Copie a **URL do SSL**. A URL do SSL será https://localhost:44300/, a menos que você tenha criado aplicativos Web SSL anteriormente.

	![habilitar SSL][rxSSL]
 
1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **Gerenciador de Contatos** e clique em **Propriedades**.
1. Na guia à esquerda, clique em **Web**.
1. Altere a **URL do Projeto** para usar a **URL de SSL** e salve a página (Ctrl+S).

	![habilitar SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr1.png)
 
1. Verifique se o Internet Explorer é o navegador que inicia o Visual Studio, como mostrado na imagem abaixo:

	![navegador padrão](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss12.PNG)

	O seletor de navegador permite que você especifique o navegador que inicia o Visual Studio.

 	![seletor de navegador](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss13.png)

	É possível selecionar vários navegadores e fazer com que o Visual Studio atualize cada navegador quando você fizer alterações. Para saber mais, confira [Utilizando o link do navegador no Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).


1. Pressione CTRL+F5 para executar o aplicativo. Siga as instruções para confiar no certificado autoassinado que o IIS Express gerou.

	 ![instruções para confiar no certificado autoassinado que o IIS Express gerou](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss26.PNG)

1. Leia o diálogo **Aviso de Segurança** e clique em **Sim**, se desejar instalar o certificado que representa o **localhost**.

 	![Aviso de certificado do IIS Express para localhost](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss27.PNG)

1. O IE mostra a *Home page* e não há nenhum aviso de SSL.

	 ![IE com SSL do localhost e sem avisos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss28.PNG)

	Google Chrome também aceita o certificado e mostrará o conteúdo de HTTPS sem aviso. O Firefox usa seu próprio repositório de certificados, portanto, será exibido um aviso.

	 ![Aviso do FireFox Cert](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss30.PNG)

## Implantar o aplicativo no Azure

1. No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publicar no menu de contexto do projeto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
   O assistente de **Publicar Web** é aberto.

1. Na caixa de diálogo **Publicar na Web**, clique em **Publicar**.

	![Publicar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr3.png)

	O aplicativo que você criou agora está em execução na nuvem. Na próxima vez que você implantar o aplicativo, somente os arquivos alterados (ou novos) serão implantados.

	![Executando na nuvem](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss4.PNG)

## Adicionar um banco de dados ao aplicativo

Em seguida, você atualizará o aplicativo para adicionar a habilidade de exibir e atualizar contatos e armazenar os dados em um banco de dados. O aplicativo usará o Entity Framework (EF) para criar o banco de dados e ler e atualizar os dados.

### Adicionar classes de modelo de dados aos contatos

Você começa criando um modelo de dados simples no código.

1. No **Gerenciador de Soluções**, clique com o botão direito na pasta Modelos e clique em **Adicionar**. Em seguida, clique em **Classe**.

	![Adicionar Classe no menu de contexto da pasta Modelos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr5.png)

2. Na caixa de diálogo **Adicionar Novo Item**, nomeie o novo arquivo de classe *Contact.cs* e clique em **Adicionar**.

	![Caixa de diálogo Adicionar Novo Item][adddb002]

3. Substitua o conteúdo do arquivo Contacts.cs pelo código a seguir.

        using System.ComponentModel.DataAnnotations;
        using System.Globalization;
        namespace ContactManager.Models
        {
            public class Contact
            {
                public int ContactId { get; set; }
                public string Name { get; set; }
                public string Address { get; set; }
                public string City { get; set; }
                public string State { get; set; }
                public string Zip { get; set; }
                [DataType(DataType.EmailAddress)]
                public string Email { get; set; }
            }
        }
A classe **Contacts** define os dados que você armazenará para cada contato, além de uma chave primária, *ContactID*, que é necessária para o banco de dados.

### Criar as páginas da web que permitem que os usuários do aplicativo trabalhem com os contatos

O recurso scaffolding do ASP.NET MVC pode gerar automaticamente o código que executa as ações CRUD (criar, ler, atualizar e excluir).

## Adicionar um controlador e uma exibição para os dados

1. Compile o projeto **(Ctrl+Shift+B)**. (Você deve construir o projeto antes de usar o mecanismo de scaffolding.) 
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Controladores e clique em **Adicionar**. Em seguida, clique em **Controlador**.

	![Adicionar Controlador no menu de contexto da pasta Controladores][addcode001]

5. Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC 5 com modos de exibição, usando o EF** e, em seguida, clique em **Adicionar**.
	
	![Adicionar Scaffold dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr6.png)


1. Na caixa suspensa **Classe do modelo**, selecione **Contato (ContactManager.Models)**. (Consulte a imagem abaixo).
1. Em **Classe do contexto de dados**, selecione **ApplicationDbContext (ContactManager.Models)**. O **ApplicationDbContext** será usado para o BD de associação e nossos dados de contato.
1. Na caixa de entrada de texto **Nome de controlador**, insira "CmController" para o nome do controlador. 

	![Novos dados ctx dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss5.PNG)

1. Clique em **Adicionar**.

   O Visual Studio cria métodos do controlador e modos de exibição para as operações de banco de dados CRUD para objetos **Contact**.

## Habilitar migrações, criar o banco de dados, adicionar dados de exemplo e um inicializador de dados ##

A próxima tarefa é habilitar o recurso [Codificar Primeiras Migrações](http://msdn.microsoft.com/library/hh770484.aspx) para criar o banco de dados com base no modelo de dados criado.

1. No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e, em seguida, **Console do Gerenciador de Pacotes**. ![Console do Gerenciador de Pacotes no menu Ferramentas](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/SS6.png)

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		enable-migrations
	O comando **enable-migrations** cria a pasta *Migrações* e coloca nessa pasta um arquivo *Configuration.cs*, que pode ser editado para propagar o banco de dados e configurar Migrações.

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		add-migration Initial


	O comando **add-migration Initial** gera um arquivo chamado **&lt;carimbo_de_data/hora&gt;Initial** na pasta *Migrações* que cria o banco de dados. O primeiro parâmetro (**Initial**) é arbitrário e é usado para criar o nome do arquivo. Você pode ver os novos arquivos de classe no **Gerenciador de Soluções**.
	Na classe **Inicial**, o método **Up** cria a tabela Contatos e o método **Down** (usado quando você deseja retornar ao estado anterior) a descarta.
3. Abra o arquivo *Migrations\Configuration.cs*. 
4. Adicione o seguinte namespace. 

    	 using ContactManager.Models;




5. Substitua o método *Seed* pelo seguinte código:

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                }
                );
        }

	Esse código inicializa (propaga) o banco de dados com as informações de contato. Para obter mais informações sobre a propagação do banco de dados, consulte [Propagando e depurando BDs do Entity Framework (EF) ](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


6. Em **Console do Gerenciador de Pacotes**, digite o comando:

		update-database

	![Comandos do Console do Gerenciador de Pacotes][addcode009]

	O **update-database** executa a primeira migração que cria o banco de dados. Por padrão, o banco de dados é criado como um banco de dados LocalDB do SQL Server Express.

7. Pressione CTRL+F5 para executar o aplicativo e, em seguida, clique no link **Demonstração de CM** ou navegue até https://localhost:(port#)/Cm.

	O aplicativo mostra os dados de semente e fornece links de edição, detalhes e exclusão. Você pode criar, editar, excluir e exibir dados.

	![Exibição do MVC de dados][rx2]



## Adicionar um provedor de OAuth2

O [OAuth](http://oauth.net/ "http://oauth.net/") é um protocolo aberto que permite autorização segura em um método simples e padrão da Web, de aplicativos móveis e de área de trabalho. O modelo de Internet do ASP.NET MVC usa OAuth para expor o Facebook, Twitter, Google e a Microsoft como provedores de autenticação. Embora este tutorial utilize apenas o Google como o provedor de autenticação, você pode facilmente modificar o código para utilizar qualquer um dos provedores. As etapas para implementar outros provedores são muito semelhantes às etapas que você verá neste tutorial. Para utilizar o Facebook como um provedor de autenticação, consulte o tutorial [Aplicativo do MVC 5 com o logon OAuth2 do Facebook, do Twitter, do LinkedIn e do Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).

Além da autenticação, o tutorial também usará funções para implementar a autorização. Somente os usuários que você adicionar à função *canEdit* poderão alterar dados (isto é, criar, editar ou excluir contatos).

Siga as instruções no tutorial [Criar um aplicativo do MVC 5 com o logon OAuth2 do Facebook, do Twitter, do LinkedIn e do Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog) em **Criando um aplicativo do Google para o OAuth 2 para configurar um aplicativo do Google para o OAuth2**. Execute e teste o aplicativo para verificar se você pode fazer logon usando a autenticação do Google.

## Usando a API de Associação
Nesta seção, você irá adicionar um usuário local e a função *canEdit* ao banco de dados de associação. Somente os usuários na função *canEdit* poderão editar os dados. A melhor prática é nomear as funções pelas ações que podem executar, portanto, *canEdit* é preferível do que uma função chamada *admin*. Quando seu aplicativo evoluir, você poderá adicionar novas funções, como *canDeleteMembers*, em vez da menos descritiva *superAdmin*.

1. Abra o arquivo *migrations\configuration.cs* e adicione as seguintes instruções `using`:

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. Adicionar o seguinte método **AddUserAndRole** à classe:

    
         bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
         {
            IdentityResult ir;
            var rm = new RoleManager<IdentityRole>
                (new RoleStore<IdentityRole>(context));
            ir = rm.Create(new IdentityRole("canEdit"));
            var um = new UserManager<ApplicationUser>(
                new UserStore<ApplicationUser>(context));
            var user = new ApplicationUser()
            {
               UserName = "user1@contoso.com",
            };
            ir = um.Create(user, "P_assw0rd1");
            if (ir.Succeeded == false)
               return ir.Succeeded;
            ir = um.AddToRole(user.Id, "canEdit");
            return ir.Succeeded;
         }

1. Chame o novo método no método **Seed**:
	<pre>
    protected override void Seed(ContactManager.Models.ApplicationDbContext context)
    {
        <mark>AddUserAndRole(context);</mark>
        context.Contacts.AddOrUpdate(p => p.Name,
            // Code removed for brevity
    }
	</pre>  
<span></span>
	As imagens a seguir mostram as alterações ao método *Seed*:

	![imagem de código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss24.PNG)

   Esse código cria uma nova função chamada *canEdit*, cria um novo usuário local *user1@contoso.com* e adiciona *user1@contoso.com* à função *canEdit*. Para saber mais, consulte os [tutoriais do ASP.NET Identity](http://www.asp.net/identity/overview/features-api).

## Usar código temporário para adicionar novos usuários de logon social à função canEdit  ##
Nesta seção, você modificará temporariamente o método **ExternalLoginConfirmation** no controlador de conta para adicionar novos usuários com registro com um provedor OAuth na função *canEdit*. Vamos modificar temporariamente o método **ExternalLoginConfirmation** para adicionar automaticamente novos usuários a uma função administrativa. Até que possamos fornecer uma ferramenta para adicionar e gerenciar funções, vamos usar o código de registro automático temporário abaixo. No futuro, esperamos fornecer uma ferramenta semelhante à [WSAT](http://msdn.microsoft.com/pt-br/library/ms228053.aspx) que permita criar e editar contas de usuário e funções.

1. Abra o arquivo **Controllers\AccountController.cs** e navegue para o método **ExternalLoginConfirmation**.
1. Adicione a seguinte chamada para **AddToRoleAsync** antes da chamada **SignInAsync**.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   O código acima adiciona o usuário recém-registrado à função "canEdit", que concede acesso aos métodos de ação que alteram (editam) dados.
	<pre>
	      // POST: /Account/ExternalLoginConfirmation
	      [HttpPost]
	      [AllowAnonymous]
	      [ValidateAntiForgeryToken]
	      public async Task<ActionResult> ExternalLoginConfirmation(ExternalLoginConfirmationViewModel model, string returnUrl)
	      {
	         if (User.Identity.IsAuthenticated)
	         {
	            return RedirectToAction("Index", "Manage");
	         }
	         if (ModelState.IsValid)
	         {
	            // Get the information about the user from the external login provider
	            var info = await AuthenticationManager.GetExternalLoginInfoAsync();
	            if (info == null)
	            {
	               return View("ExternalLoginFailure");
	            }
	            var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
	            var result = await UserManager.CreateAsync(user);
	            if (result.Succeeded)
	            {
	               result = await UserManager.AddLoginAsync(user.Id, info.Login);
	               if (result.Succeeded)
	               {
	                  <mark>await UserManager.AddToRoleAsync(user.Id, "canEdit");</mark>
	                  await SignInManager.SignInAsync(user, isPersistent: false, rememberBrowser: false);
	                  return RedirectToLocal(returnUrl);
	               }
	            }
	            AddErrors(result);
	         }
	         ViewBag.ReturnUrl = returnUrl;
	         return View(model);
	      }
	</pre>

Mais tarde no tutorial você implantará o aplicativo no Azure, onde você fará logon no Google ou outro provedor de autenticação de terceiros. Isso adicionará a conta recém-registrada à função *canEdit*. Qualquer pessoa que localize a URL do aplicativo Web e que tenha uma ID do Google poderá registrar-se e atualizar seu banco de dados. Para impedir que outras pessoas façam isso, você pode parar o site. Você poderá verificar quem está na função *canEdit* examinando o banco de dados.

No **Console do Gerenciador de Pacotes** pressione a tecla de seta para cima para exibir o seguinte comando:

		Update-Database

Execute o comando **Update-Database** que executará o método **Seed** e o **AddUserAndRole** que você acabou de adicionar. O **AddUserAndRole** criará o usuário *user1@contoso.com* e o adicionará à função *canEdit*.

## Proteger o aplicativo com SSL e o atributo Authorize ##

Nesta seção, você aplicará o atributo [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) para restringir o acesso aos métodos de ação. Os usuários anônimos poderão exibir o método de ação **Índice** do controlador doméstico apenas. Os usuários registrados poderão ver os dados de contato (as páginas **Índice** e **Detalhes** do controlador de Cm), as páginas Sobre e Contato. Somente usuários na função *canEdit* poderão acessar os métodos de ação que alteram dados.

1. Adicione o filtro [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) e o filtro [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) ao aplicativo. Uma abordagem alternativa é adicionar os atributos [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) e [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) a cada controlador, mas aplicá-los a todo o aplicativo é considerado uma melhor prática de segurança. Adicionando-os globalmente, cada novo controlador e método de ação que você adicionar será automaticamente protegido, você não precisará se lembrar de aplicá-los. Para obter mais informações, consulte [Protegendo seu aplicativo ASP.NET MVC e o novo atributo AllowAnonymous ](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Abra o arquivo *App_Start\FilterConfig.cs* e substitua o método *RegisterGlobalFilters* pelo seguinte código (que adiciona dois filtros):
		<pre>
        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());</mark>
        }
		</pre>




	O filtro [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) aplicado no código acima impedirá que usuários anônimos acessem qualquer método no aplicativo. Você usará o atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) para cancelar o requisito de autorização em alguns métodos, portanto, os usuários anônimos poderão efetuar logon e exibir a home page. O [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) exigirá que todo acesso ao aplicativo Web seja feito por HTTPS.

1. Adicione o atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) ao método **Index** para o controlador Início. O atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) permite que você adicione métodos à lista de permissões para ignorar a autenticação. 
		<pre>
	public class HomeController : Controller
   {
      <mark>[AllowAnonymous]</mark>
      public ActionResult Index()
      {
         return View();
      }
	</pre>

2. Faça uma pesquisa global por *AllowAnonymous* e você verá que ele é usado no logon e nos métodos de registro do controlador Conta.
1. Em *CmController.cs*, adicione `[Authorize(Roles = "canEdit")]` aos métodos HttpGet e HttpPost que alteram dados (Create, Edit, Delete, todos os métodos de ação exceto Index e Details) no controlador *Cm*. Uma parte do código concluído é mostrada abaixo: 
		<pre>
	// GET: Cm/Create
       <mark>[Authorize(Roles = "canEdit")]</mark>
        public ActionResult Create()
        {
           return View(new Contact { Address = "123 N 456 W",
            City="Great Falls", Email = "ab@cd.com", Name="Joe Smith", State="MT",
           Zip = "59405"});
        }
        // POST: Cm/Create
        // Para proteger contra ataques de excesso de postagens, habilite as propriedades específicas que você deseja associar, para 
        // obter mais detalhes, consulte http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
         <mark>[Authorize(Roles = "canEdit")]</mark>
        public ActionResult Create([Bind(Include = "ContactId,Name,Address,City,State,Zip,Email")] Contact contact)
        {
            if (ModelState.IsValid)
            {
                db.Contacts.Add(contact);
                db.SaveChanges();
                return RedirectToAction("Index");
            }
            return View(contact);
        }
        // GET: Cm/Edit/5
       <mark>[Authorize(Roles = "canEdit")]</mark>
        public ActionResult Edit(int? id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
            Contact contact = db.Contacts.Find(id);
            if (contact == null)
            {
                return HttpNotFound();
            }
            return View(contact);
        }
		</pre>

1. Se você ainda estiver conectado de uma sessão anterior, clique no link **Logoff**.
1. Clique nos links **Sobre** ou **Contato**. Você será redirecionado para a página de logon, porque usuários anônimos não podem exibir essas páginas. 
1. Clique no link **Registrar-se como um novo usuário** e adicione um usuário local com o email *joe@contoso.com*. Verifique se *Joe* pode exibir as páginas Página Inicial, Sobre e Contato.

	![logon](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss14.PNG)

1. Clique em *Demonstração de CM* e verifique se você vê os dados.
1. Clique em um link de edição na página, você será redirecionado para a página de logon (porque um novo usuário local não é adicionado à função *canEdit*).
1. Faça logon como *user1@contoso.com* com a senha "P_assw0rd1" (o "0" em "word" é um zero). Você será redirecionado para a página de edição selecionada anteriormente. <br/>
   Se você não conseguir efetuar logon com essa conta e senha, tente copiar a senha do código-fonte e colá-la. Se ainda não for possível efetuar logon, verifique a coluna **UserName** da tabela **AspNetUsers** para verificar se *user1@contoso.com* foi adicionado. 

1. Verifique se você pode fazer alterações nos dados.

## Implantar o aplicativo no Azure

1. No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publicar no menu de contexto do projeto][firsdeploy003]

	O assistente de **Publicar Web** é aberto.

1. Clique a guia **Configurações** no lado esquerdo da caixa de diálogo **Publicar web**. Clique no ícone **v** para selecionar a **Cadeia de conexão remota** para **ApplicationDbContext** e selecione **ContactManagerNN_db**.

   
	![configurações](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc2.png)

1. Em **ContactManagerContext**, selecione **Executar as migrações iniciais de código**.

	![configurações](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc3.png)

1. Clique em **Publicar**.
1. Faça logon como *user1@contoso.com* (com a senha "P_assw0rd1") e verifique se você pode editar dados.
1. Faça logoff.
1. Vá para o [Console de desenvolvedores do Google](https://console.developers.google.com/) e sobre a atualização da guia **Credenciais** redirecione URIS e JavaScript Orgins para utilizar a URL do Azure.
1. Faça logon usando o Google ou o Facebook. Isso irá adicionar a conta do Google ou do Facebook à função **canEdit**. Se você receber um erro HTTP 400 com a mensagem *O URI de redirecionamento na solicitação: https://contactmanager{my version}.azurewebsites.net/signin-google não correspondeu a um URI de redirecionamento registrado.*, você deverá esperar até que as alterações feitas por você sejam propagadas. Se você receber esse erro depois de mais de alguns minutos, verifique se os URIs estão corretos.

### Parar o aplicativo Web para impedir que outras pessoas se registrem  

1. No **Gerenciador de Servidores**, navegue até **Aplicativos Web**.
4. Clique com o botão direito do mouse no aplicativo Web e selecione **Parar**. 

	![parar aplicativo Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s1.png)

	Como alternativa, no [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715), você pode selecionar o aplicativo Web e clicar no ícone **parar** na parte inferior da página.

	![parar portal do aplicativo Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/stopweb.png)

### Remover AddToRoleAsync, publicar e testar

1. Comente ou remova o código a seguir do método **ExternalLoginConfirmation** no controlador de Conta: 
                `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. Compile o projeto (que salva as alterações no arquivo e verifica se não há erros de compilação).
5. No Visual Studio, clique com o botão direito no nome do projeto no **Gerenciador de Soluções** e selecione **Publicar**.

	   ![Publicar no menu de contexto do projeto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
4. Clique no botão **Iniciar Visualização**. Somente os arquivos que precisam ser atualizados são implantados.
5. Inicie o aplicativo Web no Visual Studio ou do Portal. **Você não poderá publicar enquanto o aplicativo Web estiver parado**.

	![iniciar aplicativo Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss15.png)

5. Volte para o Visual Studio e clique em **Publicar**.
3. O seu aplicativo do Azure é aberto no navegador padrão. Se você efetuou logon, faça logoff para que você possa exibir a página inicial como um usuário anônimo.  
4. Clique no link **Sobre**. Você será redirecionado para a página Logon.
5. Clique no link **Registrar** na página Logon e crie a conta local. Usaremos essa conta local para verificar se você pode acessar as páginas somente leitura, mas não pode acessar páginas que alteram dados (que são protegidas pela função *canEdit*). Mais adiante, no tutorial, removeremos o acesso à conta local. 

	![Registrar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss16.PNG)

1. Verifique se você pode navegar até as páginas *Sobre* e *Contato*.

	![Fazer logoff](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss17.PNG)

1. Clique no link **Demonstração de CM** para navegar até o controlador de **Cm**. Como alternativa, você pode acrescentar *Cm* à URL.

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr4.png)
 
1. Clique em um link de edição. Você será redirecionado para a página de logon. Em **Usar outro serviço para fazer logon**, clique em Google ou Facebook e faça logon com a conta registrada anteriormente. (Se estiver trabalhando rapidamente e o cookie da sessão não expirou, você estará automaticamente conectado à conta do Facebook ou Google usados anteriormente.)
2. Verifique se você pode editar dados enquanto estiver conectado nessa conta.
 	**Observação:** não é possível fazer logout do Google neste aplicativo e fazer logon em outra conta do Google com o mesmo navegador. Se você estiver usando um navegador, precisará navegar até o Google e fazer logoff. Você pode fazer logon com outra conta do mesmo autenticador de terceiros (como o Google) usando outro navegador.

Se ainda não preencheu o nome e o sobrenome das informações da sua conta do Google, uma NullReferenceException ocorrerá.


## Examine o BD do SQL Azure ##

1. No **Gerenciador de Servidores**, navegue até **ContactDB**.
2. Clique com o botão direito do mouse em **ContactDB** e selecione **Abrir no Pesquisador de Objetos do SQL Server**.
 
	![abrir no SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr12.png)
 
3. Se você não tiver se conectado a esse banco de dados anteriormente, será preciso adicionar uma regra de firewall para permitir o acesso para seu endereço IP atual. O endereço IP será preenchido com antecedência. Basta clicar em **Adicionar Regra de Firewall** para habilitar o acesso.

  ![adicionar regra de firewall](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/addfirewallrule.png)
        
  Em seguida, efetue o login no banco de dados com o nome de usuário e a senha que você especificou ao criar o banco de dados.
 
1. Clique com o botão direito do mouse na guia **AspNetUsers** e selecione **Exibir Dados**.

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr8.png)
 
1. Observe a ID da conta do Google com a qual você se registrou para estar na função **canEdit** e a ID de *user1@contoso.com*. Esses devem ser os únicos usuários na função **canEdit**. (Você verificará isso na próxima etapa.)

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s2.png)
 
2. No **Pesquisador de Objetos do SQL Server**, clique com o botão direito do mouse em **AspNetUserRoles** e selecione **Exibir Dados**.

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rs1.png)
 
Verifique se os **UserIds** são de *user1@contoso.com* e da conta do Google que você registrou. 


## Próximas etapas

Siga meus tutoriais que se baseiam neste exemplo:

1.	[Criar um aplicativo Web seguro do ASP.NET MVC 5 com log de redefinição de senha e de confirmação de email](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[Aplicativo do ASP.NET MVC 5 com SMS e emails de autenticação de dois fatores](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[Práticas recomendadas para implantar as senhas e outros dados confidenciais para o ASP.NET e o Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
4.	[Criar um aplicativo do ASP.NET MVC 5 com o Facebook e o Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on) Isso inclui instruções sobre como adicionar dados de perfil ao banco de dados de registro do usuário e obter instruções detalhadas sobre como utilizar o Facebook como um provedor de autenticação.
5.	[Introdução ao ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Para habilitar os botões de logon social mostrados na parte superior deste tutorial, consulte [Muitos botões de logon social para ASP.NET MVC 5 ](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/).

O excelente artigo [Introdução ao EF e ao MVC ](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) de Tom Dykstra mostra uma programação de MVC e EF mais avançada.

Este tutorial e o aplicativo de exemplo foram escritos por [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) com a assistência de Tom Dykstra e Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)).

***Deixe comentários*** sobre o que você gostou ou sobre o que você gostaria de ver melhorado, não apenas sobre o próprio tutorial, mas também sobre os produtos que ele demonstra. Seus comentários nos ajudarão a priorizar melhorias. Você também pode solicitar e votar em novos tópicos em [Mostre-me como com código](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, confira: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Using the Membership API]: #mbrDB
[Create a Data Deployment Script]: #ppd
[Update the Membership Database]: #ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxSSL.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT2.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rr1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png

[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png

[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png

[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-migrations-package-manager-console.png


[Important information about ASP.NET in Azure web apps]: #aspnetwindowsazureinfo
[Next steps]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
 

<!----HONumber=July15_HO3-->