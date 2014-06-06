<properties linkid="dev-net-tutorials-web-app-with-sql-azure-vs2013" urlDisplayName="Site com banco de dados SQL" pageTitle="Implantar um aplicativo ASP.NET MVC seguro com associação, OAuth e banco de dados SQL para um Site do Azure" metaKeywords="Tutorial hello world do Azure, tutorial de introdução ao Azure, tutorial do banco de dados SQL, tutorial de hello world do .NET no Azure, tutorial hello world do C# no Azure, Tutorial do C# para SQL Azure" description="Saiba como desenvolver um site ASP.NET MVC 5 com um banco de dados SQL back-end e implantá-lo no Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title=" OAuth" authors=""  solutions="" writer="riande" manager="wpickett" editor="mollybos"  />



# Implantar um aplicativo ASP.NET MVC 5 seguro com Associação, OAuth e Banco de Dados SQL em um site do Azure

***Por [Rick Anderson](https://twitter.com/RickAndMSFT) e Tom Dykstra. Atualizado em 2 de abril de 2014.***

Este tutorial mostra como criar um aplicativo Web ASP.NET MVC 5 seguro que permite que os usuários façam logon com credenciais do Google ou do Facebook. Você também implantará o aplicativo no Azure.

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2013, o SDK instalará automaticamente o Visual Studio 2013 para o Web Express. É possível iniciar o desenvolvimento para o Azure de maneira gratuita. Se você deseja usar o Visual Studio 2012, consulte o [tutorial anterior](/pt-br/develop/net/tutorials/web-site-with-sql-database-vs2012/). Esta versão do tutorial é muito mais simples do que a versão anterior.

Este tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure. Ao concluir este tutorial, você terá um aplicativo da Web controlado por dados seguro e em funcionamento na nuvem usando um banco de dados em nuvem.

Você aprenderá a:

* Como criar um projeto ASP.NET MVC 5 seguro e publicá-lo em um Site do Azure.
* Como usar [OAuth](http://oauth.net/ "http://oauth.net/"), [OpenID](http://openid.net/) e o banco de dados de associação do ASP.NET para proteger seu aplicativo.
* Como usar a nova API de associação para adicionar usuários e funções.
* Como usar um banco de dados SQL para armazenar dados no Azure.

Você criará um aplicativo Web de lista de contatos simples criado no ASP.NET MVC 5 e que usa o ADO.NET Entity Framework para acesso ao banco de dados. A seguinte ilustração mostra a página de logon do aplicativo concluído:

![página de logon][rxb]

>[WACOM.NOTE] Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Se não tiver uma conta, você poderá <a href="/pt-br/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">ativar os benefícios de assinante do MSDN</a> ou <a href="/pt-br/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">inscrever-se para uma avaliação gratuita</a>


Neste tutorial:

- [Configurar o ambiente de desenvolvimento](#setupdevenv)
- [Configurar o ambiente do Azure][setupwindowsazureenv]
- [Criar um aplicativo ASP.NET MVC 5][createapplication]
- [Implantar o aplicativo no Azure][deployapp1]
- [Adicionar um banco de dados ao aplicativo][adddb]
- [Adicionar um provedor de OAuth][]
- [Usando a API de Associação][]
- [Implantar o aplicativo no Azure][deployapp11]
- [Próximas etapas][]


[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

Para usar o novo certificado SSL para host local, você precisará instalar o [Visual Studio 2013 Update 2 RC](http://go.microsoft.com/fwlink/?LinkId=390521) ou posterior.

<h2><a name="bkmk_setupwindowsazure"></a>Configurar o ambiente do Azure</h2>

Em seguida, configure o ambiente do Azure criando um Site do Azure e um banco de dados SQL.

### Criar um site e um banco de dados SQL no Azure

O Site do Azure será executado em um ambiente de hospedagem compartilhado, o que significa que ele é executado em MVs (máquinas virtuais) que são compartilhadas com outros clientes do Azure. Um ambiente de hospedagem compartilhado é uma maneira de começar na nuvem a um baixo custo. Mais tarde, se seu tráfego da Web aumentar, o aplicativo poderá ser dimensionado para atender à necessidade executando em VMs dedicadas. Se precisar de uma arquitetura mais complexa, você poderá migrar para um Serviço de Nuvem do Azure. Os serviços de nuvem são executados em VMs dedicadas que podem ser configuradas de acordo com suas necessidades.

O Banco de dados SQL no Azure é um serviço de banco de dados relacional baseado em que é fundamentado em tecnologias do SQL Server. As ferramentas e os aplicativos que funcionam com o SQL Server também funcionam com o Banco de dados SQL.

1. No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), clique em **Sites da Web** na guia à esquerda e clique em  **Novo**.

	![Novo botão no Portal de Gerenciamento][rxWSnew]

1. Clique em **Site** e, em seguida, clique em **Criação Personalizada**.

	![Criar com link de banco de dados no Portal de Gerenciamento][rxCreateWSwithDB] 

	O assistente **Novo Site - Criação Personalizada** é aberto. 

1. Na etapa **Criar Site** do assistente, digite uma cadeia de caracteres na caixa **URL** para usar como URL exclusiva do seu aplicativo. A URL completa consistirá no que você digitar aqui mais o sufixo que você vê ao lado da caixa de texto. A ilustração mostra uma URL que provavelmente já é utilizada e, portanto, você precisa escolher outra.

	![Criar com link de banco de dados no Portal de Gerenciamento][rr1]

1. Na lista suspensa **Banco de Dados**, escolha **Criar um banco de dados SQL gratuito**.

1. Na lista suspensa **Região**, escolha a mesma região que você selecionou para o site.
Essa configuração especifica qual data center sua VM executará. 
1. Na caixa **Nome da Cadeia de Conexão de BD**, deixe o valor padrão de *DefaultConnection*.
1. Clique na seta que aponta para a direita na parte inferior da caixa.
O assistente avança para a etapa **Especificar configurações de banco de dados**.

1. Na caixa **Nome**, digite *ContactDB*. (consulte a imagem abaixo). 
1. Na caixa **Servidor**, selecione **Novo Servidor do Banco de Dados SQL**. (consulte a imagem abaixo). Como alternativa, se tiver criado um banco de dados SQL Server, você poderá selecionar esse SQL Server no controle suspenso.
1. Defina a **Região** como a mesma área na qual você criou o Site.
1. Insira um **Nome de Logon** e **Senha** de administrador. Se você selecionou **Novo servidor do Banco de Dados SQL** você não irá digitar um nome e uma senha existentes aqui; digite um novo nome e uma senha que você está definindo agora para usar mais tarde ao acessar o banco de dados. Se você tiver selecionado um SQL Server criado anteriormente, será solicitada a senha para o nome da conta do SQL Server anterior que você criou. Para este tutorial, não selecionamos a caixa **Avançado**.  Para um banco de dados gratuito, você pode definir somente o agrupamento.
1. Clique na marca de seleção no canto inferior direito da caixa para indicar que você concluiu.

	![Etapa de configurações do banco de dados do novo site - Criar com o assistente de banco de dados][setup007]
	
	A imagem a seguir mostra o uso de um SQL Server e logon existentes.

	![Etapa de configurações do banco de dados do novo site - Criar com o assistente de banco de dados][rxPrevDB]

	O Portal de Gerenciamento retorna para a página Sites, e a coluna **Status** mostra que o site está sendo criado. Depois de um tempo (normalmente menos de um minuto), a coluna **Status** mostra que o site foi criado com êxito. Na barra de navegação à esquerda, o número de sites presentes na conta é exibido próximo ao ícone **Sites da Web**, e o número de bancos de dados aparece ao lado do ícone **Bancos de Dados SQL**.

<h2><a name="bkmk_createmvc4app"></a>Criar um aplicativo ASP.NET MVC 5</h2>

Você criou um Site do Azure, mas ainda não há conteúdo nele. A próxima etapa é criar o aplicativo Web do Visual Studio que será publicado no Azure.

### Criar o projeto

2. No menu **Arquivo**, clique em **Novo Projeto**.

   ![Novo projeto no menu Arquivo](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

3. Na caixa de diálogo **Novo Projeto**, expanda **C#** e selecione **Web** em **Modelos Instalados** e, em seguida, selecione **Aplicativo Web ASP.NET**.


4. Nomeie o aplicativo **ContactManager** e clique em **OK**.

   ![Caixa de diálogo Novo Projeto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
   **Observação:** a imagem mostra "MyExample" como o nome, mas certifique-se de inserir "ContactManager". Os blocos de código que você irá copiar posteriormente supõem que o nome do projeto seja ContactManager. 

5. Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo **MVC**, **desmarque** a caixa de diálogo **Criar recursos remotos** e, em seguida, clique em **OK**. (A caixa de seleção deve ser rotulada **Host na nuvem** em vez de **Criar recursos remotos**.)

   ![Caixa de diálogo Novo Projeto ASP .NET](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)


### Definir o cabeçalho e o rodapé da página


1. No **Gerenciador de Soluções**, abra o arquivo *Layout.cshtml* na pasta *Views\Shared*.

	![_Layout.cshtml no Gerenciador de Soluções][newapp004]

1. Substitua as duas ocorrências de "Meu Aplicativo ASP.NET MVC" por "Gerenciador de Contatos".
1. Substitua "Nome do aplicativo" por "Demonstração de CM". 

2. Atualize o primeiro link de ação e substitua *Início* por *Cm* para usar o Controlador de *Cm*.

	![alterações de código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)


### Executar o aplicativo localmente

1. Pressione CTRL+F5 para executar o aplicativo.

	A home page do aplicativo é exibida no navegador padrão.

	![Site em execução localmente](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

Isso é tudo o que você precisa fazer por enquanto para criar o aplicativo que você implantará no Azure. Posteriormente, você adicionará a funcionalidade do banco de dados.

<h2><a name="bkmk_deploytowindowsazure1"></a>Implantar o aplicativo no Azure</h2>

1. No Visual Studio, clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

   ![Publicar no menu de contexto do projeto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
   O assistente de **Publicar Web** é aberto.

1. Na guia **Perfil** do assistente de **Publicar Web**, clique em **Sites do Azure**.

   ![Importar configurações de publicação](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss2.PNG)

1. Clique no botão **Entrar** e faça logon no portal do Azure.

 ![entrar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss22.PNG)


   Após fazer o login, a caixa de diálogo **Selecionar Site Existente** é exibida.

1. Selecione o site que você criou na primeira parte deste tutorial e, em seguida, clique em **OK**

 ![selecionar site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss3.png)


1. Na caixa de diálogo **Publicar Web**, clique em **Publicar**.

	![Publicar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

	O aplicativo que você criou agora está em execução na nuvem. Na próxima vez que você implantar o aplicativo, somente os arquivos alterados (ou novos) serão implantados.

	![Executando na nuvem](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>Adicionar um banco de dados ao aplicativo</h2>

Em seguida, você atualizará o aplicativo MVC para adicionar a habilidade de exibir e atualizar contatos e armazenar os dados em um banco de dados. O aplicativo usará o Entity Framework para criar o banco de dados e ler e atualizar dados no banco de dados.

### Adicionar classes de modelo de dados para os contatos

Você começa criando um modelo de dados simples no código.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Modelos, clique em **Adicionar** e, em seguida, em **Classe**.

	![Adicionar Classe no menu de contexto da pasta Modelos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2. Na caixa de diálogo **Adicionar Novo Item**, nomeie o novo arquivo de classe *Contact.cs* e depois clique em **Adicionar**.

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
A classe **Contatos** define os dados que você armazenará para cada contato, além de uma chave primária, *ContactID*, exigida pelo banco de dados.

### Criar páginas da Web que permitem que os usuários do aplicativo trabalhem com os contatos

O recurso scaffolding do ASP.NET MVC pode gerar automaticamente o código que executa as ações CRUD (criar, ler, atualizar e excluir).

<h2><a name="bkmk_addcontroller"></a>Adicionar um controlador e uma exibição para os dados</h2>

1. Compile o projeto **(Ctrl + Shift + B)**. (Você deve construir o projeto antes de usar o mecanismo de scaffolding.) 
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Controladores e clique em **Adicionar** e, em seguida, em **Controlador**.

	![Adicionar Controlador no menu de contexto da pasta Controladores][addcode001]

5. Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC 5 com modos de exibição, usando o EF** e, em seguida, clique em **Adicionar**.
	
	![Adicionar Scaffold dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)


1. Na caixa suspensa **Classe do modelo**, selecione **Contato (ContactManager.Models)**. (Consulte a imagem abaixo).
1. Na **Classe de contexto de dados**, selecione **ApplicationDbContext (ContactManager.Models)**. O **ApplicationDbContext** será usado para o BD de associação e nossos dados de contato.
1. Na caixa de entrada de texto **Nome de controlador**, insira "CmController" para o nome do controlador. 

	![Novos dados ctx dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

1. Clique em **Adicionar**.

   O Visual Studio cria métodos do controlador e modos de exibição para as operações CRUD do banco de dados para objetos **Contact**.

## Habilitar migrações, criar o banco de dados, adicionar dados de exemplo e um inicializador de dados ##

A próxima tarefa é habilitar o recurso [Codificar primeiras migrações (a página pode estar em inglês)](http://msdn.microsoft.com/library/hh770484.aspx) para criar o banco de dados com base no modelo de dados criado.

1. No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e, em seguida, **Console do Gerenciador de Pacotes**.
	![Console do Gerenciador de Pacotes no menu Ferramentas](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		enable-migrations
	O comando **enable-migrations** cria a pasta *Migrações* e coloca nessa pasta um arquivo *Configuration.cs* que pode ser editado para propagar o banco de dados e configurar as migrações. 

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		add-migration Initial


	O comando **add-migration Initial** gera um arquivo denominado **&lt;date_stamp&gt;Initial** na pasta *Migrações* que cria o banco de dados. O primeiro parâmetro ( **Initial** ) é arbitrário e é usado para criar o nome do arquivo. Você pode ver os novos arquivos de classe no **Gerenciador de Soluções**.
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

	Esse código inicializa (propaga) o banco de dados com as informações de contato. Para obter mais informações sobre a propagação do banco de dados, consulte [Propagando e depurando BDs do Entity Framework (EF) (a página pode estar em inglês)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


6. No **Console do Gerenciador de Pacotes**, digite o comando:

		update-database

	![Comandos do Console do Gerenciador de Pacotes][addcode009]

	O **update-database** executa a primeira migração que cria o banco de dados. Por padrão, o banco de dados é criado como um banco de dados LocalDB do SQL Server Express. 

7. Pressione CTRL+F5 para executar o aplicativo e, em seguida, clique no link **Demonstração de CM**, ou navegue até http://localhost:(port#)/Cm. 

	O aplicativo mostra os dados de propagação e fornece links de edição, detalhes e exclusão. Você pode criar, editar, excluir e exibir dados.

	![Exibição do MVC de dados][rx2]

<h2><a name="addOauth"></a><span class="short-header">OAuth</span>Adicionar um provedor de OpenID e OAuth2</h2>

[OAuth](http://oauth.net/ "http://oauth.net/") é um protocolo aberto que permite autorização segura em um método simples e padrão da Web, aplicativos móveis e de área de trabalho. O modelo de internet do ASP.NET MVC usa OAuth e [OpenID](http://openid.net/) para expor o Facebook, o Twitter, o Google e a Microsoft como provedores de autenticação. Embora este tutorial use apenas o Google como o provedor de autenticação, você pode facilmente modificar o código para usar qualquer um dos provedores. As etapas para implementar outros provedores são muito semelhantes às etapas que você verá neste tutorial. Para usar o Facebook como um provedor de autenticação, consulte o tutorial [Criar um aplicativo do ASP.NET MVC 5 com logon OAuth2 e OpenID do Facebook e do Google (a página pode estar em inglês)](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)

Além da autenticação, o tutorial também usará funções para implementar a autorização. Somente os usuários que você adicionar à função *canEdit* poderão alterar dados (isto é, criar, editar ou excluir contatos).

1. Abrir o arquivo *App_Start\Startup.Auth.cs*. Remova os caracteres de comentário do método *app.UseGoogleAuthentication()*.

1. Execute o aplicativo e clique no link **Fazer logon**. 
1. Em **Usar outro serviço para fazer logon**, clique no botão **Google**. 

	![Logon no Google](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss23.PNG)

1. Digite suas credenciais.
1. O servidor de autenticação do Google solicitará permissão para o aplicativo exibir o endereço de email e as informações básicas sobre a sua conta. Clique em **Aceitar**.
![Solicitação de permissão do Google](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss7.PNG)
1. Você é redirecionado para a página Registrar. Se desejar, você pode alterar os padrões de nome de usuário para o alias de email que você usou para se registrar. Clique em **Registrar**.

	![register](. / media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss8.PNG)

<h2><a name="mbrDB"></a><span class="short-header">BD de associação</span>Usando a API de associação</h2>
Nesta seção você irá adicionar um usuário local e a função *canEdit* ao banco de dados de associação. Somente os usuários na função *canEdit* poderão editar os dados. A melhor prática é nomear as funções pelas ações que podem executar, portanto, *canEdit* é preferível do que uma função chamada *admin*. Quando seu aplicativo evoluir, você poderá adicionar novas funções, como *canDeleteMembers*, em vez da menos descritiva *superAdmin*.

1. Abra o arquivo *migrations\configuration.cs* e adicione o seguinte `usando` as instruções:

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

2. Chame o novo método no método **Seed**:

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            AddUserAndRole(context);
            context.Contacts.AddOrUpdate(p => p.Name,
                // Code removed for brevity
        }

	As imagens a seguir mostram as alterações no método *Seed*:

	![imagem de código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)

   Esse código cria uma nova função chamada *canEdit*, cria um novo usuário local *user1@contoso.com*, e adiciona *user1@contoso.com* à função *canEdit*. Para obter mais informações, consulte a [página de recursos de Identidade do ASP.NET](http://curah.microsoft.com/55636/aspnet-identity).

## Usar código temporário para adicionar novos usuários de logon social à função canEdit  ##
Nesta seção você irá modificar temporariamente o método **ExternalLoginConfirmation** no controlador de conta para adicionar novos usuários com registro com um provedor OAuth ou OpenID à função *canEdit*. Vamos modificar temporariamente o método **ExternalLoginConfirmation** para adicionar automaticamente novos usuários a uma função administrativa. Até que possamos fornecer uma ferramenta para adicionar e gerenciar funções, vamos usar o código de registro automático temporário abaixo. No futuro, esperamos fornecer uma ferramenta semelhante à [WSAT](http://msdn.microsoft.com/pt-br/library/ms228053.aspx) que permita criar e editar contas de usuário e funções. Mais adiante no tutorial, mostrarei como você pode usar o **Gerenciador de Servidores** para adicionar usuários a funções.  

1. Abra o arquivo **Controladores\AccountController.cs** e navegue até o método **ExternalLoginConfirmation**.
1. Adicione a seguinte chamada para **AddToRoleAsync** logo antes da chamada **SignInAsync**.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   O código acima adiciona o usuário recém-registrado à função "canEdit", que oferece acesso a métodos de ação que alteram (editam) dados. Uma imagem da alteração de código é mostrada abaixo:

   ![código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

Mais tarde no tutorial você implantará o aplicativo no Azure, onde você fará logon no Google ou outro provedor de autenticação de terceiros. Isso adicionará a conta recém-registrada à função *canEdit*. Qualquer pessoa que localize a URL do site e que tenha uma ID do Google poderá registrar-se e atualizar seu banco de dados. Para impedir que outras pessoas façam isso, você pode parar o site. Você poderá verificar quem está na função *canEdit* examinando o banco de dados.

No **Console do Gerenciador de Pacotes** pressione a tecla de seta para cima para exibir o seguinte comando:

		Update-Database

Execute o comando **Update-Database** que executará o método **Seed** e o **AddUserAndRole** que você acabou de adicionar. O **AddUserAndRole** criará o usuário *user1@contoso.com* e o adicionará à função *canEdit*.

## Proteger o aplicativo com SSL e o atributo Authorize ##

Nesta seção você aplicará o atributo [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute.aspx) para restringir o acesso aos métodos de ação. Os usuários anônimos poderão exibir o método de ação **Índice** do controlador doméstico apenas. Os usuários registrados poderão ver os dados de contato (as páginas **Índice** e **Detalhes** do controlador de Cm), as páginas Sobre e Contato. Somente usuários na função *canEdit* poderão acessar os métodos de ação que alteram dados.

1. Adicione o filtro [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute.aspx) e o filtro [RequireHttps](http://msdn.microsoft.com/pt-br/library/system.web.mvc.requirehttpsattribute.aspx) ao aplicativo. Uma abordagem alternativa é adicionar os atributos [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute.aspx) e [RequireHttps](http://msdn.microsoft.com/pt-br/library/system.web.mvc.requirehttpsattribute.aspx) a cada controlador, mas aplicá-los a todo o aplicativo é considerado uma melhor prática de segurança. Adicionando-os globalmente, cada novo controlador e método de ação que você adicionar será automaticamente protegido, você não precisará se lembrar de aplicá-los. Para obter mais informações, consulte [Protegendo seu aplicativo ASP.NET MVC e o novo atributo AllowAnonymous (a página pode estar em inglês)](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Abra o arquivo *App_Start\FilterConfig.cs* e substitua o método *RegisterGlobalFilters* com o seguinte (que adiciona os dois filtros):

        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());
        }

	A imagem seguinte mostra o código alterado:

	![imagem de código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss10.PNG)

	O filtro [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute.aspx) aplicado no código acima impedirá que usuários anônimos acessem qualquer método no aplicativo. Você usará o atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) para cancelar o requisito de autorização em alguns métodos, portanto, os usuários anônimos poderão efetuar logon e exibir a home page. O [RequireHttps](http://msdn.microsoft.com/pt-br/library/system.web.mvc.requirehttpsattribute.aspx) exigirá que todo acesso ao aplicativo Web seja feito por HTTPS.

1. Adicione o atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) ao método **Index** do controlador Home. O atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) permite criar a lista branca dos métodos para os quais você deseja recusar autorização. Uma imagem de parte do HomeController é mostrada abaixo:	

  ![código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

2. Execute uma pesquisa global para *AllowAnonymous*, você pode ver que ele é usado no logon e nos métodos de registro do controlador de conta.
1. Em *CmController.cs*, adicione `[Authorize(Roles = "canEdit"]` aos métodos HttpGet e HttpPost que alteram dados (Criar, Editar, Excluir, cada método de ação exceto Índice e Detalhes) no controlador *Cm*. Uma parte do código concluído é mostrada abaixo: 

   ![imagem do código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)

## Habilitar SSL para o projeto ##

1. Habilitar o SSL. No Gerenciador de Soluções, clique no projeto **ContactManager** e clique em F4 para exibir a caixa de diálogo propriedades. Altere **SSL habilitado** para true. Copie a **URL do SSL**. A URL do SSL será https://localhost:44300/, a menos que você tenha criado Sites SSL anteriormente.

	![habilitar SSL][rxSSL]
 
1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **Gerenciador de Contatos** e clique em **Propriedades**.
1. Na guia à esquerda, clique em **Web**.
1. Altere a **Url do projeto** para usar a **URL SSL** e salve a página (Control S).

	![habilitar SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. Verifique se o Internet Explorer é o navegador que inicia o Visual Studio como mostrado na imagem abaixo:

	![navegador padrão](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)

	O seletor de navegador permite que você especifique o navegador que inicia o Visual Studio.

 ![seletor de navegador](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

	Você pode selecionar vários navegadores e fazer com que o Visual Studio atualize cada navegador quando você fizer alterações. Para obter mais informações, consulte [Usando o link do navegador no Visual Studio 2013 (a página pode estar em inglês)](http://www.asp.net/visual-studio/overview/2013/using-browser-link)


1. Pressione CTRL+F5 para executar o aplicativo. Siga as instruções para confiar no certificado autoassinado que o IIS Express gerou.

	 ![instruções para confiar no certificado autoassinado que o IIS Express gerou](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

1. Leia a caixa de diálogo **Aviso de Segurança** e clique em **Sim** se você deseja instalar o certificado que representa o  **localhost**.

 ![Aviso de certificado do IIS Express para localhost ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1. O IE mostra a *Página inicial* e não há nenhum aviso de SSL.

	 ![IE com SSL do localhost e sem avisos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)

	Google Chrome também aceita o certificado e mostrará o conteúdo de HTTPS sem aviso. O Firefox usa seu próprio repositório de certificados, portanto, será exibido um aviso. Em nosso aplicativo você pode clicar com segurança em **Eu compreendo os riscos**. 

	 ![Aviso do FireFox Cert](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)
 

1. Se você ainda estiver conectado em uma sessão anterior, pressione o link **Logoff**.
1. Clique nos links **Sobre** ou **Contato**. Você será redirecionado para a página de logon, porque usuários anônimos não podem exibir essas páginas. 
1. Clique no link **Registrar-se como um novo usuário** e adicione um usuário local com email *joe@contoso.com*. Verifique se *Joe* pode exibir as páginas Home, Sobre e Contato. 

	![logon](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

1. Clique no link *Demonstração de CM* e verifique se você vê os dados. 
1. Clique em um link de edição na página, você será redirecionado para a página de logon (porque um novo usuário local não é adicionado à função *canEdit*).
1. Faça logon como *user1@contoso* com a senha "P_assw0rd1" (o "0" em "word" é um zero). Você será redirecionado para a página de edição selecionada anteriormente. 

   Se você não puder efetuar logon com essa conta e senha, tente copiar a senha do código-fonte e colá-la. Se ainda não for possível efetuar logon, verifique a coluna **UserName** da tabela **AspNetUsers** para verificar se *user1@contoso* foi adicionado. 
1. Verifique se você pode fazer alterações nos dados.

<h2><a name="bkmk_deploytowindowsazure11"></a>Implantar o aplicativo no Azure</h2>

1. No Visual Studio, clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publicar no menu de contexto do projeto][firsdeploy003]

	O assistente de **Publicar Web** é aberto.

1. Clique a guia **Configurações** no lado esquerdo da caixa de diálogo **Publicar Web**. Clique no ícone **v** para selecionar a **Cadeia de conexão remota** para **ApplicationDbContext** e selecione **ContactDB**.

   
	![configurações](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. Em **ContactManagerContext**, selecione **Executar Codificar Migrações Iniciais**.

	![configurações](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. Clique em **Publicar**.

1. Faça logon como *user1@contoso* e verifique se você pode editar dados.

1. Faça logoff.

2. Faça logon usando o Google ou o Facebook. Isso irá adicionar a conta do Google ou do Facebook à função **canEdit**.

### Parar o site para impedir que outras pessoas se registrem  

1. No **Gerenciador de Servidores**, navegue até **Sites**.
4. Clique com o botão direito do mouse em cada instância do Site e selecione **Parar Site**. 

	![parar site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png) 

	Como alternativa, no Portal de Gerenciamento do Azure, você pode selecionar o site e clicar no ícone **parar** na parte inferior da página.

	![parar site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### Remover AddToRoleAsync, publicar e testar

1. Comente ou remova o código a seguir do método **ExternalLoginConfirmation** no Controlador de conta: 
                `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. Compile o projeto (que salva as alterações no arquivo e verifica se não há erros de compilação).
5. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Publicar**.

	   ![Publicar no menu de contexto do projeto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. Clique no botão **Iniciar Visualização**. Somente os arquivos que precisam ser atualizados são implantados.
5. Inicie o site no Visual Studio ou no Portal. **Você não pode publicar enquanto o site estiver parado**.

	![iniciar site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

5. Volte para o Visual Studio e clique em **Publicar**.
3. Seu aplicativo do Azure é aberto no navegador padrão. Se você efetuou logon, faça logoff para que você possa exibir a página inicial como um usuário anônimo.  
4. Clique no link **Sobre**. Você será redirecionado para a página Logon.
5. Clique no link **Registrar** na página Logon e crie a conta local. Usaremos essa conta local para verificar se você pode acessar as páginas somente leitura, mas não pode acessar páginas que alteram dados (que são protegidas pela função *canEdit*). Mais adiante, no tutorial, removeremos o acesso à conta local. 

	![Registrar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

1. Verifique se você pode navegar até as páginas *Sobre* e *Contato*.

	![Fazer logoff](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

1. Clique no link **Demonstração de CM** para navegar até o controlador de **Cm**. Como alternativa, você pode acrescentar *Cm* à URL. 

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. Clique em um link de edição. Você será redirecionado para a página de logon. Em **Usar outro serviço para fazer logon**, clique em Google ou Facebook e faça logon com a conta registrada anteriormente. (Se estiver trabalhando rapidamente e o cookie da sessão não expirou, você estará automaticamente conectado à conta do Facebook ou Google usados anteriormente.)
2. Verifique se você pode editar dados enquanto estiver conectado nessa conta.
 	**Observação:** não é possível fazer logon do Google neste aplicativo e fazer logon em outra conta do google com o mesmo navegador. Se você estiver usando um navegador, precisará navegar até o Google e fazer logoff. Você pode fazer logon com outra conta do mesmo autenticador de terceiros (como o Google) usando outro navegador.

## Examine o BD do SQL Azure ##

1. No **Gerenciador de Servidores**, navegue até **ContactDB**.
2. Clique com o botão direito do mouse em **ContactDB** e selecione **Abrir no Pesquisador de Objetos do SQL Server**.
 
	![abrir no SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)
 
**Observação:** se você não puder expandir **Bancos de Dados SQL** e *não puder* ver o **ContactDB** do Visual Studio, siga as instruções abaixo para abrir uma porta ou um intervalo de portas de firewall. Siga as instruções em **Configurar regras de firewall do Azure**. Talvez você precise aguardar alguns minutos para acessar o banco de dados depois de adicionar a regra de firewall.
 
1. Clique com o botão direito do mouse na guia **AspNetUsers** e selecione **Exibir Dados**.

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. Anote a Id da conta do Google com a qual você se registrou para estar na função **canEdit** e a Id de *user1@contoso.com*. Esses devem ser os únicos usuários na função **canEdit**. (Você verificará isso na próxima etapa.)

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)
 
2. No **Pesquisador de Objetos do SQL Server**, clique com o botão direito do mouse em **AspNetUserRoles** e selecione **Exibir Dados**.

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
Verifique se os **UserId**s são do *user1@contoso.com* e da conta do Google que você registrou. 


## Configurar regras de firewall do Azure ##

Siga as etapas nesta seção se não for possível se conectar ao SQL Azure do Visual Studio, ou se você receber uma caixa de diálogo de erro informando que "Não é possível abrir o servidor".

![erro do firewall](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

Você precisará adicionar o endereço IP para os IPs permitidos.

1. No Portal do Azure, selecione **Bancos de dados SQL** na guia à esquerda.

	![Selecionar SQL][rx6]

1. Clique em **ContactDB**.

1. Clique no link **Configurar regras de firewall do Azure para esse endereço IP**.

	![regras de firewall][rx7]

1. Quando você receber o prompt "O endereço IP atual xxx.xxx.xxx.xxx não está incluído nas regras de firewall existentes. Deseja atualizar as regras de firewall?", clique em **Sim**. Normalmente não é suficiente adicionar esse endereço por trás de alguns firewalls corporativos, você precisará adicionar um intervalo de endereços IP.

A próxima etapa é adicionar um intervalo de endereços IP permitidos.

1. No Portal do Azure, clique em **Bancos de Dados SQL**.
1. Selecione a guia **Servidores**e, em seguida, clique no servidor que deseja configurar.

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
1. Cole os valores de **Servidor** e **ID de usuário** na caixa de diálogo **Conectar-se ao Servidor** no Visual Studio. O valor da **ID do usuário** vai para a entrada **Logon**. Digite a senha que você usou para criar o BD SQL.

	![Conectar-se à DLG do servidor](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

Agora você pode navegar para o BD de contato usando as instruções fornecidas anteriormente.


## Para adicionar um usuário à função canEdit editando tabelas do banco de dados

Anteriormente, no tutorial, você usou código para adicionar usuários à função canEdit. Um método alternativo é manipular diretamente os dados nas tabelas de associação. As etapas a seguir mostram como usar esse método alternativo para adicionar um usuário a uma função.

2. No **Pesquisador de Objetos do SQL Server**, clique com o botão direito do mouse em **AspNetUserRoles** e selecione **Exibir Dados**.

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

1. Copie a *RoleId* e cole-a na linha vazia (nova).
	
	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)
	
2. Na tabela **AspNetUsers**, localize o usuário que você deseja colocar na função, copie a *Id* do usuário e cole-a na coluna **UserId** da tabela **AspNetUserRoles**.

Estamos trabalhando em uma ferramenta que irá facilitar bastante o gerenciamento de usuários e funções.

## Considerações sobre o registro local ##

O registro de associação ao ASP.NET atual no projeto não oferece suporte para a redefinição de senha e não verifica se o registro é de uma pessoa (por exemplo, com uma [CAPTCHA](http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership)). Quando um usuário é autenticado usando um dos provedores de terceiros, ele pode se registrar. Se você optar por desabilitar o registro local, siga estas etapas:


1. No AccountController, remova o atributo *[AllowAnonymous]* dos métodos *Register* GET e POST. Isso evitará que bots e usuários anônimos se registrem.
1. Na pasta *Views\Shared*, arquivo *_LoginPartial.cshtml*, remova o link da ação Register.
2. No arquivo *Views\Account\Login.cshtml*, remova o link da ação Register.
2. Implante o aplicativo.


<h2><a name="nextsteps"></a><span class="short-header">Próximas etapas</span>Próximas etapas</h2>

Siga o tutorial [Criar um aplicativo do ASP.NET MVC 5 com logon OAuth2 e OpenID do Facebook e do Google (a página pode estar em inglês)](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on ) para obter instruções sobre como adicionar dados de perfil ao banco de dados de registro do usuário e para obter instruções detalhadas sobre como usar o Facebook como um provedor de autenticação.

Para ativar os botões de logon social mostrados na parte superior deste tutorial, consulte [Muitos botões de logon social para ASP.NET MVC 5 (a página pode estar em inglês)](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/).

Um bom lugar para obter mais informações sobre o ASP.NET MVC é o meu tutorial [Introdução ao ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started). O excelente artigo [Introdução ao EF e ao MVC (a página pode estar em inglês)](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) de Tom Dykstra mostra uma programação de MVC e EF mais avançada.

Este tutorial e o aplicativo de exemplo foram escritos por [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) com a assistência de Tom Dykstra e Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)). 

Deixe comentários sobre o que você gostou ou do que você gostaria de ver melhorado, não apenas sobre o próprio tutorial, mas também sobre os produtos que ele demonstra. Seus comentários nos ajudarão a priorizar melhorias. Você também pode solicitar e votar em novos tópicos em [Mostre-me como com código](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

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


[Important information about ASP.NET in Azure Web Sites]: #aspnetwindowsazureinfo
[Próximas etapas]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
























