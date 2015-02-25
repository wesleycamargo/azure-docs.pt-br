<properties 
	pageTitle="Implantar um aplicativo ASP.NET MVC seguro com associação, OAuth e Banco de Dados SQL em um site do Azure" 
	description="Aprenda a desenvolver um site ASP.NET MVC 5 com um back-end de banco de dados SQL e implantá-lo no Azure." 
	services="web-sites, sql-database" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="1/28/2015" 
	ms.author="riande"/> 



# Implantar um aplicativo ASP.NET MVC 5 seguro com a associação, o OAuth e o banco de dados SQL em um site do Azure

Este tutorial mostra como criar um aplicativo Web seguro do ASP.NET MVC 5 que permite que os usuários façam logon com credenciais do Google ou do Facebook. Você também implantará o aplicativo no Azure.

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2013, o SDK instalará automaticamente o Visual Studio 2013 para o Web Express. É possível iniciar o desenvolvimento para o Azure de maneira gratuita.

Este tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure. Ao concluir este tutorial, você terá um aplicativo Web controlado por dados seguro e em funcionamento na nuvem usando um banco de dados em nuvem.

Você aprenderá a:

* Como criar um projeto ASP.NET MVC 5 seguro e publicá-lo em um site do Azure.
* Como usar [OAuth](http://oauth.net/ "http://oauth.net/") e o banco de dados de associação do ASP.NET para proteger seu aplicativo.
* Como usar um banco de dados SQL para armazenar dados no Azure.

Você criará um aplicativo Web de lista de contatos simples criado no ASP.NET MVC 5 e que usa o ADO.NET Entity Framework para acesso ao banco de dados. A seguinte ilustração mostra a página de logon do aplicativo concluído:

![página de logon][rxb]

>[AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Se não tiver uma conta, você poderá <a href="/pt-br/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">ativar os benefícios de assinante MSDN</a> ou <a href="/pt-br/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">inscrever-se para uma avaliação gratuita</a>. Se você quiser iniciar com Websites do Azure antes de assinar uma conta, acesse <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, em que é possível criar imediatamente um site de iniciante ASP.NET de vida curta nos Websites do Azure gratuitamente. Nenhum cartão de crédito é exigido, sem compromissos.


Neste tutorial:

- [Configurar o ambiente de desenvolvimento](#setupdevenv)
- [Criar um aplicativo ASP.NET MVC 5][createapplication]
- [Implantar o aplicativo no Azure][deployapp1]
- [Adicionar um banco de dados ao aplicativo][adddb]
- [Adicionar um provedor de OAuth][]
- [Implantar o aplicativo no Azure][deployapp11]
- [Próximas etapas][]


[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

Para usar o novo certificado SSL para localhost, você precisará instalar o [Visual Studio 2013 Atualização 3](http://go.microsoft.com/fwlink/?LinkId=390521) ou posterior.

<h2><a name="bkmk_createmvc4app"></a>Criar um aplicativo ASP.NET MVC 5</h2>

### Criar o projeto

1. No menu **Arquivo**, clique em **Novo Projeto**.

	![Novo projeto no menu Arquivo](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

1. Na caixa de diálogo **Novo Projeto**, expanda **C#**, selecione **Web** em **Modelos instalados** e, em seguida, selecione **Aplicativo Web ASP.NET**.

1. Nomeie o aplicativo **ContactManager** e clique em **OK**.

	![Caixa de diálogo Novo Projeto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
	**Observação:** Certifique-se de inserir "ContactManager". Os blocos de código que você irá copiar posteriormente supõem que o nome do projeto seja ContactManager. 

1. Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo **MVC**. Verifique se a **Autenticação** está definida como **Contas de Usuário Individuais*, se **Host na nuvem** está marcado e se **Site** está selecionado.

	![Caixa de diálogo Novo Projeto ASP .NET](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)

1. O assistente de configuração sugerirá um nome exclusivo baseado em  *ContactManager* (consulte a imagem abaixo). Selecione uma região perto de você. Você pode usar o [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") para localizar o data center com a menor latência. 
2. Se você não criou um servidor de banco de dados antes, selecione **Criar novo servidor**, digite um nome de usuário do banco de dados e a senha.

	![Configurar o Website do Azure](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configAz.PNG)

Se você tiver um servidor de banco de dados, use isso para criar um novo banco de dados. Os servidores de banco de dados são um recurso precioso e você geralmente deseja criar vários bancos de dados no mesmo servidor para teste e desenvolvimento em vez de criar um servidor de banco de dados por banco de dados. Certifique-se de que o seu site e banco de dados estejam na mesma região.

![Configurar o Website do Azure](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configWithDB.PNG)

### Configurar o cabeçalho e o rodapé da página


1. No **Gerenciador de Soluções**, abra o arquivo *Layout.cshtml* na pasta *Views\Shared*.

	![_Layout.cshtml no Gerenciador de Soluções][newapp004]

1. Substitua a marcação no arquivo *Layout.cshtml* pelo código a seguir. As alterações estão destacadas abaixo.

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


![alterações de código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)


### Executar o aplicativo localmente

1. Pressione CTRL + F5 para executar o aplicativo.

	A home page do aplicativo é exibida no navegador padrão.

	![Site em execução localmente](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

Isso é tudo que necessita ser feito, por enquanto, para criar o aplicativo que você implantará no Azure. 

## Habilitar SSL para o projeto ##

1. Habilite o SSL. No Gerenciador de Soluções, clique no projeto **ContactManager** e clique em F4 para exibir a caixa de diálogo de propriedades. Altere **SSL Habilitado** para verdadeiro. Copie a **URL do SSL**. A URL do SSL será https://localhost:44300/, a menos que você tenha criado Sites SSL anteriormente.

	![habilitar o SSL][rxSSL]
 
1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **Gerenciador de Contatos** e clique em **Propriedades**.
1. Na guia à esquerda, clique em **Web**.
1. Altere a **Url do projeto** para usar a **URL SSL** e salve a página (Control S).

	![habilitar SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. Verifique se o Internet Explorer é o navegador que inicia o Visual Studio, como mostrado na imagem abaixo:

	![navegador padrão](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)

	O seletor de navegador permite que você especifique o navegador que inicia o Visual Studio.

 	![seletor de navegador](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

	É possível selecionar vários navegadores e fazer com que o Visual Studio atualize cada navegador quando você fizer alterações. Para obter mais informações, consulte [Usando o link do navegador no Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).


1. Pressione CTRL+F5 para executar o aplicativo. Siga as instruções para confiar no certificado autoassinado que o IIS Express gerou.

	 ![instruções para confiar no certificado autoassinado que o IIS Express gerou](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

1. Leia a caixa de diálogo **Aviso de Segurança** e clique em **Sim** se você deseja instalar o certificado que representa o **localhost**.

 	![Aviso de certificado do IIS Express para localhost ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1. IE mostra a página *Home* e não há nenhum aviso de SSL.

	 ![IE com SSL do localhost e sem avisos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)

	O Google Chrome também aceita o certificado e mostrará o conteúdo de HTTPS sem aviso. O Firefox usa seu próprio repositório de certificados, portanto, será exibido um aviso.

	 ![Aviso do FireFox Cert](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

<h2><a name="bkmk_deploytowindowsazure1"></a>Implantar o aplicativo no Azure</h2>

1. No Visual Studio, clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publicar no menu de contexto do projeto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
   O assistente de **Publicar Web** é aberto.

1. Na caixa de diálogo **Publicar Web**, clique em **Publicar**.

	![Publicar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

	O aplicativo que você criou agora está em execução na nuvem. Na próxima vez que você implantar o aplicativo, somente os arquivos alterados (ou novos) serão implantados.

	![Executando na nuvem](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>Adicionar um banco de dados ao aplicativo</h2>

Em seguida, você atualizará o aplicativo para adicionar a habilidade de exibir e atualizar contatos e armazenar os dados em um banco de dados. O aplicativo usará o Entity Framework (EF) para criar o banco de dados e ler e atualizar os dados.

### Adicionar classes de modelo de dados aos contatos

Você começa criando um modelo de dados simples no código.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Modelos, clique em **Adicionar**e, em seguida, em **Classe**.

	![Adicionar Classe no menu de contexto da pasta Modelos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2. Na caixa de diálogo **Adicionar Novo Item**, nomeie o novo arquivo de classe  *Contact.cs* e clique em **Adicionar**.

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

O recurso scaffolding do ASP.NET MVC pode gerar automaticamente o código que executa as ações (CRUD) (criar, ler, atualizar e excluir).

<h2><a name="bkmk_addcontroller"></a>Adicionar um controlador e uma visualização para os dados</h2>

1. Compile o projeto **(Ctrl + Shift + B)**. (Você deve compilar o projeto antes de usar o mecanismo de scaffolding.) 
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Controladores e clique em **Adicionar** e, em seguida, em **Controlador**.

	![Adicionar Controlador no menu de contexto da pasta Controladores][addcode001]

5. Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC 5 com modos de exibição, usando o EF** e, em seguida, clique em **Adicionar**.
	
	![Adicionar Scaffold dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)


1. Na caixa suspensa **Classe do modelo**, selecione **Contato (ContactManager.Models)**. ((Consulte a imagem abaixo).)
1. Em **Classe do contexto de dados**, selecione **ApplicationDbContext (ContactManager.Models)**. O **ApplicationDbContext** será usado para o BD de associação e nossos dados de contato.
1. Na caixa de entrada de texto **Nome de controlador**, insira "CmController" para o nome do controlador. 

	![Novos dados ctx dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

1. Clique em **Adicionar**.

   O Visual Studio cria métodos do controlador e modos de exibição para as operações de banco de dados CRUD para objetos **Contact**.

## Habilitar migrações, criar o banco de dados, adicionar dados de exemplo e um inicializador de dados ##

A próxima tarefa é habilitar o recurso [Codificar Primeiras Migrações](http://msdn.microsoft.com/library/hh770484.aspx) para criar o banco de dados com base no modelo de dados criado.

1. No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e, em seguida, **Console do Gerenciador de Pacotes**.
	![Console do Gerenciador de Pacotes no menu Ferramentas](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		enable-migrations
	O comando **enable-migrations** cria uma pasta *Migrations* e coloca nessa pasta um arquivo *Configuration.cs* que pode ser editado para propagar o banco de dados e configurar migrações. 

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		add-migration Initial


	O comando **inicial add-migration** gera um arquivo chamado **&lt;date_stamp&gt;inicial** na pasta *Migrations* que cria o banco de dados. O primeiro parâmetro (**Initial** ) é arbitrário e é usado para criar o nome do arquivo. Você pode ver os novos arquivos de classe no **Gerenciador de Soluções**.
	Na classe **Initial**, o método **Up** cria a tabela Contatos, e o método **Down** (usado quando você deseja retornar ao estado anterior) a descarta.
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

	Esse código inicializa (propaga) o banco de dados com as informações de contato. Para obter mais informações sobre a propagação do banco de dados, consulte [Propagando e depurando (BDs do Entity Framework )EF](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


6. Em **Console do Gerenciador de Pacotes**, digite o comando:

		update-database

	![Comandos do Console do Gerenciador de Pacotes][addcode009]

	O **update-database** executa a primeira migração que cria o banco de dados. Por padrão, o banco de dados é criado como um banco de dados LocalDB do SQL Server Express. 

7. Pressione CTRL+F5 para executar o aplicativo e, em seguida, clique no link **Demonstração de CM**, ou navegue até http://localhost:(port#)/Cm. 

	O aplicativo mostra os dados de semente e fornece links de edição, detalhes e exclusão. Você pode criar, editar, excluir e exibir dados.

	![Exibição de dados do MVC][rx2]



<h2><a name="addOauth"></a>Adicionar um provedor de OAuth2</h2>

O [OAuth](http://oauth.net/ "http://oauth.net/") é um protocolo aberto que permite autorização segura em um método simples e padrão da web, aplicativos móveis e de área de trabalho. O modelo de Internet do ASP.NET MVC usa OAuth para expor o Facebook, Twitter, Google e a Microsoft como provedores de autenticação. Embora este tutorial use apenas o Google como o provedor de autenticação, você pode facilmente modificar o código para usar qualquer um desses provedores. As etapas para implementar outros provedores são muito semelhantes às etapas que você verá neste tutorial. Para usar o Facebook como um provedor de autenticação, consulte o tutorial [aplicativo MVC 5 com o Facebook, Twitter, LinkedIn e logon do Google OAuth2 ](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).

Além da autenticação, o tutorial também usará funções para implementar a autorização. Somente os usuários que você adicionar à função *canEdit* poderão alterar dados (isto é, criar, editar ou excluir contatos).

Siga as instruções no meu tutorial [aplicativo MVC 5 com o Facebook, Twitter, LinkedIn e logon do Google OAuth2 ](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog) em **Criando um aplicativo do Google para OAuth 2 para configurar um aplicativo do Google para OAuth2**. Execute e teste o aplicativo para verificar se você pode fazer logon usando a autenticação do Google.

<h2><a name="mbrDB"></a>Usando a API de Associação</h2>
Nesta seção, você adicionará um usuário local e a função *canEdit* ao banco de dados de associação. Somente os usuários na função *canEdit* poderão editar os dados. A melhor prática é nomear as funções pelas ações que podem executar, portanto, *canEdit* é preferível do que uma função chamada *admin*. Quando seu aplicativo evoluir você pode adicionar novas funções, como *canDeleteMembers* em vez da *superAdmin*menos descritiva.

1. Abra o arquivo *migrations\configuration.cs* e adicione as seguintes `using` instruções:

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
	As imagens a seguir mostram as alterações no método *Seed*:

	![imagem de código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)

   Esse código cria uma nova função chamada *canEdit*, cria um novo usuário local *user1@contoso.com*e adiciona *user1@contoso.com* à função *canEdit*. Para obter mais informações, consulte a página de recursos de [Identidade do ASP.NET](http://curah.microsoft.com/55636/aspnet-identity).

##Usar código temporário para adicionar novos usuários de logon social à função canEdit##
Nesta seção, você modificará temporariamente o método **ExternalLoginConfirmation** no controlador de conta para adicionar novos usuários com registro com um provedor OAuth na função *canEdit*. Vamos modificar temporariamente o método **ExternalLoginConfirmation** para adicionar automaticamente novos usuários a uma função administrativa. Até que possamos fornecer uma ferramenta para adicionar e gerenciar funções, vamos usar o código de registro automático temporário abaixo. No futuro, esperamos fornecer uma ferramenta semelhante à [WSAT](http://msdn.microsoft.com/pt-br/library/ms228053.aspx) que permita criar e editar contas de usuário e funções. Mais adiante no tutorial, mostrarei como você pode usar o **Gerenciador de Servidores** para adicionar usuários a funções.  

1. Abra o arquivo **Controladores\AccountController.cs** e navegue até o método **ExternalLoginConfirmation**.
1. Adicione a seguinte chamada para **AddToRoleAsync** logo antes da chamada **SignInAsync**.

                aguarde UserManager.AddToRoleAsync(user.ID, "canEdit");

   O código acima adiciona o usuário recém-registrado à função "canEdit", que oferece acesso a métodos de ação que alteram (editam) dados. Uma imagem da alteração de código é mostrada abaixo:

   ![código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

Mais tarde no tutorial você implantará o aplicativo no Azure, onde você fará logon no Google ou outro provedor de autenticação de terceiros. Isso adicionará a conta recém-registrada à função *canEdit*. Qualquer pessoa que localize a URL do site e que tenha uma ID do Google poderá registrar-se e atualizar seu banco de dados. Para impedir que outras pessoas façam isso, você pode parar o site. Você poderá verificar quem está na função *canEdit* examinando o banco de dados.

No **Console do Gerenciador de Pacotes** pressione a tecla de seta para cima para exibir o seguinte comando:

		Update-Database

Execute o comando **Update-Database** que executará o método **Seed** e o **AddUserAndRole** que você acabou de adicionar. O **AddUserAndRole** criará o usuário *user1@contoso.com* e o adicionará para a função *canEdit*.

## Proteger o aplicativo com SSL e o atributo Authorize ##

Nesta seção, você aplicará o atributo [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute.aspx) para restringir o acesso aos métodos de ação. Os usuários anônimos poderão exibir o método de ação **Índice** do controlador doméstico apenas. Os usuários registrados poderão ver os dados de contato (as páginas **Índice** e **Detalhes** do controlador de Cm), as páginas Sobre e Contato. Somente usuários na função  *canEdit* poderão acessar os métodos de ação que alteram dados.

1. Adicione o filtro [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute.aspx) e o filtro [RequireHttps](http://msdn.microsoft.com/pt-br/library/system.web.mvc.requirehttpsattribute.aspx) ao aplicativo. Uma abordagem alternativa é adicionar os atributos [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute.aspx) e [RequireHttps](http://msdn.microsoft.com/pt-br/library/system.web.mvc.requirehttpsattribute.aspx) a cada controlador, mas aplicá-los a todo o aplicativo é considerado uma melhor prática de segurança. Adicionando-os globalmente, cada novo controlador e método de ação que você adicionar será automaticamente protegido, você não precisará se lembrar de aplicá-los. Para obter mais informações, consulte [Protegendo seu aplicativo ASP.NET MVC e o novo atributo AllowAnonymous ](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Abra o arquivo *App_Start\FilterConfig.cs* e substitua o método  *RegisterGlobalFilters* pelo seguinte (que adiciona os dois filtros):
		<pre>
        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());</mark>
        }
		</pre>




	O filtro [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute.aspx) aplicado no código acima impedirá que usuários anônimos acessem qualquer método no aplicativo. Você usará o atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) para cancelar o requisito de autorização em alguns métodos, portanto, os usuários anônimos poderão efetuar logon e exibir a home page. O [RequireHttps](http://msdn.microsoft.com/pt-br/library/system.web.mvc.requirehttpsattribute.aspx) exigirá que todo acesso ao aplicativo Web seja feito por HTTPS.

1. Adicione o atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) ao método **Index** para o controlador Home. O atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) permite criar a lista branca dos métodos para os quais você deseja recusar autorização. Uma imagem de parte do HomeController é mostrada abaixo:	

  	![código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

2. Execute uma pesquisa global por *AllowAnonymous*, você pode ver que ele é usado no logon e nos métodos de registro do controlador de conta.
1. Em *CmController.cs*, adicione `[Authorize(Roles = "canEdit")]` aos métodos HttpGet e HttpPost que alteram dados (Criar, editar, Excluir, cada método de ação exceto Índice e Detalhes) no controlador *Cm*. Uma parte do código concluído é mostrada abaixo: 

   	![imagem do código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)



1. Se você ainda estiver conectado em uma sessão anterior, pressione o link **Logoff**.
1. Clique nos links **Sobre** ou **Contato**. Você será redirecionado para a página de logon, porque usuários anônimos não podem exibir essas páginas. 
1. Clique no link **Registrar-se como um novo usuário** e adicione um usuário local com email *joe@contoso.com*. Verifique se *Joe* pode exibir as páginas Inicial, Sobre e Contato. 

	![logon](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

1. Clique no link *CM Demo* e verifique se você vê os dados. 
1. Clique em um link de edição na página, você será redirecionado para a página ( (porque um novo usuário local não é adicionado à função *canEdit*).
1. Faça logon como *user1@contoso.com* com senha "P_assw0rd1" (o "0" em "word" é um zero). Você será redirecionado para a página de edição selecionada anteriormente. <br/>
   Se você não puder efetuar logon com essa conta e senha, tente copiar a senha do código-fonte e colá-la. Se ainda não for possível efetuar logon, verifique a coluna **UserName** da tabela **AspNetUsers** para verificar se  *user1@contoso.com* foi adicionado. 

1. Verifique se você pode fazer alterações nos dados.

<h2><a name="bkmk_deploytowindowsazure11"></a>Implantar o aplicativo no Azure</h2>

1. No Visual Studio, clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publicar no menu de contexto do projeto][firsdeploy003]

	O assistente de **Publicar Web** é aberto.

1. Clique a guia **Configurações** no lado esquerdo da caixa de diálogo **Publicar Web**. Clique no ícone **v** para selecionar a **Cadeia de conexão remota** para **ApplicationDbContext** e selecione **ContactManagerNN_db**.

   
	![configurações](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. Em **ContactManagerContext**, selecione **Executar Codificar Migrações Iniciais**.

	![configurações](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. Clique em **Publicar**.
1. Faça logon como *user1@contoso.com* (com a senha "P_assw0rd1") e verifique se você pode editar dados.
1. Faça logoff.
1. Vá para o [Console para Desenvolvedores do Google](https://console.developers.google.com/) e na guia **Credenciais** atualize os URIS de redirecionamento e JavaScript Orgins para usar a URL do Azure.
1. Faça logon usando o Google ou o Facebook. Isso irá adicionar a conta do Google ou do Facebook à função **canEdit**. Se você receber um erro de HTTP 400 com a mensagem *o URI de redirecionamento na solicitação: https://contactmanager{my version}.azurewebsites.net/signin-google um URI de redirecionamento registrado não coincidem.*, você terá de esperar até que as alterações são propagadas. Se você receber esse erro depois de mais de um minuto, verifique se os URIs estão corretas.

### Parar o site para impedir que outras pessoas se registrem  

1. No No **Gerenciador de Servidores**, navegue até **Websites**.
4. Clique com o botão direito do mouse em cada instância do site e selecione **Parar Website**. 

	![parar site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png) 

	Como alternativa, no portal de gerenciamento do Azure, você pode selecionar o site, clique no ícone **parar** na parte inferior da página.

	![parar site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### Remover AddToRoleAsync, publicar e testar

1. Comente ou remova o código a seguir do método **ExternalLoginConfirmation** no controlador de Conta: 
                `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. Compile o projeto (que salva as alterações no arquivo e verifica se não há erros de compilação).
5. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Publicar**.

	   ![Publicar no menu de contexto do projeto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. Clique no botão **Iniciar Visualização**. Somente os arquivos que precisam ser atualizados são implantados.
5. Iniciar o site no Visual Studio ou no Portal. **Não será possível publicar enquanto o site estiver parado**.

	![iniciar site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

5. Volte para o Visual Studio e clique em **Publicar**.
3. O seu aplicativo do Azure é aberto no navegador padrão. Se você efetuou logon, faça logoff para que você possa exibir a página inicial como um usuário anônimo.  
4. Clique no link **Sobre**. Você será redirecionado para a página Logon.
5. Clique no link **Registrar** na página Logon e crie a conta local. Usaremos essa conta local para verificar se você pode acessar as páginas somente leitura, mas não pode acessar páginas que alteram dados (que são protegidas pela função *canEdit*). Mais adiante, no tutorial, removeremos o acesso à conta local. 

	![Registrar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

1. Verifique se você pode navegar até as páginas *About* e *Contact*.

	![Fazer logoff](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

1. Clique no link **Demonstração de CM** para navegar até o controlador de **Cm**. Como alternativa, você pode acrescentar *Cm* para a URL. 

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. Clique em um link de edição. Você será redirecionado para a página de logon. Em **Usar outro serviço para fazer logon**, clique em Google ou Facebook e faça logon com a conta registrada anteriormente. (Se estiver trabalhando rapidamente e o cookie da sessão não expirou, você estará automaticamente conectado à conta do Facebook ou Google utilizados anteriormente.)
2. Verifique se você pode editar dados enquanto estiver conectado nessa conta.
 	**Observação::** não é possível fazer logon do Google neste aplicativo e fazer logon em outra conta do google com o mesmo navegador. Se você estiver usando um navegador, precisará navegar até o Google e fazer logoff. É possível fazer logon com outra conta do mesmo autenticador de terceiros (como o Google) utilizando outro navegador.

Se ainda não preencheu o nome e o sobrenome das informações da sua conta do Google, uma NullReferenceException ocorrerá.


## Examine o BD do SQL Azure ##

1. No **Gerenciador de Servidores**, navegue até **ContactDB**
2. Clique com o botão direito do mouse em **ContactDB** e selecione **Abrir no Pesquisador de Objetos do SQL Server**.
 
	![abrir no SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)
 
**Observação::** se você não puder expandir os **Bancos de Dados SQL** e *can't*não puder ver o **ContactDB** do Visual Studio, siga as instruções abaixo para abrir uma porta ou um intervalo de portas de firewall. Siga as instruções em **Configurar regras de firewall do Azure**. Talvez você precise aguardar alguns minutos para acessar o banco de dados depois de adicionar a regra de firewall.
 
1. Clique com o botão direito do mouse na guia **AspNetUsers** e selecione **Exibir Dados**.

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. Observe a ID da conta do Google com a qual você se registrou para estar na função **canEdit** e a ID de *user1@contoso.com*. Esses devem ser os únicos usuários na função **canEdit**. (Você verificará isso na próxima etapa.)

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)
 
2. No **Pesquisador de Objetos do SQL Server**, clique com o botão direito do mouse em **AspNetUserRoles** e selecione **Exibir Dados**.

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
Verifique se os **UserId**s são de *user1@contoso.com* e a conta Google que você registrou. 


## Configurar regras de firewall do Azure ##

Siga as etapas nesta seção se não for possível se conectar ao SQL Azure do Visual Studio, ou se você receber uma caixa de diálogo de erro informando que "Não é possível abrir o servidor".

![erro do firewall](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

Você precisará adicionar o endereço IP para os IPs permitidos.

1. No Portal do Azure, selecione **Bancos de dados SQL** na guia esquerda.

	![Selecione SQL][rx6]

1. Clique em **ContactDB**.

1. Clique no link **Configurar regras de firewall do Azure para esse endereço IP**.

	![regras de firewall][rx7]

1. Quando você receber o prompt "O endereço IP atual xxx.xxx.xxx.xxx não está incluído nas regras de firewall existentes. Deseja atualizar as regras de firewall?", clique em **Sim**. Normalmente não é suficiente adicionar esse endereço por trás de alguns firewalls corporativos, você precisará adicionar um intervalo de endereços IP.

A próxima etapa é adicionar um intervalo de endereços IP permitidos.

1. No Portal do Azure, clique em **Bancos de Dados SQL**.
1. Selecione a guia **Servidores** e, em seguida, clique no servidor que deseja configurar.

	![Guia de servidores no Azure ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

1. Clique na guia **Configurar**.

1. Adicione um nome de regra e endereços IP iniciais e finais.

	![intervalo IP][rx9]

1. Na parte inferior da página, clique em **Salvar**.
1. Deixe comentários e avise-me se for necessário adicionar um intervalo de endereços IP para se conectar.

Finalmente, você pode se conectar à instância do Banco de Dados SQL no Pesquisador de Objetos do SQL Server (SSOX)

1. No menu Exibir, clique em **Pesquisador de Objetos do SQL Server**.
1. Clique com o botão direito do mouse em **SQL Server** e selecione **Adicionar SQL Server**.
1. Na caixa de diálogo **Conectar-se ao servidor**, defina a **Autenticação** como **Autenticação do SQL Server**. Você obterá o **Nome do servidor** e o **Logon** no Portal do Azure.
1. Em seu navegador, navegue até o portal e selecione **Bancos de Dados SQL**.
1. Selecione o **ContactDB** e, em seguida, clique em **Exibir cadeias de conexão do Banco de Dados SQL**.
1. Na página **Cadeias de Conexão**, copie o **Servidor** e a **ID do usuário**.
 
	![cadeia de conexão](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)
1. Cole os valores de **Servidor** e **ID de usuário** na caixa de diálogo **Conectar-se ao Servidor** no Visual Studio. O valor da **ID do Usuário** vai na entrada **Logon**. Digite a senha que você usou para criar o BD SQL.

	![Conectar-se à DLG do servidor](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

Agora você pode navegar para o BD de contato usando as instruções fornecidas anteriormente.


## Adicionar um usuário à função canEdit editando tabelas do banco de dados

Anteriormente, no tutorial, você utilizou o código para adicionar usuários à função canEdit. Um método alternativo é manipular diretamente os dados nas tabelas de associação. As etapas a seguir mostram como usar esse método alternativo para adicionar um usuário a uma função.

2. No **Pesquisador de Objetos do SQL Server**, clique com o botão direito do mouse em **AspNetUserRoles** e selecione **Exibir Dados**.

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

1. Copie a *RoleId* e cole-a na linha vazia (nova).
	
	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)
	
2. Na tabela **AspNetUsers**, localize o usuário que você deseja colocar na função, copie a *Id* do usuário e cole-a na coluna **UserId** da tabela **AspNetUserRoles**.

Estamos trabalhando em uma ferramenta que facilitará bastante o gerenciamento de usuários e funções.


<h2><a name="nextsteps"></a>Próximas etapas</h2>

Siga meus tutoriais que se baseiam neste exemplo:

1.	[Criar um aplicativo Web ASP.NET MVC 5 seguro com logon, confirmação de email e redefinição de senha, confirmação de email](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[Aplicativo ASP.NET MVC 5 com Autenticação Multifator de SMS e email](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[Práticas recomendadas para implantar senhas e outros dados confidenciais no ASP.NET e no Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)
4.	[Criar um aplicativo do ASP.NET MVC 5 com Google OAuth2 e Facebook](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on ) Inclui instruções sobre como adicionar dados de perfil ao banco de dados de registro do usuário e para obter instruções detalhadas sobre como usar o Facebook como um provedor de autenticação.
5.	[Introdução ao ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Para habilitar os botões de logon social mostrados na parte superior deste tutorial, consulte [Muitos botões de logon social para ASP.NET MVC 5](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/).

O excelente artigo [Introdução ao EF e ao MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) de Tom Dykstra mostra uma programação de MVC e EF mais avançada.

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


























<!--HONumber=42-->
