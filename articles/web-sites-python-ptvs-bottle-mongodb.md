<properties 
	pageTitle="Bottle e MongoDB no Azure com Python Tools 2.1 para Visual Studio" 
	description="Aprenda a usar o Python Tools para Visual Studio para criar um aplicativo Bottle que armazena dados em uma instância do MongoDB e pode ser implantado em um site." 
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




# Bottle e MongoDB no Azure com Python Tools 2.1 para Visual Studio

Neste tutorial, usaremos o [Python Tools para Visual Studio][] para criar um aplicativo de votação simples usando um dos modelos de exemplo de PTVS. Este tutorial também está disponível como um [vídeo](https://www.youtube.com/watch?v=8hQMyf8p_Jo).

O aplicativo de pesquisas define uma abstração para seu repositório, para que possa alternar facilmente entre diferentes tipos de repositórios (In-Memory, Azure Table Storage, MongoDB).

Aprenderemos como usar um dos serviços MongoDB hospedados no Azure, como configurar o aplicativo para usar o MongoDB e como publicar o aplicativo em um website do Azure.

Consulte o [Python Developer Center][] para obter mais artigos que abrangem o desenvolvimento de websites do Azure com PTVS usando estruturas da web Bottle, Flask e Django, com serviços MongoDB, Azure Table Storage, MySQL e banco de dados SQL.  Embora este artigo se concentre nos sites do Azure, as etapas são semelhantes ao desenvolvimento de [Serviços de Nuvem do Azure][].

## Pré-requisitos

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 para Visual Studio][]
 - [Exemplos VSIX do Python Tools 2.1 para Visual Studio][]
 - [Ferramentas do SDK do Azure para VS 2013][] ou [Ferramentas do SDK do Azure para VS 2012][]
 - [Python 2.7 de 32 bits][] ou [Python 3.4 de 32 bits][]
 - [RoboMongo][] (opcional)

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Criar o projeto

Nesta seção, criaremos um projeto Visual Studio usando um modelo de amostra.  Criaremos um ambiente virtual e instalaremos pacotes necessários.  Em seguida, executaremos o aplicativo localmente usando o repositório da memória padrão.

1.  No Visual Studio, selecione **Arquivo**, **Novo Projeto**. 

1.  Os modelos de projeto dos exemplos VSIX do PTVS estão disponíveis em **Python**, **Exemplos**.  Selecione **Projeto Web Bottle de Votações** e clique em OK para criar o projeto.

  	![Caixa de diálogo Novo Projeto](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleNewProject.png)

1.  Você será solicitado a instalar pacotes externos.  Selecione **Instalar em um ambiente virtual**.

  	![Caixa de diálogo Pacotes Externos](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleExternalPackages.png)

1.  Selecione **Python 2.7** ou **Python 3.4** como o interpretador de base.

  	![Caixa de diálogo Adicionar Ambiente Virtual](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAddVirtualEnv.png)

1.  Confirme se o aplicativo funciona pressionando <kbd>F5</kbd>.  Por padrão, o aplicativo usa um repositório da memória que não requer configuração.  Todos os dados são perdidos quando o servidor Web é interrompido.

1.  Clique em **Criar Votações de Exemplo** e, em seguida, clique em uma votação e vote.

  	![Navegador da Web](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleInMemoryBrowser.png)

## Criar um banco de dados MongoDB

Para o banco de dados, criaremos um banco de dados MongoLab hospedado no Azure.

Como uma alternativa, é possível criar sua própria Máquina Virtual em execução no Azure, em seguida, instale e administre o MongoDB você mesmo.

Você pode criar uma avaliação gratuita com MongoLab seguindo estas etapas.

1.  Faça logon no [Portal de Gerenciamento do Azure][].

1.  Na parte inferior do painel de navegação, clique em **NOVO**.

  	![Botão Novo](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAzurePlusNew.png)

1.  Clique em **ARMAZENAR** e, em seguida, em **MongoLab**.

  	![Escolha a caixa de diálogo Complemento](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon1.png)

1.  Em Nome, digite um nome a ser usado para o serviço de banco de dados.

1.  Escolha uma Região/Grupo de Afinidade no qual localizar o serviço do banco de dados. Se você estiver usando o banco de dados de seu aplicativo do Azure, selecione a mesma região na qual você implantará seu aplicativo.

  	![Caixa de diálogo Personalizar Complemento](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon2.png)

1.  Clique em **COMPRAR**.

## Configurar o projeto

Nesta seção, configuraremos nosso aplicativo para usar o banco de dados MongoDB que acabamos de criar.  Veremos como obter configurações de conexão no portal do Azure.  Em seguida, executaremos o aplicativo localmente.

1.  No [Portal de Gerenciamento do Azure][], clique em **COMPLEMENTOS** e, em seguida, clique no serviço MongoLab que você criou anteriormente.

