<properties 
	pageTitle="Django e MySQL no Azure com Python Tools 2.1 para Visual Studio" 
	description="Aprenda a usar o Python Tools para Visual Studio para criar um aplicativo Django que armazena dados em uma instância de banco de dados MySQL e pode ser implantado em um site." 
	services="app-service\web" 
	tags="python"
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Django e MySQL no Azure com Python Tools 2.1 para Visual Studio 

Neste tutorial, usaremos o [Python Tools para Visual Studio][] para criar um aplicativo de votação simples usando um dos modelos de exemplo de PTVS. Este tutorial também está disponível como um [vídeo](https://www.youtube.com/watch?v=oKCApIrS0Lo).

Aprenderemos a usar um serviço MySQL hospedado no Azure, configurar o aplicativo para usar o MySQL e publicar o aplicativo em um site do Azure.

Consulte o [Python Developer Center][] para obter mais artigos que abrangem o desenvolvimento de websites do Azure com PTVS usando estruturas da web Bottle, Flask e Django, com serviços MongoDB, Azure Table Storage, MySQL e banco de dados SQL.  Embora este artigo se concentre nos sites do Azure, as etapas são semelhantes ao desenvolvimento de [Serviços de Nuvem do Azure][].

## Pré-requisitos

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 para Visual Studio][]
 - [Exemplos VSIX do Python Tools 2.1 para Visual Studio][]
 - [Ferramentas do SDK do Azure para VS 2013][] ou [Ferramentas do SDK do Azure para VS 2012][]
 - [Python 2.7 de 32 bits][]

