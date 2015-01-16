<properties urlDisplayName="Website with SQL Database" pageTitle="Implantar um aplicativo ASP.NET MVC seguro com Associação, OAuth e banco de dados SQL em um site do Azure" metaKeywords="tutorial do Azure hello world, obtendo o tutorial de introdução, tutorial do banco de dados SQL, tutorial do Azure .NET hello world, tutorial do Azure C# hello world, tutorial do SQL Azure C#" description="Aprenda a desenvolver um site ASP.NET MVC 5 com um back-end de banco de dados SQL e implantá-lo no Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title="Deploy a Secure ASP.NET MVC 5 app with Membership, OAuth, and SQL Database to an Azure Website" authors="riande"  solutions="" writer="riande" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="riande" /> 



# Implantar um aplicativo ASP.NET MVC 5 seguro com a associação, o OAuth e o banco de dados SQL em um site do Azure

***Atualizado em 12 de outubro de 2014.***

Este tutorial mostra como criar um aplicativo Web seguro do ASP.NET MVC 5 que permite que os usuários façam logon com credenciais do Google ou do Facebook. Você também implantará o aplicativo no Azure.

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2013, o SDK instalará automaticamente o Visual Studio 2013 para o Web Express. É possível iniciar o desenvolvimento para o Azure de maneira gratuita.

Este tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure. Ao concluir este tutorial, você terá um aplicativo Web seguro controlado por dados e em funcionamento na nuvem utilizando um banco de dados de nuvem.

Você aprenderá a:

