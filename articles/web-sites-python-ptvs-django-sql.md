<properties linkid="web-sites-python-ptvs-django-sql" title="Django and SQL Database on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Django e o banco de dados SQL no Azure com Python Tools 2.1 para Visual Studio" description="Aprenda a usar o Python Tools para Visual Studio para criar um aplicativo Django que armazena dados em uma instância do banco de dados SQL e pode ser implantado em um website." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Django e banco de dados SQL no Azure com Python Tools 2.1 para Visual Studio 

Neste tutorial, criaremos um aplicativo de pesquisas simples usando um dos modelos de amostra PTVS. Este tutorial também está disponível como um [vídeo](https://www.youtube.com/watch?v=ZwcoGcIeHF4).

Aprenderemos como usar um banco de dados SQL hospedado no Azure, como configurar o aplicativo para usar um banco de dados SQL e como publicar o aplicativo em um website do Azure.

Consulte o [Python Developer Center][] para obter mais artigos que abrangem o desenvolvimento de websites do Azure com PTVS usando estruturas da web Bottle, Flask e Django, com serviços MongoDB, Azure Table Storage, MySQL e banco de dados SQL.  Embora este artigo foque nos websites do Azure, as etapas são semelhantes ao desenvolvimento de [Serviços de nuvem do Azure][].

+ [Pré-requisitos](#prerequisites)
+ [Criar o projeto](#create-the-project)
+ [Criar um banco de dados SQL](#create-a-sql-database)
+ [Configurar o projeto](#configure-the-project)
+ [Publicar para um website do Azure](#publish-to-an-azure-website)
+ [Próximas etapas](#next-steps)

##<a name="prerequisites"></a>Pré-requisitos

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 para Visual Studio][]
 - [Python Tools 2.1 para Visual Studio Samples VSIX][]
 - [Ferramentas do SDK do Azure para VS 2013][] ou [Ferramentas do SDK do Azure para VS 2012][]
 - [Python 2.7 32 bits][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Criar o projeto

Nesta seção, criaremos um projeto Visual Studio usando um modelo de amostra. Criaremos um ambiente virtual e instalaremos pacotes necessários.  Criaremos um banco de dados local usando sqlite.  Em seguida, executaremos o aplicativo localmente.

1.  No Visual Studio, selecione **Arquivo**, **Novo Projeto**.

1.  Os modelos de projeto do PTVS Samples VSIX estão disponíveis em **Python**, **Amostras**.  Selecione **Polls Django Web Project** e clique em OK para criar o projeto.

  	![New Project Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Você será solicitado a instalar pacotes externos.  Selecione **Instalar em um ambiente virtual**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Selecione **Python 2.7** como o intérprete base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Clique com o botão direito no nó do projeto e selecione **Python**, **Django Sync DB**.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  Isso abrirá um Console de Gerenciamento Django.  Siga os prompts para criar um usuário.

    Isso criará um banco de dados sqlite na pasta do projeto.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Confirme se o aplicativo funciona pressionando <kbd>F5</kbd>.

1.  Clique em **Fazer logon** na barra de navegação na parte superior.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Insira as credenciais para o usuário que criou quando sincronizou o banco de dados.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Clique em **Criar pesquisas de amostra**.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Clique em uma pesquisa e vote.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-sql-database"></a>Criar um banco de dados SQL

Para o banco de dados, criaremos um banco de dados SQL do Azure.

Você pode criar um banco de dados seguindo estas etapas.

1.  Faça logon no [Portal de Gerenciamento do Azure][].

1.  Na parte inferior do painel de navegação, clique em **NOVO**.

  	![New Button](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png)

1.  Clique em **SERVIÇOS DE DADOS**, em seguida, **BANCO DE DADOS SQL** e clique em **CRIAÇÃO RÁPIDA**.

  	![Quick Create SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png)

1.  Como criar um novo Servidor de Banco de Dados SQL.

1.  Escolha uma Região/Grupo de Afinidade no qual localizar o banco de dados. Se você estiver usando o banco de dados de seu aplicativo do Azure, selecione a mesma região na qual você implantará seu aplicativo.

##<a name="configure-the-project"></a>Configurar o projeto

Nesta seção, configuraremos nosso aplicativo para usar o banco de dados SQL que acabamos de criar.  Veremos como obter configurações de conexão no portal do Azure.  Também instalaremos pacotes adicionais Python necessários para usar bancos de dados SQL com Django.  Em seguida, executaremos o aplicativo localmente.

1.  No [Portal de Gerenciamento do Azure][], clique em **BANCOS DE DADOS SQL**, em seguida, clique no banco de dados que criou anteriormente.

1.  Clique em **GERENCIAR**.

  	![Manage Button](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlManage.png)

1.  Você será solicitado a atualizar as regras de firewall. Clique em **SIM**.Isso permitirá conexões ao servidor de banco de dados em seu computador de desenvolvimento.

  	![Allow Connections](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlUpdateFirewall.png)

1.  Clique em **BANCOS DE DADOS SQL**, em seguida, **SERVIDORES**.  Clique no servidor para seu banco de dados, em seguida, em **CONFIGURAR**.

1.  Nesta página, você verá o endereço IP de cada computador que tem permissão para se conectar ao servidor de banco de dados.  Você deve ver o endereço IP de seu computador.

    Abaixo, em **serviços permitidos**, assegure-se de que os serviços do Azure tenham acesso permitido ao servidor.  Quando o aplicativo estiver executando em um website do Azure (que faremos na próxima seção deste tutorial), será permitido se conectar ao banco de dados.Clique em **SALVAR** para aplicar a mudança.

  	![Allowed Services](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlAllowedServices.png)

1.  No Visual Studio, abra **settings.py**, na pasta *ProjectName*.Edite a definição de `DATABASES`.

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<User>@<ServerName>',
                'PASSWORD': '<Password>',
                'HOST': '<ServerName>.database.windows.net',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

    `<DatabaseName>`, `<User>` e `<Password>` são os valores especificados quando você criou o banco de dados e o servidor.

    Os valores para `<ServerName>` e `<ServerPort>` são gerados pelo Azure quando o servidor for criado e podem ser localizados na seção **Conectar-se ao seu banco de dados**.

1.  No Gerenciador de Soluções, em **Ambientes Python**, clique com o botão direito no ambiente virtual e selecione **Instalar pacote Python**.

1.  Instale o pacote `pyodbc` usando **easy_install**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Instale o pacote `django-pyodbc-azure` usando **pip**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Clique com o botão direito no nó do projeto e selecione **Python**, **Django Sync DB**.  

    Isso criará as tabelas de banco de dados SQL que criamos na seção anterior.  Siga os prompts para criar um usuário, que não tem de corresponder ao usuário no banco de dados sqlite criado na primeira seção.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Execute o aplicativo com <kbd>F5</kbd>.  Pesquisas que são criadas com **Criar Pesquisas de Amostra** e os dados enviados por voto serão serializados no banco de dados SQL.


##<a name="publish-to-an-azure-website"></a>Publicar para um website do Azure

O PTVS fornece uma forma fácil de implantar seu aplicativo Web para um website do Azure.

1.  No **Gerenciador de Soluções**, clique com botão direito no nó do projeto e selecione **Publicar**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  Clique no **Microsoft Azure Websites**.

1.  Clique em **Novo** para criar um novo site.

1.  Selecione um **Nome do site** e uma **Região** e clique em **Criar**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png)

1.  Aceite todos os outros padrões e clique em **Publicar**.

1.  Seu navegador da Web será aberto automaticamente para o sie publicado.  Você deve ver o aplicativo funcionando conforme o esperado, usando o banco de dados **SQL** hospedado no Azure.

    Parabéns!

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Próximas etapas

Siga estas etapas para aprender mais sobre o Python Tools para Visual Studio, Django e banco de dados SQL.

- [Documentação do Python Tools para Visual Studio][]
  - [Projetos da Web][]
  - [Projetos de serviço de nuvem][]
  - [Depuração remota no Microsoft Azure][]
- [Documentação do Django][]
- [Banco de dados SQL][]


<!--Link references-->
[Python Developer Center]: /pt-br/develop/python/
[Serviços de Nuvem do Azure]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
[Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 para Visual Studio Samples VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Ferramentas do SDK do Azure para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Ferramentas do SDK do Azure para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Documentação do Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation
[Depuração remota no Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projetos da Web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projetos de serviço de nuvem]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Documentação do Django]: https://www.djangoproject.com/
[Banco de dados SQL]: /pt-br/documentation/services/sql-database/

<!--HONumber=35.1-->
