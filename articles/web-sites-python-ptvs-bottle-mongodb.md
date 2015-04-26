<properties 
	pageTitle="Bottle e MongoDB no Azure com Python Tools 2.1 for Visual Studio" 
	description="Saiba como usar as ferramentas do Python para o Visual Studio para criar um aplicativo de Garrafa que armazena dados em uma instância do banco de dados MongoDB e pode ser implantado em um site da Web." 
	services="web-sites" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="huvalo"/>




# Bottle e MongoDB no Azure com Python Tools 2.1 for Visual Studio

Neste tutorial, criaremos um aplicativo de pesquisas simples usando um dos modelos de amostra PTVS. Este tutorial também está disponível como um [vídeo](https://www.youtube.com/watch?v=8hQMyf8p_Jo).

O aplicativo de pesquisas define uma abstração para seu repositório, para que possa alternar facilmente entre diferentes tipos de repositórios (In-Memory, Azure Table Storage, MongoDB).

Aprenderemos como usar um dos serviços MongoDB hospedados no Azure, como configurar o aplicativo para usar o MongoDB e como publicar o aplicativo em um website do Azure.

Consulte o [Centro de desenvolvedores do Python][] para obter mais artigos que abrangem o desenvolvimento de websites do Azure com PTVS usando estruturas da web Bottle, Flask e Django, com serviços MongoDB, Azure Table Storage, MySQL e banco de dados SQL.  Embora este artigo foque nos websites do Azure, as etapas são semelhantes ao desenvolvimento de [Serviços de nuvem do Azure][].

+ [Pré-requisitos](#prerequisites)
+ [Criar o projeto](#create-the-project)
+ [Criar o banco de dados MongoDB](#create-a-mongodb-database)
+ [Configurar o projeto](#configure-the-project)
+ [Explorar o banco de dados MongoDB](#explore-the-mongodb-database)
+ [Publicar para um website do Azure](#publish-to-an-azure-website)
+ [Configurar Site do Azure](#configure-the-azure-website)
+ [Próximas etapas](#next-steps)

##<a name="prerequisites"></a>Pré-requisitos

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 para Visual Studio][]
 - [Python Tools 2.1 para Visual Studio Samples VSIX][]
 - [Ferramentas do SDK do Azure para VS 2013][] ou [Ferramentas do SDK do Azure para VS 2012][]
 - [Python 2.7 32-bit][] ou [Python 3,4 32-bit][]
 - [RoboMongo][] (opcional)

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Criar o projeto

Nesta seção, criaremos um projeto Visual Studio usando um modelo de amostra.  Criaremos um ambiente virtual e instalaremos pacotes necessários.  Em seguida, executaremos o aplicativo localmente usando o repositório da memória padrão.

1.  No Visual Studio, selecione **Arquivo**, **Novo Projeto**. 

1.  Os modelos de projeto do PTVS Samples VSIX estão disponíveis em **Python**, **Amostras**.  Selecione **Polls Bottle Web Project** e clique em OK para criar o projeto.

  	![New Project Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleNewProject.png)

1.  Você será solicitado a instalar pacotes externos.  Selecione **Instalar em um ambiente virtual**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleExternalPackages.png)

1.  Selecione **Python 2.7** ou **Python 3.4** como o intérprete base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAddVirtualEnv.png)

1.  Confirme se o aplicativo funciona pressionando <kbd>F5</kbd>.  Por padrão, o aplicativo usa um repositório da memória que não requer configuração.  Todos os dados são perdidos quando o servidor Web é interrompido.

1.  Clique em **Criar pesquisas de amostra**, em seguida, clique em uma pesquisa e vote.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleInMemoryBrowser.png)

##<a name="create-a-mongodb-database"></a>Criar o banco de dados MongoDB

Para o banco de dados, criaremos um banco de dados MongoLab hospedado no Azure.

Como uma alternativa, é possível criar sua própria Máquina Virtual em execução no Azure, em seguida, instale e administre o MongoDB você mesmo.

Você pode criar uma avaliação gratuita com MongoLab seguindo estas etapas.

1.  Faça logon no [Portal de Gerenciamento do Azure][].

1.  Na parte inferior do painel de navegação, clique em **NOVO**.

  	![New Button](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAzurePlusNew.png)

1.  Clique em **ARMAZENAR**, em seguida, **MongoLab**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon1.png)

1.  No Nome, digite um nome para usar o serviço do banco de dados.

1.  Escolha uma Região/Grupo de Afinidade no qual localizar o serviço do banco de dados. Se você estiver usando o banco de dados de seu aplicativo do Azure, selecione a mesma região na qual você implantará seu aplicativo.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon2.png)

1.  Clique em **COMPRA**.

##<a name="configure-the-project"></a>Configurar o projeto

Nesta seção, configuraremos nosso aplicativo para usar o banco de dados MongoDB que acabamos de criar.  Veremos como obter configurações de conexão no portal do Azure.  Em seguida, executaremos o aplicativo localmente.

1.  No [Portal de Gerenciamento do Azure][], clique em **COMPLEMENTOS**, em seguida, clique no serviço MongoLab que criou anteriormente.

1.  Clique em **INFORMAÇÕES DE CONEXÃO**.  É possível usar o botão de cópia para colocar o valor de **MONGOLAB\_URI** na área de transferência.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  No Visual Studio, clique com o botão direito no nó do projeto no Gerenciador de Soluções e selecione **Propriedades**.  Clique na guia **Depurar**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBProjectDebugSettings.png)

1.  Configure os valores de variáveis de ambiente necessários pelo aplicativo em **Depurar comando do servidor**, **Ambiente**.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    Isso configurará as variáveis de ambiente quando você **Iniciar a depuração**.  Se você quiser que as variáveis sejam configuradas quando **Iniciar sem depurar**, configure os mesmos valores em **Executar comando do servidor** também.

    Como alternativa, é possível definir variáveis de ambiente usando o Painel de Controle do Windows.  Esta é uma opção melhor se desejar evitar armazenar credenciais no código-fonte/arquivo de projeto.  Observe que precisará reiniciar o Visual Studio para que os novos valores de ambiente estejam disponíveis ao aplicativo.

1.  O código que implementa o repositório MongoDB está em **models/mongodb.py**.

1.  Execute o aplicativo com <kbd>F5</kbd>.  Pesquisas que são criadas com **Criar Pesquisas de Amostra** e os dados enviados por voto serão serializados no MongoDB.

1.  Navegue até a página **Sobre** para verificar se o aplicativo está usando o repositório **MongoDB**.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBAbout.png)

##<a name="explore-the-mongodb-database"></a>Explorar o banco de dados MongoDB

É possível usar um aplicativo como [RoboMongo][] para consultar e fazer edições em um banco de dados MongoDB.  Nesta seção, usaremos o RoboMongo para visualizar o conteúdo do banco de dados de aplicativo de pesquisas.

1.  Crie uma nova conexão.  Será necessário o **MONGOLAB\_URI** recuperado na seção anterior.

    Observe o formato da URI: `mongodb://<name>:<password>@<address>:<port>/<name>`

    O nome corresponde ao nome inserido quando criou o serviço com Azure.  É usado para ambos os nomes do banco de dados e de usuário.

1.  Na página de conexão, configure o **Nome** para qualquer nome que gostaria para a conexão.  Configure também os campos **Endereço** e **Porta** para  *address* e *port* de **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  Na página autenticação, defina o **banco de dados** e **nome de usuário** para o *name* de **MONGOLAB\_URI**.  Também configure a **Senha** para a  *password* de **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Salve e conecte-se ao banco de dados.  Agora é possível consultar a coleção de pesquisas.

  	![RoboMongo Query Results](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoQuery.png)

##<a name="publish-to-an-azure-website"></a>Publicar para um website do Azure

O PTVS fornece uma forma fácil de implantar seu aplicativo Web para um website do Azure.

1.  No **Gerenciador de Soluções**, clique com botão direito no nó do projeto e selecione **Publicar**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  Clique no **Microsoft Azure Websites**.

1.  Clique em **Novo** para criar um novo site.

1.  Selecione um **Nome do site** e uma **Região** e clique em **Criar**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonCreateWebSite.png)

