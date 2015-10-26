<properties 
	pageTitle="Django e Banco de Dados SQL no Azure com Ferramentas Python 2.2 para Visual Studio" 
	description="Aprenda a usar o Python Tools para Visual Studio para criar um aplicativo Django que armazena dados em uma instância do banco de dados SQL e o implanta em Aplicativos Web do Serviço de Aplicativo do Azure." 
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
	ms.date="08/30/2015"
	ms.author="huguesv"/>




# Django e Banco de Dados SQL no Azure com Ferramentas Python 2.2 para Visual Studio 

Neste tutorial, usaremos o [Python Tools para Visual Studio] para criar um aplicativo Web de votação simples, usando um dos modelos de exemplo de PTVS. Este tutorial também está disponível como um [vídeo](https://www.youtube.com/watch?v=ZwcoGcIeHF4).

Aprenderemos como usar um banco de dados SQL hospedado no Azure, como configurar o aplicativo para usar um banco de dados SQL e como publicar o aplicativo Web em [Aplicativos Web do Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Confira o [Python Developer Center] para obter mais artigos que abrangem o desenvolvimento de Aplicativos Web do Serviço de Aplicativo do Azure com PTVS usando estruturas da Web Bottle, Flask e Django, com serviços MongoDB, Azure Table Storage, MySQL e banco de dados SQL. Embora este artigo se concentre no Serviço de Aplicativo, as etapas são semelhantes ao desenvolvimento de [Serviços de Nuvem do Azure].

## Pré-requisitos

 - Visual Studio 2013 ou 2015
 - [Ferramentas Python 2.2 para Visual Studio]
 - [VSIX de amostra de Ferramentas Python 2.2 para Visual Studio]
 - [Ferramentas do SDK do Azure para VS 2013] ou [Ferramentas do SDK do Azure para VS 2015]
 - [Python 2.7 de 32 bits]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

## Criar o projeto

Nesta seção, criaremos um projeto Visual Studio usando um modelo de amostra. Criaremos um ambiente virtual e instalaremos pacotes necessários. Criaremos um banco de dados local usando sqlite. Em seguida, vamos executar o aplicativo Web localmente.

1.  No Visual Studio, selecione **Arquivo**, **Novo Projeto**.

1.  Os modelos de projeto dos exemplos VSIX do PTVS estão disponíveis em **Python**, **Exemplos**. Selecione **Projeto Web Django de Votações** e clique em OK para criar o projeto.

  	![Caixa de diálogo Novo Projeto](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Você será solicitado a instalar pacotes externos. Selecione **Instalar em um ambiente virtual**.

  	![Caixa de diálogo Pacotes Externos](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Selecione **Python 2.7** como o interpretador de base.

  	![Caixa de diálogo Adicionar Ambiente Virtual](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Clique com o botão direito do mouse no nó do projeto e selecione **Python**, **Banco de dados de Sincronização do Django**.

  	![Comando de banco de dados de sincronização do Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  Isso abrirá um Console de Gerenciamento Django. Siga os prompts para criar um usuário.

    Isso criará um banco de dados sqlite na pasta do projeto.

  	![Janela de Console de Gerenciamento do Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Confirme se o aplicativo funciona pressionando <kbd>F5</kbd>.

1.  Clique em **Fazer logon** na barra de navegação na parte superior.

  	![Navegador da Web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Insira as credenciais para o usuário que você criou quando sincronizou o banco de dados.

  	![Navegador da Web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Clique em **Criar Votações de Exemplo**.

  	![Navegador da Web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Clique em uma pesquisa e vote.

  	![Navegador da Web](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## Criar um banco de dados SQL

Para o banco de dados, vamos criar um banco de dados SQL do Azure.

Você pode criar um banco de dados seguindo estas etapas.

1.  Faça logon no [Portal do Azure].

1.  Na parte inferior do painel de navegação, clique em **NOVO**., clique em **Dados + Armazenamento** > **Banco de dados SQL**.

  	<!-- ![New Button](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png) -->

1.  Configure o novo banco de dados SQL criando um novo grupo de recursos e selecione o local apropriado para o mesmo.

  	<!-- ![Quick Create SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png) -->

1.  Depois de criar o banco de dados SQL, clique em **Abrir no Visual Studio** na folha do banco de dados.
2.  Clique em **Configurar seu firewall**.
3.  Na folha **Configurações de Firewall**, adicione uma regra de firewall com **IP inicial** e **IP final** definido como o endereço IP público de sua máquina de desenvolvimento. Clique em **Salvar**.

	Isso permitirá conexões ao servidor de banco de dados em seu computador de desenvolvimento.

4.  Na folha do banco de dados, clique em **Propriedades** e em **Mostrar cadeias de conexão do banco de dados**.

2.  É possível usar o botão de cópia para colocar o valor de **ADO.NET** na área de transferência.

## Configurar o projeto

Nesta seção, configuraremos nosso aplicativo Web para usar o banco de dados SQL que acabamos de criar. Também instalaremos pacotes adicionais Python necessários para usar bancos de dados SQL com Django. Em seguida, vamos executar o aplicativo Web localmente.

1.  No Visual Studio, abra **settings.py**, na pasta *ProjectName*. Cole temporariamente a cadeia de conexão obtida no editor. A cadeia de conexão está neste formato:

        Server=<ServerName>,<ServerPort>;Database=<DatabaseName>;User ID=<UserName>;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

Editar a definição de `DATABASES` para usar os valores acima.

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<UserName>',
                'PASSWORD': '{your_password_here}',
                'HOST': '<ServerName>',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

1.  No Gerenciador de Soluções, em **Ambientes Python**, clique com o botão direito do mouse no ambiente virtual e selecione **Instalar Pacote Python**.

1.  Instale o pacote `pyodbc` usando **easy\_install**.

  	![Caixa de diálogo Instalar Pacote Python](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Instale o pacote `django-pyodbc-azure` usando **pip**.

  	![Caixa de diálogo Instalar Pacote Python](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Clique com o botão direito do mouse no nó do projeto e selecione **Python**, **Banco de dados de Sincronização do Django**.

    Isso criará as tabelas de banco de dados SQL que criamos na seção anterior. Siga os prompts para criar um usuário, que não tem de corresponder ao usuário no banco de dados sqlite criado na primeira seção.

  	![Janela de Console de Gerenciamento do Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Execute o aplicativo com `F5`. As votações criadas com **Criar Votações de Exemplo** e os dados enviados pela votação serão serializados no banco de dados SQL.


## Publicar aplicativo Web para Serviço de Aplicativo do Azure

O SDK .NET do Azure fornece uma forma fácil de implantar seu aplicativo Web Aplicativos Web do Serviço de Aplicativo do Azure.

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no nó do projeto e selecione **Publicar**.

  	<!-- ![Publish Web Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png) -->

1.  Clique em **Aplicativos Web do Microsoft Azure**.

1.  Clique em **Novo** para criar um novo aplicativo Web.

1.  Preencha os campos a seguir e clique em **Criar**.
	-	**Nome do aplicativo Web**
	-	**Plano do Serviço de Aplicativo**
	-	**Grupo de recursos**
	-	**Região**
	-	Deixe **Servidor de banco de dados** definido como **Nenhum banco de dados**

  	<!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png) -->

1.  Aceite todos os outros padrões e clique em **Publicar**.

1.  Seu navegador da Web será aberto automaticamente para o aplicativo Web publicado. Você deve ver o aplicativo funcionando conforme o esperado, usando o banco de dados **SQL** hospedado no Azure.

    Parabéns!

  	![Navegador da Web](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## Próximas etapas

Siga estas etapas para aprender mais sobre o Python Tools para Visual Studio, Django e banco de dados SQL.

- [Ferramentas Python para documentação do Visual Studio]
  - [Projetos da Web]
  - [Projetos do serviço de nuvem]
  - [Depuração remota no Microsoft Azure]
- [Documentação do Django]
- [Banco de Dados SQL]

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, confira: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--Link references-->
[Python Developer Center]: /develop/python/
[Serviços de Nuvem do Azure]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[Portal do Azure]: https://portal.azure.com
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Ferramentas Python 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[VSIX de amostra de Ferramentas Python 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Ferramentas do SDK do Azure para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Ferramentas do SDK do Azure para VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
[Ferramentas Python para documentação do Visual Studio]: http://aka.ms/ptvsdocs
[Depuração remota no Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Projetos da Web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Projetos do serviço de nuvem]: http://go.microsoft.com/fwlink/?LinkId=624028
[Documentação do Django]: https://www.djangoproject.com/
[Banco de Dados SQL]: /documentation/services/sql-database/

<!---HONumber=Oct15_HO3-->