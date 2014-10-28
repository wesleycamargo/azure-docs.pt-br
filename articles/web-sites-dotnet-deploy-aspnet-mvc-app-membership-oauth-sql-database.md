<properties linkid="dev-net-tutorials-web-app-with-sql-azure-vs2013" urlDisplayName="Website with SQL Database" pageTitle="Deploy a Secure ASP.NET MVC app with Membership, OAuth, and SQL Database to an Azure Website" metaKeywords="Azure hello world tutorial, Azure getting started tutorial, SQL Database tutorial, Azure .NET hello world tutorial, Azure C# hello world tutorial, SQL Azure C# tutorial" description="Learn how to develop an ASP.NET MVC 5 website with a SQL Database back-end deploy it to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title="Deploy a Secure ASP.NET MVC 5 app with Membership, OAuth, and SQL Database to an Azure Website" authors="riande"  solutions="" writer="riande" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande"></tags>

# Implantar um aplicativo ASP.NET MVC 5 seguro com a associação, o OAuth e o banco de dados SQL em um site do Azure

***Atualizado em 2 de abril de 2014.***

Este tutorial mostra como criar um aplicativo Web ASP.NET MVC 5 seguro que permite que os usuários façam logon com credenciais do Google ou do Facebook. Você também implantará o aplicativo no Azure.

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2013, o SDK instalará automaticamente o Visual Studio 2013 para o Web Express. É possível iniciar o desenvolvimento para o Azure de maneira gratuita. Se você deseja usar o Visual Studio 2012, consulte o [tutorial anterior][tutorial anterior]. Esta versão do tutorial é muito mais simples do que a versão anterior.

Este tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure. Ao concluir este tutorial, você terá um aplicativo Web controlado por dados seguro e em funcionamento na nuvem usando um banco de dados em nuvem.

Você aprenderá a:

-   Como criar um projeto ASP.NET MVC 5 seguro e publicá-lo em um site do Azure.
-   Como usar [OAuth][OAuth], [OpenID][OpenID] e o banco de dados de associações do ASP.NET para proteger seu aplicativo.
-   Como usar a nova API de associação para adicionar usuários e funções.
-   Como usar um banco de dados SQL para armazenar dados no Azure.

Você criará um aplicativo Web de lista de contatos simples criado no ASP.NET MVC 5 e que usa o ADO.NET Entity Framework para acesso ao banco de dados. A seguinte ilustração mostra a página de logon do aplicativo concluído:

![página de logon][página de logon]

> [WACOM.NOTE] Para concluir este tutorial, é necessário uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [ativar os benefícios de assinante do MSDN][ativar os benefícios de assinante do MSDN] ou [inscrever-se para uma avaliação gratuita][inscrever-se para uma avaliação gratuita].

Neste tutorial:

-   [Configurar o ambiente de desenvolvimento][Configurar o ambiente de desenvolvimento]
-   [Configurar o ambiente do Azure][Configurar o ambiente do Azure]
-   [Criar um aplicativo ASP.NET MVC 5][Criar um aplicativo ASP.NET MVC 5]
-   [Implantar o aplicativo no Azure][Implantar o aplicativo no Azure]
-   [Adicionar um banco de dados ao aplicativo][Adicionar um banco de dados ao aplicativo]
-   [Adicionar um provedor de OAuth][Adicionar um provedor de OAuth]
-   [Usando a API de Associação][Usando a API de Associação]
-   [Implantar o aplicativo no Azure][1]
-   [Próximas etapas][Próximas etapas]

[WACOM.INCLUDE [install-sdk-2013-only][install-sdk-2013-only]]

Para usar o novo certificado SSL para localhost, você precisará instalar o [Visual Studio 2013 Update 2 RC][Visual Studio 2013 Update 2 RC] ou posterior.

## <a name="bkmk_setupwindowsazure"></a>Configurar o ambiente do Azure

Em seguida, configure o ambiente do Azure criando um site do Azure e um banco de dados SQL.

### Criar um site e um banco de dados SQL no Azure

O seu site do Azure será executado em um ambiente de hospedagem compartilhado, o que significa que ele é executado em máquinas virtuais (VMs) compartilhadas com outros clientes do Azure. Um ambiente de hospedagem compartilhado é uma maneira de começar na nuvem a um baixo custo. Mais tarde, se seu tráfego da Web aumentar, o aplicativo poderá ser dimensionado para atender à necessidade executando em VMs dedicadas. Se precisar de uma arquitetura mais complexa, você poderá migrar para um Serviço de Nuvem do Azure. Os serviços de nuvem são executados em VMs dedicadas que podem ser configuradas de acordo com suas necessidades.

O Banco de Dados SQL no Azure é um serviço de banco de dados relacional baseado em que é fundamentado em tecnologias do SQL Server. As ferramentas e os aplicativos que funcionam com o SQL Server também funcionam com o Banco de Dados SQL.

1.  No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Sites** na guia esquerda, em seguida, clique em **Novo**.

    ![Novo botão no Portal de Gerenciamento][Novo botão no Portal de Gerenciamento]

2.  Clique em **Site** e clique em **Criação personalizada**.

    ![Criar com link de banco de dados no Portal de Gerenciamento][Criar com link de banco de dados no Portal de Gerenciamento]

    O assistente **Novo site - Criação personalizada** é aberta.

3.  Na etapa **Criar Site** do assistente, insira uma cadeia de caracteres na caixa **URL** a ser usada como a URL exclusiva de seu aplicativo. A URL completa consistirá no que você digitar aqui mais o sufixo que você vê ao lado da caixa de texto. A ilustração mostra uma URL que provavelmente já é utilizada e, portanto, você precisa escolher outra.

    ![Criar com link de banco de dados no Portal de Gerenciamento][2]

