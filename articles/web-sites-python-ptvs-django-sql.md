<properties linkid="web-sites-python-ptvs-django-sql" title="Django and SQL Database on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Django e banco de dados SQL no Azure com ferramentas Python 2.1 para Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a SQL database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Django e banco de dados SQL no Azure com ferramentas Python 2.1 para Visual Studio 

Neste tutorial, vamos criar um aplicativo de pesquisa simples usando um dos modelos de exemplo PTVS.

Iremos aprender como usar um banco de dados SQL hospedado no Azure, como configurar o aplicativo para usar um banco de dados SQL e como publicar o aplicativo em um site do Azure.

Consulte o [Python Developer Center][] para obter mais artigos que abordam o desenvolvimento de sites do Azure com o PTVS usando estruturas web Bottle, Flask e Django, com serviços MongoDB, armazenamento de tabela do Azure, MySQL e banco de dados SQL.  Embora este artigo se concentre em sites do Azure, as etapas são semelhantes ao desenvolvimento de [serviços de nuvem do Azure][].

+ [Pré-requisitos](#prerequisites)
+ [Criar o projeto](#create-the-project)
+ [criar um banco de dados SQL](#create-a-sql-database)
+ [Configurar o projeto](#configure-the-project)
+ [Publicar em um Site do Azure](#publish-to-an-azure-website)
+ [Próximas etapas](#next-steps)

##<a name="prerequisites"></a>Pré-requisitos

 - Visual Studio 2012 ou 2013
 - [Python Tools 2,1 para Visual Studio][]
 - [Ferramentas Python 2.1 para amostras VSIX do Visual Studio][]
 - [Azure SDK Tools para VS 2013][] ou [Azure SDK Tools para VS 2012][]
 - [Python 2.7 32-bit][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Criar o projeto

Nesta seção, vamos criar um projeto do Visual Studio usando um modelo de exemplo. Vamos criar um ambiente virtual e instalar os pacotes necessários.  Vamos criar um banco de dados local usando o sqlite.  Em seguida, vamos executar o aplicativo localmente.

1.  No Visual Studio, selecione **Arquivo**, **Novo projeto**.

1.  Os modelos de projeto do VSIX de exemplos PTVS estão disponíveis em **Python**, **Exemplos**.  Selecione o **Projeto Web Django de pesquisas** e clique em OK para criar o projeto.

  	![New Project Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Você precisará instalar pacotes externos.  Selecione **Instalar em um ambiente virtual**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Selecione **Python 2.7** como o interpretador de base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Clique com o botão direito do mouse no nó do projeto e selecione **Python**, **Django Sync DB**.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  Isso abrirá um console de gerenciamento do Django.  Siga os prompts para criar um usuário.

    Isso criará um banco de dados sqlite na pasta do projeto.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Confirme se o aplicativo funciona pressionando <kbd>F5</kbd>.

1.  Na barra de navegação na parte superior, clique **Logon**.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Insira as credenciais do usuário que você criou ao sincronizar o banco de dados.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Clique em **Criar pesquisas de exemplo**.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Clique em uma pesquisa e vote.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-sql-database"></a>criar um banco de dados SQL

Para o banco de dados, vamos criar um banco de dados SQL do Azure.

Você pode criar um banco de dados seguindo essas etapas.

1.  Faça logon no [Portal de Gerenciamento do Azure][].

1.  Na parte inferior do painel de navegação, clique em **NOVO**.

  	![New Button](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png)

1.  Clique em **SERVIÇOS DE DADOS**, em **BANCO DE DADOS SQL** e,em seguida, clique em **CRIAÇÃO RÁPIDA**.

  	![Quick Create SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png)

1.  Escolha esta opção para criar um novo servidor de banco de dados SQL.

1.  Escolha um Grupo de Região/Afinidade no qual deseja localizar o banco de dados. Se você usar o banco de dados de seu aplicativo do Azure, selecione a mesma região onde você implantará seu aplicativo.

##<a name="configure-the-project"></a>Configurar o projeto

Nesta seção, configuraremos nosso aplicativo para usar o banco de dados SQL que acabamos de criar.  Veremos como obter as configurações de conexão do portal do Azure.  Instalaremos também pacotes Python adicionais necessários para usar bancos de dados SQL com o Django.  Em seguida, vamos executar o aplicativo localmente.

1.  No [Portal de Gerenciamento do Azure][], clique em **BANCOS DE DADOS SQL**, e clique no banco de dados que você criou anteriormente.

1.  Clique em **GERENCIAR**.

  	![Manage Button](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlManage.png)

1.  Você precisará atualizar as regras de firewall. Clique em **SIM**.  Isso permitirá conexões com o servidor de banco de dados do computador de desenvolvimento.

  	![Allow Connections](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlUpdateFirewall.png)

1.  Clique em **BANCOS DE DADOS SQL**, em seguida, **SERVIDORES**.  Clique no servidor do banco de dados, em seguida, em **CONFIGURAR**.

1.  Nessa página, você verá o endereço IP de todos os computadores que têm permissão para se conectar ao servidor de banco de dados.  Você deve ver o endereço IP do seu computador.

    A seguir, em **serviços permitidos**, certifique-se de que os serviços do Azure tenham permissão de acesso ao servidor.  Quando o aplicativo é executado em um site do Azure (o que faremos na próxima seção deste tutorial), ele terá permissão para se conectar ao banco de dados.  Clique em **SALVAR** para aplicar a alteração.

  	![Allowed Services](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlAllowedServices.png)

1.  No Visual Studio, abra **settings.py**, da pasta *ProjectName*. Edite a definição de `DATABASES`.

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

    `<DatabaseName>`, `<User>` e `<Password>` são os valores que você especificou quando criou o banco de dados e o servidor.

    Os valores para `<ServerName>` e `<ServerPort>` são gerados pelo Azure quando o servidor é criado e podem ser encontrados na seção **Conectar-se ao seu banco de dados**.

1.  No Gerenciador de Soluções, em **Ambientes de Python**, clique com o botão direito do mouse no ambiente virtual e selecione **Instalar pacote Python**.

1.  Instale o pacote `pyodbc` usando o **easy_install**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Instale o pacote `django-pyodbc-azure` usando **pip**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Clique com o botão direito do mouse no nó do projeto e selecione **Python**, **Django Sync DB**.  

    Isso criará as tabelas do banco de dados SQL criadas na seção anterior.  Siga os prompts para criar um usuário, que não precisa corresponder ao usuário no banco de dados sqlite criado na primeira seção.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Execute o aplicativo com <kbd>F5</kbd>.  As pesquisas que são criadas com **Criar pesquisas de exemplo** e os dados enviados por votação poderão ser serializadas no banco de dados SQL.


##<a name="publish-to-an-azure-website"></a>Publicar em um Site do Azure

O PTVS fornece uma maneira fácil de implantar seu aplicativo Web em um site do Azure.

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto de nó e selecione **Publicar**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  Clique em **Sites do Microsoft Azure**.

1.  Clique em **Novo** para criar um novo site.

1.  Selecione um **Nome de site** e uma **Região** e clique em **Criar**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png)

1.  Aceite todos os outros padrões e clique em **Publicar**.

1.  Seu navegador da Web será aberto automaticamente no site publicado.  Você deve ver o aplicativo em execução como esperado, usando o banco de dados **SQL** hospedado no Azure.

    Parabéns!

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Próximas etapas

Siga estes links para saber mais sobre as ferramentas do Python para Visual Studio, Django e banco de dados SQL.

- [Ferramentas Python para documentação do Visual Studio][]
  - [Projetos da Web][]
  - [Projetos de serviços de nuvem][]
  - [Depuração remota no Microsoft Azure][]
- [Documentação do Django][]
- [Banco de dados SQL][]


<!--Link references-->
[Centro de desenvolvedores do Python]: /pt-br/develop/python/
[Serviços de nuvem do Azure]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
[Python Tools 2,1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Ferramentas Python 2.1 para amostras VSIX do Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Ferramentas do SDK do Azure para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Ferramentas do SDK do Azure para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Ferramentas Python para documentação do Visual Studio]: http://pytools.codeplex.com/documentation
[Depuração remota no Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projetos da Web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projetos de serviços de nuvem]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Documentação do Django]: https://www.djangoproject.com/
[Banco de dados SQL]: /pt-br/documentation/services/sql-database/
