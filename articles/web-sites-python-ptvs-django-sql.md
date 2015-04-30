<properties 
	pageTitle="Django e banco de dados SQL no Azure com Python Tools 2.1 para Visual Studio" 
	description="Aprenda a usar o Python Tools para Visual Studio para criar um aplicativo Django que armazena dados em uma instância de banco de dados SQL e pode ser implantado em um site." 
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




# Django e banco de dados SQL no Azure com Python Tools 2.1 para Visual Studio 

Neste tutorial, usaremos o [Python Tools para Visual Studio][] para criar um aplicativo de votação simples usando um dos modelos de exemplo de PTVS. Este tutorial também está disponível como um [vídeo](https://www.youtube.com/watch?v=ZwcoGcIeHF4).

Aprenderemos como usar um banco de dados SQL hospedado no Azure, como configurar o aplicativo para usar um banco de dados SQL e como publicar o aplicativo em um website do Azure.

Consulte o [Python Developer Center][] para obter mais artigos que abrangem o desenvolvimento de websites do Azure com PTVS usando estruturas da web Bottle, Flask e Django, com serviços MongoDB, Azure Table Storage, MySQL e banco de dados SQL.  Embora este artigo se concentre nos sites do Azure, as etapas são semelhantes ao desenvolvimento de [Serviços de Nuvem do Azure][].

## Pré-requisitos

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 para Visual Studio][]
 - [Exemplos VSIX do Python Tools 2.1 para Visual Studio][]
 - [Ferramentas do SDK do Azure para VS 2013][] ou [Ferramentas do SDK do Azure para VS 2012][]
 - [Python 2.7 de 32 bits][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Criar o projeto

Nesta seção, criaremos um projeto Visual Studio usando um modelo de amostra. Criaremos um ambiente virtual e instalaremos pacotes necessários.  Criaremos um banco de dados local usando sqlite.  Em seguida, executaremos o aplicativo localmente.

1.  No Visual Studio, selecione **Arquivo**, **Novo Projeto**.

1.  Os modelos de projeto dos exemplos VSIX do PTVS estão disponíveis em **Python**, **Exemplos**.  Selecione **Projeto Web Django de Sondagens** e clique em OK para criar o projeto.

  	![Caixa de diálogo Novo Projeto](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Você será solicitado a instalar pacotes externos.  Selecione **Instalar em um ambiente virtual**.

  	![Caixa de diálogo pacotes externos](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Selecione **Python 2.7** como o interpretador de base.

  	![Caixa de diálogo Adicionar Ambiente Virtual](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Clique com o botão direito do mouse no nó do projeto e selecione **Python**, **Banco de dados de Sincronização do Django**.

  	![Comando de banco de dados de sincronização do Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  Isso abrirá um Console de Gerenciamento Django.  Siga os prompts para criar um usuário.

    Isso criará um banco de dados sqlite na pasta do projeto.

  	![Janela de Console de Gerenciamento do Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Confirme se o aplicativo funciona pressionando <kbd>F5</kbd>.

1.  Clique em **Fazer logon** na barra de navegação na parte superior.

  	![Navegador da Web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Insira as credenciais para o usuário que você criou quando sincronizou o banco de dados.

  	![Navegador da Web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Clique em **Criar Sondagens de Exemplo**.

  	![Navegador da Web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Clique em uma pesquisa e vote.

  	![Navegador da Web](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## Criar um banco de dados SQL

Para o banco de dados, criaremos um banco de dados SQL do Azure.

Você pode criar um banco de dados seguindo estas etapas.

1.  Faça logon no [Portal de Gerenciamento do Azure][].

1.  Na parte inferior do painel de navegação, clique em **NOVO**.

  	![Botão Novo](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png)

1.  Clique em **SERVIÇOS DE DADOS**, em **BANCO DE DADOS SQL** e em **CRIAÇÃO RÁPIDA**.

  	![Criação rápida de banco de dados SQL](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png)

1.  Escolha esta opção para criar um novo servidor de banco de dados SQL.

1.  Escolha uma Região/Grupo de Afinidade no qual localizar o banco de dados. Se você estiver usando o banco de dados de seu aplicativo do Azure, selecione a mesma região na qual você implantará seu aplicativo.

## Configurar o projeto

Nesta seção, configuraremos nosso aplicativo para usar o banco de dados SQL 
que acabamos de criar.  Veremos como obter configurações de conexão no portal 
do Azure.  Também instalaremos pacotes do Python adicionais necessários para usar os 
bancos de dados SQL com Django.  Em seguida, executaremos o aplicativo localmente.

1.  No [Portal de Gerenciamento do Azure][], clique em **BANCOS DE DADOS SQL** e clique no banco de dados que você criou anteriormente.

1.  Clique em **GERENCIAR**.

  	![Botão Gerenciar](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlManage.png)

1.  Você será solicitado a atualizar as regras de firewall. Clique em **SIM**.  Isso permitirá conexões ao servidor de banco de dados em seu computador de desenvolvimento.

  	![Permitir Conexões](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlUpdateFirewall.png)

1.  Clique em **BANCOS DE DADOS SQL** e em **SERVIDORES**.  Clique no servidor para seu banco de dados e em **CONFIGURAR**.

1.  Nesta página, você verá o endereço IP de cada computador que tem permissão para se conectar ao servidor de banco de dados.  Você deve ver o endereço IP de seu computador.

    Abaixo, em **serviços permitidos**, verifique se os serviços do Azure têm acesso permitido ao servidor.  Quando o aplicativo estiver executando em um website do Azure (que faremos na próxima seção deste tutorial), será permitido se conectar ao banco de dados.  Clique em **SALVAR** para aplicar a alteração.

  	![Serviços Permitidos](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlAllowedServices.png)

1.  No Visual Studio, abra **settings.py** na pasta *ProjectName*. Edite a definição de `DATABASES`.

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

    Os valores de `<ServerName>` e `<ServerPort>` são gerados pelo Azure quando o servidor é criado e podem ser encontrados na seção **Conectar-se ao banco de dados**.

1.  No Gerenciador de Soluções, em **Ambientes Python**, clique com o botão direito do mouse no ambiente virtual e selecione **Instalar Pacote Python**.

1.  Instale o pacote `pyodbc` usando **easy_install**.

  	![Caixa de diálogo Instalar Pacote Python](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Instale o pacote `django-pyodbc-azure` usando **pip**.

  	![Caixa de diálogo Instalar Pacote Python](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Clique com o botão direito do mouse no nó do projeto e selecione **Python**, **Banco de dados de Sincronização do Django**.  

    Isso criará as tabelas de banco de dados SQL que criamos na seção anterior.  Siga os prompts para criar um usuário, que não tem de corresponder ao usuário no banco de dados sqlite criado na primeira seção.

  	![Janela de Console de Gerenciamento do Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Execute o aplicativo com <kbd>F5</kbd>.  As votações criadas com **Criar Votações de Exemplo** e os dados enviados pela votação serão serializados no banco de dados SQL.


## Publicar para um website do Azure

O PTVS fornece uma forma fácil de implantar seu aplicativo Web para um website do Azure.

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no nó do projeto e selecione **Publicar**.

  	![Caixa de diálogo Web Publicar](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  Clique em **Sites do Microsoft Azure**.

1.  Clique em **Novo** para criar um novo site.

1.  Selecione um **Nome do site** e uma **Região** e clique em **Criar**.

  	![Caixa de diálogo Criar Site no Microsoft Azure](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png)

1.  Aceite todos os outros padrões e clique em **Publicar**.

1.  Seu navegador da Web será aberto automaticamente para o sie publicado.  Você deve ver o aplicativo funcionando conforme o esperado, usando o banco de dados **SQL** hospedado no Azure.

    Parabéns!

  	![Navegador da Web](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## Próximas etapas

Siga estas etapas para aprender mais sobre o Python Tools para Visual Studio, Django e banco de dados SQL.

- [Documentação do Python Tools para Visual Studio][]
  - [Projetos da Web][]
  - [Projetos de serviço de nuvem][]
  - [Depuração remota no Microsoft Azure][]
- [Documentação do Django][]
- [Banco de Dados SQL][]


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
[Banco de Dados SQL]: /documentation/services/sql-database/

<!--HONumber=52-->