4.  Na lista suspensa **Banco de dados**, escolha **Criar um banco de dados SQL gratuito**.

5.  Na lista suspensa **Região**, escolha a mesma região selecionada para o site.
    Esta configuração especifica qual centro de dados a sua VM será executada.
6.  Na caixa **Nome da Cadeia de Conexão do Banco de Dados**, deixe o valor padrão de *DefaultConnection*.
7.  Clique na seta apontado na parte inferior direita da caixa de diálogo.
    O assistente avança para a etapa **Especificar configurações de banco de dados**.

8.  Na caixa **Nome**, digite *ContactDB*. (consulte a imagem abaixo).
9.  Na caixa **Servidor**, selecione **Novo servidor de banco de dados SQL**. (consulte a imagem abaixo). Como alternativa, se tiver criado um banco de dados SQL Server, você poderá selecionar esse SQL Server no controle suspenso.
10. Defina a **Região** como a mesma área na qual você criou o site.
11. Insira um **Nome de Logon** e **Senha** de administrador. Se selecionou **Novo servidor do Banco de Dados SQL**, você não irá digitar um nome e uma senha existentes aqui; digite um novo nome e uma senha que você está definindo agora para usar mais tarde ao acessar o banco de dados. Se você tiver selecionado um SQL Server criado anteriormente, será solicitada a senha para o nome da conta do SQL Server anterior que você criou. Neste tutorial, não selecionaremos a caixa **Avançado**. Para um banco de dados gratuito, você pode definir somente o agrupamento.
12. Clique na marca de seleção no canto inferior direito da caixa para indicar que você concluiu.

    ![Etapa Configurações do banco de dados do Novo site - Criar com o assistente de banco de dados][Etapa Configurações do banco de dados do Novo site - Criar com o assistente de banco de dados]

    A imagem a seguir mostra o uso de um SQL Server e logon existentes.

    ![Etapa Configurações do banco de dados do Novo site - Criar com o assistente de banco de dados][3]

    O Portal de Gerenciamento do Azure retorna para a página Sites e a coluna **Status** mostra que o site está sendo criado. Depois de um tempo (normalmente menos de um minuto), a coluna **Status** mostra que o site foi criado com êxito. Na barra de navegação à esquerda, o número de sites presentes na sua conta é exibido próximo ao ícone **Sites**, enquanto o número de bancos de dados aparece ao lado do ícone **Bancos de Dados SQL**.

## <a name="bkmk_createmvc4app"></a>Criar um aplicativo ASP.NET MVC 5

Você criou um Site do Azure, mas ainda não há conteúdo nele. A próxima etapa é criar o aplicativo Web do Visual Studio que será publicado no Azure.

### Criar o projeto

1.  No menu **Arquivo**, clique em **Novo Projeto**.

 ![Novo projeto no menu Arquivo][Novo projeto no menu Arquivo]

2.  Na caixa de diálogo **Novo Projeto**, expanda **C\#**, selecione **Web** em **Modelos instalados** e, em seguida, selecione **Aplicativo Web ASP.NET**.

3.  Nomeie o aplicativo **ContactManager** e clique em **OK**.

 ![Caixa de diálogo Novo Projeto][Caixa de diálogo Novo Projeto]

 **Observação:** A imagem mostra "MyExample" como o nome, mas certifique-se de inserir "ContactManager". Os blocos de código que você irá copiar posteriormente supõem que o nome do projeto seja ContactManager.

4.  Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo **MVC**, **desmarque** da caixa de seleção **Criar recursos remotos** e clique em **OK**. (A caixa de seleção deve ser rotulada **Host na nuvem** em vez de **Criar recursos remotos**.)

![Caixa de diálogo Novo Projeto ASP .NET][Caixa de diálogo Novo Projeto ASP .NET]

### Configurar o cabeçalho e o rodapé da página

1.  No **Gerenciador de Soluções**, abra o arquivo *Layout.cshtml* na pasta *Views\\Shared*.

    ![\_Layout.cshtml no Gerenciador de Soluções][\_Layout.cshtml no Gerenciador de Soluções]

2.  Substitua as duas ocorrências de "Meu Aplicativo ASP.NET MVC" por "Gerenciador de Contatos".
3.  Substitua "Nome do aplicativo" por "Demonstração de CM".

4.  Atualize o primeiro link de ação e substitua *Home* por *Cm* para usar o Controlador de *Cm*.

    ![alterações de código][alterações de código]

### Executar o aplicativo localmente

1.  Pressione CTRL+F5 para executar o aplicativo.

    A home page do aplicativo é exibida no navegador padrão.

    ![Site em execução localmente][Site em execução localmente]

Isso é tudo o que você precisa fazer por enquanto para criar o aplicativo que você implantará no Azure. Posteriormente, você adicionará a funcionalidade do banco de dados.

## <a name="bkmk_deploytowindowsazure1"></a>Implantar o aplicativo no Azure

1.  No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

 ![Publicar no menu de contexto do projeto][Publicar no menu de contexto do projeto]

 O assistente de **Publicar Web** é aberto.

2.  Na guia **Perfil** do assistente **Publicar a web**, clique em **Sites do Azure**.

 ![Importar configurações de publicação][Importar configurações de publicação]

3.  Clique no botão **Entrar** e faça logon no portal do Azure.

 ![entrar][entrar]

 Após fazer logon, a caixa de diálogo **Selecionar site existente** é exibida.

4.  Selecione o site criado na primeira parte deste tutorial e clique em **OK**.

 ![selecionar site][selecionar site]

5.  Na caixa de diálogo **Publicar na Web**, clique em **Publicar**.

    ![Publicar][Publicar]

    O aplicativo que você criou agora está em execução na nuvem. Na próxima vez que você implantar o aplicativo, somente os arquivos alterados (ou novos) serão implantados.

    ![Executando na nuvem][Executando na nuvem]

