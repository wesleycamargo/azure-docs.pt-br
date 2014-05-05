<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title=" OAuth" authors="riande" solutions="" manager="wpickett" editor="mollybos" />




# Implantar um aplicativo ASP.NET MVC seguro com Associação, OAuth e banco de dados SQL em um Site do Azure

***Por [Rick Anderson](https://twitter.com/RickAndMSFT) e Tom Dykstra. Atualizado em 15 de outubro de 2013.***


<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/develop/net/tutorials/web-site-with-sql-database/" title="Visual Studio 2013">Visual Studio 2013</a><a href="/pt-br/develop/net/tutorials/web-site-with-sql-database-vs2012/" title="Visual Studio 2012" class="current">Visual Studio 2012</a></div>

<div class="dev-callout"><strong>Observação</strong><p>Uma <a href="/pt-br/develop/net/tutorials/web-site-with-sql-database/">versão mais recente deste tutorial</a> está disponível. Você ainda pode seguir esta versão se você deseja usar o Visual Studio 2012, mas a versão mais recente é significativamente mais fácil de seguir.</p></div>

Este tutorial mostra como criar um aplicativo da web ASP.NET MVC 4 seguro que permite que os usuários façam logon com credenciais do Google, Facebook e Yahoo. Você também implantará o aplicativo no Azure.

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2012, o SDK instalará automaticamente o Visual Studio 2012 para o Web Express. É possível iniciar o desenvolvimento para o Azure de maneira gratuita.

Este tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure. Ao concluir este tutorial, você terá um aplicativo da Web controlado por dados seguro e em funcionamento na nuvem usando um banco de dados em nuvem.

Você aprenderá:

* Como habilitar a máquina para desenvolvimento do Azure instalando o SDK do Azure.
* Como criar um projeto ASP.NET MVC 4 seguro e publicá-lo em um Site do Azure.
* Como usar OAuth e o banco de dados de associação do ASP.NET para proteger seu aplicativo.
* Como implantar um banco de dados de associação no Azure.
* Como usar um banco de dados SQL para armazenar dados no Azure.
* Como usar o Visual Studio para atualizar e gerenciar o banco de dados de associação.

Você irá criar um aplicativo Web de lista de tarefas simples que é criado no ASP.NET MVC 4 e usa o ADO.NET Entity Framework para acesso ao banco de dados. A seguinte ilustração mostra a página de logon do aplicativo concluído:

![página de logon](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Neste tutorial:

- [Definir o ambiente de desenvolvimento][setupdbenv]
- [Configurar o ambiente do Azure][setupwindowsazureenv]
- [Criar um aplicativo ASP.NET MVC 4][createapplication]
- [Implantar o aplicativo no Azure][deployapp1]
- [Adicionar um banco de dados ao aplicativo][adddb]
- [Adicionar um provedor de OAuth][]
- [Adicionar funções ao banco de dados de associação][]
- [Criar um Script de implantação de dados][]
- [Implantar o aplicativo no Azure][deployapp11]
- [Atualizar o banco de dados de associação][]
- [Próximas etapas][]

<h2><a name="bkmk_setupdevenv"></a>Definir o ambiente de desenvolvimento</h2>

Para começar, defina o ambiente de desenvolvimento instalando o SDK do Azure para o .NET Framework. 

1. Para instalar o SDK do Azure para .NET, clique no link abaixo. Se você ainda não tiver o Visual Studio 2012 instalado, ele será instalado pelo link. Este tutorial requer o Visual Studio 2012. 
[SDK do Azure do Visual Studio 2012]( http://go.microsoft.com/fwlink/?LinkId=254364)
1. Quando for solicitado a executar ou salvar a instalação executável, clique em **Executar**.
1. Na janela do Instalador de Plataforma Web, clique em **Instalar** e prossiga com a instalação.

![Instalador de Plataforma Web - SDK do Azure para .NET][WebPIAzureSdk20NetVS12]


Quando a instalação for concluída, você terá todo o necessário para iniciar o desenvolvimento.



<h2><a name="bkmk_setupwindowsazure"></a>Configurar o ambiente do Azure</h2>

Em seguida, configure o ambiente do Azure criando um Site do Azure e um banco de dados SQL.

### Criar um site e um banco de dados SQL no Azure

O Site do Azure será executado em um ambiente de hospedagem compartilhado, o que significa que ele é executado em MVs (máquinas virtuais) que são compartilhadas com outros clientes do Azure. Um ambiente de hospedagem compartilhado é uma maneira de começar na nuvem a um baixo custo. Mais tarde, se seu tráfego da Web aumentar, o aplicativo poderá ser dimensionado para atender a necessidade executando em VMs dedicadas. Se precisar de uma arquitetura mais complexa, você poderá migrar para um Serviço de Nuvem do Azure. Os serviços de nuvem são executados em VMs dedicadas que podem ser configuradas de acordo com suas necessidades.

O Banco de dados SQL no Azure é um serviço de banco de dados relacional baseado em que é fundamentado em tecnologias do SQL Server. As ferramentas e os aplicativos que funcionam com o SQL Server também funcionam com o Banco de dados SQL.

1. No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), clique em **Sites da Web** na guia à esquerda e clique em  **Novo**.

![Novo botão no Portal de Gerenciamento][rxWSnew]

2. Clique em **CRIAÇÃO PERSONALIZADA**.

	![Criar com link de banco de dados no Portal de Gerenciamento][rxCreateWSwithDB]

O assistente de **Novo Site - Criação Personalizada** é aberto. 

3. Na etapa **Novo Site** do assistente, insira uma cadeia de caracteres na caixa **URL** a ser usada como a URL exclusiva de seu aplicativo. A URL completa consistirá no que você digitar aqui mais o sufixo que você vê ao lado da caixa de texto. A ilustração mostra "contactmgr22", mas essa URL provavelmente já é utilizada e, portanto, você precisará escolher outra.

	![Criar com link de banco de dados no Portal de Gerenciamento][rxCreateWSwithDB_2]

4. Na lista suspensa **Banco de Dados**, escolha **Criar um novo banco de dados SQL**.

5. Na lista suspensa **Região**, escolha a mesma região que você selecionou para o site. Essa configuração especifica qual centro de dados sua máquina virtual será executada em. No **NOME DA CADEIA DE CONEXÃO DO BANCO DE DADOS**, digite *connectionString1*.

	![Etapa Criar um novo site de Novo Site - Criar com o assistente de banco de dados][rxCreateWSwithDB_2]

6. Clique na seta que aponta para a direita na parte inferior da caixa. O assistente avança para a etapa **Configurações do Banco de Dados**.

7. Na caixa **Nome**, digite *ContactDB*.

8. Na caixa **Servidor**, selecione **Novo Servidor do Banco de Dados SQL**. Como alternativa, se tiver criado um banco de dados SQL Server, você poderá selecionar esse SQL Server no controle suspenso.

9. Insira o **NOME DE LOGON** e **SENHA** do administrador. Se você selecionou **Novo servidor do Banco de Dados SQL**, você não irá digitar um nome e senha existentes aqui, você irá inserir um novo nome e uma senha que você está definindo agora para usar mais tarde ao acessar o banco de dados. Se você tiver selecionado um SQL Server criado anteriormente, será solicitada a senha para o nome da conta do SQL Server anterior que você criou. Para este tutorial, não marcamos a caixa **Avançado**. A caixa **Avançado** permite que você defina o tamanho do banco de dados (o padrão é 1 GB, mas você pode aumentar esse tamanho para 150 GB) e o agrupamento.

10. Clique na marca de seleção na parte inferior da caixa para indicar que você concluiu.
	
	![Etapa Configurações do banco de dados do novo site - Criar com o assistente de banco de dados][setup007]
	
	A imagem a seguir mostra o uso de um SQL Server e logon existentes.
	![Etapa de configurações do banco de dados do novo Site - Criar com o assistente de banco de dados][rxPrevDB]

	O Portal de Gerenciamento retorna para a página Sites da Web e a coluna **Status** mostra que o site está sendo criado. Depois de um tempo (normalmente menos de um minuto), a coluna **Status** mostra que o site foi criado com êxito. Na barra de navegação à esquerda, o número de sites presentes na conta é exibido próximo ao ícone **Sites da Web**, e o número de bancos de dados aparece ao lado do ícone **Bancos de Dados SQL**.


<h2><a name="bkmk_createmvc4app"></a>Criar um aplicativo ASP.NET MVC 4</h2>

Você criou um Site do Azure, mas ainda não há conteúdo nele. A próxima etapa é criar o projeto de aplicativo Web do Visual Studio que será publicado no Azure.

### Criar o projeto

1. Inicie o Visual Studio 2012.
1. No menu **Arquivo**, clique em **Novo Projeto**.
3. Na caixa de diálogo **Novo Projeto**, expanda **Visual C#** e selecione **Web** e em **Modelos Instalados** e, em seguida, selecione **Aplicativo da web ASP.NET MVC 4**. Mantenha o padrão **.NET Framework 4.5**. Nomeie o aplicativo **ContactManager** e clique em **OK**.

	![Caixa de diálogo Novo Projeto][newapp002]

6. Na caixa de diálogo **Novo Projeto ASP.NET MVC 4**, selecione o modelo **Aplicativo Internet**. Mantenha o **Mecanismo de Exibição** do Razor padrão e clique em **OK**.

	![Caixa de diálogo Novo Projeto ASP.NET MVC 4][rxb2]

### Definir o cabeçalho e o rodapé da página


1. No **Gerenciador de Soluções**, expanda a pasta Visualizações\Compartilhados e abra o arquivo *_Layout.cshtml*.

	![_Layout.cshtml no Gerenciador de Soluções][newapp004]

1. Substitua cada ocorrência do "Meu Aplicativo ASP.NET MVC" por "Gerenciador de Contatos".
1. Substitua o "seu logo aqui" por "Demonstração de CM".



### Executar o aplicativo localmente

1. Pressione CTRL+F5 para executar o aplicativo. A home page do aplicativo é exibida no navegador padrão.
![Home page da lista de tarefas pendentes][rxa]

Isso é tudo o que você precisa fazer por enquanto para criar o aplicativo que você implantará no Azure. Posteriormente, você adicionará a funcionalidade do banco de dados.

<h2><a name="bkmk_deploytowindowsazure1"></a>Implantar o aplicativo no Azure</h2>

1. No navegador, abra o [Portal de Gerenciamento do Azure](http://manage.windowsazure.com "portal").

2. Na guia **Sites**, clique no nome do seu site que você criou anteriormente.

	![Entre em contato com o aplicativo gerenciador na guia Sites do Portal de Gerenciamento][setup009]

3. No lado direito da janela, clique em **Baixar perfil de publicação**

	![Guia de início rápido e o botão Baixar perfil de publicação][firsdeploy001]

	Essa etapa faz o download de um arquivo que contém todas as configurações que você precisa para implantar um aplicativo em seu Site da Web. Você importará esse arquivo para o Visual Studio de forma que não precise inserir essas informações manualmente.

4. Salve o arquivo .*publishsettings* em uma pasta que você possa acessar pelo Visual Studio.

	![salvando o arquivo .publishsettings][firsdeploy002]


	[WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)]

5. No Visual Studio, clique com o botão direito do mouse no **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publicar no menu de contexto do projeto][PublishVSSolution]
	
	O assistente de **Publicar Web** é aberto.

6. Na guia **Perfil** do assistente de **Web de publicação**, clique em **Importar**.

	![Importar configurações de publicação][ImportPublishSettings]

	A caixa de diálogo **Importar Perfil de Publicação** é aberta.

1. Se você ainda não adicionou a assinatura do Azure no Visual Studio, execute as etapas a seguir. Nestas etapas você adiciona sua assinatura de forma que a lista suspensa em **Importar de um Site do Azure** incluirá o seu site.
    
	a. Na caixa de diálogo **Perfil de Publicação de Importação**, clique em **Adicionar Assinatura do Azure**. 

	![adicionar assinatura do az](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzAddWAsub.png)
	
	b. Na caixa de diálogo **Importar Assinaturas do Azure**, clique em **Baixar arquivo de assinatura**.
    
	![baixar sub](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png)
	
	c. Na janela do navegador, salve o arquivo *.publishsettings*.
    
	![baixar o arquivo pub](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDown2.png)
    
	> [WACOM.NOTE]
	> O arquivo .publishsettings contém suas credenciais (sem codificação) que são usadas para administrar suas assinaturas e serviços do Azure. A melhor prática de segurança para este arquivo é armazená-lo temporariamente fora dos diretórios de origem (por exemplo, na pasta Libraries\Documents) e, em seguida, excluí-la após a conclusão da importação. Um usuário mal-intencionado que obtenha acesso ao arquivo .publishsettings pode editar, criar e excluir os serviços do Azure.
	
    
	d. Na caixa de diálogo **Importar Assinaturas do Azure**, clique na guia **Procurar** e navegue para o arquivo *.publishsettings*.
    
	![baixar sub](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png)
	
	e. Clique em **Importar**.
    
	![import](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzImp.png)

7. Na caixa de diálogo **Importar Perfil de Publicação**, selecione **Importar de um site do Azure**, selecione seu site na lista suspensa e clique em **OK**.

	![Importar Perfil de Publicação][ImportPublishProfile]

	O aplicativo que você criou agora está em execução na nuvem. Na próxima vez que você implantar o aplicativo, somente os arquivos alterados (ou novos) serão implantados.
	
	![A home page da lista de tarefas pendentes em execução no Azure][newapp005]


<h2><a name="bkmk_addadatabase"></a>Adicionar um banco de dados ao aplicativo</h2>

Em seguida, você atualizará o aplicativo MVC para adicionar a habilidade de exibir e atualizar contatos e armazenar os dados em um banco de dados. O aplicativo usará o Entity Framework para criar o banco de dados e ler e atualizar dados no banco de dados.

### Adicionar classes de modelo de dados para os contatos

Você começa criando um modelo de dados simples no código.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Modelos, clique em **Adicionar** e, em seguida, em **Classe**.

![Adicionar Classe no menu de contexto da pasta Modelos][adddb001]

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

A classe **Contatos** define os dados que você armazenará para cada contato, além de uma chave primária, *ContactID*, que é necessária para o banco de dados.

### Criar páginas da Web que permitem que os usuários do aplicativo trabalhem com os contatos

O recurso scaffolding do ASP.NET MVC pode gerar automaticamente o código que executa as ações CRUD (criar, ler, atualizar e excluir).

<h2><a name="bkmk_addcontroller"></a>Adicionar um Controlador e um modo de exibição para os dados</h2>

1. Compile o projeto **(Ctrl+Shift+B)**. (Você deve compilar o projeto antes de usar o mecanismo de scaffolding.) 
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Controladores e clique em **Adicionar** e, em seguida, em **Controlador**.

	![Adicionar Controlador no menu de contexto da pasta Controladores][addcode001]

5. Na caixa de diálogo **Adicionar Controlador**, digite "HomeController" como o nome do seu controlador. 
1. Defina o modelo de **opções de Scaffolding** como **Controlador MVC com ações de leitura/gravação e modos de exibição, usando o Entity Framework**.
6. Selecione **Contato** como sua classe de modelo e **&lt;Novo contexto de dados...&gt;** como sua classe de contexto de dados.

	![Caixa de diálogo Adicionar Controlador][addcode002]

7. Na caixa de diálogo **Novo Contexto de Dados**, aceite o valor padrão *ContactManager.Models.ContactManagerContext*.
	![Caixa de diálogo Adicionar Controlador][rxNewCtx]

8. Clique em **OK** e, em seguida, clique em **Adicionar** na caixa de diálogo **Adicionar Controlador**.
9. Na caixa de diálogo de substituição **Adicionar Controlador**, verifique se todas as opções estão marcadas e clique em **OK**.

	![Caixa de mensagem Adicionar Controlador][rxOverwrite] 

O Visual Studio cria métodos do controlador e modos de exibição para as operações CRUD do banco de dados para objetos **Contact**.

## Habilitar migrações, criar o banco de dados, adicionar dados de exemplo e um inicializador de dados ##

A próxima tarefa é habilitar o recurso [Codificar primeiras migrações (a página pode estar em inglês)](http://msdn.microsoft.com/library/hh770484.aspx) para criar o banco de dados com base no modelo de dados criado.

1. No menu **Ferramentas**, selecione **Gerenciador de Pacotes da Biblioteca** e **Console do Gerenciador de Pacotes**.

	![Console do Gerenciador de Pacotes no menu Ferramentas][addcode008]

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		enable-migrations -ContextTypeName ContactManagerContext

	![enable-migrations][rxE] 
	Você deve especificar o nome do tipo de contexto (**ContactManagerContext**) porque o projeto contém duas classes derivadas [DbContext](http://msdn.microsoft.com/pt-br/library/system.data.entity.dbcontext(v=VS.103).aspx), a **ContactManagerContext** que acabamos de adicionar e a **UsersContext**, que é usada para o banco de dados de associação. A classe **ContactManagerContext** foi adicionada pelo Assistente de scaffolding do Visual Studio.

	O comando **enable-migrations** cria a pasta *Migrations* e coloca nessa pasta um arquivo *Configuration.cs* que pode ser editado para configurar Migrações. 

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		add-migration Initial


	O comando **add-migration Initial** gera um arquivo denominado **&lt;date_stamp&gt;Initial** na pasta *Migrações* que cria o banco de dados. O primeiro parâmetro ( **Initial** ) é arbitrário e é usado para criar o nome do arquivo. Você pode ver os novos arquivos de classe no **Gerenciador de Soluções**.

	Na classe **Initial**, o método **Up** cria a tabela Contatos, e o método **Down** (usado quando você deseja retornar ao estado anterior) a descarta.

3. Abrir o arquivo *Migrations\Configuration.cs*. 
4. Adicione os seguintes namespaces. 

    	 using ContactManager.Models;



5. Substitua o método *Seed* pelo seguinte código:

        protected override void Seed(ContactManager.Models.ContactManagerContext context)
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

	O código acima inicializa o banco de dados com as informações de contato. Para obter mais informações sobre a propagação do banco de dados, consulte [Propagando e depurando BDs do Entity Framework (EF) (a página pode estar em inglês)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


6. No **Console do Gerenciador de Pacotes**, digite o comando:

		update-database

	![Comandos do Console do Gerenciador de Pacotes][addcode009]

	O **update-database** executa a primeira migração que cria o banco de dados. Por padrão, o banco de dados é criado como um banco de dados LocalDB do SQL Server Express. (A menos que você tenha o SQL Server Express instalado, em cujo caso, o banco de dados é criado usando a instância do SQL Server Express.)

7. Pressione CTRL+F5 para executar o aplicativo. 

O aplicativo mostra os dados de propagação e fornece links de edição, detalhes e exclusão.

![Exibição do MVC de dados][rx2]

<h2><a name="addOauth"></a><span class="short-header">OAuth</span>Adicionar um provedor de OAuth</h2>

[OAuth](http://oauth.net/ "http://oauth.net/") é um protocolo aberto que permite autorização segura em um método simples e padrão da Web, aplicativos móveis e de área de trabalho. O modelo de internet do ASP.NET MVC usa OAuth para expor o Facebook, Twitter, Google, Yahoo e a Microsoft como provedores de autenticação. Embora este tutorial use apenas o Facebook, Google e Yahoo como o provedores de autenticação, você pode facilmente modificar o código para usar qualquer um dos provedores. As etapas para implementar outros provedores são muito semelhantes às etapas que você verá neste tutorial. 

Além da autenticação, o tutorial também usará funções para implementar a autorização. Somente os usuários que você adicionar à função canEdit serão capazes de criar, editar e excluir contatos.

## Registrar-se com um provedor externo ##

Para autenticar os usuários com credenciais de alguns provedores externos, você deve registrar seu site com o provedor e obter um segredo chave e de conexão. Google e Yahoo não exigem que você se registre e obtenha chaves.

Este tutorial não mostra todas as etapas que você deve executar para registrar esses provedores. As etapas normalmente não são difíceis. Para registrar o seu site com êxito, siga as instruções fornecidas nesses sites. Para começar a registrar o site, consulte o site do desenvolvedor para:

- [Facebook](http://developers.facebook.com/)
- [Microsoft](http://go.microsoft.com/fwlink/?LinkID=144070)
- [Twitter](http://dev.twitter.com/)

Navegue até a página [https://developers.facebook.com/apps](https://developers.facebook.com/apps/) e efetue logon, se necessário. Clique no botão **Registrar-se como um desenvolvedor** e conclua o processo de registro. Depois que você concluir o registro, clique em **Criar novo aplicativo**. Insira um nome para o aplicativo. Você não precisa inserir um espaço para nome do aplicativo.

![Criar novo aplicativo do FB](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBapp.png)


Digite localhost para o **Domínio de aplicativo** e http://localhost/ para a **URL do Site**. Clique em **Habilitado** para o **Modo Sandbox**, em seguida clique em **Salvar alterações**.


Você precisará da **ID do App** e o **Segredo do aplicativo** para implementar OAuth neste aplicativo.
	![Novo aplicativo do FB][rxFB]

## Criando usuários de teste ##
No painel esquerdo, em **Configurações**, clique em **Funções de desenvolvedor**. Clique no link **Criar** na linha **Usuários de teste** (não na linha de **Testadores**).

![Testadores do FB](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBt.png)

Clique no link **Modificar** para obter o e-mail dos usuários de teste (que você usará para fazer logon no aplicativo). Clique no link **Ver mais**, depois clique em **Editar** para definir a senha de usuários de teste.

## Adicionar a id do aplicativo e o segredo do provedor ##

Abrir o arquivo *App_Start\AuthConfig.cs*. Remova os caracteres do comentário do método *RegisterFacebookClient* e adicione a id do aplicativo e o segredo do aplicativo. Use os valores que você adquiriu, os valores mostrados abaixo não funcionarão. Remova os caracteres do comentário da chamada *OAuthWebSecurity.RegisterGoogleClient* e adicione *OAuthWebSecurity.RegisterYahooClient* como mostrado abaixo. Os provedores Google e Yahoo não exigem que você se registre e obtenha chaves. 
Aviso: Proteja sua identificação de aplicativo e o segredo. Um usuário mal-intencionado que tenha a sua ID de aplicativo e o segredo pode fingir ser seu aplicativo.

     public static void RegisterAuth()
        {
            OAuthWebSecurity.RegisterFacebookClient(
                appId: "digite uma chave numérica aqui",
                appSecret: "digite um segredo numérico aqui");

            OAuthWebSecurity.RegisterGoogleClient();
            OAuthWebSecurity.RegisterYahooClient();
        }


1. Execute o aplicativo e clique no link **Fazer logon**. 
1. Clique no botão **Facebook**. 
1. Insira suas credenciais do Facebook ou uma das credenciais do usuário de teste.
1. Clique em **OK** para permitir que o aplicativo acesse os recursos do Facebook.
1. Você é redirecionado para a página Registrar. Se você estiver conectado usando uma conta de teste, você pode alterar o **nome de usuário** para algo mais curto, por exemplo "teste do FB Bil". Clique no botão **Registrar** que salvará o alias do nome e e-mail do usuário no banco de dados de associação. 
1. Registrar outro usuário. Atualmente um erro no log do sistema impede você de fazer logoff e logon como outro usuário usando o mesmo provedor (ou seja, você não pode fazer logoff da sua conta do Facebook e fazer logon novamente com uma conta diferente do Facebook). Para contornar isso, navegue até o site usando um navegador diferente e registre outro usuário. Um usuário será adicionado à função de gerente e ter acesso ao aplicativo de edição, o outro usuário somente terá acesso a métodos sem edição no site. Os usuários anônimos só terão acesso à home page.
1. Registrar outro usuário usando um provedor diferente.
1. **Opcional**: você também pode criar uma conta local não associada a um dos provedores. Mais tarde no tutorial, removeremos a capacidade de criar contas locais. Para criar uma conta local, clique no link **Sair** (se você está conectado), em seguida, clique no link **Registrar**. Você pode querer criar uma conta local para fins de administração que não esteja associada com algum provedor de autenticação externa.

<h2><a name="mbrDB"></a><span class="short-header">DB de associação</span>Adicionar funções ao banco de dados de associação</h2>
Nesta seção você irá adicionar a função *canEdit* ao banco de dados de associação. Somente os usuários na função canEdit poderão editar os dados. A melhor prática é nomear as funções pelas ações que podem executar, portanto, *canEdit* é preferível do que uma função chamada *admin*. Quando seu aplicativo evoluir, você poderá adicionar novas funções, como *canDeleteMembers*, em vez da menos descritiva *superAdmin*.

1. No menu **Exibir**, clique em **Pesquisador do Servidor**. 

1. Na **Server Explorer**, expanda **DefaultConnection** , em seguida, expanda **Tabelas**.

1. Clique com botão direito em **UserProfile** e clique em **Exibir dados da tabela**.
 
	![Mostrar dados da tabela][rxSTD]
 
1. Registre o **UserId** para o usuário que terá a função canEdit. Na imagem abaixo, o usuário *ricka* com **UserId** 2 terá a função canEdit para o site.

	![IDs de usuário][rxUid]
 
1. Clique com botão direito nas **funções_webpages** e clique em **Exibir dados da tabela**.
1. Digite **canEdit** na célula **RoleName**. O **RoleId** será 1 se esta for a primeira vez que você adicionou uma função. Registro do RoleID. Certifique-se de que não haja um caractere de espaço sobrando, "canEdit" na tabela de função não corresponderá a "canEdit" no código do controlador.

	![roleID][rxRoleID] 

1. Clique com botão direito nas **webpages UsersInRoles** e clique em **Exibir dados da tabela**. Digite o **UserId** para o usuário que você deseja conceder o acesso *canEdit* e o **RoleId**.

	![tabela de identificação de função do usr][rxUR] 

A tabela **webpages_OAuthMembership** contém o provedor OAuth, o ID de usuário do provedor e a identificação do usuário para cada usuário OAuth registrado. <!-- Não substitua "-" com "_" ou não validará -->A tabela **webpages-Membership** contém a tabela de associação do ASP.NET. Você pode adicionar usuários a esta tabela usando o link de registro. É uma boa ideia adicionar um usuário com a função *canEdit* que não está associado com o Facebook ou outro provedor de autorização de terceiros para que você possa ter sempre o acesso *canEdit*, mesmo quando o provedor de autorização de terceiros não estiver disponível. Posteriormente no tutorial, desativaremos o registro de associação do ASP.NET.

## Proteger o aplicativo com o atributo Authorize ##

Nesta seção aplicaremos o atributo [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) para restringir o acesso aos métodos de ação. Os usuários anônimos poderão visualizar a home page apenas. Os usuários registrados poderão ver detalhes do contato, a cerca de e as páginas de contatos. Somente usuários na função *canEdit* poderão acessar os métodos de ação que alteram dados.

1. Adicione os filtros [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) e o filtro [RequireHttps](http://msdn.microsoft.com/pt-br/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) ao aplicativo. Uma abordagem alternativa é adicionar os atributos [Authorize](http://msdn.microsoft.com/pt-br/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) e [RequireHttps](http://msdn.microsoft.com/pt-br/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) a cada controlador, mas aplicá-los a todo o aplicativo é considerado uma melhor prática de segurança. Adicionando-os globalmente, cada novo controlador e método de ação que você adicionar será automaticamente protegido, você não precisará se lembrar de aplicá-los. Para obter mais informações, consulte [Protegendo seu aplicativo ASP.NET MVC 4 e o novo atributo AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Abra o arquivo *App_Start\FilterConfig.cs* e substitua o método *RegisterGlobalFilters* com o seguinte.

        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());
        }

1. Adicione o atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) para o método **índice**. O atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) permite criar a lista branca dos métodos para os quais você deseja recusar autorização.
1. Adicione [Authorize(Funções = "canEdit")] para os métodos Get e Post que alteram dados (Criar, editar, excluir). 
1. Adicione os métodos *Sobre* e *Contato*. Uma parte do código concluído é mostrada abaixo.

	    public class HomeController : Controller
	    {
	        private ContactManagerContext db = new ContactManagerContext();
	        [AllowAnonymous]
	        public ActionResult Index()
	        {
	            return View(db.Contacts.ToList());
	        }
	
	        public ActionResult About()
	        {
	            return View();
	        }
	
	        public ActionResult Contact()
	        {
	            return View();
	        }
	
	        [Authorize(Roles = "canEdit")]
	        public ActionResult Create()
	        {
	            return View();
	        }
	        // Métodos movidos e omitidos para fins de esclarecimento.
	    }

1. Remova o registro de associação do ASP.NET. O registro de associação ao ASP.NET atual no projeto não oferece suporte para a redefinição de senha e não verifica se o registro é de uma pessoa (por exemplo, com uma [CAPTCHA](http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership)). Quando um usuário é autenticado usando um dos provedores de terceiros, ele pode se registrar. No AccountController, remova o atributo *[AllowAnonymous]* dos métodos *Registrar* GET e POST. Isso evitará que bots e usuários anônimos se registrem.
1. Na pasta *Visualizações\compartilhado_LoginPartial.cshtml*, remova o link da ação Registrar.
1. Habilitar o SSL. No Gerenciador de Soluções, clique no projeto **ContactManager** e clique em F4 para exibir a caixa de diálogo propriedades. Altere **SSL habilitado** para true. Copie a **URL do SSL**.

	![habilitar SSL][rxSSL]
 
1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **Gerenciador de Contatos** e clique em **Propriedades**.
1. Na guia à esquerda, clique em **Web**.
1. Altere a **Url do projeto** para usar a **URL SSL**.
1. Clique em **Criar o diretório virtual**.
	
	![habilitar SSL][rxS2]
 
1. Pressione CTRL+F5 para executar o aplicativo. O navegador exibirá um aviso de certificado. Em nosso aplicativo, você pode clicar com segurança no link **Continuar neste site**. Verifique se apenas os usuários na função *canEdit* podem alterar os dados. Verifique se os usuários anônimos só podem exibir a home page.

	![av. de cert.][rxNOT]

	![av. de cert.][rxNOT2]
 
Os sites do Azure incluem um certificado de segurança válido, portanto você não verá esse aviso quando você implantar no Azure.
<h2><a name="ppd"></a><span class="short-header">Preparar o DB</span>Criar um Script de implantação de dados</h2>


O banco de dados de associação não é gerenciado pelo Entity Framework Code First, assim você não pode usar as migrações para implantá-lo.  Usaremos o provedor [dbDacFx](http://msdn.microsoft.com/pt-br/library/dd394698.aspx) para implantar o esquema de banco de dados, e configuraremos o perfil de publicação para executar um script que irá inserir dados da associação inicial em tabelas de associação.

Este tutorial usará o SQL Server Management Studio (SSMS) para criar scripts de implantação de dados. 

Instale o SSMS do [Centro de Download do Microsoft SQL Server 2012 Express](http://www.microsoft.com/pt-br/download/details.aspx?id=29062):

- [ENU\x64\SQLManagementStudio_x64_ENU.exe](http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x64/SQLManagementStudio_x64_ENU.exe) para sistemas de 64 bits.
- [ENU\x86\SQLManagementStudio_x86_ENU.exe](http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x86/SQLManagementStudio_x86_ENU.exe) para sistemas de 32 bits.

 Se você escolher o caminho errado para seu sistema, ele não conseguirá instalar e você pode tentar o outro.

(Observe que se trata de um download de 600 megabytes. Pode levar muito tempo para instalar e pode exigir a reinicialização do computador.)

Na primeira página do Centro de instalação do SQL Server, clique em **Instalação autônoma do novo SQL Server ou adicione recursos a uma instalação existente** e siga as instruções, aceitando as opções padrão. A imagem a seguir mostra a etapa que instala o SSMS.

![Instalação do SQL][rxSS] 

### Crie o script do banco de dados de desenvolvimento ###


1. Execute o SSMS.
1. Na caixa de diálogo **Conectar ao servidor**, digite *(localdb)\v11.0* como o nome do servidor, deixe a **autenticação** definida como **Autenticação do Windows** e, em seguida, clique em **Conectar**. Se você tiver instalado o SQL Express, digite **.\SQLEXPRESS**.
	
	![con para dlg srvr][rxC2S] 

1. Na janela do **Pesquisador de Objetos**, expanda o **banco de dados**, clique com o botão direito em **aspnet-ContactManager**, clique em **Tarefas** e, em seguida, clique em **Gerar Scripts**.
	
	![Gerar Scripts][rxGenScripts] 

1. Na caixa de diálogo **Gerar e publicar Scripts**, clique em **Definir opções de script**.
Você pode ignorar a etapa **Escolher objetos**, pois o padrão é banco de dados de Script e todos os objetos de banco de dados e isso é o que você deseja.

1. Clique em **Advançado**.

	![Defina as opções de script][rx11] 

1. Na caixa de diálogo **Opções avançadas de criação de scripts**, role para baixo até **Tipos de dados para o script** e clique na opção **Somente dados** na lista suspensa. (Consulte a imagem abaixo da próxima etapa.)

1. Altere o **Script usa o banco de dados** para **Falso**.  As instruções de uso não são válidas para o banco de dados do SQL do Azure e não são necessárias para a implantação do SQL Server Express no ambiente de teste. 
	
	![Defina as opções de script][rxAdv] 

1. Clique em **OK**.
1. Na caixa de diálogo **Gerar e publicar Scripts**, a caixa **Nome do arquivo** especifica qual o script que será criado. Altere o caminho para a pasta de solução (a pasta que contém o arquivo *Contacts.sln*) e altere o nome de arquivo para *aspnet-data-membership.sql*.
1. Clique em **Próximo** para ir para a guia de **Resumo** e, em seguida, clique em **Próximo** novamente para criar o script.

	![Salvar ou pub][rx1] 

1. Clique em **Concluir**.

<h2><a name="bkmk_deploytowindowsazure11"></a>Implantar o aplicativo no Azure</h2>

1. Abrir o arquivo *Web.config* da raiz do aplicativo. Encontre a marcação *DefaultConnection* e, em seguida, copie e cole-a na linha de marcação *DefaultConnection*. Renomeie o elemento copiado *DefaultConnectionDeploy*. Você precisará dessa cadeia de conexão para implantar os dados do usuário no banco de dados de associação.
	![3 cons str][rxD] 

1. Crie o aplicativo.
1. No Visual Studio, clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publicar no menu de contexto do projeto][firsdeploy003]

O assistente de **Publicar Web** é aberto.

1. Clique na guia **Configurações**. Clique no ícone **v** para selecionar a **Cadeia de conexão remota** para o **ContactManagerContext** e **DefaultConnectionDeploy**. Os três bancos de dados listados irão usar a mesma sequência de conexão. O banco de dados **ContactManagerContext** armazena os contatos, o **DefaultConnectionDeploy** é usado somente para implantar os dados da conta do usuário no banco de dados de associação e o banco de dados **UsersContext** é o banco de dados de associação.
	
	![configurações][rxD2] 

1. Em **ContactManagerContext**, verifique **Executar as Migrações Iniciais do Código**.
	
	![configurações][rxSettings] 

1. Em **DefaultConnectionDeploy**, marque a **Atualização do banco de dados**, em seguida clique no link **Configurar atualizações do banco de dados**.
1. Clique no link **Adicionar Script SQL** e navegue até o arquivo *aspnet-data-membership.sql*. Você só precisa fazer isso uma vez. A próxima implantação você pode desmarcar a **Atualização do banco de dados**, pois você não precisa adicionar os dados do usuário nas tabelas de associação.

	![adicionar sql][rxAddSQL2] 

1. Clique em **Publicar**.
1. Navegue até a página [https://developers.facebook.com/apps](https://developers.facebook.com/apps/) e altere as configurações dos **Domínios do aplicativo** e da **URL do Site** para a URL do Azure.
1. Testar o aplicativo. Verifique se apenas os usuários na função *canEdit* podem alterar os dados. Verifique se os usuários anônimos só podem exibir a home page. Verifique se os usuários autenticados podem navegar até todos os links que não alteram os dados.
1. Na próxima vez que você publicar o aplicativo, certifique-se de desmarcar a **Atualização do banco de dados** na **DefaultConnectionDeploy**.
	
	![configurações][rxSettings]

<h2><a name="ppd2"></a><span class="short-header">Atualizar o DB</span>Atualize o banco de dados de associação</h2>

Depois que o site for implantado no Azure e você tiver mais usuários registrados, talvez você queira tornar algumas delas membros da função *canEdit*. Nesta seção, usaremos o Visual Studio para se conectar ao banco de dados SQL e adicionar usuários à função *canEdit*.

![configurações][rxSettings] 

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e clique em **Publicar**.
	![Publicar][rxP]

1. Clique na guia **Configurações**.
2. Copie a cadeia de conexão. Por exemplo, a sequência de conexão usada neste exemplo é:
	Fonte de dados=tcp:d015leqjqx.database.windows.net,1433;
	Inicial catálogo=ContactDB2;User Id=ricka0@d015lxyze;Password=xyzxyz
1. Feche a caixa de diálogo Publicar.
1. No menu **Exibir**, clique em **Pesquisador do Servidor**. 

1. Clique no ícone **Conectar ao banco de dados**.
	
	![Publicar][rxc]

1. Se você for solicitado para a fonte de dados, clique em **Microsoft SQL Server**.
	![Publicar][rx3]

1. Copie e cole o **Nome do servidor**, que começa com *tcp* (consulte a imagem abaixo).
1. Clique em **Usar SQL Server Authentication**
1. Digite seu **Nome de usuário** e **Senha**, que estão na cadeia de conexão que você copiou.
1. Digite o nome do banco de dados (ContactDB, ou a sequência após "Initial Catalog=" no banco de dados se você não o nomeou ContactDB.) Se você receber uma caixa de diálogo de erro, consulte a próxima seção. 
1. Clique em **Testar Conexão**. Se você receber uma caixa de diálogo de erro, consulte a próxima seção. 
	![adicionar dlg con][rx4]

## Não é possível abrir o erro de logon do servidor ##
Se você receber uma caixa de diálogo de erro informando que "Não é possível abrir o servidor", você precisará adicionar o endereço IP para o IPs permitidos.

![erro do firewall][rx5]

1. No Portal do Azure, selecione **Bancos de dados SQL** na guia à esquerda.

	![Selecionar SQL][rx6]

1. Selecione o banco de dados que você deseja abrir.

1. Clique no link **Configurar regras de firewall do Azure para esse endereço IP**.

	![regras de firewall][rx7]

1. Quando você receber o prompt "O endereço IP atual xxx.xxx.xxx.xxx não está incluído nas regras de firewall existentes. Deseja atualizar as regras de firewall?", clique em **Sim**. Normalmente não é suficiente adicionar esse endereço, você precisará adicionar um intervalo de endereços IP.

## Adicionando um intervalo de endereços IP permitidos ##

1. No Portal do Azure, clique em **Bancos de Dados SQL**.
1. Clique no **Servidor** que hospeda seu banco de dados.

	![servidor do bd][rx8]

1. Clique em **Configurar** na parte superior da página.
1. Adicione um nome de regra e endereços IP iniciais e finais.
	![intervalo IP][rx9]

1. Na parte inferior da página, clique em **Salvar**.
1. Agora você pode editar o banco de dados de associação usando as etapas mostradas anteriormente.

<h2><a name="nextsteps"></a><span class="short-header">Próximas etapas</span>Próximas etapas</h2>

Este tutorial e o aplicativo de exemplo foram escritos por [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) com a assistência de Tom Dykstra, Tom FitzMacken e Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)). 

Deixe comentários sobre o que você gostou ou do que você gostaria de ver melhorado, não apenas sobre o próprio tutorial, mas também sobre os produtos que ele demonstra. Seus comentários nos ajudarão a priorizar melhorias. Estamos especialmente interessados em saber quanto há de interesse em mais automação para o processo de configuração e implantação de banco de dados de associação. 

Para obter os botões de registro do Facebook, Google e Yahoo coloridos, consulte a postagem no blog [Personalizar botões de Login externos no ASP.NET MVC 4](http://www.beabigrockstar.com/customizing-external-login-buttons-in-asp-net-mvc-4/). Para obter informações sobre como usar a autenticação do Windows, consulte o seguinte:

- [Autenticação do Azure ](http://www.asp.net/vnext/overview/fall-2012-update/windows-azure-authentication)
- [Como criar um Site da Intranet usando ASP.NET MVC](http://msdn.microsoft.com/pt-br/library/gg703322(v=vs.98).aspx)

Outra maneira de armazenar dados em um aplicativo do Azure é usar o Armazenamento do Azure, que fornece armazenamento de dados não relacionais na forma de blobs e tabelas. Os links a seguir fornecem mais informações sobre a ASP.NET MVC e o Azure. 

- [Aplicativo de várias camadas .NET usando Tabelas de armazenamento, Filas e Blobs](http://www.windowsazure.com/pt-br/develop/net/tutorials/multi-tier-web-site/1-overview/).
- [Introdução ao ASP.NET MVC 4](http://www.asp.net/mvc/tutorials/mvc-4/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4)
- [Introdução ao Entity Framework usando MVC][EFCodeFirstMVCTutorial]
- [OAuth 2.0 e registro único](http://blogs.msdn.com/b/vbertocci/archive/2013/01/02/oauth-2-0-and-sign-in.aspx)


Você viu como implantar um aplicativo Web em um site do Azure. Para saber mais sobre como configurar, gerenciar e dimensionar sites do Azure, consulte os tópicos de instruções na página [Tarefas Comuns][CommonTasks].

Para saber como depurar Sites do Azure, consulte [Solução de problemas dos Sites do Azure no Visual Studio](/pt-br/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

Para saber como implantar um aplicativo em um Serviço de Nuvem do Azure, consulte [A versão do Serviço de Nuvem deste tutorial][NetAppWithSqlAzure] e [Desenvolvendo aplicativos web com o Azure][DevelopingWebAppsWithWindowsAzure]. Entre algumas razões para optar por executar um aplicativo web ASP.NET em um Serviço de Nuvem do Azure, e não um Site do Azure, estão as seguintes:

* Você deseja permissões de administrador no servidor web em que o aplicativo é executado.
* Você deseja usar Conexão de Área de Trabalho Remota para acessar o servidor web em que o aplicativo é executado. 
* O aplicativo é multicamada e você deseja distribuir o trabalho entre vários servidores virtuais (Web e trabalhadores).

Para obter mais informações sobre o Banco de dados SQL e Armazenamento do Azure, consulte [Opções de Armazenamento de Dados no Azure][WindowsAzureDataStorageOfferings].

Para saber mais sobre como usar o banco de dados SQL, consulte [Trabalhando com o banco de dados do SQL Azure do mapa de conteúdo de acesso de dados ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=282414#ssdb).

Para saber mais sobre o Entity Framework e Codificar Migrações Iniciais, consulte os seguintes recursos:

* [Introdução ao Entity Framework usando MVC][EFCodeFirstMVCTutorial]
* [Codificar Migrações Iniciais][EFCFMigrations]




<!-- bookmarks -->
[Adicionar um provedor de OAuth]: #addOauth
[Adicionar funções ao banco de dados de associação]:#mbrDB
[Criar um Script de implantação de dados]:#ppd
[Atualizar o banco de dados de associação]:#ppd2
[setupdbenv]: #bkmk_setupdevenv
[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase




<!-- links -->




[WindowsAzureDataStorageOfferings]: http://social.technet.microsoft.com/wiki/contents/articles/data-storage-offerings-on-the-windows-azure-platform.aspx

[NetAppWithSQLAzure]: http://www.windowsazure.com/pt-br/develop/net/net-app-with-sql-azure





[CommonTasks]: http://windowsazure.com/develop/net/common-tasks/





[EFCodeFirstMVCTutorial]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[EFCFMigrations]: http://msdn.microsoft.com/pt-br/library/hh770484


<!-- links from Tom's hopefully no collisions -->

[WindowsAzureDataStorageOfferings]: http://social.technet.microsoft.com/wiki/contents/articles/data-storage-offerings-on-the-windows-azure-platform.aspx

[NetAppWithSQLAzure]: http://www.windowsazure.com/pt-br/develop/net/tutorials/cloud-service-with-sql-database/





[CommonTasks]: http://www.windowsazure.com/pt-br/develop/net/common-tasks/







[EFCodeFirstMVCTutorial]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[EFCFMigrations]: http://msdn.microsoft.com/pt-br/library/hh770484
[DevelopingWebAppsWithWindowsAzure]: http://msdn.microsoft.com/pt-br/library/Hh674484

<!-- images-->
[rxE]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxE.png
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx2.png
[rxP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxP.png
[rxFB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFB.png
[rxSTD]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSTD.png
[rxUid]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUid.png
[rxRoleID]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxRoleID.png
[rxUR]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUR.png
[rxC2S]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxC2S.png
[rxGenScripts]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxGenScripts.png
[rx11]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx11.png
[rxAdv]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAdv.png
[rx1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx1.png
[rxd]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxd.png
[rxSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png
[rxD2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxD2.png
[rxAddSQL2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAddSQL2.png
[rxc]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxc.png
[rx3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx3.png
[rx4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx4.png
[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx9.png
[rxa]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxa.png
[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb.png
[rxSS]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSS.png
[rxp2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxp2.png
[rxp3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxp3.png
[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSSL.png
[rxS2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxS2.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT2.png
[rxb2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb2.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png
[rxCreateWSwithDB_2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png
[rxNewCtx]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNewCtx.png
[rxCreateWSwithDB_2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png 
[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxPrevDB.png
[rxOverwrite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxOverwrite.png
[rxNewCtx]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNewCtx.png
[rxSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png
[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB.png
[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-004.png
[setup009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-006.png
[newapp002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-002.png
[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-004.png
[newapp005]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/newapp005.png
[firsdeploy001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-download-profile.png
[firsdeploy002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-save-profile.png
[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-publish-001.png
[adddb001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-001.png
[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-context-menu.png
[addcode002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-controller-dialog.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-console.png
[Important information about ASP.NET in Azure Web Sites]: #aspnetwindowsazureinfo
[Next steps]: #nextsteps
[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishProfile.png
[PublishVSSolution]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/PublishVSSolution.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/WebPIAzureSdk20NetVS12.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/WebPIAzureSdk20NetVS12.png

