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
	ms.date="10/07/2015" 
	ms.author="riande"/>

# Criar um aplicativo ASP.NET MVC com autenticação e o banco de dados SQL e implantar no Serviço de Aplicativo do Azure

Este tutorial mostra como criar um aplicativo Web ASP.NET MVC 5 seguro que permite que os usuários façam logon com credenciais do Google ou do Facebook. O aplicativo é uma lista de contatos simples que usa o ADO.NET Entity Framework para acesso ao banco de dados. Você implantará o aplicativo no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Ao concluir o tutorial, você terá um aplicativo Web controlado por dados seguro e em funcionamento na nuvem usando um banco de dados em nuvem. A ilustração a seguir mostra a página de logon do aplicativo concluído.

![página de logon][rxb]

O que você aprenderá:

* Como criar um projeto Web ASP.NET MVC 5 seguro no Visual Studio.
* Como autenticar e autorizar usuários que fazem logon com credenciais de suas contas do Google ou Facebook (autenticação de provedor social usando [OAuth 2.0](http://oauth.net/2 "http://oauth.net/2")).
* Como autenticar e autorizar usuários que se registram em um banco de dados gerenciado pelo aplicativo (autenticação local usando [ASP.NET Identity](http://asp.net/identity/)).
* Como usar o ADO.NET Entity Framework 6 Code First para ler e gravar dados em um banco de dados SQL.
* Como usar Entity Framework Code First Migrations para implantar um banco de dados.
* Como armazenar dados relacionais na nuvem usando o Banco de Dados SQL do Azure.
* Como implantar um projeto Web que usa um banco de dados em um [aplicativo Web](http://go.microsoft.com/fwlink/?LinkId=529714) no Serviço de Aplicativo do Azure.

>[AZURE.NOTE]Este é um tutorial longo. Se quiser ver uma breve introdução a projetos da Web do Serviço de Aplicativo do Azure e do Visual Studio, consulte [Criar um aplicativo Web ASP.NET no Serviço de Aplicativo do Azure](web-sites-dotnet-get-started.md).
>
>Ou se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

## Pré-requisitos

Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [ativar os benefícios de assinante MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) ou [inscrever-se para uma avaliação gratuita](/pricing/free-trial/?WT.mc_id=A261C142F).

Para configurar o ambiente de desenvolvimento, você deverá instalar o [Visual Studio 2013 Atualização 4](http://go.microsoft.com/fwlink/?LinkId=390521) ou superior e a versão mais recente do [SDK do Azure para .NET](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409). Este artigo foi escrito para o Visual Studio Atualização 4 e para o SDK 2.7.1. As mesmas instruções servem para o Visual Studio 2015 com a versão mais recente do [SDK do Azure para .NET](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409) instalado, mas algumas telas terão uma aparência diferente das ilustrações.

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

1. Clique em **OK**.

3. Quando o diálogo **Definir as configurações de aplicativo Web do Microsoft Azure** aparecer, verifique se você está conectado ao Azure; entre, se ainda não tiver feito isso, ou digite novamente suas credenciais, se o logon tiver expirado.

	![Digite novamente as credenciais](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/reentercredentials.png)

2. Se você quiser especificar um nome para seu aplicativo Web, altere o valor na caixa **nome do aplicativo Web**.

	A URL do aplicativo Web será {nome}.azurewebsites.net; portanto, o nome precisa ser exclusivo no domínio azurewebsites.net. O assistente de configuração sugere um nome exclusivo adicionando um número ao nome do projeto "ContactManager" e que serve para este tutorial.

5. No menu suspenso **plano do Serviço de Aplicativo**, selecione **Criar novo plano do Serviço de Aplicativo** e digite um nome como "StandardWeb", conforme mostrado na ilustração.

	Se preferir, você pode selecionar um plano de Serviço de Aplicativo já existente. Para obter informações sobre os planos do Serviço de Aplicativo, consulte [Visão geral dos planos de Serviço de Aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. No menu suspenso **Grupo de recursos**, selecione **Criar novo grupo de recursos** e digite um nome como "ExampleMVC", conforme mostrado na ilustração.

	Se preferir, você pode selecionar um grupo de recursos que já possui. Mas se você criar um novo grupo de recursos e só usá-lo para este tutorial, será mais fácil excluir todos os recursos do Azure que você criou para o tutorial quando terminar de usá-los. Para mais informações sobre grupos de recursos, consulte [Visão geral do Gerenciador de Recursos do Azure](../resource-group-overview.md).

7. Selecione uma região perto de você.

	Não clique ainda em **OK**. Na próxima etapa, você irá configurar o recurso de banco de dados. A caixa de diálogo agora se parece com a ilustração a seguir.

	![Novo plano e grupo de recursos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newplanandgroup.png)
 
2. Selecione **Criar novo servidor**, insira um nome de servidor, nome de usuário e senha.

	O nome do servidor tem que ser exclusivo. Ele pode conter letras minúsculas, números e hifens. Ele não pode conter um hífen final. O nome de usuário e a senha são novas credenciais que você está criando para o novo servidor.

	Se você já tiver um servidor de banco de dados, pode selecioná-lo em vez de criar um. Os servidores de banco de dados são um recurso precioso e você geralmente deseja criar vários bancos de dados no mesmo servidor de teste e desenvolvimento em vez de criar um servidor de banco de dados por banco de dados. No entanto, para este tutorial, você somente precisa do servidor temporariamente e, ao criar o servidor no mesmo grupo de recursos do site da Web, fica mais fácil excluir os recursos de banco de dados e de aplicativos Web com a exclusão do grupo de recursos quando você conclui o tutorial.

	Se você selecionar um servidor de banco de dados existente, verifique se seu aplicativo Web e o banco de dados estão na mesma região.

	![Usar novo banco de dados](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newdb.png)

4. Clique em **OK**.

	O Visual Studio cria o projeto Web ContactManager, o grupo de recursos e o plano do Serviço de Aplicativo especificado, e cria um aplicativo Web no Serviço de Aplicativo do Azure com o nome especificado.

### Configurar o cabeçalho e o rodapé da página

1. No **Gerenciador de Soluções**, abra o arquivo *Layout.cshtml* na pasta *Views\\Shared*.

	![\_Layout.cshtml no Gerenciador de Soluções][newapp004]

1. Substitua o conteúdo do arquivo *Layout.cshtml* pelo código a seguir.

		<!DOCTYPE html>
		<html>
		<head>
		    <meta charset="utf-8" />
		    <meta name="viewport" content="width=device-width, initial-scale=1.0">
		    <title>@ViewBag.Title - Contact Manager</title>
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		
		</head>
		<body>
		    <div class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		                @Html.ActionLink("CM Demo", "Index", "Cm", new { area = "" }, new { @class = "navbar-brand" })
		            </div>
		            <div class="navbar-collapse collapse">
		                <ul class="nav navbar-nav">
		                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
		                    <li>@Html.ActionLink("About", "About", "Home")</li>
		                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
		                </ul>
		                @Html.Partial("_LoginPartial")
		            </div>
		        </div>
		    </div>
		    <div class="container body-content">
		        @RenderBody()
		        <hr />
		        <footer>
		            <p>&copy; @DateTime.Now.Year - Contact Manager</p>
		        </footer>
		    </div>
		
		    @Scripts.Render("~/bundles/jquery")
		    @Scripts.Render("~/bundles/bootstrap")
		    @RenderSection("scripts", required: false)
		</body>
		</html>

	Esse código altera o nome do aplicativo no cabeçalho e no rodapé de "Meu aplicativo ASP.NET" e "Nome do aplicativo" para "Contact Manager" e "Demonstração de CM".
 
### Executar o aplicativo localmente

1. Pressione CTRL+F5 para executar o aplicativo.

	A home page do aplicativo é exibida no navegador padrão.

	![Aplicativo Web em execução local](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr2.png)

Isso é tudo o que você precisa fazer por enquanto para criar o aplicativo que você implantará no Azure.

## Implantar o aplicativo no Azure

1. No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publicar no menu de contexto do projeto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
	O assistente de **Publicar Web** é aberto.

1. Na caixa de diálogo **Publicar na Web**, clique em **Publicar**.

	![Publicar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr3.png)

	O aplicativo que você criou agora está em execução na nuvem. Na próxima vez que você implantar o aplicativo, somente os arquivos alterados (ou novos) serão implantados.

	![Executando na nuvem](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss4.PNG)

## Habilitar SSL para o projeto ##

1. No **Gerenciador de Soluções**, clique no projeto **ContactManager** e clique em F4 para exibir a janela **Propriedades**.

3. Altere **SSL Habilitado** para **Verdadeiro**.

4. Copie a **URL do SSL**.

	A URL do SSL será https://localhost:44300/, a menos que você tenha criado aplicativos Web SSL anteriormente.

	![habilitar SSL][rxSSL]
 
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Gerenciador de Contatos** e clique em **Propriedades**.

1. Clique na guia **Web**.

1. Altere a **URL do Projeto** para usar a **URL de SSL** e salve a página (Ctrl+S).

	![habilitar SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr1.png)
 
1. Verifique se o Internet Explorer é o navegador que inicia o Visual Studio, como mostrado na imagem abaixo:

	![navegador padrão](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss12.PNG)

	O seletor de navegador permite que você especifique o navegador que inicia o Visual Studio. É possível selecionar vários navegadores e fazer com que o Visual Studio atualize cada navegador quando você fizer alterações. Para saber mais, confira [Utilizando o link do navegador no Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).

 	![seletor de navegador](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss13.png)

1. Pressione CTRL+F5 para executar o aplicativo. Clique em **Sim**para iniciar o processo de confiar no certificado autoassinado que o IIS Express gerou.

	 ![instruções para confiar no certificado autoassinado que o IIS Express gerou](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss26.PNG)

1. Leia o diálogo **Aviso de Segurança** e clique em **Sim**, se desejar instalar o certificado que representa o **localhost**.

 	![Aviso de certificado do IIS Express para localhost](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss27.PNG)

1. O IE mostra a *Home page* e não há nenhum aviso de SSL.

	 ![IE com SSL do localhost e sem avisos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss28.PNG)

	 O Internet Explorer é uma boa opção quando você estiver usando SSL porque ele aceita o certificado e mostra o conteúdo HTTPS sem aviso. Microsoft Edge e Google Chrome também aceitam o certificado. O Firefox usa seu próprio repositório de certificados; portanto, é exibido um aviso.

	 ![Aviso do FireFox Cert](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss30.PNG)

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
A classe **Contact** define os dados que você armazenará para cada contato, além de uma chave primária, *ContactID*, que é necessária para o banco de dados.

### Criar as páginas da web que permitem que os usuários do aplicativo trabalhem com os contatos

O recurso scaffolding do ASP.NET MVC pode gerar automaticamente o código que executa as ações CRUD (criar, ler, atualizar e excluir).

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

   O Visual Studio cria um controlador com métodos e modos de exibição para as operações de banco de dados CRUD para objetos **Contact**.

## Habilitar migrações, criar o banco de dados, adicionar dados de exemplo e um inicializador de dados ##

A próxima tarefa é habilitar o recurso [Codificar Primeiras Migrações](http://msdn.microsoft.com/library/hh770484.aspx) para criar o banco de dados com base no modelo de dados criado.

1. No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e, em seguida, **Console do Gerenciador de Pacotes**.

	![Console do Gerenciador de Pacotes no menu Ferramentas](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/SS6.png)

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		enable-migrations

	O comando **enable-migrations** cria a pasta *Migrações* e coloca nessa pasta um arquivo *Configuration.cs*, que pode ser editado para propagar o banco de dados e configurar Migrações.

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		add-migration Initial


	O comando **add-migration Initial** gera um arquivo chamado **&lt;carimbo\_de\_data/hora&gt;Initial** na pasta *Migrações*. O código nesse arquivo cria as tabelas de banco de dados. O primeiro parâmetro (**Initial**) é usado para criar o nome do arquivo. Você pode ver os novos arquivos de classe no **Gerenciador de Soluções**.

	Na classe **Inicial**, o método **Up** cria a tabela Contatos e o método **Down** (usado quando você deseja retornar ao estado anterior) a descarta.

3. Abra o arquivo *Migrations\\Configuration.cs*.

4. Adicione a instrução `using` a seguir:

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

	Esse código inicializa (propaga) o banco de dados com informações de contato. Para obter mais informações sobre a propagação do banco de dados, consulte [Propagando e depurando BDs do Entity Framework (EF) ](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).

6. Em **Console do Gerenciador de Pacotes**, digite o comando:

		update-database

	![Comandos do Console do Gerenciador de Pacotes][addcode009]

	O **update-database** executa a primeira migração que cria o banco de dados. Por padrão, o banco de dados é criado como um banco de dados LocalDB do SQL Server Express.

7. Pressione CTRL+F5 para executar o aplicativo e, em seguida, clique no link **Demonstração de CM** ou navegue até https://localhost:(port#)/Cm.

	O aplicativo mostra os dados de semente e fornece links de edição, detalhes e exclusão. Você pode criar, editar, excluir e exibir dados.

	![Exibição do MVC de dados][rx2]

## Adicionar um provedor de OAuth2

O [OAuth](http://oauth.net/ "http://oauth.net/") é um protocolo aberto que permite autorização segura em um método simples e padrão da Web, de aplicativos móveis e de área de trabalho. O modelo de Internet do ASP.NET MVC usa OAuth para expor o Facebook, Twitter, Google e a Microsoft como provedores de autenticação. Embora este tutorial utilize apenas o Google como o provedor de autenticação, você pode facilmente modificar o código para utilizar qualquer um dos provedores. As etapas para implementar outros provedores são muito semelhantes às etapas que você vê neste tutorial. Para utilizar o Facebook como um provedor de autenticação, consulte [Aplicativo do MVC 5 com o logon OAuth2 do Facebook, do Twitter, do LinkedIn e do Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).

Além da autenticação, o tutorial também usa funções para implementar a autorização. Somente os usuários que você adicionar à função *canEdit* podem alterar dados (isto é, criar, editar ou excluir contatos).

1. Siga as instruções em [Criar um aplicativo do MVC 5 com o logon OAuth2 do Facebook, do Twitter, do LinkedIn e do Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog) em **Criando um aplicativo do Google para o OAuth 2 para configurar um aplicativo do Google para o OAuth2**.

3. Execute e teste o aplicativo para verificar se você pode fazer logon usando a autenticação do Google.

2. Se quiser criar botões de logon social com ícones específicos do provedor, consulte [Botões de logon social para ASP.NET MVC 5](http://www.jerriepelser.com/blog/pretty-social-login-buttons-for-asp-net-mvc-5)

## Usando a API de Associação

Nesta seção, você irá adicionar um usuário local e a função *canEdit* ao banco de dados de associação. Somente os usuários na função *canEdit* poderão editar os dados. A melhor prática é nomear as funções pelas ações que podem executar, portanto, *canEdit* é preferível do que uma função chamada *admin*. Quando seu aplicativo evoluir, você poderá adicionar novas funções, como *canDeleteMembers*, em vez da menos descritiva *superAdmin*.

1. Abra o arquivo *migrations\\configuration.cs* e adicione as seguintes instruções `using`:

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

		protected override void Seed(ContactManager.Models.ApplicationDbContext context)
		{
		    AddUserAndRole(context);
		    context.Contacts.AddOrUpdate(p => p.Name,
		        // Code removed for brevity
		}

	As imagens a seguir mostram as alterações do método *Seed*:

	![imagem de código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss24.PNG)

	Esse código cria uma nova função chamada *canEdit*, cria um novo usuário local **user1@contoso.com* e adiciona **user1@contoso.com* à função *canEdit*. Para saber mais, consulte os [tutoriais do ASP.NET Identity](http://www.asp.net/identity/overview/features-api) no site do ASP.NET.

## Usar código temporário para adicionar novos usuários de logon social à função canEdit  ##

Nesta seção, você modificará temporariamente o método **ExternalLoginConfirmation** no controlador de conta para adicionar novos usuários com registro com um provedor OAuth na função *canEdit*. No futuro, esperamos fornecer uma ferramenta semelhante à [WSAT](http://msdn.microsoft.com/library/ms228053.aspx) que permita criar e editar contas de usuário e funções. Até lá, você pode realizar a mesma função usando o código temporário.

1. Abra o arquivo **Controllers\\AccountController.cs** e navegue para o método **ExternalLoginConfirmation**.

1. Adicione a seguinte chamada para **AddToRoleAsync** antes da chamada **SignInAsync**.

		await UserManager.AddToRoleAsync(user.Id, "canEdit");

   O código acima adiciona o usuário recém-registrado à função "canEdit", que oferece acesso a métodos de ação que alteram (editam) dados. O trecho a seguir mostra a nova linha de código no contexto.

		  // POST: /Account/ExternalLoginConfirmation
		  [HttpPost]
		  [AllowAnonymous]
		  [ValidateAntiForgeryToken]
		  public async Task ExternalLoginConfirmation(ExternalLoginConfirmationViewModel model, string returnUrl)
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
		              await UserManager.AddToRoleAsync(user.Id, "canEdit");
		              await SignInManager.SignInAsync(user, isPersistent: false, rememberBrowser: false);
		              return RedirectToLocal(returnUrl);
		           }
		        }
		        AddErrors(result);
		     }
		     ViewBag.ReturnUrl = returnUrl;
		     return View(model);
		  }

Mais tarde no tutorial você implantará o aplicativo no Azure, onde você fará logon no Google ou outro provedor de autenticação de terceiros. Isso adicionará a conta recém-registrada à função *canEdit*. Qualquer pessoa que localize a URL do aplicativo Web e que tenha uma ID do Google poderá registrar-se e atualizar seu banco de dados. Para impedir que outras pessoas façam isso, você pode parar o site. Você poderá verificar quem está na função *canEdit* examinando o banco de dados.

No **Console do Gerenciador de Pacotes** pressione a tecla de seta para cima para exibir o seguinte comando:

		Update-Database

O comando **Update-Database** executa o método **Seed**, que executa o método **AddUserAndRole** adicionado anteriormente. O método **AddUserAndRole** cria a usuária **user1@contoso.com* e a adiciona à função *canEdit*.

## Proteger o aplicativo com SSL e o atributo Authorize ##

Nesta seção, você aplica o atributo [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) para restringir o acesso aos métodos de ação. Os usuários anônimos poderão exibir somente o método de ação **Índice** do controlador doméstico. Os usuários registrados poderão ver os dados de contato (as páginas **Índice** e **Detalhes** do controlador de Cm), as páginas Sobre e Contato. Somente usuários na função *canEdit* poderão acessar os métodos de ação que alteram dados.

1. Abra o arquivo *App\_Start\\FilterConfig.cs* e substitua o método *RegisterGlobalFilters* pelo seguinte (que adiciona os dois filtros):

		public static void RegisterGlobalFilters(GlobalFilterCollection filters)
		{
		    filters.Add(new HandleErrorAttribute());
		    filters.Add(new System.Web.Mvc.AuthorizeAttribute());
		    filters.Add(new RequireHttpsAttribute());
		}
		
	Esse código adiciona o filtro [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) e o filtro [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) ao aplicativo. O filtro [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) evita que usuários anônimos acessem métodos no aplicativo. Você usará o atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) para cancelar o requisito de autorização em alguns métodos, portanto, os usuários anônimos poderão efetuar logon e exibir a home page. O [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) exige que todo acesso ao aplicativo Web seja feito por HTTPS.

	Uma abordagem alternativa é adicionar os atributos [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) e [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) a cada controlador, mas aplicá-los a todo o aplicativo é considerado uma melhor prática de segurança. Adicionando-os globalmente, cada novo controlador e método de ação que você adicionar é automaticamente protegido; você não precisa se lembrar de aplicá-los. Para obter mais informações, consulte [Protegendo seu aplicativo ASP.NET MVC e o novo atributo AllowAnonymous ](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx).

1. Adicione o atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) ao método **Index** para o controlador Início. O atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) permite criar a lista branca dos métodos para os quais você deseja recusar autorização.

		public class HomeController : Controller
		{
		  [AllowAnonymous]
		  public ActionResult Index()
		  {
		     return View();
		  }

	Se você fizer uma pesquisa global por *AllowAnonymous*, verá que ele é usado no logon e nos métodos de registro do controlador Conta.

1. Em *CmController.cs*, adicione `[Authorize(Roles = "canEdit")]` aos métodos HttpGet e HttpPost que alteram dados (Create, Edit, Delete, todos os métodos de ação exceto Index e Details) no controlador *Cm*. Uma parte do código concluído é mostrada abaixo:

		// GET: Cm/Create
		[Authorize(Roles = "canEdit")]
		public ActionResult Create()
		{
		   return View(new Contact { Address = "123 N 456 W",
		    City="Great Falls", Email = "ab@cd.com", Name="Joe Smith", State="MT",
		   Zip = "59405"});
		}
		// POST: Cm/Create
		// To protect from overposting attacks, please enable the specific properties you want to bind to, for 
		// more details see http://go.microsoft.com/fwlink/?LinkId=317598.
		[HttpPost]
		[ValidateAntiForgeryToken]
		 [Authorize(Roles = "canEdit")]
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
		[Authorize(Roles = "canEdit")]
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
		
1. Pressione CTRL+F5 para executar o aplicativo.

1. Se você ainda estiver conectado de uma sessão anterior, clique no link **Logoff**.

1. Clique nos links **Sobre** ou **Contato**. Você é redirecionado para a página de logon, porque usuários anônimos não podem exibir essas páginas.

1. Clique no link **Registrar-se como um novo usuário** e adicione um usuário local com o email **joe@contoso.com*. Verifique se *Joe* pode exibir as páginas Home, Sobre e Contato.

	![logon](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss14.PNG)

1. Clique em *Demonstração de CM* e verifique se você vê os dados.

1. Clique em um link de edição na página, você será redirecionado para a página de logon (porque um novo usuário local não é adicionado à função *canEdit*).

1. Faça logon como **user1@contoso.com* com a senha "P\_assw0rd1" (o "0" em "word" é um zero). Você é redirecionado para a página de edição selecionada anteriormente.

	Se você não puder efetuar logon com essa conta e senha, tente copiar a senha do código-fonte e colá-la. Se ainda não for possível efetuar logon, verifique a coluna **UserName** da tabela **AspNetUsers** para verificar se **user1@contoso.com* foi adicionado.

1. Verifique se você pode fazer alterações nos dados.

## Implantar o aplicativo no Azure

1. No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publicar no menu de contexto do projeto][firsdeploy003]

	O assistente de **Publicar Web** é aberto.

1. Clique a guia **Configurações** no lado esquerdo da caixa de diálogo **Publicar web**.

2. Clique no ícone **v** para selecionar a **cadeia de conexão remota** para **ApplicationDbContext** e selecione o banco de dados que você criou quando você criou o projeto.
   
	![configurações](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc2.png)

1. Em **ContactManagerContext**, selecione **Executar as migrações iniciais de código**.

	![configurações](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc3.png)

1. Clique em **Publicar**.

1. Faça logon como **user1@contoso.com* (com a senha "P\_assw0rd1") e verifique se você pode editar dados.

1. Faça logoff.

1. Vá para o [Console de desenvolvedores do Google](https://console.developers.google.com/) e sobre a atualização da guia **Credenciais** redirecione URIS e JavaScript Orgins para utilizar a URL do Azure.

1. Faça logon usando o Google ou o Facebook. Isso irá adicionar a conta do Google ou do Facebook à função **canEdit**. Se você receber um erro HTTP 400 com a mensagem *O URI de redirecionamento na solicitação: https://contactmanager{my version}.azurewebsites.net/signin-google não correspondeu a um URI de redirecionamento registrado.*, você deverá esperar até que as alterações feitas por você sejam propagadas. Se você receber esse erro depois de mais de alguns minutos, verifique se os URIs estão corretos.

### Parar o aplicativo Web para impedir que outras pessoas se registrem  

1. Em **Server Explorer**, navegue até **Azure > Serviço de Aplicativo > {seu grupo de recursos} > {seu aplicativo Web}**.

4. Clique com o botão direito do mouse no aplicativo Web e selecione **Parar**.

	Como alternativa, no [portal de visualização do Azure](https://portal.azure.com/), vá até a folha do aplicativo Web e clique no ícone **Parar** na parte superior da folha.

	![parar portal do aplicativo Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/stopweb.png)

### Remover AddToRoleAsync, publicar e testar

1. Comente ou remova o código a seguir do método **ExternalLoginConfirmation** no controlador de Conta:

		await UserManager.AddToRoleAsync(user.Id, "canEdit");

1. Compile o projeto (que salva as alterações no arquivo e verifica se não há erros de compilação).

5. No Visual Studio, clique com o botão direito no nome do projeto no **Gerenciador de Soluções** e selecione **Publicar**.

	   ![Publicar no menu de contexto do projeto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
4. Clique no botão **Iniciar Visualização**. Somente os arquivos que precisam ser atualizados são implantados.

5. Inicie o aplicativo Web no Visual Studio ou do Portal. **Você não poderá publicar enquanto o aplicativo Web estiver parado**.

	![iniciar aplicativo Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss15.png)

5. Volte para o Visual Studio e clique em **Publicar**.

3. O seu aplicativo do Azure é aberto no navegador padrão. Se você efetuou logon, faça logoff para que você possa exibir a página inicial como um usuário anônimo.

4. Clique no link **Sobre**. Você será redirecionado para a página Logon.

5. Clique no link **Registrar** na página Logon e crie a conta local. Usaremos essa conta local para verificar se você pode acessar as páginas somente leitura, mas não pode acessar páginas que alteram dados (que são protegidas pela função *canEdit*). Mais adiante, no tutorial, você removerá o acesso à conta local.

	![Registrar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss16.PNG)

1. Verifique se você pode navegar até as páginas *Sobre* e *Contato*.

	![Fazer logoff](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss17.PNG)

1. Clique no link **Demonstração de CM** para navegar até o controlador de **Cm**. Como alternativa, você pode acrescentar *Cm* à URL.

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr4.png)
 
1. Clique em um link de edição.

	Você é redirecionado à página de logon.

2. Em **Usar outro serviço para fazer logon**, clique em Google ou Facebook e faça logon com a conta registrada anteriormente. (Se estiver trabalhando rapidamente e o cookie da sessão não expirou, você estará automaticamente conectado à conta do Facebook ou Google usados anteriormente.)

2. Verifique se você pode editar dados enquanto estiver conectado nessa conta.

	**Observação:** não é possível fazer logout do Google neste aplicativo e fazer logon em outra conta do Google com o mesmo navegador. Se você estiver usando um navegador, precisará navegar até o Google e fazer logoff. Você pode fazer logon com outra conta do mesmo autenticador de terceiros (como o Google) usando outro navegador.

	Se ainda não preencheu o nome e o sobrenome das informações da sua conta do Google, uma NullReferenceException ocorrerá.

## Examine o BD do SQL Azure ##

1. Em **Server Explorer**, navegue até **Azure > Bancos de Dados SQL > {seu banco de dados}**

2. Clique com o botão direito do mouse em seu banco de dados e selecione **Abrir no Gerenciador de Objetos do SQL Server**.
 
	![abrir no SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr12.png)
 
3. Se você não tiver se conectado a esse banco de dados anteriormente, será preciso adicionar uma regra de firewall para permitir o acesso para seu endereço IP atual. O endereço IP será preenchido com antecedência. Basta clicar em **Adicionar Regra de Firewall** para habilitar o acesso.

	![adicionar regra de firewall](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/addfirewallrule.png)

3. Efetue o login no banco de dados com o nome de usuário e a senha que você especificou ao criar o servidor de banco de dados.
 
1. Clique com o botão direito do mouse na tabela **AspNetUsers** e selecione **Exibir Dados**.

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr8.png)
 
1. Observe a ID da conta do Google com a qual você se registrou para estar na função **canEdit** e a ID do **user1@contoso.com*. Esses devem ser os únicos usuários na função **canEdit**. (Você verificará isso na próxima etapa.)

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s2.png)
 
2. No **Pesquisador de Objetos do SQL Server**, clique com o botão direito do mouse em **AspNetUserRoles** e selecione **Exibir Dados**.

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rs1.png)
 
3. Verifique se a **UserId** é de **user1@contoso.com* e da conta do Google que você registrou.

## Próximas etapas

Você criou um aplicativo Web ASP.NET MVC básico que autentica usuários. Para saber mais sobre tarefas comuns de autenticação e como proteger dados confidenciais, consulte os tutoriais a seguir.

- [Criar um aplicativo Web seguro do ASP.NET MVC 5 com log de redefinição de senha e de confirmação de email](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
- [Aplicativo do ASP.NET MVC 5 com SMS e emails de autenticação de dois fatores](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
- [Práticas recomendadas para implantar as senhas e outros dados confidenciais para o ASP.NET e o Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
- [Criar um aplicativo do ASP.NET MVC 5 com o Facebook e o Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on) Isso inclui instruções sobre como adicionar dados de perfil ao banco de dados de registro do usuário e obter instruções detalhadas sobre como utilizar o Facebook como um provedor de autenticação.
- [Introdução ao ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Para obter um tutorial mais avançado sobre como usar o Entity Framework, consulte [Introdução ao EF e ao MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

Este tutorial foi escrito por [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) com a assistência de Tom Dykstra e Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)).

***Deixe comentários*** sobre o que você gostou ou sobre o que você gostaria de ver melhorado, não apenas sobre o próprio tutorial, mas também sobre os produtos que ele demonstra. Seus comentários nos ajudarão a priorizar melhorias. Você também pode solicitar e votar em novos tópicos em [Mostre-me como com código](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

## O que mudou

* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a alteração do portal do Azure para o portal de visualização do Azure, consulte: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

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
 

<!---HONumber=Oct15_HO3-->