1.  Aceite todos os outros padrões e clique em **Publicar**.

1.  Seu navegador da Web será aberto automaticamente para o sie publicado.  Se navegar à página sobre, você verá que ele usa o repositório **Na memória**, não o repositório **MongoDB**.

    Isso é porque as variáveis de ambiente não estão configuradas no Website do Azure, portanto, ele usa os valores padrão especificados em **settings.py**.

##<a name="configure-the-azure-website"></a>Configurar Site do Azure

Nesta seção, configuraremos variáveis do ambiente para o site.

1.  No [Portal de gerenciamento do Azure][], clique no site criado na seção anterior.

1.  No menu principal, clique em **CONFIGURAR**.

  	![Top Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteTopMenu.png)

1.  Role para baixo até a seção **Configurações do aplicativo** e configure os valores para **REPOSITORY\_NAME**, **MONGODB\_HOST** e **MONGODB\_DATABASE** conforme descrito na seção acima.

  	![App Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  No menu inferior, clique em **SALVAR**, em seguida, em **REINICIAR** e finalmente em **NAVEGAR**.

  	![Bottom Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Você deve ver o aplicativo funcionando conforme o esperado, usando o repositório **MongoDB**.

    Parabéns!

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleAzureBrowser.png)

##<a name="next-steps"></a>Próximas etapas

Siga estes links para aprender mais sobre o Python Tools para Visual Studio, Bottle e MongoDB.

- [Documentação do Python Tools para Visual Studio][]
  - [Projetos da Web][]
  - [Projetos de serviços de nuvem][]
  - [Depuração remota no Microsoft Azure][]
- [Documentação do Bottle][]
- [MongoDB][]
- [Documentação do PyMongo][]
- [PyMongo][]


<!--Link references-->
[Centro de desenvolvedores do Python]: /pt-br/develop/python/
[Serviços de nuvem do Azure]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 para Visual Studio Samples VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Ferramentas do SDK do Azure para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Ferramentas do SDK do Azure para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3,4 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentação do Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation
[Documentação do Bottle]: http://bottlepy.org/docs/dev/index.html
[MongoDB]: http://www.mongodb.org/
[Documentação do PyMongo]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Depuração remota no Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projetos da Web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projetos de serviços de nuvem]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project



<!--HONumber=42-->
