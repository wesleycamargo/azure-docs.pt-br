<properties linkid="dev-net-tutorials-web-app-with-sql-azure-vs2013" urlDisplayName="Site com banco de dados SQL" pageTitle="Implantar um aplicativo ASP.NET MVC seguro com associação, OAuth e banco de dados SQL para um Site do Windows Azure" metaKeywords="Tutorial hello world do Azure, tutorial de introdução ao Azure, tutorial do banco de dados SQL, tutorial de hello world do .NET no Azure, tutorial hello world do C# no Azure, Tutorial do C# para banco de dados SQL no Azure" description="Saiba como desenvolver um site ASP.NET MVC 5 com um banco de dados SQL back-end e implantá-lo no Windows Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title=" OAuth" authors=""  solutions="" writer="riande" manager="wpickett" editor="mollybos"  />



# Implantar um aplicativo ASP.NET MVC 5 seguro com Associação, OAuth e banco de dados SQL em um Site do Windows Azure

***Por [Rick Anderson](https://twitter.com/RickAndMSFT) e Tom Dykstra. Atualizado em 18 de outubro de 2013.***


<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/develop/net/tutorials/web-site-with-sql-database/" title="Visual Studio 2013" class="current">Visual Studio 2013</a><a href="/pt-br/develop/net/tutorials/web-site-with-sql-database-vs2012/" title="Visual Studio 2012">Visual Studio 2012</a></div>

Este tutorial mostra como criar um aplicativo Web ASP.NET MVC 5 seguro que permite que os usuários façam logon com credenciais do Google ou do Facebook. Você implantará o aplicativo no Windows Azure.

É possível abrir uma conta do Windows Azure gratuitamente e, se você ainda não tiver o Visual Studio 2013, o SDK instalará automaticamente o Visual Studio 2013 for Web Express. É possível iniciar o desenvolvimento para o Windows Azure de maneira gratuita. Se você desejar usar o Visual Studio 2012, consulte o [tutorial anterior](/pt-br/develop/net/tutorials/web-site-with-sql-database-vs2012/). Esta versão do tutorial é muito mais simples do que a versão anterior.

Este tutorial pressupõe que você não tem nenhuma experiência anterior com o Windows Azure. Ao concluir este tutorial, você terá um aplicativo da Web controlado por dados seguro e em funcionamento na nuvem usando um banco de dados em nuvem.

Você aprenderá:

* Como criar um projeto ASP.NET MVC 5 seguro e publicá-lo em um Site do Windows Azure.
* Como usar [OAuth](http://oauth.net/ "http://oauth.net/"), [OpenID](http://openid.net/) e o banco de dados de associação do ASP.NET para proteger seu aplicativo.
* Como usar a nova API de associação para adicionar usuários e funções.
* Como usar um banco de dados SQL para armazenar dados no Windows Azure.

Você irá criar um aplicativo Web de lista de tarefas simples criado no ASP.NET MVC 5 e que usa o ADO.NET Entity Framework para acesso ao banco de dados. A seguinte ilustração mostra a página de logon do aplicativo concluído:

![página de logon][rxb]

<div class="dev-callout"><p><strong>Observação</strong> para concluir este tutorial, você precisará de uma conta do Windows Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">Avaliação gratuita do Windows Azure</a>.</p></div>


Neste tutorial:

- [Configurar o ambiente de desenvolvimento](#setupdevenv)
- [Configurar o ambiente do Windows Azure][setupwindowsazureenv]
- [Criar um aplicativo ASP.NET MVC 5][createapplication]
- [Implantar o aplicativo no Windows Azure][deployapp1]
- [Adicionar um banco de dados ao aplicativo][adddb]
- [Adicionar um provedor de OAuth][]
- [Usando a API de Associação][]
- [Implantar o aplicativo no Windows Azure][deployapp11]
- [Próximas etapas][]


[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]


<h2><a name="bkmk_setupwindowsazure"></a>Configurar o ambiente do Windows Azure</h2>

Em seguida, configure o ambiente do Windows Azure criando um Site do Windows Azure e um banco de dados SQL.

### Criar um site e um banco de dados SQL no Windows Azure

O Site do Windows Azure será executado em um ambiente de hospedagem compartilhado, o que significa que ele é executado em VMs (máquinas virtuais) que são compartilhadas com outros clientes do Windows Azure. Um ambiente de hospedagem compartilhado é uma maneira de começar na nuvem a um baixo custo. Mais tarde, se seu tráfego da Web aumentar, o aplicativo poderá ser dimensionado para atender a necessidade executando em VMs dedicadas. Se precisar de uma arquitetura mais complexa, você poderá migrar para um Serviço de Nuvem do Windows Azure. Os serviços de nuvem são executados em VMs dedicadas que podem ser configuradas de acordo com suas necessidades.

O Banco de dados SQL no Windows Azure é um serviço de banco de dados relacional baseado em que é fundamentado em tecnologias do SQL Server. As ferramentas e os aplicativos que funcionam com o SQL Server também funcionam com o Banco de dados SQL.

1. No [Portal de Gerenciamento do Windows Azure](https://manage.windowsazure.com), clique em **Sites da Web** na guia à esquerda e clique em **Novo**.

	![Novo botão no Portal de Gerenciamento][rxWSnew]

1. Clique em **Site** e, em seguida, clique em **Criação Personalizada**.

	![Criar com link de banco de dados no Portal de Gerenciamento][rxCreateWSwithDB] 

	O assistente de **Novo Site - Criação Personalizada** é aberto. 

1. Na etapa **Criar Site** do assistente, insira uma cadeia de caracteres na caixa **URL** a ser usada como a URL exclusiva de seu aplicativo. A URL completa consistirá no que você digitar aqui mais o sufixo que você vê ao lado da caixa de texto. A ilustração mostra uma URL que provavelmente já é utilizada e, portanto, você precisa escolher outra.

	![Criar com link de banco de dados no Portal de Gerenciamento][rr1]

1. Na lista suspensa **Banco de Dados**, escolha **Criar um banco de dados SQL de 20 MB gratuito**.

1. Na lista suspensa **Região**, escolha a mesma região que você selecionou para o site.
Essa configuração especifica qual data center sua VM executará. 
1. Na caixa **Nome da cadeia de conexão do banco de dados**, deixe o valor padrão de *DefaultConnection*.
1. Clique na seta que aponta para a direita na parte inferior da caixa.
O assistente avança para a etapa **Especificar configurações de banco de dados**.

1. Na caixa **Nome**, digite *ContactDB*. (consulte a imagem abaixo). 
1. Na caixa **Servidor**, selecione **Novo Servidor do Banco de Dados SQL**. (consulte a imagem abaixo). Como alternativa, se tiver criado um banco de dados SQL Server, você poderá selecionar esse SQL Server no controle suspenso.
1. Defina a **Região** como a mesma área na qual você criou o Site.
1. Insira um **Nome de Logon** e **Senha** de administrador. Se você selecionou **Novo servidor do Banco de Dados SQL** você irá digitar um nome e senha existentes aqui, você irá inserir um novo nome e uma senha que você está definindo agora para usar mais tarde ao acessar o banco de dados. Se você tiver selecionado um SQL Server criado anteriormente, será solicitada a senha para o nome da conta do SQL Server anterior que você criou. Para este tutorial, não selecionamos a caixa **Avançado**.  Para um banco de dados gratuito, você pode definir somente o agrupamento.
1. Clique na marca de seleção no canto inferior direito da caixa para indicar que você concluiu.

	![Etapa de configurações do banco de dados do novo Site - Criar com o assistente de banco de dados][setup007]
	
	A imagem a seguir mostra o uso de um SQL Server e logon existentes.

	![Etapa de configurações do banco de dados do novo Site - Criar com o assistente de banco de dados][rxPrevDB]

	O Portal de Gerenciamento retorna para a página Sites da Web e a coluna **Status** mostra que o site está sendo criado. Depois de um tempo (normalmente menos de um minuto), a coluna **Status** mostra que o site foi criado com êxito. Na barra de navegação à esquerda, o número de sites presentes na conta é exibido próximo ao ícone **Sites da Web**, e o número de bancos de dados aparece ao lado do ícone **Bancos de Dados SQL**.

<h2><a name="bkmk_createmvc4app"></a>Criar um aplicativo ASP.NET MVC 5</h2>

Você criou um Site do Windows Azure, mas ainda não há conteúdo nele. A próxima etapa é criar o projeto de aplicativo Web do Visual Studio que será publicado no Windows Azure.

### Criar o projeto

2. No menu **Arquivo**, clique em **Novo Projeto**.

   ![Novo projeto no menu Arquivo](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

3. Na caixa de diálogo **Novo Projeto**, expanda **C#** e selecione **Web** em **Modelos Instalados** e, em seguida, selecione **Aplicativo Web ASP.NET**.


4. Nomeie o aplicativo **ContactManager** e clique em **OK**.

   ![Caixa de diálogo Novo Projeto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
   **Observação:** a imagem mostra "MyExample" como o nome, mas certifique-se de inserir "ContactManager". Os blocos de código que você irá copiar posteriormente supõem que o nome do projeto seja ContactManager. 

5. Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo **MVC** e clique em **Alterar Autenticação**.

   ![Caixa de diálogo Novo Projeto ASP .NET](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13changeauth.png)

6. Mantenha o padrão **Contas de Usuário Individuais** na caixa de diálogo **Alterar Autenticação**. 

   A caixa de diálogo explica que **Contas de Usuário Individuais** é para os aplicativos que armazenam perfis de usuário em um banco de dados SQL onde os usuários podem registrar-se usando suas contas de Facebook, Twitter e Google existentes. Para obter informações sobre outras opções de autenticação, consulte [Criando projetos da Web ASP.NET no Visual Studio 2013 - métodos de autenticação (a página pode estar em inglês)](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#auth).

7. Clique em **OK**.

5. Na caixa de diálogo **Novo Projeto ASP .NET**, clique em **OK**.

     ![Caixa de diálogo Novo Projeto ASP .NET](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13changeauth.png)


### Definir o cabeçalho e o rodapé da página


1. No **Gerenciador de Soluções**, abra o arquivo *Layout.cshtml* na pasta *Views\Shared*.
	![_Layout.cshtml no Gerenciador de Soluções][newapp004]
1. Substitua as duas ocorrências de "Meu Aplicativo ASP.NET MVC" por "Gerenciador de Contatos".
1. Substitua "Nome do aplicativo" por "Demonstração de CM". 
2. Atualize o primeiro link de ação e substitua *Home* por *Cm* para usar o Controlador de *Cm*.

![alterações de código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)


### Executar o aplicativo localmente

1. Pressione CTRL+F5 para executar o aplicativo.

	A home page do aplicativo é exibida no navegador padrão.

![Site em execução localmente](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

Isso é tudo o que você precisa fazer por enquanto para criar o aplicativo que você implantará no Windows Azure. Posteriormente, você adicionará a funcionalidade do banco de dados.

<h2><a name="bkmk_deploytowindowsazure1"></a>Implantar o aplicativo no Windows Azure</h2>

5. No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

   ![Publicar no menu de contexto do projeto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
   O assistente de **Publicar Web** é aberto.

6. Na guia **Perfil** do assistente de **Publicar Web**, clique em **Importar**.

   ![Importar configurações de publicação][ImportPublishSettings]

   A caixa de diálogo **Importar Perfil de Publicação** é aberta.

5. Use um dos seguintes métodos para habilitar o Visual Studio para se conectar à sua conta do Windows Azure.

   
	* Clique em **Entrar** e, em seguida, insira as credenciais de sua conta do Windows Azure.

		Esse método é mais rápido e fácil, mas se usar esse método, você não poderá ver o Banco de Dados SQL ou os Serviços Móveis no Windows Azure na janela **Gerenciador de Servidores**.

	* Clique em **Gerenciar assinaturas** para instalar um certificado de gerenciamento que permita o acesso à sua conta.

		Na caixa de diálogo **Gerenciar Assinaturas do Windows Azure**, clique na guia **Certificados** e, em seguida, clique em **Importar**. Siga as instruções para baixar e importar um arquivo de assinatura (também chamado de arquivo *.publishsettings*) para a sua conta do Windows Azure.

		> [WACOM.NOTE]
		> Baixe o arquivo de assinatura em uma pasta fora de seus diretórios de código-fonte (por exemplo, na pasta Downloads) e exclua-o após a conclusão da importação. Um usuário mal-intencionado que obtenha acesso ao arquivo de assinatura pode editar, criar e excluir os serviços do Windows Azure.

		Para obter mais informações, consulte [Conectar-se ao Windows Azure no Visual Studio (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=324796).

7. Na caixa de diálogo **Importar Perfil de publicação**, selecione o site na lista suspensa e clique em **OK**.

![Importar Perfil de Publicação](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs4.png)

1. Na caixa de diálogo **Publicar Web**, clique em **Publicar**.

	![Publicar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

	O aplicativo que você criou agora está em execução na nuvem. Na próxima vez que você implantar o aplicativo, somente os arquivos alterados (ou novos) serão implantados.

<h2><a name="bkmk_addadatabase"></a>Adicionar um banco de dados ao aplicativo</h2>

Em seguida, você atualizará o aplicativo MVC para adicionar a habilidade de exibir e atualizar contatos e armazenar os dados em um banco de dados. O aplicativo usará o Entity Framework para criar o banco de dados e ler e atualizar dados no banco de dados.

### Adicionar classes de modelo de dados para os contatos

Você começa criando um modelo de dados simples no código.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Modelos, clique em **Adicionar**e, em seguida, em **Classe**.

![Adicionar Classe no menu de contexto da pasta Modelos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

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

### Criar páginas da Web que permitem que os usuários do aplicativo trabalhem com os contatos

O recurso scaffolding do ASP.NET MVC pode gerar automaticamente o código que executa as ações CRUD (criar, ler, atualizar e excluir).

<h2><a name="bkmk_addcontroller"></a>Adicionar um controlador e um modo de exibição para os dados</h2>

1. Compile o projeto **(Ctrl + Shift + B)**. (Você deve construir o projeto antes de usar o mecanismo de scaffolding.) 
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Controladores e clique em **Adicionar** e, em seguida, em **Controlador**.

	![Adicionar Controlador no menu de contexto da pasta Controladores][addcode001]

5. Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC 5 com modos de exibição, usando o EF** e, em seguida, clique em **Adicionar**.
	
![Adicionar Scaffold dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)

5. Na caixa de diálogo **Adicionar Controlador**, digite "CmController" como o nome do controlador. (Consulte a imagem abaixo).
1. Na caixa suspensa **Classe do modelo**, selecione **Contact (ContactManager.Models)**.
1. Em **Classe de contexto de dados**, selecione **ApplicationDbContext (ContactManager.Models)**. O **ApplicationDbContext** será usado para o BD de associação e nossos dados de contato.

![Novos dados ctx dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrCtx.png)

1. Clique em **Adicionar**.

   O Visual Studio cria métodos do controlador e modos de exibição para as operações de banco de dados CRUD para objetos **Contact**.

## Habilitar migrações, criar o banco de dados, adicionar dados de exemplo e um inicializador de dados ##

A próxima tarefa é habilitar o recurso [Codificar primeiras migrações (a página pode estar em inglês)](http://msdn.microsoft.com/library/hh770484.aspx) para criar o banco de dados com base no modelo de dados criado.

1. No menu **Ferramentas**, selecione **Gerenciador de Pacotes da Biblioteca** e **Console do Gerenciador de Pacotes**.
	![Console do Gerenciador de Pacotes no menu Ferramentas][addcode008]
2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		enable-migrations
	O comando **enable-migrations** cria uma pasta *Migrations* e coloca nessa pasta um arquivo *Configuration.cs* que pode ser editado para propagar o banco de dados e configurar migrações. 

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		add-migration Initial


	O comando **add-migration Initial** gera um arquivo chamado **&lt;date_stamp&gt;Initial** na pasta *Migrations* que cria o banco de dados. O primeiro parâmetro ( **Initial** ) é arbitrário e é usado para criar o nome do arquivo. Você pode ver os novos arquivos de classe no **Gerenciador de Soluções**.
	Na classe **Initial**, o método **Up** cria a tabela Contacts, e o método **Down** (usado quando você deseja retornar ao estado anterior) a descarta.
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

	Esse código inicializa (propaga) o banco de dados com as informações de contato. Para obter mais informações sobre a propagação do banco de dados, consulte [Propagando e depurando BDs do Entity Framework (EF) (a página pode estar em inglês)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


6. No **Console do Gerenciador de Pacotes**, digite o comando:

		update-database

	![Comandos do Console do Gerenciador de Pacotes][addcode009]

	O **update-database** executa a primeira migração que cria o banco de dados. Por padrão, o banco de dados é criado como um banco de dados LocalDB do SQL Server Express. 

7. Pressione CTRL+F5 para executar o aplicativo e, em seguida, clique no link **Demonstração de CM** ou navegue até http://localhost:(port#)/Cm. 

O aplicativo mostra os dados de propagação e fornece links de edição, detalhes e exclusão. Você pode criar, editar, excluir e exibir dados.

![Exibição do MVC de dados][rx2]

<h2><a name="addOauth"></a><span class="short-header">OAuth</span>Adicionar um provedor de OpenID e OAuth2</h2>

[OAuth](http://oauth.net/ "http://oauth.net/") é um protocolo aberto que permite autorização segura em um método simples e padrão da Web, aplicativos móveis e de área de trabalho. O modelo de internet do ASP.NET MVC usa OAuth e [OpenID](http://openid.net/) para expor o Facebook, o Twitter, o Google e a Microsoft como provedores de autenticação. Embora este tutorial use apenas o Google como o provedor de autenticação, você pode facilmente modificar o código para usar qualquer um dos provedores. As etapas para implementar outros provedores são muito semelhantes às etapas que você verá neste tutorial. Para usar o Facebook como um provedor de autenticação, consulte o tutorial [Criar um aplicativo do ASP.NET MVC 5 com logon OAuth2 e OpenID do Facebook e do Google (a página pode estar em inglês)](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)

Além da autenticação, o tutorial também usará funções para implementar a autorização. Somente os usuários que você adicionar à função *canEdit* poderão alterar dados (isto é, criar, editar ou excluir contatos).

1. Abra o arquivo *App_Start\Startup.Auth.cs*. Remova os caracteres de comentários do método *app.UseGoogleAuthentication()*.

1. Execute o aplicativo e clique no link **logon**. 
1. Em **Usar outro serviço para fazer logon**, clique no botão **Google**. 
1. Digite suas credenciais.
1. Clique em **Aceitar** para permitir que o aplicativo acesse seus emails e informações básicas.
1. Você é redirecionado para a página Registrar. Você pode alterar o **Nome do usuário** se desejar. Clique em **Registrar**.

![registrar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr8.png)

<h2><a name="mbrDB"></a><span class="short-header">BD de associação</span>Usando a API de associação</h2>
Nesta seção você irá adicionar um usuário local e a função *canEdit* ao banco de dados de associação. Somente os usuários na função *canEdit* poderão editar os dados. A melhor prática é nomear as funções pelas ações que podem executar, portanto, *canEdit* é preferível do que uma função chamada *admin*. Quando seu aplicativo evoluir, você poderá adicionar novas funções, como *canDeleteMembers*, em vez da menos descritiva *superAdmin*.

1. Abra o arquivo *migrations\configuration.cs* e adicione as seguintes instruções `using`:

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
               UserName = "user1",
            };
            ir = um.Create(user, "Passw0rd1");
            if (ir.Succeeded == false)
               return ir.Succeeded;
            ir = um.AddToRole(user.Id, "canEdit");
            return ir.Succeeded;
         }

2. Chame o novo método no método **Seed**:

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            AddUserAndRole(context);
            context.Contacts.AddOrUpdate(p => p.Name,
                // Code removed for brevity
        }

   Esse código cria uma nova função chamada *canEdit*, cria um novo usuário local *user1*e adiciona *user1* à função *canEdit*. 

## Usar código temporário para adicionar novos usuários de logon social à função canEdit  ##
Nesta seção você irá modificar temporariamente o método **ExternalLoginConfirmation** no controlador de conta para adicionar novos usuários com registro com um provedor OAuth ou OpenID na função *canEdit*. Vamos modificar temporariamente o método **ExternalLoginConfirmation** para adicionar automaticamente novos usuários a uma função administrativa. Até que possamos fornecer uma ferramenta para adicionar e gerenciar funções, vamos usar o código de registro automático temporário abaixo. No futuro, esperamos fornecer uma ferramenta semelhante a [WSAT](http://msdn.microsoft.com/pt-br/library/ms228053(v=vs.90).aspx) que permita criar e editar contas de usuário e funções. Mais adiante no tutorial, mostrarei como você pode usar o **Gerenciador de Servidores** para adicionar usuários a funções.  

1. Abra o arquivo **Controllers\AccountController.cs** e navegue para o método **ExternalLoginConfirmation**.
1. Adicione a seguinte chamada de **AddToRoleAsync** antes da chamada de **SignInAsync**.

                await UserManager.AddToRoleAsync(user.Id, "CanEdit");

   O código acima adiciona o usuário recém-registrado à função "CanEdit" que oferece acesso a métodos de ação que alteram (editam) dados. Uma imagem da alteração de código é mostrada abaixo:

   ![código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr9.png)

Mais tarde no tutorial você implantará o aplicativo no Windows Azure, onde você irá fazer logon no Google ou outro provedor de autenticação de terceiros. Isso adicionará a conta recém-registrada à função *canEdit*. Qualquer pessoa que localize a URL do site e que tenha uma ID do Google poderá registrar-se e atualizar seu banco de dados. Para impedir que outras pessoas façam isso, você pode parar o site. Você poderá verificar quem está na função *canEdit* examinando o banco de dados.

No **Console do Gerenciador de Pacotes** pressione a tecla de seta para cima para exibir o seguinte comando:

		Update-Database

Execute o comando **Update-Database** que executará o método **Seed** e o **AddUserAndRole** que você acabou de adicionar. O **AddUserAndRole** irá criar o usuário *user1* e o adicionará à função *canEdit*.

## Proteger o aplicativo com SSL e o atributo Authorize ##

Nesta seção você irá aplicar o atributo [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) para restringir o acesso aos métodos de ação. Os usuários anônimos poderão exibir o método de ação **Índice** do controlador doméstico apenas. Os usuários registrados poderão ver os dados de contato (as páginas **Índice** e **Detalhes** do controlador de Cm), as páginas Sobre e Contato. Somente usuários na função *canEdit* poderão acessar os métodos de ação que alteram dados.

1. Adicionar os filtros [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) e [RequireHttps](http://msdn.microsoft.com/pt-br/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) ao aplicativo. Uma abordagem alternativa é adicionar os atributos [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) e [RequireHttps](http://msdn.microsoft.com/pt-br/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) a cada controlador, mas aplicá-los a todo o aplicativo é considerado uma melhor prática de segurança. Adicionando-os globalmente, cada novo controlador e método de ação que você adicionar será automaticamente protegido, você não precisará se lembrar de aplicá-los. Para obter mais informações, consulte [Protegendo seu aplicativo ASP.NET MVC e o novo atributo AllowAnonymous (a página pode estar em inglês)](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Abra o arquivo *App_Start\FilterConfig.cs* e substitua o método *RegisterGlobalFilters* pelo seguinte (que adiciona os dois filtros):

        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());
        }

   O filtro [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) aplicado no código acima impedirá que usuários anônimos acessem qualquer método no aplicativo. Você usará o atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) para cancelar o requisito de autorização em alguns métodos, portanto, os usuários anônimos poderão efetuar logon e exibir a home page. O [RequireHttps](http://msdn.microsoft.com/pt-br/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) exigirá que todo acesso ao aplicativo Web seja feito por HTTPS.

1. Adicione o atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) ao método **Index** do controlador residencial. O atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) permite criar a lista branca dos métodos para os quais você deseja recusar autorização. Uma parte do HomeController é mostrada abaixo:	

         namespace ContactManager.Controllers
         {
            public class HomeController : Controller
            {
               [AllowAnonymous]
               public ActionResult Index()
               {
                  return View();
               }

2. Execute uma pesquisa global por *AllowAnonymous*, você pode ver que ele é usado no logon e nos métodos de registro do controlador de conta.
1. No *CmController.cs*, adicione '[Authorize(Roles = "canEdit")]` aos métodos HttpGet e HttpPost que alteram dados (Create, Edit, Delete, cada método de ação, exceto Index e Details) no controlador de *Cm*. Uma parte do código concluído é mostrada abaixo: 

   ![imagem do código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)

## Habilitar SSL para o projeto ##

1. Habilite o SSL. No Gerenciador de Soluções, clique no projeto **ContactManager** e clique em F4 para exibir a caixa de diálogo de propriedades. Altere **SSL habilitado** para true. Copie a **URL do SSL**. A URL do SSL será https://localhost:44300/, a menos que você tenha criado Sites SSL anteriormente.

	![habilitar SSL][rxSSL]
 
1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **Gerenciador de Contatos** e clique em **Propriedades**.
1. Na guia à esquerda, clique em **Web**.
1. Altere a **Url do projeto** para usar a **URL SSL** e salve a página (Control S).

	![habilitar SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. Pressione CTRL+F5 para executar o aplicativo. O navegador exibirá um aviso de certificado. Em nosso aplicativo, você pode clicar com segurança no link **Continuar neste site**. 
 
	![av. de cert.][rxNOT]


	![av. de cert.][rxNOT2]
 
   O navegador padrão mostra a página **índice** do controlador doméstico. 

1. Se você ainda estiver conectado de uma sessão anterior, pressione o link **Logoff**.
1. Clique no link **Sobre** ou **Contato**. Você será redirecionado para a página de logon, porque usuários anônimos não podem exibir essas páginas. 
1. Clique no link **Registrar** link e adicione um usuário local chamado *Joe*. Verifique se *Joe* pode exibir as páginas Home, Sobre e Contato. 
1. Clique em *Demonstração de CM* e verifique se você vê os dados. 
1. Clique em um link de edição na página, você será redirecionado para a página de logon (porque um novo usuário local não é adicionado à função *canEdit*).
1. Faça logon como *user1* com a senha "Passw0rd1" (o "0" em "word" é um zero). Você será redirecionado para a página de edição selecionada anteriormente. 

   Se você não puder efetuar logon com essa conta e senha, tente copiar a senha do código-fonte e colá-la. Se ainda não for possível efetuar logon, verifique a tabela **AspNetUsers** para verificar se *user1* foi adicionado. Mais adiante, no tutorial, mostro como verificar a tabela **AspNetUsers**.
1. Verifique se você pode fazer alterações nos dados.

<h2><a name="bkmk_deploytowindowsazure11"></a>Implantar o aplicativo no Windows Azure</h2>

1. Crie o aplicativo.
1. No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publicar no menu de contexto do projeto][firsdeploy003]

O assistente de **Publicar Web** é aberto.

1. Clique na guia **Configurações**. Clique no ícone **v** para selecionar a **Cadeia de conexão remota** para **ApplicationDbContext** e selecione **ContactDB**.

   (Se você fechou e abriu novamente o Visual Studio depois de criar o perfil de publicação, talvez você não veja a cadeia de conexão na lista suspensa. Nesse caso, em vez de editar o perfil de publicação que você criou anteriormente, crie um novo da mesma maneira como criou anteriormente e siga estas etapas na guia **Configurações**.)

	![configurações](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. Em **ContactManagerContext**, selecione **Executar Codificar Migrações Iniciais**.

![configurações](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. Clique em **Publicar**.

1. Faça logon como *user1* e verifique se você pode editar dados.

1. Faça logoff.

2. Faça logon usando o Google ou o Facebook. Isso irá adicionar a conta do Google ou do Facebook à função **canEdit**.

### Parar o site para impedir que outras pessoas se registrem  

1. No **Gerenciador de Servidores**, navegue até **Sites da Web**.
4. Clique com o botão direito do mouse em cada instância do Site e selecione **Parar Site**. 

	![parar site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png) 

Como alternativa, no Portal de Gerenciamento do Windows Azure, você pode selecionar o site e clicar no ícone **parar** na parte inferior da página.

![parar site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### Remover AddToRoleAsync, publicar e testar

1. Remova o código a seguir do método **ExternalLoginConfirmation** no Controlador de conta: 
                `await UserManager.AddToRoleAsync(user.Id, "CanEdit");`
1. Compile o projeto (que salva as alterações no arquivo e verifica se não há erros de compilação).
5. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Publicar**.

	   ![Publicar no menu de contexto do projeto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. Clique no botão **Iniciar Visualização**. Somente os arquivos que precisam ser atualizados são implantados.
5. Inicie o Site. A maneira mais fácil de fazer isso é no Portal. **Você não pode publicar enquanto o site estiver parado**.
5. Volte para o Visual Studio e clique em **Publicar**.
3. Seu aplicativo do Windows Azure é aberto no navegador padrão. Você está exibindo a home page como um usuário anônimo.  
4. Clique no link **Sobre**. Você será redirecionado para a página Logon.
5. Clique no link **Registrar** na página Logon e crie a conta local. Usaremos essa conta local para verificar se você pode acessar as páginas somente leitura, mas não pode acessar páginas que alteram dados (que são protegidas pela função *canEdit*). Mais adiante, no tutorial, removeremos o acesso à conta local. 
<!--
1. Log out of the local user account and log in with the Google account you previously registered with. Verify you can edit data. 
-->

![Fazer logoff](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr6.png)

1. Verifique se você pode navegar até as páginas *Sobre* e *Contato*.

![Fazer logoff](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr7.png)

1. Clique no link **Demonstração de CM** para navegar até o controlador de **Cm**. Como alternativa, você pode acrescentar *Cm* à URL. 

![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. Clique em um link de edição. Você será redirecionado para a página de logon. Em **Usar outro serviço para fazer logon**, clique em Google ou Facebook e faça logon com a conta registrada anteriormente.
2. Verifique se você pode editar dados enquanto estiver conectado nessa conta.
 	**Observação:** não é possível fazer logon do Google neste aplicativo e fazer logon em outra conta do google com o mesmo navegador. Se você estiver usando um navegador, precisará navegar até o Google e fazer logoff. Você pode fazer logon com outra conta do mesmo autenticador de terceiros (como o Google) usando outro navegador.


## Examine o BD do SQL Azure ##

1. No **Gerenciador de Servidores**, navegue até **ContactDB**.
2. Clique com o botão direito do mouse em **ContactDB** e selecione **Abrir no Pesquisador de Objetos do SQL Server**.
 
![abrir no SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)

 
**Observação:** se você não puder expandir **Bancos de Dados SQL** e *não puder* ver o **ContactDB** do Visual Studio, siga as instruções abaixo para abrir uma porta ou um intervalo de portas de firewall. Siga as instruções em **Adicionando um intervalo de endereços IP permitidos** e **Conectando-se a um Banco de Dados SQL Azure no SSOX**. Talvez você precise aguardar alguns minutos para acessar o banco de dados depois de adicionar a regra de firewall.
 
1. Clique com o botão direito do mouse na guia **AspNetUsers** e selecione **Exibir Dados**.

![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. Anote a Id da conta do Google com a qual você se registrou para estar na função **canEdit** e a Id de *user1*. Esses devem ser os únicos usuários na função **canEdit**. (Você verificará isso na próxima etapa.)

![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)
 
2. No **Pesquisador de Objetos do SQL Server**, clique com o botão direito do mouse em **AspNetUserRoles** e selecione **Exibir Dados**.

![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
Verifique se os **UserId**s são do *user1* e da conta do Google que você registrou. 


## Erro de logon - Não é possível abrir servidor ##

Siga as etapas nesta seção somente se você receber uma caixa de diálogo de erro informando "Não é possível abrir o servidor".
	![erro do firewall][rx5]

Você precisará adicionar o endereço IP para os IPs permitidos.

1. No Portal do Windows Azure, selecione **Bancos de Dados SQL** na guia à esquerda.
	![Selecionar SQL][rx6]

1. Selecione o banco de dados que você deseja abrir.
1. Clique no link **Configurar regras de firewall do Windows Azure para esse endereço IP**.

	![regras de firewall][rx7]

1. Quando você receber o prompt "O endereço IP atual xxx.xxx.xxx.xxx não está incluído nas regras de firewall existentes. Deseja atualizar as regras de firewall?", clique em **Sim**. Normalmente não é suficiente adicionar esse endereço por trás de alguns firewalls corporativos, você precisará adicionar um intervalo de endereços IP.

A próxima etapa é adicionar um intervalo de endereços IP permitidos.

1. No Portal do Windows Azure, clique em **Bancos de Dados SQL**.
1. Clique no **Servidor** que hospeda seu banco de dados.

	![servidor do bd][rx8]

1. Clique no link **Configurar** na parte superior da página.
1. Adicione um nome de regra e endereços IP iniciais e finais.

	![intervalo IP][rx9]

1. Na parte inferior da página, clique em **Salvar**.
1. Deixe comentários e avise-me se for necessário adicionar um intervalo de endereços IP para se conectar.

Finalmente, você pode se conectar à instância do Banco de Dados SQL no SSOX

1. No menu Exibir, clique em **Pesquisador de Objetos do SQL Server**
1. Clique com o botão direito do mouse em **SQL Server** e selecione **Adicionar SQL Server**.
1. Na caixa de diálogo **Conectar-se ao servidor**, defina a **Autenticação** como **Autenticação do SQL Server**. Você obterá o **Nome do servidor** e o **Logon** no Portal do Windows Azure.
1. Em seu navegador, navegue até o portal e selecione **Bancos de Dados SQL**.
1. Selecione o **ContactDB** e, em seguida, clique em **Exibir cadeias de conexão do Banco de Dados SQL**.
1. Na página **Cadeias de Conexão**, copie o **Servidor** e a **ID do usuário**. 
1. Cole os valores de **Servidor** e **ID de usuário** na caixa de diálogo **Conectar-se ao Servidor** no Visual Studio. O valor da **ID do usuário** vai para a entrada **Logon**. Digite a senha que você usou para criar o BD SQL.

![Conectar-se à DLG do servidor](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

Agora você pode navegar para o BD de contato usando as instruções fornecidas anteriormente.


## Para adicionar um usuário à função canEdit editando tabelas do banco de dados

Anteriormente, no tutorial, você usou código para adicionar usuários à função canEdit. Um método alternativo é manipular diretamente os dados nas tabelas de associação. As etapas a seguir mostram como usar esse método alternativo para adicionar um usuário a uma função.

2. No **Pesquisador de Objetos do SQL Server**, clique com o botão direito do mouse em **AspNetUserRoles** e selecione **Exibir Dados**.

![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

1. Copie a *RoleId* e cole-a na linha vazia (nova).
	
![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)
	
2. Na tabela **AspNetUsers**, localize o usuário que você deseja colocar na função e copie a *Id* do usuário e cole-a na coluna **UserId** da tabela **AspNetUserRoles**.

Estamos trabalhando em uma ferramenta que irá facilitar bastante o gerenciamento de usuários e funções.

## Considerações sobre o registro local ##

O registro de associação ao ASP.NET atual no projeto não oferece suporte para a redefinição de senha e não verifica se o registro é de uma pessoa (por exemplo, com uma [CAPTCHA](http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership)). Quando um usuário é autenticado usando um dos provedores de terceiros, ele pode se registrar. Se você optar por desabilitar o registro local, siga estas etapas:


1. No AccountController, remova o atributo *[AllowAnonymous]* dos métodos *Register* GET e POST. Isso evitará que bots e usuários anônimos se registrem.
1. Na pasta *Views\Shared*, arquivo *_LoginPartial.cshtml*, remova o link da ação Register.
2. No arquivo *Views\Account\Login.cshtml*, remova o link da ação Register.
2. Implante o aplicativo.


<h2><a name="nextsteps"></a><span class="short-header">Próximas etapas</span>Próximas etapas</h2>

Siga o tutorial [Criar um aplicativo do ASP.NET MVC 5 com logon OAuth2 e OpenID do Facebook e do Google (a página pode estar em inglês)](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on ) para obter instruções sobre como adicionar dados de perfil ao banco de dados de registro do usuário e para obter instruções detalhadas sobre como usar o Facebook como um provedor de autenticação.


Um bom lugar para obter mais informações sobre o ASP.NET MVC é o meu tutorial [Introdução ao ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started). O excelente artigo [Introdução ao EF e ao MVC (a página pode estar em inglês)](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) de Tom Dykstra mostra uma programação de EF mais avançada.

Este tutorial e o aplicativo de exemplo foi escrito por [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) com a ajuda de Tom Dykstra e de Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)). 

Deixe comentários sobre o que você gostou ou do que você gostaria de ver melhorado, não apenas sobre o próprio tutorial, mas também sobre os produtos que ele demonstra. Seus comentários nos ajudarão a priorizar melhorias. 

<!--
To get the colorful Facebook, Google and Yahoo log on buttons, see the blog post [Customizing External Login Buttons in ASP.NET MVC 5](http://www.beabigrockstar.com/customizing-external-login-buttons-in-asp-net-mvc-4/). 
 -->
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




<!-- links -->






















<!-- links from Tom's hopefully no collisions -->





















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









[Informações importantes sobre o ASP.NET em Sites do Windows Azure]: #aspnetwindowsazureinfo
[Próximas etapas]: #nextsteps



[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
