## <a name="bkmk_addadatabase"></a>Adicionar um banco de dados ao aplicativo

Em seguida, você atualizará o aplicativo MVC para adicionar a habilidade de exibir e atualizar contatos e armazenar os dados em um banco de dados. O aplicativo usará o Entity Framework para criar o banco de dados e ler e atualizar dados no banco de dados.

### Adicionar classes de modelo de dados aos contatos

Você começa criando um modelo de dados simples no código.

1.  No **Gerenciador de Soluções**, clique com o botão direito na pasta Modelos e clique em **Adicionar**. Em seguida, clique em **Classe**.

    ![Adicionar Classe no menu de contexto da pasta Modelos][Adicionar Classe no menu de contexto da pasta Modelos]

2.  Na caixa de diálogo **Adicionar Novo Item**, nomeie o novo arquivo de classe *Contact.cs* e clique em **Adicionar**.

    ![Caixa de diálogo Adicionar Novo Item][Caixa de diálogo Adicionar Novo Item]

3.  Substitua o conteúdo do arquivo Contacts.cs pelo código a seguir.

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

## <a name="bkmk_addcontroller"></a>Adicionar um controlador e uma visualização para os dados

1.  Compile o projeto **(Ctrl+Shift+B)**. (Você deve construir o projeto antes de usar o mecanismo de scaffolding.)
2.  No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Controladores e clique em **Adicionar**. Em seguida, clique em **Controlador**.

    ![Adicionar Controlador no menu de contexto da pasta Controladores][Adicionar Controlador no menu de contexto da pasta Controladores]

3.  Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC 5 com modos de exibição, usando o EF** e, em seguida, clique em **Adicionar**.

    ![Adicionar Scaffold dlg][Adicionar Scaffold dlg]

4.  Na caixa suspensa **Classe do modelo**, selecione **Contato (ContactManager.Models)**. (Consulte a imagem abaixo).
5.  Em **Classe do contexto de dados**, selecione **ApplicationDbContext (ContactManager.Models)**. O **ApplicationDbContext** será usado para o BD de associação e nossos dados de contato.
6.  Na caixa de entrada de texto **Nome de controlador**, insira "CmController" para o nome do controlador.

    ![Novos dados ctx dlg][Novos dados ctx dlg]

7.  Clique em **Adicionar**.

O Visual Studio cria métodos do controlador e modos de exibição para as operações de banco de dados CRUD para objetos **Contact**.

## Habilitar migrações, criar o banco de dados, adicionar dados de exemplo e um inicializador de dados

A próxima tarefa é habilitar o recurso [Codificar Primeiras Migrações][Codificar Primeiras Migrações] para criar o banco de dados com base no modelo de dados criado.

1.  No menu **Ferramentas**, selecione **Gerenciador de pacotes NuGet** e, em seguida, selecione **Console do gerenciador de pacotes**.
    ![Package Manager Console in Tools menu][Package Manager Console in Tools menu]

2.  Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

        enable-migrations

    O comando **enable-migrations** cria a pasta *Migrações* e coloca nessa pasta um arquivo *Configuration.cs*, que pode ser editado para propagar o banco de dados e configurar Migrações.

3.  Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

        add-migration Initial

    O comando **add-migration Initial** gera um arquivo chamado **\<date\_stamp\>Initial** na pasta *Migrações* que cria o banco de dados. O primeiro parâmetro (**Initial**) é arbitrário e é usado para criar o nome do arquivo. É possível ver os novos arquivos de classe em **Gerenciador de Soluções**.
    Na classe **Inicial**, o método **Up** cria a tabela Contatos e o método **Down** (usado quando você deseja retornar ao estado anterior) a descarta.

4.  Abra o arquivo *Migrations\\Configuration.cs*.
5.  Adicione o seguinte namespace.

         using ContactManager.Models;

6.  Substitua o método *Seed* pelo seguinte código:

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

    Esse código inicializa (propaga) o banco de dados com as informações de contato. Para obter mais informações sobre a propagação do banco de dados, consulte [Propagando e depurando BDs do Entity Framework (EF)][Propagando e depurando BDs do Entity Framework (EF)].

7.  Em **Console do Gerenciador de Pacotes**, digite o comando:

        update-database

    ![Comandos do Console do Gerenciador de Pacotes][Comandos do Console do Gerenciador de Pacotes]

    O **update-database** executa a primeira migração que cria o banco de dados. Por padrão, o banco de dados é criado como um banco de dados LocalDB do SQL Server Express.

8.  Pressione CTRL+F5 para executar o aplicativo e, em seguida, clique no link **Demonstração de CM**, ou navegue até <http://localhost>:(port\#)/Cm.

    O aplicativo mostra os dados de semente e fornece links de edição, detalhes e exclusão. Você pode criar, editar, excluir e exibir dados.

    ![Exibição do MVC de dados][Exibição do MVC de dados]

## <a name="addOauth"></a><span class="short-header">OAuth</span>Adicionar um provedor de OAuth2 e OpenID

[OAuth][4] é um protocolo aberto que permite autorização segura em um método simples e padrão da web, aplicativos móveis e de área de trabalho. O modelo de Internet do ASP.NET MVC usa OAuth e [OpenID][OpenID] para expor o Facebook, o Twitter, o Google e a Microsoft como provedores de autenticação. Embora este tutorial use apenas o Google como o provedor de autenticação, você pode facilmente modificar o código para usar qualquer um dos provedores. As etapas para implementar outros provedores são muito semelhantes às etapas que você verá neste tutorial. Para usar o Facebook como um provedor de autenticação, consulte o tutorial [Criar um aplicativo do ASP.NET MVC 5 com logon OAuth2 e OpenID do Facebook e do Google][Criar um aplicativo do ASP.NET MVC 5 com logon OAuth2 e OpenID do Facebook e do Google].