* Como criar um projeto ASP.NET MVC 5 seguro e publicá-lo em um site do Azure.
* Como utilizar o [OAuth](http://oauth.net/ "http://oauth.net/") e o banco de dados de associação do ASP.NET para proteger seu aplicativo.
* Como usar um banco de dados SQL para armazenar dados no Azure.

Você criará um aplicativo Web de lista de contatos simples que será criado no ASP.NET MVC 5 e que utilizará o ADO.NET Entity Framework para acesso ao banco de dados. A seguinte ilustração mostra a página de logon do aplicativo concluído:

![login page][rxb]

>[WACOM.NOTE] Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Se não tiver uma conta, você poderá <a href="/pt-br/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">ativar os benefícios de assinante do MSDN</a> ou <a href="/pt-br/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">inscrever-se para uma avaliação gratuita</a>.


Neste tutorial:

- [Configurar o ambiente de desenvolvimento](#setupdevenv)
- [Criar um aplicativo ASP.NET MVC 5][createapplication]
- [Implantar o aplicativo no Azure][deployapp1]
- [Adicionar um banco de dados ao aplicativo][adddb]
- [Adicionar um provedor de OAuth][]
- [Implantar o aplicativo no Azure][deployapp11]
- [Próximas etapas][]


[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

Para utilizar o novo certificado SSL para localhost, você precisará instalar o [Visual Studio 2013 Atualização 3](http://go.microsoft.com/fwlink/?LinkId=390521) ou posterior.

<h2><a name="bkmk_createmvc4app"></a>Criar um aplicativo ASP.NET MVC 5</h2>

### Criar o projeto

1. No menu **Arquivo**, clique em **Novo Projeto**.

	![New Project in File menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

1. Na caixa de diálogo **Novo Projeto**, expanda **C#** e selecione **Web** em **Modelos instalados** e, em seguida, selecione **Aplicativo Web ASP.NET**.

1. Nomeie o aplicativo **ContactManager** e clique em **OK**.

	![New Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
	**Observação:**Certifique-se de inserir "ContactManager". Os blocos de código que você irá copiar posteriormente supõem que o nome do projeto seja ContactManager. 

1. Na caixa de diálogo **Novo projeto do ASP.NET** selecione o modelo **MVC**. Verifique se a **autenticação** está definida para **Contas individuais de usuário*, o **Host na nuvem** é verificado e **Site** está selecionado.

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)

1. O Assistente de configuração sugerirá um nome exclusivo com base em *ContactManager* (consulte a imagem abaixo). Selecione uma região perto de você. É possível utilizar o [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") para localizar o datacenter de latência mais baixa. 
2. Se você não criou um servidor de banco de dados antes, selecione **Criar novo servidor**, digite um nome de usuário de banco de dados e uma senha.

	![Configure Azure Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configAz.PNG)

Se você tiver um servidor de banco de dados, utilize isso para criar um novo banco de dados. Os servidores de banco de dados são um recurso precioso e você geralmente deseja criar vários bancos de dados no mesmo servidor de teste e desenvolvimento em vez de criar um servidor de banco de dados por banco de dados. Verifique se que seu site e o banco de dados estão na mesma região.

![Configure Azure Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configWithDB.PNG)

### Configurar o cabeçalho e o rodapé da página


1. No **Gerenciador de Soluções**, abra o arquivo *Layout.cshtml* na pasta *Views\Shared*.

	![_Layout.cshtml in Solution Explorer][newapp004]

1. Substitua a marcação no arquivo *Layout.cshtml* com o código a seguir. As alterações estão destacadas abaixo.

<pre>
			&lt;!DOCTYPE html&gt;
			&lt;html&gt;
			&lt;head&gt;
			    &lt;meta charset="utf-8" /&gt;
			    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
			    &lt;title&gt;@ViewBag.Title - <mark>Contact Manager</mark>&lt;/title&gt;
			    @Styles.Render("~/Content/css")
			    @Scripts.Render("~/bundles/modernizr")
			
			&lt;/head&gt;
			&lt;body&gt;
			    &lt;div class="navbar navbar-inverse navbar-fixed-top"&gt;
			        &lt;div class="container"&gt;
			            &lt;div class="navbar-header"&gt;
			                &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                &lt;/button&gt;
			                @Html.ActionLink("<mark>CM Demo</mark>", "Index", "<mark>Cm</mark>", new { area = "" }, new { @class = "navbar-brand" })
			            &lt;/div&gt;
			            &lt;div class="navbar-collapse collapse"&gt;
			                &lt;ul class="nav navbar-nav"&gt;
			                    &lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt;
			                &lt;/ul&gt;
			                @Html.Partial("_LoginPartial")
			            &lt;/div&gt;
			        &lt;/div&gt;
			    &lt;/div&gt;
			    &lt;div class="container body-content"&gt;
			        @RenderBody()
			        &lt;hr /&gt;
			        &lt;footer&gt;
			            &lt;p&gt;&amp;copy; @DateTime.Now.Year - <mark>Contact Manager</mark>&lt;/p&gt;
			        &lt;/footer&gt;
			    &lt;/div&gt;
			
			    @Scripts.Render("~/bundles/jquery")
			    @Scripts.Render("~/bundles/bootstrap")
		    @RenderSection("scripts", required: false)
			&lt;/body&gt;
			&lt;/html&gt;
</pre>


![code changes](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)


### Executar o aplicativo localmente

1. Pressione CTRL+F5 para executar o aplicativo.

	A home page do aplicativo é exibida no navegador padrão.

	![Web site running locally](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

Isso é tudo que necessita ser feito, por enquanto, para criar o aplicativo que você implantará no Azure. 

## Habilitar SSL para o projeto ##

1. Habilitar o SSL. No Gerenciador de Soluções, clique no projeto **ContactManager** e clique em F4 para exibir a caixa de diálogo de propriedades. Altere **SSL Habilitado** para verdadeiro. Copie a **URL do SSL**. A URL do SSL será https://localhost:44300/, a menos que você tenha criado sites do SSL anteriormente.

	![enable SSL][rxSSL]
 
1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **Contact Manager** e clique em **Propriedades**.
1. Na guia à esquerda, clique em **Web**.
1. Altere a **Url do projeto** para utilizar a **URL do SSL** e salve a página (Ctrl+S).

	![enable SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. Verifique se o Internet Explorer é o navegador que inicia o Visual Studio, como mostrado na imagem abaixo:

	![default browser](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)

	O seletor de navegador permite que você especifique o navegador que inicia o Visual Studio.

 	![browser selector](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

	É possível selecionar vários navegadores e fazer com que o Visual Studio atualize cada navegador quando você fizer alterações. Para obter mais informações, consulte [Utilizando o link do navegador no Visual Studio 2013 ](http://www.asp.net/visual-studio/overview/2013/using-browser-link).


1. Pressione CTRL+F5 para executar o aplicativo. Siga as instruções para confiar no certificado autoassinado que o IIS Express gerou.

	 ![instructions to trust the self-signed certificate that IIS Express has generated](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

1. Leia a caixa de diálogo **Aviso de segurança** e clique em **Sim** se você deseja instalar o certificado que representa o **localhost**.

 	![localhost IIS Express certificate warning ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1. O IE mostra a *Home* page e não há nenhum aviso do SSL.

	 ![IE with localhost SSL and no warnings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)

	O Google Chrome também aceita o certificado e mostrará o conteúdo de HTTPS sem aviso. O Firefox utiliza seu próprio repositório de certificados, portanto, será exibido um aviso.

	 ![FireFox Cert Warning](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

<h2><a name="bkmk_deploytowindowsazure1"></a>Implantar o aplicativo no Azure</h2>

1. No Visual Studio, clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
	O assistente **Publicar Web** é aberto.

1. Na caixa de diálogo **Publicar Web**, clique em **Publicar**.

	![Publish](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

O aplicativo que você criou agora está em execução na nuvem. Na próxima vez que você implantar o aplicativo, somente os arquivos alterados (ou novos) serão implantados.

	![Running in Cloud](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>Adicionar um banco de dados ao aplicativo</h2>

Em seguida, você atualizará o aplicativo para adicionar a habilidade de exibir e atualizar contatos e armazenar os dados em um banco de dados. O aplicativo utilizará o Entity Framework (EF) para criar o banco de dados e para ler e atualizar dados.

### Adicionar classes de modelo de dados aos contatos

Você começa criando um modelo de dados simples no código.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Modelos, clique em **Adicionar** e, em seguida, em **Classe**.

	![Add Class in Models folder context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2. Na caixa de diálogo **Adicionar novo item**, nomeie o novo arquivo de classe *Contact.cs* e, em seguida, clique em **Adicionar**.

	![Add New Item dialog box][adddb002]

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
A classe **Contacts** define os dados que você armazenará para cada contato, além de uma chave primária, *ContactID*, exigida pelo banco de dados.

### Criar as páginas da web que permitem que os usuários do aplicativo trabalhem com os contatos

O recurso scaffolding do ASP.NET MVC pode gerar automaticamente o código que executa as ações CRUD (criar, ler, atualizar e excluir).

<h2><a name="bkmk_addcontroller"></a>Adicionar um controlador e uma exibição para os dados</h2>

1. Compile o projeto **(Ctrl + Shift + B)**. (Você deve construir o projeto antes de usar o mecanismo de scaffolding.) 
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Controladores e clique em **Adicionar** e, em seguida, em **Controlador**.

	![Add Controller in Controllers folder context menu][addcode001]

5. Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC 5 com modos de exibição, utilizando o EF** e, em seguida, clique em **Adicionar**.
	
	![Add Scaffold dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)


1. Na caixa suspensa **Classe do modelo**, selecione **Contato (ContactManager.Models)**. (Consulte a imagem abaixo).
1. Na **Classe de contexto de dados**, selecione **ApplicationDbContext (ContactManager.Models)**. O **ApplicationDbContext** será utilizado para o banco de dados de associação e nossos dados de contato.
1. Na caixa de entrada de texto **Nome de controlador**, insira "CmController" para o nome do controlador. 

	![New data ctx dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

1. Clique em **Adicionar**.

	O Visual Studio cria métodos de controlador e modos de exibição para as operações de banco de dados CRUD para objetos **Contato**.

## Habilitar migrações, criar o banco de dados, adicionar dados de exemplo e um inicializador de dados ##

A próxima tarefa é habilitar o recurso [Code First Migrations](http://msdn.microsoft.com/library/hh770484.aspx) para criar o banco de dados com base no modelo de dados criado.

1. No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e, em seguida, **Console do Gerenciador de Pacotes**.
	![Package Manager Console in Tools menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		enable-migrations
	O comando **enable-migrations** cria a pasta *Migrations* e coloca nessa pasta um arquivo *Configuration.cs* que pode ser editado para enviar o banco de dados e configurar as migrações. 

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		add-migration Initial


O comando **add-migration Initial** gera um arquivo denominado **&lt;date_stamp&gt;Initial** na pasta *Migrations* que cria o banco de dados. O primeiro parâmetro (**Initial**) é arbitrário e é usado para criar o nome do arquivo. Você pode ver os novos arquivos de classe no **Gerenciador de Soluções**.
	Na classe **Initial**, o método **Up** cria a tabela Contatos e o método **Down** (utilizado quando você deseja retornar ao estado anterior) a descarta.
3. Abra o arquivo *Migrations\Configuration.cs*. 
4. Adicione o seguinte namespace. 

    	 using ContactManager.Models;



5. Substitua o método *Seed* por este código:

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


6. No **Console do Gerenciador de Pacotes**, insira o comando:

		update-database

	![Package Manager Console commands][addcode009]

O **update-database** executa a primeira migração que cria o banco de dados. Por padrão, o banco de dados é criado como um banco de dados LocalDB do SQL Server Express. 

7. Pressione CTRL+F5 para executar o aplicativo e, em seguida, clique no link **Demonstração de CM**, ou navegue até http://localhost:(port#)/Cm. 

O aplicativo mostra os dados de propagação e fornece links de edição, detalhes e exclusão. Você pode criar, editar, excluir e exibir dados.

	![MVC view of data][rx2]



<h2><a name="addOauth"></a>Adicionar um provedor de OAuth2</h2>

[OAuth](http://oauth.net/ "http://oauth.net/") é um protocolo aberto que permite autorização segura em um método simples e padrão da Web, aplicativos móveis e de área de trabalho. O modelo de Internet do ASP.NET MVC usa OAuth para expor o Facebook, Twitter, Google e a Microsoft como provedores de autenticação. Embora este tutorial utilize apenas o Google como o provedor de autenticação, você pode facilmente modificar o código para utilizar qualquer um dos provedores. As etapas para implementar outros provedores são muito semelhantes às etapas que você verá neste tutorial. Para utilizar o Facebook como um provedor de autenticação, consulte meu tutorial [Aplicativo do MVC 5 com o logon do Facebook, Twitter, LinkedIn e Google OAuth2 ](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).

Além da autenticação, o tutorial também utilizará funções para implementar a autorização. Somente os usuários que você adicionar à função *canEdit* poderão alterar dados (isto é, criar, editar ou excluir contatos).

Siga as instruções em meu tutorial [Criar um aplicativo do MVC 5 com o logon do Facebook, twitter, LinkedIn e Google OAuth2 ](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog) em **Criando um aplicativo do Google para o OAuth 2 para configurar um aplicativo do Google para o OAuth2**. Executar e testar o aplicativo para verificar se você pode fazer logon utilizando a autenticação do Google.

<h2><a name="mbrDB"></a>Usando a API de Associação</h2>
Nesta seção, você adicionar um usuário local e a função *canEdit* para o banco de dados de associação. Somente os usuários na função *canEdit* poderão editar os dados. A melhor prática é nomear as funções pelas ações que podem executar, portanto, *canEdit* é preferível do que uma função chamada *admin*. Quando seu aplicativo evoluir, você poderá adicionar novas funções, como *canDeleteMembers*, em vez da menos descritiva *superAdmin*.

1. Abra o arquivo *migrations\configuration.cs* e adicione as seguintes instruções 'using':

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. Adicione o seguinte método **AddUserAndRole** à classe: 

    
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
	The following images shows the changes to *Seed* method:

	![code image](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)

   This code creates a new role called *canEdit*, creates a new local user *user1@contoso.com*, and adds *user1@contoso.com* to the *canEdit* role. For more information, see the [ASP.NET Identity resource page](http://curah.microsoft.com/55636/aspnet-identity).

## Use Temporary Code to Add New Social Login Users to the canEdit Role  ##
In this section you will temporarily modify the **ExternalLoginConfirmation** method in the Account controller to add new users registering with an OAuth provider to the *canEdit* role. We will temporarily modify the **ExternalLoginConfirmation** method to automatically add new users to an administrative role. Until we provide a tool to add and manage roles, we'll use the temporary automatic registration code below. We hope to provide a tool similar to [WSAT](http://msdn.microsoft.com/pt-br/library/ms228053.aspx) in the future which allow you to create and edit user accounts and roles. Later in the tutorial I'll show how you can use **Server Explorer** to add users to roles.  

1. Open the **Controllers\AccountController.cs** file and navigate to the **ExternalLoginConfirmation** method.
1. Add the following call to **AddToRoleAsync** just before the **SignInAsync** call.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   The code above adds the newly registered user to the "canEdit" role, which gives them access to action methods that change (edit) data. An image of the code change is shown below:

   ![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

Later in the tutorial you will deploy the application to Azure, where you will log-on with Google or another third party authentication provider. This will add your newly registered account to the *canEdit* role. Anyone who finds your site's URL and has a Google ID can then register and update your database. To prevent other people from doing that, you can stop the site. You'll be able to verify who is in the *canEdit* role by examining the database.

In the **Package Manager Console** hit the up arrow key to bring up the following command:

		Update-Database

Run the  **Update-Database** command which will run the **Seed** method, and that will run the **AddUserAndRole** you just added. The **AddUserAndRole** will create the user *user1@contoso.com* and add her to the *canEdit* role.

## Protect the Application with SSL and the Authorize Attribute ##

In this section you will apply the [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute.aspx) attribute to restrict access to the action methods. Anonymous users will be able to view the **Index** action method of the home controller only. Registered users will be able to see contact data (The **Index** and **Details** pages of the Cm controller), the About, and the Contact pages. Only users in the *canEdit* role will be able to access action methods that change data.

1. Add the [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute.aspx) filter and the [RequireHttps](http://msdn.microsoft.com/pt-br/library/system.web.mvc.requirehttpsattribute.aspx) filter to the application. An alternative approach is to add the [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute.aspx) attribute and the [RequireHttps](http://msdn.microsoft.com/pt-br/library/system.web.mvc.requirehttpsattribute.aspx) attribute to each controller, but it's considered a security best practice to apply them to the entire application. By adding them globally, every new controller and action method you add will automatically be protected, you won't need to remember to apply them. For more information see [Securing your ASP.NET MVC  App and the new AllowAnonymous Attribute](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Open the *App_Start\FilterConfig.cs* file and replace the *RegisterGlobalFilters* method with the following (which adds the two filters):
		<pre>
        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());</mark>
        }
		</pre>


	The following image shows the changed code:


	The [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute.aspx) filter applied in the code above will prevent anonymous users from accessing any methods in the application. You will use the [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute to opt out of the authorization requirement in a couple methods, so anonymous users can log in and can view the home page. The  [RequireHttps](http://msdn.microsoft.com/pt-br/library/system.web.mvc.requirehttpsattribute.aspx) will require all access to the web app be through HTTPS.

1. Add the [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute to the **Index** method of the Home controller. The [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute enables you to white-list the methods you want to opt out of authorization. An image of a portion of the HomeController is shown below:	

  	![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

2. Do a global search for *AllowAnonymous*, you can see it is used in the log in and registration methods of the Account controller.
1. In *CmController.cs*, add `[Authorize(Roles = "canEdit")]` to the HttpGet and HttpPost methods that change data (Create, Edit, Delete, every action method except Index and Details) in the *Cm* controller. A portion of the completed code is shown below: 

   	![img of code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)



1. If you are still logged in from a previous session, hit the **Log out** link.
1. Click on the **About** or **Contact** links. You will be redirected to the log in page because anonymous users cannot view those pages. 
1. Click the **Register as a new user** link and add a local user with email *joe@contoso.com*. Verify *Joe* can view the Home, About and Contact pages. 

	![login](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

1. Click the *CM Demo* link and verify you see the data. 
1. Click an edit link on the page, you will be redirected to the log in page (because a new local user is not added to the *canEdit* role).
1. Log in as *user1@contoso.com* with password of "P_assw0rd1" (the "0" in "word" is a zero). You will be redirected to the edit page you previously selected. <br/>
   If you can't log in with that account and password, try copying the password from the source code and pasting it. If you still can't log in, check the **UserName** column of the **AspNetUsers** table to verify *user1@contoso.com* was added. 

1. Verify you can make data changes.

<h2><a name="bkmk_deploytowindowsazure11"></a>Implantar o aplicativo no Azure</h2>

1. No Visual Studio, clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publish in project context menu][firsdeploy003]

	O assistente **Publicar Web** é aberto.

1. Clique a guia **Configurações** no lado esquerdo da caixa de diálogo **Publicar Web**. Clique no ícone **v** para selecionar a **Cadeia de conexão remota** para **ApplicationDbContext** e selecione **ContactManagerNN_db**.

   
	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. Em **ContactManagerContext**, selecione **Executar Code First Migrations**.

	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. Clique em **Publicar**.
1. Faça logon como *user1@contoso.com* (com a senha "P_assw0rd1") e verifique se você pode editar dados.
1. Faça logoff.
1. Vá para o [Console de desenvolvedores do Google](https://console.developers.google.com/) e sobre a atualização da guia **Credenciais** redirecione URIS e JavaScript Orgins para utilizar a URL do Azure.
1. Faça logon usando o Google ou o Facebook. Isso irá adicionar a conta do Google ou do Facebook à função **canEdit**. Se você receber um erro de HTTP 400 com a mensagem *A URI de redirecionamento na solicitação: https://contactmanager{my version}.azurewebsites.net/signin-google did not match a registered redirect URI.*, you'll have to wait until the changes you made are propagated. If you get this error after more than a minute, verify the URIs are correct.

### Parar o site para impedir que outras pessoas se registrem  

1. No **Gerenciador de Servidores**, navegue até **Sites**.
4. Clique com o botão direito do mouse em cada instância do Site e selecione **Parar Site**. 

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png) 

	Alternativamente, no Portal de gerenciamento do Azure será possível selecionar o site e clicar no ícone **parar** na parte inferior da página.

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### Remover AddToRoleAsync, publicar e testar

1. Comente ou remova o código a seguir do método **ExternalLoginConfirmation** no Controlador de conta: 
                `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. Compile o projeto (que salva as alterações no arquivo e verifica se não há erros de compilação).
5. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Publicar**.

	   ![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. Clique no botão **Iniciar Visualização**. Somente os arquivos que precisam ser atualizados são implantados.
5. Iniciar o site no Visual Studio ou no Portal. **Não será possível publicar enquanto o site estiver parado**.

	![start web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

5. Volte para o Visual Studio e clique em **Publicar**.
3. Seu aplicativo do Azure é aberto no navegador padrão. Se você efetuou logon, faça logoff para que você possa exibir a página inicial como um usuário anônimo.  
4. Clique no link **Sobre**. Você será redirecionado para a página Logon.
5. Clique no link **Registrar** na página Logon e crie a conta local. Usaremos essa conta local para verificar se você pode acessar as páginas somente leitura, mas não pode acessar páginas que alteram dados (que são protegidas pela função *canEdit*). Mais adiante, no tutorial, removeremos o acesso à conta local. 

	![Register](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

1. Verifique se você pode navegar até as páginas *Sobre* e *Contato*.

	![Log off](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

1. Clique no link **Demonstração de CM** para navegar até o controlador de **Cm**. Alternativamente, é possível acrescentar *Cm* à URL. 

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. Clique em um link de edição. Você será redirecionado para a página de logon. Em **Usar outro serviço para fazer logon**, clique em Google ou Facebook e faça logon com a conta registrada anteriormente. (Se estiver trabalhando rapidamente e o cookie da sessão não expirou, você estará automaticamente conectado à conta do Facebook ou Google utilizados anteriormente.)
2. Verifique se você pode editar dados enquanto estiver conectado nessa conta.
 	**Observação:** Não é possível fazer logon do Google neste aplicativo e fazer logon em outra conta do Google com o mesmo navegador. Se você estiver usando um navegador, precisará navegar até o Google e fazer logoff. É possível fazer logon com outra conta do mesmo autenticador de terceiros (como o Google) utilizando outro navegador.

Se ainda não preencheu o nome e o sobrenome das informações da sua conta do Google, uma NullReferenceException ocorrerá.


## Examine o banco de dados SQL do Azure ##

1. No **Gerenciador de Servidores**, navegue até **ContactDB**.
2. Clique com o botão direito do mouse em **ContactDB** e selecione **Abrir no Explorador de objetos do SQL Server**.
 
	![open in SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)
 
**Observação:** Se não for possível expandir o item **Bancos de dados SQL** e *não* puder visualizar o **ContactDB** do Visual Studio, você deverá seguir as instruções para abrir uma porta de firewall ou um grupo de portas. Siga as instruções em **Configurar regras de firewall do Azure**. Talvez você precise aguardar alguns minutos para acessar o banco de dados depois de adicionar a regra de firewall.
 
1. Clique com o botão direito do mouse na tabela **AspNetUsers** e selecione **Exibir dados**.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. Anote a ID da conta do Google com a qual você se registrou para estar na função **canEdit** e a ID de *user1@contoso.com*. Esses devem ser os únicos usuários na função **canEdit**. (Você verificará isso na próxima etapa.)

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)
 
2. No **Explorador de objetos do SQL Server**, clique com o botão direito do mouse em **AspNetUserRoles** e selecione **Exibir dados**.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
Verifique se as **IDs de usuário** são de *user1@contoso.com e da conta do Google que você registrou. 


## Configurar regras de firewall do Azure ##

Siga as etapas nesta seção se não for possível se conectar ao SQL Azure do Visual Studio, ou se você receber uma caixa de diálogo de erro informando que "Não é possível abrir o servidor".

![firewall error](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

Você precisará adicionar o endereço IP para os IPs permitidos.

1. No Portal do Azure, selecione **Bancos de dados SQL** na guia à esquerda.

	![Select SQL][rx6]

1. Clique em **ContactDB**.

1. Clique no link **Configurar regras de firewall do Azure para esse endereço IP**.

	![firewall rules][rx7]

1. Quando você receber o prompt "O endereço IP atual xxx.xxx.xxx.xxx não está incluído nas regras de firewall existentes. Deseja atualizar as regras de firewall?", clique em **Sim**. Normalmente não é suficiente adicionar esse endereço por trás de alguns firewalls corporativos, você precisará adicionar um intervalo de endereços IP.

A próxima etapa é adicionar um intervalo de endereços IP permitidos.

1. No Portal do Azure, clique em **Bancos de dados SQL**.
1. Selecione a guia **Servidores** e, em seguida, clique no servidor que deseja configurar.

	![Servers tab in Azure ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

1. Clique na guia **Configurar**.

1. Adicione um nome de regra e endereços IP iniciais e finais.

	![ip range][rx9]

1. Na parte inferior da página, clique em **Salvar**.
1. Deixe comentários e avise-me se for necessário adicionar um intervalo de endereços IP para se conectar.

Finalmente, você pode se conectar à instância do Banco de Dados SQL no Pesquisador de Objetos do SQL Server (SSOX)

1. No menu Exibir, clique em **Explorador de objetos do SQL Server**.
1. Clique com o botão direito do mouse em **SQL Server** e selecione **Adicionar SQL Server**.
1. Na caixa de diálogo **Conectar-se ao servidor**, defina a **Autenticação** como **Autenticação do SQL Server**. Você obterá o **Nome do servidor** e o **Logon** no Portal do Azure.
1. Em seu navegador, navegue até o portal e selecione **Bancos de dados SQL**.
1. Selecione o **ContactDB** e, em seguida, clique em **Exibir cadeias de conexão de banco de dados SQL**.
1. Na página **Cadeias de conexão**, copie o **Servidor** e a **ID de usuário**.
 
	![con string](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)
1. Cole os valores de **Servidor** e **ID de usuário** na caixa de diálogo **Conectar-se ao Servidor** no Visual Studio. O valor da **ID do Usuário** vai na entrada **Logon**. Digite a senha que você utilizou para criar o banco de dados SQL.

	![Connect to Server DLG](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

Agora você pode navegar para o BD de contato usando as instruções fornecidas anteriormente.


## Adicionar um usuário à função canEdit editando tabelas do banco de dados

Anteriormente, no tutorial, você utilizou o código para adicionar usuários à função canEdit. Um método alternativo é manipular diretamente os dados nas tabelas de associação. As etapas a seguir mostram como utilizar esse método alternativo para adicionar um usuário a uma função.

2. No **Explorador de objetos do SQL Server**, clique com o botão direito do mouse em **AspNetUserRoles** e selecione **Exibir dados**.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

1. Copie a *RoleId* e cole-a na linha vazia (nova).
	
	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)
	
2. Na tabela **AspNetUsers**, localize o usuário que você deseja colocar na função, copie a *Id* do usuário e cole-a na coluna **UserId** da tabela **AspNetUserRoles**.

Estamos trabalhando em uma ferramenta que irá facilitar bastante o gerenciamento de usuários e funções.


<h2><a name="nextsteps"></a>Próximas etapas</h2>

Siga meus tutoriais que se baseiam neste exemplo:

1.	[Criar um aplicativo Web seguro do ASP.NET MVC 5 com log de redefinição de senha e de confirmação de email](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[Aplicativo do ASP.NET MVC 5 com SMS e emails de autenticação de dois fatores](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[Práticas recomendadas para implantar as senhas e outros dados confidenciais para o ASP.NET e o Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
4.	[Criar um aplicativo do ASP.NET MVC 5 com o Facebook e o Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on ) Isso inclui instruções sobre como adicionar dados de perfil ao banco de dados de registro do usuário e obter instruções detalhadas sobre como utilizar o Facebook como um provedor de autenticação.
5.	[Introdução ao ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Para habilitar os botões de logon social mostrados na parte superior deste tutorial, consulte [Muitos botões de logon social para o ASP.NET MVC 5 ](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/).

O excelente artigo [Introdução ao EF e ao MVC ](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) de Tom Dykstra mostra uma programação de MVC e EF mais avançada.

Este tutorial e o aplicativo de exemplo foram escritos por [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) com a assistência de Tom Dykstra e Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)). 

***Deixe comentários*** sobre o que você gostou ou sobre o que você gostaria de ver melhorado, não apenas sobre o próprio tutorial, mas também sobre os produtos que ele demonstra. Seus comentários nos ajudarão a priorizar melhorias. Você também pode solicitar e votar em novos tópicos em [Mostre-me como com código](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

<!-- bookmarks -->
[Adicionar um provedor de OAuth]: #addOauth
[Usando a API de Associação]:#mbrDB
[Criar um Script de implantação de dados]:#ppd
[Atualizar o banco de dados de associação]:#ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png

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

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png


[Informações importantes sobre o ASP.NET em Sites do Azure]: #aspnetwindowsazureinfo
[Próximas etapas]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
