> [AZURE.NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Você pode <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">ativar os benefícios de assinante do MSDN</a> ou <a href="http://azure.microsoft.com/pricing/free-trial/">se inscrever para fazer uma avaliação gratuita</a>.
> 
> Se você quiser iniciar com Websites do Azure antes de assinar uma conta, acesse <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a>, em que é possível criar imediatamente um site inicial ASP.NET de curta duração nos Websites do Azure, gratuitamente. Nenhum cartão de crédito é exigido, sem compromissos.

## Criar o projeto

Nesta seção, criaremos um projeto Visual Studio usando um modelo de amostra. Criaremos um ambiente virtual e instalaremos pacotes necessários.  Criaremos um banco de dados local usando sqlite.  Em seguida, executaremos o aplicativo localmente.

1.  No Visual Studio, selecione **Arquivo**, **Novo Projeto**.

1.  Os modelos de projeto dos exemplos VSIX do PTVS estão disponíveis em **Python**, **Exemplos**.  Selecione **Projeto Web Django de Sondagens** e clique em OK para criar o projeto.

  	![Caixa de diálogo Novo Projeto](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  Você será solicitado a instalar pacotes externos.  Selecione **Instalar em um ambiente virtual**.

  	![Caixa de diálogo Pacotes Externos](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  Selecione **Python 2.7** como o interpretador de base.

  	![Caixa de diálogo Adicionar Ambiente Virtual](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  Clique com o botão direito do mouse no nó do projeto e selecione **Python**, **Banco de dados de Sincronização do Django**.

  	![Comando de banco de dados de sincronização do Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  Isso abrirá um Console de Gerenciamento Django.  Siga os prompts para criar um usuário.

    Isso criará um banco de dados sqlite na pasta do projeto.

  	![Janela de Console de Gerenciamento do Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Confirme se o aplicativo funciona pressionando <kbd>F5</kbd>.

1.  Clique em **Fazer logon** na barra de navegação na parte superior.

  	![Navegador da Web](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Insira as credenciais para o usuário que você criou quando sincronizou o banco de dados.

  	![Navegador da Web](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Clique em **Criar Votações de Exemplo**.

  	![Navegador da Web](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  Clique em uma pesquisa e vote.

  	![Navegador da Web](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## Criar um banco de dados MySQL

Para o banco de dados, criaremos um banco de dados ClearDB MySQL hospedado no Azure.

Como uma alternativa, é possível criar sua própria Máquina Virtual em execução no Azure, em seguida, instale e administre o MySQL você mesmo.

Você pode criar um banco de dados com um plano grátis seguindo estas etapas.

1.  Faça logon no [Portal de Gerenciamento do Azure][].

1.  Na parte inferior do painel de navegação, clique em **NOVO**.

  	![Botão Novo](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  Clique em **ARMAZENAR** e, em seguida, em **Banco de dados ClearDB MySQL**.

  	![Caixa de diálogo Escolher Complemento](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  Em Nome, digite um nome a ser usado para o serviço de banco de dados.

1.  Escolha uma Região/Grupo de Afinidade no qual localizar o serviço do banco de dados. Se você estiver usando o banco de dados de seu aplicativo do Azure, selecione a mesma região na qual você implantará seu aplicativo.

  	![Caixa de diálogo Personalizar Complemento](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  Clique em **COMPRAR**.

## Configurar o projeto

Nesta seção, configuraremos nosso aplicativo para usar o banco de dados MySQL que acabamos de criar.  Veremos como obter configurações de conexão no portal do Azure.  Também instalaremos pacotes adicionais Python necessários para usar bancos de dados MySQL com Django.  Em seguida, executaremos o aplicativo localmente.

1.  No [Portal de Gerenciamento do Azure][], clique em **COMPLEMENTOS** e, em seguida, clique no banco de dados ClearDB MySQL que você criou anteriormente.

1.  Clique em **INFORMAÇÕES DE CONEXÃO**.  É possível usar o botão de cópia para colocar o valor de **CONNECTIONSTRING** na área de transferência.

  	![Caixa de diálogo Informações de Conexão](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  No Visual Studio, abra **settings.py** na pasta *ProjectName*.  Cole temporariamente a cadeia de conexão obtida no editor.  A cadeia de conexão está neste formato:

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Altere o banco de dados padrão **MECANISMO** para usar MySQL e defina os valores para **NOME**, **USUÁRIO**, **SENHA** e **HOST** de **CONNECTIONSTRING**.

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


1.  No Gerenciador de Soluções, em **Ambientes Python**, clique com o botão direito do mouse no ambiente virtual e selecione **Instalar Pacote Python**.

1. Instale o pacote `mysql-python` usando **easy_install**.

  	![Caixa de diálogo Instalar Pacote](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  Clique com o botão direito do mouse no nó do projeto e selecione **Python**, **Banco de dados de Sincronização do Django**.  

    Isso criará as tabelas de banco de dados MySQL que criamos na seção anterior.  Siga os prompts para criar um usuário, que não tem de corresponder ao usuário no banco de dados sqlite criado na primeira seção.

  	![Janela de Console de Gerenciamento do Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Execute o aplicativo com <kbd>F5</kbd>.  As votações criadas com **Criar Votações de Exemplo** e os dados enviados por voto serão serializados no banco de dados MySQL.

## Publicar para um website do Azure

O PTVS fornece uma forma fácil de implantar seu aplicativo Web para um website do Azure.

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no nó do projeto e selecione **Publicar**.

  	![Caixa de diálogo Web Publicar](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  Clique em **Sites do Microsoft Azure**.

1.  Clique em **Novo** para criar um novo site.

1.  Selecione um **Nome do site** e uma **Região** e clique em **Criar**.

  	![Caixa de diálogo Criar Site no Microsoft Azure](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  Aceite todos os outros padrões e clique em **Publicar**.

1.  Seu navegador da Web será aberto automaticamente para o sie publicado.  Você deve ver o aplicativo funcionando conforme o esperado, usando o banco de dados **MySQL** hospedado no Azure.

    Parabéns!

  	![Navegador da Web](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

## Próximas etapas

Siga estas etapas para aprender mais sobre o Python Tools para Visual Studio, Django e MySQL.

- [Documentação do Python Tools para Visual Studio][]
  - [Projetos da Web][]
  - [Projetos de serviço de nuvem][]
  - [Depuração remota no Microsoft Azure][]
- [Documentação do Django][]
- [MySQL][]


<!--Link references-->
[Python Developer Center]: /develop/python/
[Serviços de nuvem do Azure]: cloud-services-python-ptvs.md

<!--External Link references-->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Exemplos VSIX do Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Ferramentas do SDK do Azure para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Ferramentas do SDK do Azure para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Documentação do Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation
[Depuração remota no Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projetos da Web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projetos de serviço de nuvem]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Documentação do Django]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/


<!--HONumber=52-->