1.  Clique em **INFORMAÇÕES DE CONEXÃO**.  É possível usar o botão de cópia para colocar o valor de **MONGOLAB_URI** na área de transferência.

  	![Caixa de diálogo Informações de Conexão](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  No Visual Studio, clique com o botão direito do mouse no nó do projeto no Gerenciador de Soluções e selecione **Propriedades**.  Clique na guia **Depurar**.

  	![Configurações de depuração do projeto](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBProjectDebugSettings.png)

1.  Defina os valores de variáveis de ambiente necessários para o aplicativo em **Depurar Comando do Servidor**, **Ambiente**.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    Isso definirá as variáveis de ambiente quando você **Iniciar a Depuração**.  Se quiser que as variáveis sejam definidas quando você **Iniciar sem Depurar**, configure também os mesmos valores em **Executar Comando do Servidor**.

    Como alternativa, é possível definir variáveis de ambiente usando o Painel de Controle do Windows.  Esta é uma opção melhor se desejar evitar armazenar credenciais no código-fonte/arquivo de projeto.  Observe que precisará reiniciar o Visual Studio para que os novos valores de ambiente estejam disponíveis ao aplicativo.

1.  O código que implementa o repositório MongoDB está em **models/mongodb.py**.

1.  Execute o aplicativo com <kbd>F5</kbd>.  As votações criadas com **Criar Votações de Exemplo** e os dados enviados por voto serão serializados no MongoDB.

1.  Navegue até a página **Sobre** para verificar se o aplicativo está usando o repositório **MongoDB**.

  	![Navegador da Web](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBAbout.png)

## Explorar o banco de dados MongoDB

É possível usar um aplicativo como [RoboMongo][] para consultar e fazer edições em um banco de dados MongoDB.  Nesta seção, usaremos o RoboMongo para visualizar o conteúdo do banco de dados de aplicativo de pesquisas.

1.  Crie uma nova conexão.  Será necessário o **MONGOLAB_URI** recuperado na seção anterior.

    Observe o formato da URI: `mongodb://<name>:<password>@<address>:<port>/<name>`

    O nome corresponde ao nome inserido quando você criou o serviço com o Azure.  É usado para ambos os nomes do banco de dados e de usuário.

1.  Na página de conexão, defina o **Nome** como qualquer nome que deseja usar para a conexão.  Também defina os campos **Endereço** e **Porta** como o *address* e a *port* de **MONGOLAB_URI**.

  	![Caixa de diálogo Configurações de Conexão](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  Na página de autenticação, defina o **Banco de dados** e o **Nome de usuário** como o *name* de **MONGOLAB_URI**.  Defina também a **Senha** como a *password* de **MONGOLAB_URI**.

  	![Caixa de diálogo Configurações de Conexão](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Salve e conecte-se ao banco de dados.  Agora é possível consultar a coleção de pesquisas.

  	![Resultados da consulta do RoboMongo](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoQuery.png)

## Publicar em um site do Azure

O PTVS fornece uma forma fácil de implantar seu aplicativo Web para um website do Azure.

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no nó do projeto e selecione **Publicar**.

  	![Caixa de diálogo Web Publicar](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  Clique em **Sites do Microsoft Azure**.

1.  Clique em **Novo** para criar um novo site.

1.  Selecione um **Nome do site** e uma **Região** e clique em **Criar**.

  	![Caixa de diálogo Criar Site no Microsoft Azure](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonCreateWebSite.png)

1.  Aceite todos os outros padrões e clique em **Publicar**.

1.  Seu navegador da Web será aberto automaticamente para o sie publicado.  Se navegar até página Sobre, você verá que ela usa o repositório **Na memória**, não o repositório **MongoDB**.

    Isso ocorre porque as variáveis de ambiente não estão configuradas no site do Azure; portanto, ele usa os valores padrão especificados em **settings.py**.

## Configurar Site do Azure

Nesta seção, configuraremos variáveis do ambiente para o site.

1.  No [Portal de gerenciamento do Azure][], clique no site criado na seção anterior.

1.  No menu superior, clique em **CONFIGURAR**.

  	![Menu superior](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteTopMenu.png)

1.  Role para baixo até a seção **configurações do aplicativo** e defina os valores para **REPOSITORY_NAME**, **MONGODB_HOST** e **MONGODB_DATABASE** conforme descrito na seção acima.

  	![Configurações do aplicativo](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  No menu inferior, clique em **SALVAR**, em **REINICIAR** e, finalmente, em **NAVEGAR**.

  	![Menu inferior](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Você deve ver o aplicativo funcionando conforme o esperado, usando o repositório **MongoDB**.

    Parabéns!

  	![Navegador da Web](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleAzureBrowser.png)

## Próximas etapas

Siga estes links para aprender mais sobre o Python Tools para Visual Studio, Bottle e MongoDB.

- [Documentação do Python Tools para Visual Studio][]
  - [Projetos da Web][]
  - [Projetos de serviço de nuvem][]
  - [Depuração remota no Microsoft Azure][]
- [Documentação do Bottle][]
- [MongoDB][]
- [Documentação do PyMongo][]
- [PyMongo][]


<!--Link references-->
[Python Developer Center]: /develop/python/
[Serviços de nuvem do Azure]: cloud-services-python-ptvs.md

<!--External Link references-->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Exemplos VSIX do Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Ferramentas do SDK do Azure para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Ferramentas do SDK do Azure para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentação do Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation
[Documentação do Bottle]: http://bottlepy.org/docs/dev/index.html
[MongoDB]: http://www.mongodb.org/
[Documentação do PyMongo]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Depuração remota no Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projetos da Web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projetos de serviço de nuvem]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project


<!--HONumber=52-->