Além da autenticação, o tutorial também usará funções para implementar a autorização. Somente os usuários que você adicionar à função *canEdit* poderão alterar dados (isto é, criar, editar ou excluir contatos).

1.  Siga as instruções em meu tutorial [Criar um aplicativo ASP.NET MVC 5 com o logon do Facebook, Google OAuth2 e OpenID][Criar um aplicativo do ASP.NET MVC 5 com logon OAuth2 e OpenID do Facebook e do Google] em **Criando um aplicativo do Google para o OAuth 2 para configurar um aplicativo do Google para o OAuth2**.
2.  Abra o arquivo *App\_Start\\Startup.Auth.cs*. Remova os caracteres de comentários do método *app.UseGoogleAuthentication()* e digite o **clientId** e **clientSecret**.


             public void ConfigureAuth(IAppBuilder app)
             {
                // Permite que o aplicativo use um cookie para armazenar as informações para o usuário conectado
                app.UseCookieAuthentication(new CookieAuthenticationOptions
                {
                   AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
                   LoginPath = new PathString("/Account/Login")
                });
                // Use um cookie para armazenar temporariamente as informações sobre a conexão de um usuário a um provedor de logon de terceiros
                app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);

                app.UseGoogleAuthentication(
                   clientId: "000-000.apps.googleusercontent.com",
                   clientSecret: "00000000000");
             } 

1.  Execute o aplicativo e clique no link **Logon**.
2.  Em **Usar outro serviço para fazer logon**, clique no botão **Google**.

    ![Logon no Google][Logon no Google]

3.  Insira suas credenciais.
4.  O servidor de autenticação do Google solicitará permissão para o aplicativo exibir o seu endereço de email e as informações básicas sobre a sua conta. Clique em **Aceitar**.
    ![GOOG asking for permission][GOOG asking for permission]
5.  Você é redirecionado para a página Registrar. Se desejar, você pode alterar os padrões de nome de usuário para o alias de email que você usou para se registrar. Clique em **Registrar**.

    ![registrar][registrar]

## <a name="mbrDB"></a><span class="short-header">DB de associação</span>Usando a API de associação

Nesta seção, você irá adicionar um usuário local e a função *canEdit* ao banco de dados de associação. Somente os usuários na função *canEdit* poderão editar os dados. A melhor prática é nomear as funções pelas ações que podem executar, portanto, *canEdit* é preferível do que uma função chamada *admin*. Quando seu aplicativo evoluir, você poderá adicionar novas funções, como *canDeleteMembers*, em vez da menos descritiva *superAdmin*.

1.  Abra o arquivo *migrations\\configuration.cs* e adicione as seguintes instruções `using`:

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

2.  Adicionar o seguinte método **AddUserAndRole** à classe:

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

