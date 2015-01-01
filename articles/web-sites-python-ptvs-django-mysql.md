<properties linkid="web-sites-python-ptvs-django-mysql" title="Django and MySQL on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Django e MySQL no Azure com Python Tools 2.1 para Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a MySQL database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Django e MySQL no Azure com Python Tools 2.1 para Visual Studio 

Neste tutorial, criaremos um aplicativo de pesquisas simples usando um dos modelos de amostra PTVS. Este tutorial também está disponível como um [vídeo](https://www.youtube.com/watch?v=oKCApIrS0Lo).

Aprenderemos como usar um serviço MySQL hospedado no Azure, como configurar o aplicativo para usar o MySQL e como publicar o aplicativo em um website do Azure.

Consulte o [Python Developer Center][] para obter mais artigos que abrangem o desenvolvimento de websites do Azure com PTVS usando estruturas da web Bottle, Flask e Django, com serviços MongoDB, Azure Table Storage, MySQL e banco de dados SQL.  Embora este artigo foque nos websites do Azure, as etapas são semelhantes ao desenvolvimento de [Serviços de nuvem do Azure][].

+ [Pré-requisitos](#prerequisites)
+ [Criar o projeto](#create-the-project)
+ [Criar um Banco de dados MySQL](#create-a-mysql-database)
+ [Configurar o projeto](#configure-the-project)
+ [Publicar para um website do Azure](#publish-to-an-azure-website)
+ [Próximas etapas](#next-steps)

##<a name="prerequisites"></a>Pré-requisitos

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 para Visual Studio][]
 - [Python Tools 2.1 para Visual Studio Samples VSIX][]
 - [Ferramentas do SDK do Azure para VS 2013][] ou [Ferramentas do SDK do Azure para VS 2012][]
 - [Python 2.7 32 bits][]

> [WACOM.NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Você pode <a href="http://azure.microsoft.com/pt-br/pricing/member-offers/msdn-benefits-details/">ativar seus benefícios de assinante MSDN</a> ou <a href="http://azure.microsoft.com/pt-br/pricing/free-trial/">inscrever-se para uma avaliação gratuita</a>.
> 
> Se quiser conhecer os Sites do Azure antes de assinar uma conta, acesse <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a>, onde é possível criar imediatamente um site ASP.NET de vida curta nos Sites do Azure gratuitamente. Nenhum cartão de crédito necessário, nenhum compromisso.

##<a name="create-the-project"></a>Criar o projeto

Nesta seção, criaremos um projeto Visual Studio usando um modelo de amostra. Criaremos um ambiente virtual e instalaremos pacotes necessários.  Criaremos um banco de dados local usando sqlite.  Em seguida, executaremos o aplicativo localmente.

1.  No Visual Studio, selecione **Arquivo**, **Novo Projeto**.

1.  Os modelos de projeto do PTVS Samples VSIX estão disponíveis em **Python**, **Amostras**.  Selecione **Polls Django Web Project** e clique em OK para criar o projeto.

  	![New Project Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  Você será solicitado a instalar pacotes externos.  Selecione **Instalar em um ambiente virtual**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  Selecione **Python 2.7** como o intérprete base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  Clique com o botão direito no nó do projeto e selecione **Python**, **Django Sync DB**.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  Isso abrirá um Console de Gerenciamento Django.  Siga os prompts para criar um usuário.

    Isso criará um banco de dados sqlite na pasta do projeto.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Confirme se o aplicativo funciona pressionando <kbd>F5</kbd>.

1.  Clique em **Fazer logon** na barra de navegação na parte superior.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Insira as credenciais para o usuário que criou quando sincronizou o banco de dados.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Clique em **Criar pesquisas de amostra**.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  Clique em uma pesquisa e vote.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-mysql-database"></a>Criar um Banco de dados MySQL

Para o banco de dados, criaremos um banco de dados ClearDB MySQL hospedado no Azure.

Como uma alternativa, é possível criar sua própria Máquina Virtual em execução no Azure, em seguida, instale e administre o MySQL você mesmo.

Você pode criar um banco de dados com um plano grátis seguindo estas etapas.

1.  Faça logon no [Portal de Gerenciamento do Azure][].

1.  Na parte inferior do painel de navegação, clique em **NOVO**.

  	![New Button](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  Clique em **STORE**, em seguida, **ClearDB MySQL Database**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  No Nome, digite um nome para usar o serviço do banco de dados.

1.  Escolha uma Região/Grupo de Afinidade no qual localizar o serviço do banco de dados. Se você estiver usando o banco de dados de seu aplicativo do Azure, selecione a mesma região na qual você implantará seu aplicativo.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  Clique em **COMPRA**.

##<a name="configure-the-project"></a>Configurar o projeto

Nesta seção, configuraremos nosso aplicativo para usar o banco de dados MySQL que acabamos de criar.  Veremos como obter configurações de conexão no portal do Azure.  Também instalaremos pacotes adicionais Python necessários para usar bancos de dados MySQL com Django.  Em seguida, executaremos o aplicativo localmente.

1.  No [Portal de Gerenciamento do Azure][], clique em **COMPLEMENTOS**, em seguida, clique no banco de dados ClearDB MySQL que criou anteriormente.

1.  Clique em **INFORMAÇÕES DE CONEXÃO**.  É possível usar o botão de cópia para colocar o valor de **CONNECTIONSTRING** na área de transferência.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  No Visual Studio, abra **settings.py**, na pasta *ProjectName*.  Cole temporariamente a cadeia de conexão obtida no editor.A cadeia de conexão está neste formato:

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Altere o banco de dados padrão **MECANISMO** para usar MySQL e configure os valores para **NAME**, **USER**, **PASSWORD** e **HOST** do **CONNECTIONSTRING**.

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1.  No Gerenciador de Soluções, em **Ambientes Python**, clique com o botão direito no ambiente virtual e selecione **Instalar pacote Python**.

1. Instale o pacote `mysql-python` usando **easy_install**.

  	![Install Package Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  Clique com o botão direito no nó do projeto e selecione **Python**, **Django Sync DB**.  

    Isso criará as tabelas de banco de dados MySQL que criamos na seção anterior.  Siga os prompts para criar um usuário, que não tem de corresponder ao usuário no banco de dados sqlite criado na primeira seção.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Execute o aplicativo com <kbd>F5</kbd>.  Pesquisas que são criadas com **Criar Pesquisas de Amostra** e os dados enviados por voto serão serializados no banco de dados MySQL.

##<a name="publish-to-an-azure-website"></a>Publicar para um website do Azure

O PTVS fornece uma forma fácil de implantar seu aplicativo Web para um website do Azure.

1.  No **Gerenciador de Soluções**, clique com botão direito no nó do projeto e selecione **Publicar**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  Clique no **Microsoft Azure Websites**.

1.  Clique em **Novo** para criar um novo site.

1.  Selecione um **Nome do site** e uma **Região** e clique em **Criar**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  Aceite todos os outros padrões e clique em **Publicar**.

1.  Seu navegador da Web será aberto automaticamente para o sie publicado.  Você deve ver o aplicativo funcionando conforme o esperado, usando o banco de dados **MySQL** hospedado no Azure.

    Parabéns!

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Próximas etapas

Siga estas etapas para aprender mais sobre o Python Tools para Visual Studio, Django e MySQL.

- [Documentação do Python Tools para Visual Studio][]
  - [Projetos da Web][]
  - [Projetos de serviço de nuvem][]
  - [Depuração remota no Microsoft Azure][]
- [Documentação do Django][]
- [MySQL][]


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
[MySQL]: http://www.mysql.com/

<!--HONumber=35_1-->
