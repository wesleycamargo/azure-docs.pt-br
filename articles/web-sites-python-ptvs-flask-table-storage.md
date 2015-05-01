<properties 
	pageTitle="Flask e armazenamento de tabela do Azure no Azure com ferramentas 2.1 do Python para Visual Studio" 
	description="Aprenda a usar o Python Tools para Visual Studio para criar um aplicativo Flask que armazena dados no armazenamento de tabelas do Azure e pode ser implantado em um site." 
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




# Flask e armazenamento de tabela do Azure no Azure com ferramentas 2.1 do Python para Visual Studio 

Neste tutorial, usaremos o [Python Tools para Visual Studio][] para criar um aplicativo de votação simples usando um dos modelos de exemplo de PTVS. Este tutorial também está disponível como um [vídeo](https://www.youtube.com/watch?v=qUtZWtPwbTk).

O aplicativo de pesquisas define uma abstração para seu repositório, para que possa alternar facilmente entre diferentes tipos de repositórios (In-Memory, Azure Table Storage, MongoDB).

Aprenderemos como criar uma conta de Armazenamento do Azure, como configurar o aplicativo para usar o Armazenamento de Tabela do Azure e como publicar o aplicativo em um website do Azure.

Consulte o [Python Developer Center][] para obter mais artigos que abrangem o desenvolvimento de websites do Azure com PTVS usando estruturas da web Bottle, Flask e Django, com serviços MongoDB, Azure Table Storage, MySQL e banco de dados SQL.  Embora este artigo se concentre nos sites do Azure, as etapas são semelhantes ao desenvolvimento de [Serviços de Nuvem do Azure][].

## Pré-requisitos

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 para Visual Studio][]
 - [Exemplos VSIX do Python Tools 2.1 para Visual Studio][]
 - [Ferramentas do SDK do Azure para VS 2013][] ou [Ferramentas do SDK do Azure para VS 2012][]
 - [Python 2.7 de 32 bits][] ou [Python 3.4 de 32 bits][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Criar o projeto

Nesta seção, criaremos um projeto Visual Studio usando um modelo de amostra.  Criaremos um ambiente virtual e instalaremos pacotes necessários.  Em seguida, executaremos o aplicativo localmente usando o repositório da memória padrão.

1.  No Visual Studio, selecione **Arquivo**, **Novo Projeto**.

1.  Os modelos de projeto dos exemplos VSIX do PTVS estão disponíveis em **Python**, **Exemplos**.  Selecione **Projeto Web Flask de Votações** e clique em OK para criar o projeto.

  	![Caixa de diálogo Novo Projeto](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)

1.  Você será solicitado a instalar pacotes externos.  Selecione **Instalar em um ambiente virtual**.

  	![Caixa de diálogo Pacotes Externos](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)

1.  Selecione **Python 2.7** ou **Python 3.4** como o interpretador de base.

  	![Caixa de diálogo Adicionar Ambiente Virtual](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)

1.  Confirme se o aplicativo funciona pressionando <kbd>F5</kbd>.  Por padrão, o aplicativo usa um repositório da memória que não requer configuração.  Todos os dados são perdidos quando o servidor Web é interrompido.

1.  Clique em **Criar Votações de Exemplo** e, em seguida, clique em uma votação e vote.

  	![Navegador da Web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

## Criar uma conta de armazenamento do Azure

Para usar as operações de armazenamento, você precisa de uma conta de armazenamento do Azure. Você pode criar uma conta de armazenamento seguindo essas etapas.

1.  Faça logon no [Portal de Gerenciamento do Azure][].

1.  Na parte inferior do painel de navegação, clique em **NOVO**.

  	![Botão Novo](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzurePlusNew.png)

1.  Clique em **SERVIÇOS DE DADOS**, **ARMAZENAMENTO** e em **CRIAÇÃO RÁPIDA**.

  	![Criação rápida](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)

1.  Em URL, digite um nome de subdomínio a ser usado no URI para a conta de armazenamento.  A entrada pode conter de 3 a 24 letras minúsculas e números. Esse valor torna-se o nome de host no URI que é usado para lidar com os recursos de Blob, Fila ou Tabela da assinatura em questão.

1.  Escolha uma Região/Grupo de Afinidade no qual localizar o armazenamento. Se você estiver usando o armazenamento de seu aplicativo do Azure, selecione a mesma região na qual você implantará seu aplicativo.

1.  Opcionalmente, você pode habilitar a replicação geográfica.  Com a replicação geográfica, o Armazenamento do Azure agora mantém seus dados duráveis em dois locais. Em ambos os locais, o Armazenamento do Azure mantém constantemente diversas réplicas saudáveis de seus dados.

1.  Clique em **CRIAR CONTA DE ARMAZENAMENTO**.

## Configurar o projeto

Nesta seção, configuraremos nosso aplicativo para usar a conta de armazenamento que acabamos de criar.  Veremos como obter configurações de conexão no portal do Azure.  Em seguida, executaremos o aplicativo localmente.

1.  No [Portal de Gerenciamento do Azure][], clique na conta de armazenamento criada na seção anterior.

1.  Clique em **GERENCIAR CHAVES DE ACESSO**.

  	![Caixa de diálogo Gerenciar Chaves de Acesso](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  No Visual Studio, clique com o botão direito do mouse no nó do projeto no Gerenciador de Soluções e selecione **Propriedades**.  Clique na guia **Depurar**.

  	![Configurações de depuração do projeto](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)

1.  Defina os valores de variáveis de ambiente necessários para o aplicativo em **Depurar Comando do Servidor**, **Ambiente**.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    Isso definirá as variáveis de ambiente quando você **Iniciar a Depuração**.  Se quiser que as variáveis sejam definidas quando você **Iniciar sem Depurar**, configure também os mesmos valores em **Executar Comando do Servidor**.

    Como alternativa, é possível definir variáveis de ambiente usando o Painel de Controle do Windows.  Esta é uma opção melhor se desejar evitar armazenar credenciais no código-fonte/arquivo de projeto.  Observe que precisará reiniciar o Visual Studio para que os novos valores de ambiente estejam disponíveis ao aplicativo.

1.  O código que implementa o repositório do Armazenamento de Tabela do Azure está em **models/azuretablestorage.py**.  Consulte a [documentação] para obter mais informações sobre como usar o Serviço de Tabela do Python.

1.  Execute o aplicativo com <kbd>F5</kbd>.  As votações que são criadas com **Criar Votações de Exemplo** e os dados enviados por voto serão serializados no Armazenamento de Tabela do Azure.

1.  Navegue até a página **Sobre** para verificar se o aplicativo está usando o repositório do **Armazenamento de Tabela do Azure**.

  	![Navegador da Web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

## Explorar o Armazenamento de tabela do Azure

É fácil visualizar e editar tabelas de armazenamento usando o Gerenciador de Servidores no Visual Studio.  Nesta seção, usaremos o Gerenciador de Servidores para visualizar o conteúdo das tabelas do aplicativo de pesquisas.

> [AZURE.NOTE] Isso requer que o Microsoft Azure Tools seja instalado, disponível como parte do [SDK do Azure para .NET][].

1.  Abra o **Gerenciador de Servidores**.  Expanda **Azure**, **Armazenamento**, sua conta de armazenamento e, em seguida, **Tabelas**.

  	![Gerenciador de Servidores](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorer.png)

1.  Clique duas vezes na tabela **votações** ou **opções** para exibir o conteúdo da tabela em uma janela de documento, bem como adicionar/remover/editar entidades.

  	![Resultados da consulta de tabela](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorerTable.png)

## Publicar em um site do Azure

O PTVS fornece uma forma fácil de implantar seu aplicativo Web para um website do Azure.

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no nó do projeto e selecione **Publicar**.

  	![Caixa de diálogo Web Publicar](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  Clique em **Sites do Microsoft Azure**.

1.  Clique em **Novo** para criar um novo site.

1.  Selecione um **Nome do site** e uma **Região** e clique em **Criar**.

  	![Caixa de diálogo Criar Site no Microsoft Azure](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonCreateWebSite.png)

1.  Aceite todos os outros padrões e clique em **Publicar**.

1.  Seu navegador da Web será aberto automaticamente para o sie publicado.  Se navegar até a página sobre, você verá que ele usa o repositório **Na memória**, não o repositório do **Armazenamento de tabela do Azure**.

    Isso ocorre porque as variáveis de ambiente não estão configuradas no site do Azure; portanto, ele usa os valores padrão especificados em **settings.py**.

## Configurar Site do Azure

Nesta seção, configuraremos variáveis do ambiente para o site.

1.  No [Portal de Gerenciamento do Azure][], clique no site criado na seção anterior.

1.  No menu superior, clique em **CONFIGURAR**.

  	![Menu superior](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteTopMenu.png)

1.  Role para baixo até a seção **configurações do aplicativo** e defina os valores para **REPOSITORY\_NAME**, **STORAGE\_NAME** e **STORAGE\_KEY** conforme descrito na seção acima.

  	![Configurações do Aplicativo](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. No menu inferior, clique em **SALVAR**, em **REINICIAR** e, finalmente, em **NAVEGAR**.

  	![Menu inferior](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Você deve ver o aplicativo funcionando conforme o esperado, usando o repositório do **Armazenamento de Tabela do Azure**.

    Parabéns!

  	![Navegador da Web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

## Próximas etapas

Siga estes links para saber mais sobre as ferramentas Python para Visual Studio, Flask e armazenamento de tabela do Azure.

- [Documentação do Python Tools para Visual Studio][]
  - [Projetos da Web][]
  - [Projetos de serviço de nuvem][]
  - [Depuração remota no Microsoft Azure][]
- [Documentação do Flask][]
- [Armazenamento do Azure][]
- [SDK do Azure para Python][]
- [Como usar o serviço de armazenamento de tabela por meio do Python][]


<!--Link references-->
[Python Developer Center]: /develop/python/
[Serviços de nuvem do Azure]: cloud-services-python-ptvs.md
[documentação]: storage-python-how-to-use-table-storage.md
[Como usar o serviço de armazenamento de tabela por meio do Python]: storage-python-how-to-use-table-storage.md

<!--External Link references-->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
[SDK do Azure para .NET]: http://azure.microsoft.com/downloads/
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Exemplos VSIX do Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Ferramentas do SDK do Azure para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Ferramentas do SDK do Azure para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentação do Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation
[Documentação do Flask]: http://flask.pocoo.org/
[Depuração remota no Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projetos da Web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projetos de serviço de nuvem]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Armazenamento do Azure]: http://azure.microsoft.com/documentation/services/storage/
[SDK do Azure para Python]: https://github.com/Azure/azure-sdk-for-python


<!--HONumber=52-->