3.  Chamar o novo método do método **Seed**:

         protected override void Seed(ContactManager.Models.ApplicationDbContext context) { AddUserAndRole(context); context.Contacts.AddOrUpdate(p => p.Name, // Code removed for brevity }

    <span></span>
    As imagens a seguir mostram as mudanças no método *Seed*:

    </p>
    ![imagem de código][imagem de código]

Esse código cria uma nova função chamada *canEdit*, cria um novo usuário local <*user1@contoso.com*> e adiciona [\*user1@contoso.com\*][\*user1@contoso.com\*] à função *canEdit*. Para obter mais informações, consulte a [página de recursos de Identidade do ASP.NET][página de recursos de Identidade do ASP.NET].

## Usar código temporário para adicionar novos usuários de logon social à função canEdit

Nesta seção, você irá modificar temporariamente o método **ExternalLoginConfirmation** no controlador de conta para adicionar novos usuários com registro com um provedor OAuth ou OpenID na função *canEdit*. Vamos modificar temporariamente o método **ExternalLoginConfirmation** para adicionar automaticamente novos usuários a uma função administrativa. Até que possamos fornecer uma ferramenta para adicionar e gerenciar funções, vamos usar o código de registro automático temporário abaixo. No futuro, esperamos fornecer uma ferramenta semelhante à [WSAT][WSAT] que permita criar e editar contas de usuário e funções. Mais adiante no tutorial, mostrarei como você pode usar o **Gerenciador de Servidores** para adicionar usuários a funções.

1.  Abra o arquivo **Controllers\\AccountController.cs** e navegue para o método **ExternalLoginConfirmation**.
2.  Adicione a seguinte chamada para **AddToRoleAsync** antes da chamada **SignInAsync**.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

O código acima adiciona o usuário recém-registrado à função "canEdit", que oferece acesso a métodos de ação que alteram (editam) dados. Uma imagem da alteração de código é mostrada abaixo:

![código][código]

Mais tarde no tutorial você implantará o aplicativo no Azure, onde você fará logon no Google ou outro provedor de autenticação de terceiros. Isso adicionará a conta recém-registrada à função *canEdit*. Qualquer pessoa que localize a URL do site e que tenha uma ID do Google poderá registrar-se e atualizar seu banco de dados. Para impedir que outras pessoas façam isso, você pode parar o site. Você poderá verificar quem está na função *canEdit* examinando o banco de dados.

No **Console do Gerenciador de Pacotes** pressione a tecla de seta para cima para exibir o seguinte comando:

        Update-Database

Execute o comando **Update-Database** que executará o método **Seed** e o **AddUserAndRole** que você acabou de adicionar. O **AddUserAndRole** irá criar o usuário <*user1@contoso.com>\* e o adicionará à função *canEdit*.

## Proteger o aplicativo com SSL e o atributo Authorize

Nesta seção, você aplicará o atributo [Authorize][Authorize] para restringir o acesso aos métodos de ação. Os usuários anônimos poderão exibir o método de ação **Índice** do controlador doméstico apenas. Os usuários registrados poderão ver os dados de contato (as páginas **Índice** e **Detalhes** do controlador de Cm), as páginas Sobre e Contato. Somente usuários na função *canEdit* poderão acessar os métodos de ação que alteram dados.

1.  Adicione o filtro [Authorize][Authorize] e o filtro [RequireHttps][RequireHttps] ao aplicativo. Uma abordagem alternativa é adicionar os atributos [Authorize][Authorize] e [RequireHttps][RequireHttps] a cada controlador, mas aplicá-los a todo o aplicativo é considerado uma melhor prática de segurança. Adicionando-os globalmente, cada novo controlador e método de ação que você adicionar será automaticamente protegido, você não precisará se lembrar de aplicá-los. Para obter mais informações, consulte [Protegendo seu aplicativo ASP.NET MVC e o novo atributo AllowAnonymous][Protegendo seu aplicativo ASP.NET MVC e o novo atributo AllowAnonymous]. Abra o arquivo *App\_Start\\FilterConfig.cs* e substitua o método *RegisterGlobalFilters* com o método seguinte (o qual adiciona os dois filtros):

         public static void RegisterGlobalFilters(GlobalFilterCollection filters) { filters.Add(new HandleErrorAttribute()); filters.Add(new System.Web.Mvc.AuthorizeAttribute()); filters.Add(new RequireHttpsAttribute()); } 

    <span></span>

    </p>
    A imagem seguinte mostra o código alterado:

    ![imagem de código][5]

    O filtro [Authorize][Authorize] aplicado no código acima impedirá que usuários anônimos acessem qualquer método no aplicativo. Você usará o atributo [AllowAnonymous][Protegendo seu aplicativo ASP.NET MVC e o novo atributo AllowAnonymous] para cancelar o requisito de autorização em alguns métodos, portanto, os usuários anônimos poderão efetuar logon e exibir a home page. O [RequireHttps][RequireHttps] exigirá que todo acesso ao aplicativo Web seja feito por HTTPS.

2.  Adicione o atributo [AllowAnonymous][Protegendo seu aplicativo ASP.NET MVC e o novo atributo AllowAnonymous] ao método **Index** para o controlador Início. O atributo [AllowAnonymous][Protegendo seu aplicativo ASP.NET MVC e o novo atributo AllowAnonymous] permite criar a lista branca dos métodos para os quais você deseja recusar autorização. Uma imagem de parte do HomeController é mostrada abaixo:

    ![código][6]

3.  Faça uma pesquisa global por *AllowAnonymous* e você verá que ele é usado no logon e nos métodos de registro do controlador Conta.
4.  In *CmController.cs*, add `[Authorize(Roles = "canEdit")]` to the HttpGet and HttpPost methods that change data (Create, Edit, Delete, every action method except Index and Details) in the *Cm* controller. Uma parte do código concluído é mostrada abaixo:

    ![imagem do código][imagem do código]

## Habilitar SSL para o projeto

1.  Habilite o SSL. No Gerenciador de Soluções, clique no projeto **ContactManager** e clique em F4 para exibir a caixa de diálogo de propriedades. Altere **SSL Habilitado** para verdadeiro. Copie a **URL do SSL**. A URL do SSL será <https://localhost:44300/>, a menos que você tenha criado Sites SSL anteriormente.

    ![habilitar SSL][habilitar SSL]

2.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **Gerenciador de Contatos** e clique em **Propriedades**.
3.  Na guia à esquerda, clique em **Web**.
4.  Altere a **URL do Projeto** para usar a **URL de SSL** e salve a página (Ctrl+S).

    ![habilitar SSL][7]

5.  Verifique se o Internet Explorer é o navegador que inicia o Visual Studio, como mostrado na imagem abaixo:

    ![navegador padrão][navegador padrão]

    O seletor de navegador permite que você especifique o navegador que inicia o Visual Studio.

 ![seletor de navegador][seletor de navegador]

    You can select multiple browsers and have Visual Studio update each browser when you make changes. For more information see [Using Browser Link in Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).

1.  Pressione CTRL+F5 para executar o aplicativo. Siga as instruções para confiar no certificado autoassinado que o IIS Express gerou.

    ![instruções para confiar no certificado autoassinado que o IIS Express gerou][instruções para confiar no certificado autoassinado que o IIS Express gerou]

2.  Leia o diálogo **Aviso de Segurança** e clique em **Sim**, se desejar instalar o certificado que representa o **localhost**.

 ![Aviso de certificado do IIS Express para localhost][Aviso de certificado do IIS Express para localhost]

1.  O IE mostra a *Home page* e não há nenhum aviso de SSL.

    ![IE com SSL do localhost e sem avisos][IE com SSL do localhost e sem avisos]

    Google Chrome também aceita o certificado e mostrará o conteúdo de HTTPS sem aviso. O Firefox usa seu próprio repositório de certificados, portanto, será exibido um aviso. Em nosso aplicativo, você pode clicar com segurança em **Eu compreendo os riscos**.

    ![Aviso do FireFox Cert][Aviso do FireFox Cert]

2.  Se você ainda estiver conectado de uma sessão anterior, clique no link **Logoff**.
3.  Clique nos links **Sobre** ou **Contato**. Você será redirecionado para a página de logon, porque usuários anônimos não podem exibir essas páginas.
4.  Clique no link **Registrar-se como um novo usuário** e adicione um usuário local com email <*joe@contoso.com*>. Verifique se *Joe* pode exibir as páginas Home, Sobre e Contato.

    ![logon][logon]

5.  Clique em *Demonstração de CM* e verifique se você vê os dados.
6.  Clique em um link de edição na página, você será redirecionado para a página de logon (porque um novo usuário local não é adicionado à função *canEdit*).
7.  Faça logon como <*user1@contoso.com>\* com a senha "P\_assw0rd1" (o "0" em "word" é um zero). Você será redirecionado para a página de edição selecionada anteriormente.

 Se você não puder efetuar logon com essa conta e senha, tente copiar a senha do código-fonte e colá-la. Se ainda não for possível fazer logon, verifique a coluna **UserName** da tabela **AspNetUsers** para verificar se <*user1@contoso.com>\* foi adicionado.

1.  Verifique se você pode fazer alterações nos dados.

## <a name="bkmk_deploytowindowsazure11"></a>Implantar o aplicativo no Azure

1.  No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

    ![Publicar no menu de contexto do projeto][8]

    O assistente de **Publicar Web** é aberto.

2.  Clique a guia **Configurações** no lado esquerdo da caixa de diálogo **Publicar web**. Clique no ícone **v** para selecionar a **Cadeia de conexão remota** para **ApplicationDbContext** e selecione **ContactDB**.

    ![configurações][configurações]

3.  Em **ContactManagerContext**, selecione **Executar as migrações iniciais de código**.

    ![configurações][9]

4.  Clique em **Publicar**.

5.  Faça logon como <*user1@contoso.com>\* e verifique se você pode editar dados.

6.  Faça logoff.

7.  Faça logon usando o Google ou o Facebook. Isso irá adicionar a conta do Google ou do Facebook à função **canEdit**.

### Parar o site para impedir que outras pessoas se registrem

1.  No **Gerenciador de Servidores**, navegue até **Sites**.
2.  Clique com o botão direito do mouse em cada instância do Site e selecione **Parar Site**.

    ![parar site][parar site]

    Alternativamente, no Portal de Gerenciamento do Azure, será possível selecionar o site e clicar no ícone **parar** na parte inferior da página.

    ![parar site][10]

### Remover AddToRoleAsync, publicar e testar

1.  Comente ou remova o código a seguir do método **ExternalLoginConfirmation** no controlador de Conta:
     `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
2.  Compile o projeto (que salva as alterações no arquivo e verifica se não há erros de compilação).
3.  No Visual Studio, clique com o botão direito no nome do projeto no **Gerenciador de Soluções** e selecione **Publicar**.

    ![Publicar no menu de contexto do projeto][Publicar no menu de contexto do projeto]

4.  Clique no botão **Iniciar Visualização**. Somente os arquivos que precisam ser atualizados são implantados.
5.  Iniciar o site no Visual Studio ou no Portal. **Não será possível publicar enquanto o site estiver parado**.

    ![iniciar site][iniciar site]

6.  Volte para o Visual Studio e clique em **Publicar**.
7.  O seu aplicativo do Azure é aberto no navegador padrão. Se tiver feito logon, faça logoff para que seja possível exibir a home page como um usuário anônimo.
8.  Clique no link **Sobre**. Você será redirecionado para a página Logon.
9.  Clique no link **Registrar** na página Logon e crie a conta local. Usaremos essa conta local para verificar se você pode acessar as páginas somente leitura, mas não pode acessar páginas que alteram dados (que são protegidas pela função *canEdit*). Mais adiante, no tutorial, removeremos o acesso à conta local.

    ![Registrar][Registrar]

10. Verifique se você pode navegar até as páginas *Sobre* e *Contato*.

    ![Fazer logoff][Fazer logoff]

11. Clique no link **Demonstração de CM** para navegar até o controlador de **Cm**. Como alternativa, você pode acrescentar *Cm* à URL.

    ![Página CM][Página CM]

12. Clique em um link de edição. Você será redirecionado para a página de logon. Em **Usar outro serviço para fazer logon**, clique em Google ou Facebook e faça logon com a conta registrada anteriormente. (Se estiver trabalhando rapidamente e o cookie da sessão não expirou, você estará automaticamente conectado à conta do Facebook ou Google usados anteriormente.)
13. Verifique se é possível editar dados enquanto estiver conectado nessa conta.
    **Observação:** Não é possível fazer logon do Google neste aplicativo e fazer logon em outra conta do Google com o mesmo navegador. Se você estiver usando um navegador, precisará navegar até o Google e fazer logoff. Você pode fazer logon com outra conta do mesmo autenticador de terceiros (como o Google) usando outro navegador.

Se ainda não preencheu o nome e o sobrenome das informações da sua conta do Google, uma NullReferenceException ocorrerá.

## Examine o BD do SQL Azure

1.  No **Gerenciador de Servidores**, navegue até **ContactDB**.
2.  Clique com o botão direito do mouse em **ContactDB** e selecione **Abrir no Pesquisador de Objetos do SQL Server**.

    ![abrir no SSOX][abrir no SSOX]

**Observação:** Se não for possível expandir o item **Bancos de dados SQL** e *não puder* ver o item **ContactDB** no Visual Studio, você deverá seguir as instruções para abrir uma porta de firewall ou um grupo de portas. Siga as instruções em **Configurar regras de firewall do Azure**. Talvez você precise aguardar alguns minutos para acessar o banco de dados depois de adicionar a regra de firewall.

1.  Clique com o botão direito do mouse na guia **AspNetUsers** e selecione **Exibir Dados**.

    ![Página CM][11]

2.  Observe o ID da conta do Google com a qual você se registrou para estar na função **canEdit** e a ID de <*user1@contoso.com*>. Esses devem ser os únicos usuários na função **canEdit**. (Você verificará isso na próxima etapa.)

    ![Página CM][12]

3.  No **Pesquisador de Objetos do SQL Server**, clique com o botão direito do mouse em **AspNetUserRoles** e selecione **Exibir Dados**.

    ![Página CM][13]

Verifique se as **UserId**s são de <*user1@contoso.com>\* e da conta do Google que você registrou.

## Configurar regras de firewall do Azure

Siga as etapas nesta seção se não for possível se conectar ao SQL Azure do Visual Studio, ou se você receber uma caixa de diálogo de erro informando que "Não é possível abrir o servidor".

![erro do firewall][erro do firewall]

Você precisará adicionar o endereço IP para os IPs permitidos.

1.  No Portal do Azure, selecione **Bancos de dados SQL** na guia esquerda.

    ![Selecionar SQL][Selecionar SQL]

2.  Clique em **ContactDB**.

3.  Clique no link **Configurar regras de firewall do Azure para esse endereço IP**.

    ![regras de firewall][regras de firewall]

4.  Quando você receber o prompt "O endereço IP atual xxx.xxx.xxx.xxx não está incluído nas regras de firewall existentes. Deseja atualizar as regras de firewall?", clique em **Sim**. Normalmente não é suficiente adicionar esse endereço por trás de alguns firewalls corporativos, você precisará adicionar um intervalo de endereços IP.

A próxima etapa é adicionar um intervalo de endereços IP permitidos.

1.  No Portal do Azure, clique em **Bancos de Dados SQL**.
2.  Selecione a guia **Servidores** e, em seguida, clique no servidor que deseja configurar.

    ![Guia de servidores no Azure][Guia de servidores no Azure]

3.  Clique na guia **Configurar**.

4.  Adicione um nome de regra e endereços IP iniciais e finais.

    ![intervalo IP][intervalo IP]

5.  Na parte inferior da página, clique em **Salvar**.
6.  Deixe comentários e avise-me se for necessário adicionar um intervalo de endereços IP para se conectar.

Finalmente, você pode se conectar à instância do Banco de Dados SQL no Pesquisador de Objetos do SQL Server (SSOX)

1.  No menu Exibir, clique em **Pesquisador de Objetos do SQL Server**.
2.  Clique com o botão direito do mouse em **SQL Server** e selecione **Adicionar SQL Server**.
3.  Na caixa de diálogo **Conectar-se ao servidor**, defina a **Autenticação** como **Autenticação do SQL Server**. Você obterá o **Nome do servidor** e o **Logon** no Portal do Azure.
4.  Em seu navegador, navegue até o portal e selecione **Bancos de Dados SQL**.
5.  Selecione o **ContactDB** e, em seguida, clique em **Exibir cadeias de conexão do Banco de Dados SQL**.
6.  Na página **Cadeias de Conexão**, copie o **Servidor** e a **ID do Usuário**.

    ![cadeia de conexão][cadeia de conexão]

7.  Cole os valores de **Servidor** e **ID de usuário** na caixa de diálogo **Conectar-se ao Servidor** no Visual Studio. O valor da **ID do Usuário** vai na entrada **Logon**. Digite a senha que você usou para criar o BD SQL.

    ![Conectar-se à DLG do servidor][Conectar-se à DLG do servidor]

Agora você pode navegar para o BD de contato usando as instruções fornecidas anteriormente.

## Adicionar um usuário à função canEdit editando tabelas do banco de dados

Anteriormente, no tutorial, você usou código para adicionar usuários à função canEdit. Um método alternativo é manipular diretamente os dados nas tabelas de associação. As etapas a seguir mostram como usar esse método alternativo para adicionar um usuário a uma função.

1.  No **Pesquisador de Objetos do SQL Server**, clique com o botão direito do mouse em **AspNetUserRoles** e selecione **Exibir Dados**.

    ![Página CM][13]

2.  Copie a *RoleId* e cole-a na linha vazia (nova).

    ![Página CM][14]

3.  Na tabela **AspNetUsers**, encontre o usuário que você deseja incluir na função, copiando a *Id* do usuário e, em seguida, cole-a na coluna **UserId** da tabela **AspNetUserRoles**.

Estamos trabalhando em uma ferramenta que irá facilitar bastante o gerenciamento de usuários e funções.

## Considerações sobre o registro local

O registro de associação ao ASP.NET atual no projeto não oferece suporte para a redefinição de senha e não verifica se o registro é de uma pessoa (por exemplo, com uma [CAPTCHA][CAPTCHA]). Quando um usuário é autenticado usando um dos provedores de terceiros, ele pode se registrar. Se você optar por desabilitar o registro local, siga estas etapas:

1.  No AccountController, remova o atributo *[AllowAnonymous]* de do métodos GET e POST em *Registrar*. Isso evitará que bots e usuários anônimos se registrem.
2.  Na pasta *Views\\Shared*, remova o arquivo \*\_LoginPartial.cshtml\* e remova o link de ação Register.
3.  No arquivo *Views\\Account\\Login.cshtml*, remova o link de ação Registrar.
4.  Implante o aplicativo.

## <a name="nextsteps"></a><span class="short-header">Próximas etapas</span>Próximas etapas

Siga o tutorial [Criar um aplicativo do ASP.NET MVC 5 com logon OAuth2 e OpenID do Facebook e do Google][Criar um aplicativo do ASP.NET MVC 5 com logon OAuth2 e OpenID do Facebook e do Google] para obter instruções sobre como adicionar dados de perfil ao banco de dados de registro do usuário e para obter instruções detalhadas sobre como usar o Facebook como um provedor de autenticação.

Para habilitar os botões de logon social mostrados na parte superior deste tutorial, consulte [Muitos botões de logon social para ASP.NET MVC 5][Muitos botões de logon social para ASP.NET MVC 5].

Um bom lugar para obter mais informações sobre o ASP.NET MVC é o meu tutorial [Introdução ao ASP.NET MVC 5][Introdução ao ASP.NET MVC 5]. O excelente artigo [Introdução ao EF e ao MVC][Introdução ao EF e ao MVC] de Tom Dykstra mostra uma programação de MVC e EF mais avançada.

Este tutorial e o aplicativo de exemplo foram escritos por [Rick Anderson][Rick Anderson] (Twitter [@RickAndMSFT][@RickAndMSFT]) com a assistência de Tom Dykstra e Barry Dorrans (Twitter [@blowdart][@blowdart]).

Deixe comentários sobre o que você gostou ou do que você gostaria de ver melhorado, não apenas sobre o próprio tutorial, mas também sobre os produtos que ele demonstra. Seus comentários nos ajudarão a priorizar melhorias. Você também pode solicitar e votar em novos tópicos em [Mostre-me como com código][Mostre-me como com código].

<!-- bookmarks --> 
  [CAPTCHA]: http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership
  [Muitos botões de logon social para ASP.NET MVC 5]: http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/
  [Introdução ao ASP.NET MVC 5]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
  [Introdução ao EF e ao MVC]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
  [Rick Anderson]: http://blogs.msdn.com/b/rickandy/
  [@RickAndMSFT]: https://twitter.com/RickAndMSFT
  [@blowdart]: https://twitter.com/blowdart
  [Mostre-me como com código]: http://aspnet.uservoice.com/forums/228522-show-me-how-with-code
  [tutorial anterior]: /en-us/develop/net/tutorials/web-site-with-sql-database-vs2012/
  [OAuth]: http://oauth.net/ "http://oauth.net/"
  [OpenID]: http://openid.net/
  [página de logon]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png
  [ativar os benefícios de assinante do MSDN]: /en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [inscrever-se para uma avaliação gratuita]: /en-us/pricing/free-trial/?WT.mc_id=A261C142F
  [Configurar o ambiente de desenvolvimento]: #setupdevenv
  [Configurar o ambiente do Azure]: #bkmk_setupwindowsazure
  [Criar um aplicativo ASP.NET MVC 5]: #bkmk_createmvc4app
  [Implantar o aplicativo no Azure]: #bkmk_deploytowindowsazure1
  [Adicionar um banco de dados ao aplicativo]: #bkmk_addadatabase
  [Adicionar um provedor de OAuth]: #addOauth
  [Usando a API de Associação]: #mbrDB
  [1]: #bkmk_deploytowindowsazure11
  [Próximas etapas]: #nextsteps
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [Visual Studio 2013 Update 2 RC]: http://go.microsoft.com/fwlink/?LinkId=390521
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com

<!-- images-->
[Novo botão no Portal de Gerenciamento]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
  [Criar com link de banco de dados no Portal de Gerenciamento]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png
  [2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png
  [Etapa Configurações do banco de dados do Novo site - Criar com o assistente de banco de dados]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png
  [3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png
  [Novo projeto no menu Arquivo]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png
  [Caixa de diálogo Novo Projeto]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png
  [Caixa de diálogo Novo Projeto ASP .NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG
  [\_Layout.cshtml no Gerenciador de Soluções]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png
  [alterações de código]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png
  [Site em execução localmente]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png
  [Publicar no menu de contexto do projeto]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png
  [Importar configurações de publicação]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss2.PNG
  [entrar]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss22.PNG
  [selecionar site]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss3.png
  [Publicar]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png
  [Executando na nuvem]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG
  [Adicionar Classe no menu de contexto da pasta Modelos]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png
  [Caixa de diálogo Adicionar Novo Item]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png
  [Adicionar Controlador no menu de contexto da pasta Controladores]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png
  [Adicionar Scaffold dlg]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png
  [Novos dados ctx dlg]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG
  [Codificar Primeiras Migrações]: http://msdn.microsoft.com/library/hh770484.aspx
  [Package Manager Console in Tools menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png
  [Propagando e depurando BDs do Entity Framework (EF)]: http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx
  [Comandos do Console do Gerenciador de Pacotes]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png
  [Exibição do MVC de dados]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png
  [4]: http://oauth.net/ "http://oauth.net"
  [Criar um aplicativo do ASP.NET MVC 5 com logon OAuth2 e OpenID do Facebook e do Google]: http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on
  [Logon no Google]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss23.PNG
  [GOOG asking for permission]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss7.PNG
  [registrar]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss8.PNG
  [imagem de código]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG
  [\*user1@contoso.com\*]: mailto:*user1@contoso.com
  [página de recursos de Identidade do ASP.NET]: http://curah.microsoft.com/55636/aspnet-identity
  [WSAT]: http://msdn.microsoft.com/en-us/library/ms228053.aspx
  [código]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG
  [Authorize]: http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute.aspx
  [RequireHttps]: http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx
  [Protegendo seu aplicativo ASP.NET MVC e o novo atributo AllowAnonymous]: http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx
  [5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss10.PNG
  [6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG
  [imagem do código]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png
  [habilitar SSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png
  [7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png
  [navegador padrão]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG
  [seletor de navegador]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png
  [instruções para confiar no certificado autoassinado que o IIS Express gerou]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG
  [Aviso de certificado do IIS Express para localhost]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG
  [IE com SSL do localhost e sem avisos]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG
  [Aviso do FireFox Cert]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG
  [logon]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG
  [8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png
  [configurações]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png
  [9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png
  [parar site]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png
  [10]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png
  [iniciar site]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png
  [Registrar]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG
  [Fazer logoff]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG
  [Página CM]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png
  [abrir no SSOX]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png
  [11]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png
  [12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png
  [13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png
  [erro do firewall]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
  [Selecionar SQL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
  [regras de firewall]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
  [Guia de servidores no Azure]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG
  [intervalo IP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png
  [cadeia de conexão]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG
  [Conectar-se à DLG do servidor]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png
  [14]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png

