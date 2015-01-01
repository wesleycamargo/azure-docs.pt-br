<properties linkid="web-sites-python-ptvs-bottle-table-storage" title="Bottle and Azure Table Storage on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Bottle e Armazenamento de Tabela do Azure com Python Tools 2.1 para Visual Studio" description="Aprenda a usar o Python Tools para Visual Studio para criar um aplicativo Bottle que armazena dados no Armazenamento de tabela do Azure e pode ser implantado em um website." metaKeywords="" services="web-sites" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Bottle e Armazenamento de Tabela do Azure com Python Tools 2.1 para Visual Studio 

Neste tutorial, criaremos um aplicativo de pesquisas simples usando um dos modelos de amostra PTVS. Este tutorial também está disponível como um [vídeo](https://www.youtube.com/watch?v=GJXDGaEPy94).

O aplicativo de pesquisas define uma abstração para seu repositório, para que possa alternar facilmente entre diferentes tipos de repositórios (In-Memory, Azure Table Storage, MongoDB).

Aprenderemos como criar uma conta de Armazenamento do Azure, como configurar o aplicativo para usar o Armazenamento de Tabela do Azure e como publicar o aplicativo em um website do Azure.

Consulte o [Python Developer Center][] para obter mais artigos que abrangem o desenvolvimento de websites do Azure com PTVS usando estruturas da web Bottle, Flask e Django, com serviços MongoDB, Azure Table Storage, MySQL e banco de dados SQL.  Embora este artigo foque nos websites do Azure, as etapas são semelhantes ao desenvolvimento de [Serviços de nuvem do Azure][].

+ [Pré-requisitos](#prerequisites)
+ [Criar o projeto](#create-the-project)
+ [Criar uma conta de armazenamento do Azure](#create-an-azure-storage-account)
+ [Configurar o projeto](#configure-the-project)
+ [Explorar o Armazenamento de tabela do Azure](#explore-the-azure-table-storage)
+ [Publicar para um website do Azure](#publish-to-an-azure-website)
+ [Configurar Site do Azure](#configure-the-azure-website)
+ [Próximas etapas](#next-steps)

##<a name="prerequisites"></a>Pré-requisitos

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 para Visual Studio][]
 - [Python Tools 2.1 para Visual Studio Samples VSIX][]
 - [Ferramentas do SDK do Azure para VS 2013][] ou [Ferramentas do SDK do Azure para VS 2012][]
 - [Python 2.7 de 32 bits][] ou [Python 3.4 de 32 bits][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Criar o projeto

Nesta seção, criaremos um projeto Visual Studio usando um modelo de amostra.  Criaremos um ambiente virtual e instalaremos pacotes necessários.  Em seguida, executaremos o aplicativo localmente usando o repositório da memória padrão.

1.  No Visual Studio, selecione **Arquivo**, **Novo Projeto**.

1.  Os modelos de projeto do PTVS Samples VSIX estão disponíveis em **Python**, **Amostras**.  Selecione **Polls Bottle Web Project** e clique em OK para criar o projeto.

  	![New Project Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleNewProject.png)

1.  Você será solicitado a instalar pacotes externos.  Selecione **Instalar em um ambiente virtual**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleExternalPackages.png)

1.  Selecione **Python 2.7** ou **Python 3.4** como o intérprete base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAddVirtualEnv.png)

1.  Confirme se o aplicativo funciona pressionando <kbd>F5</kbd>.  Por padrão, o aplicativo usa um repositório da memória que não requer configuração.  Todos os dados são perdidos quando o servidor Web é interrompido.

1.  Clique em **Criar pesquisas de amostra**, em seguida, clique em uma pesquisa e vote.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleInMemoryBrowser.png)

##<a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

Para usar as operações de armazenamento, você precisa de uma conta de armazenamento do Azure. Você pode criar uma conta de armazenamento seguindo essas etapas.

1.  Faça logon no [Portal de Gerenciamento do Azure][].

1.  Na parte inferior do painel de navegação, clique em **NOVO**.

  	![New Button](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzurePlusNew.png)

1.  Clique em **SERVIÇOS DE DADOS**, depois em **ARMAZENAMENTO** e, por fim, clique em **CRIAÇÃO RÁPIDA**.

  	![Quick Create](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzureStorageCreate.png)

1.  Em URL, digite um nome de subdomínio a ser usado no URI para a conta de armazenamento.  A entrada pode conter de 3 a 24 letras minúsculas e números. Esse valor torna-se o nome de host no URI que é usado para lidar com os recursos de Blob, Fila ou Tabela da assinatura em questão.

1.  Escolha uma Região/Grupo de Afinidade no qual localizar o armazenamento. Se você estiver usando o armazenamento de seu aplicativo do Azure, selecione a mesma região na qual você implantará seu aplicativo.

1.  Opcionalmente, você pode habilitar a replicação geográfica.  Com a replicação geográfica, o Armazenamento do Azure agora mantém seus dados duráveis em dois locais. Em ambos os locais, o Armazenamento do Azure mantém constantemente diversas réplicas saudáveis de seus dados.

1.  Clique em **CRIAR CONTA DE ARMAZENAMENTO**.

##<a name="configure-the-project"></a>Configurar o projeto

Nesta seção, configuraremos nosso aplicativo para usar a conta de armazenamento que acabamos de criar.  Veremos como obter configurações de conexão no portal do Azure.  Em seguida, executaremos o aplicativo localmente.

1.  No [Portal de gerenciamento do Azure][], clique na conta de armazenamento criada na seção anterior.

1.  Clique em **GERENCIAR CHAVES DE ACESSO**.

  	![Manage Access Keys Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  No Visual Studio, clique com o botão direito no nó do projeto no Gerenciador de Soluções e selecione **Propriedades**.  Clique na guia **Depurar**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageProjectDebugSettings.png)

1.  Configure os valores de variáveis de ambiente necessários pelo aplicativo em **Depurar comando do servidor**, **Ambiente**.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    Isso configurará as variáveis de ambiente quando você **Iniciar a depuração**.  Se você quiser que as variáveis sejam configuradas quando **Iniciar sem depurar**, configure os mesmos valores em **Executar comando do servidor** também.

    Como alternativa, é possível definir variáveis de ambiente usando o Painel de Controle do Windows.  Esta é uma opção melhor se desejar evitar armazenar credenciais no código-fonte/arquivo de projeto.  Observe que precisará reiniciar o Visual Studio para que os novos valores de ambiente estejam disponíveis ao aplicativo.

1.  O código que implementa o repositório Armazenamento de tabela do Azure está em **models/azuretablestorage.py**.  Consulte a [documentação] para obter mais informações sobre como usar o Serviço Tabela de Python.

1.  Execute o aplicativo com <kbd>F5</kbd>.  Pesquisas que são criadas com **Criar Pesquisas de Amostra** e os dados enviados por voto serão serializados no Armazenamento de tabela do Azure.

1.  Navegue até a página **Sobre** para verificar se o aplicativo está usando o repositório **Armazenamento de tabela do Azure**.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageAbout.png)

##<a name="explore-the-azure-table-storage"></a>Explorar o Armazenamento de tabela do Azure

É fácil visualizar e editar tabelas de armazenamento usando o Gerenciador de Servidores no Visual Studio.  Nesta seção, usaremos o Gerenciador de Servidores para visualizar o conteúdo das tabelas do aplicativo de pesquisas.

> [WACOM.NOTE] Isso requer que o Microsoft Azure Tools seja instalado, disponível como parte do [SDK do Azure para .NET][].

1.  Abra **Gerenciador de Servidores**.  Expanda **Azure**, **Armazenamento**, sua conta de armazenamento, em seguida, **Tabelas**.

  	![Server Explorer](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png)

1.  Clique duas vezes na tabela **pesquisas** ou **opções** para visualizar o conteúdo da tabela em uma janela do documento, bem como adicionar/remover/editar entidades.

  	![Table Query Results](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png)

##<a name="publish-to-an-azure-website"></a>Publicar para um website do Azure

O PTVS fornece uma forma fácil de implantar seu aplicativo Web para um website do Azure.

1.  No **Gerenciador de Soluções**, clique com botão direito no nó do projeto e selecione **Publicar**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  Clique no **Microsoft Azure Websites**.

1.  Clique em **Novo** para criar um novo site.

1.  Selecione um **Nome do site** e uma **Região** e clique em **Criar**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonCreateWebSite.png)

1.  Aceite todos os outros padrões e clique em **Publicar**.

1.  Seu navegador da Web será aberto automaticamente para o sie publicado.  Se navegar até a página sobre, você verá que ele usa o repositório **Na memória**, não o repositório **Armazenamento de tabela do Azure**.

    Isso é porque as variáveis de ambiente não estão configuradas no Website do Azure, portanto, ele usa os valores padrão especificados em **settings.py**.

##<a name="configure-the-azure-website"></a>Configurar Site do Azure

Nesta seção, configuraremos variáveis do ambiente para o site.

1.  No [Portal de gerenciamento do Azure][], clique no site criado na seção anterior.

1.  No menu principal, clique em **CONFIGURAR**.

  	![Top Menu](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteTopMenu.png)

1.  Role para baixo até a seção **Configurações do aplicativo** e configure os valores para **REPOSITORY\_NAME**, **STORAGE\_NAME** e **STORAGE\_KEY** conforme descrito na seção acima.

  	![App Settings](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. No menu inferior, clique em **SALVAR**, em seguida, em **REINICIAR** e finalmente em **NAVEGAR**.

  	![Bottom Menu](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Você deve ver o aplicativo funcionando conforme o esperado, usando o repositório **Armazenamento de tabela do Azure**.

    Parabéns!

  	![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureBrowser.png)

##<a name="next-steps"></a>Próximas etapas

Siga estes links para aprender mais sobre o Python Tools para Visual Studio, Bottle e Armazenamento de tabela do Azure.

- [Documentação do Python Tools para Visual Studio][]
  - [Projetos da Web][]
  - [Projetos de serviço de nuvem][]
  - [Depuração remota no Microsoft Azure][]
- [Documentação do Bottle][]
- [Armazenamento do Azure][]
- [SDK do Azure para Python][]
- [Como usar o serviço de armazenamento de tabela por meio do Python][]


<!--Link references-->
[Python Developer Center]: /pt-br/develop/python/
[Serviços de Nuvem do Azure]: ../cloud-services-python-ptvs/
[documentação]: ../storage-python-how-to-use-table-storage/
[Como usar o serviço de armazenamento de tabela por meio do Python]: ../storage-python-how-to-use-table-storage/

<!--External Link references-->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
[SDK do Azure para .NET]: http://azure.microsoft.com/pt-br/downloads/
[Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 para Visual Studio Samples VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Ferramentas do SDK do Azure para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Ferramentas do SDK do Azure para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentação do Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation
[Documentação do Bottle]: http://bottlepy.org/docs/dev/index.html
[Depuração remota no Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projetos da Web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projetos de serviço de nuvem]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Armazenamento do Azure]: http://azure.microsoft.com/pt-br/documentation/services/storage/
[SDK do Azure para Python]: https://github.com/Azure/azure-sdk-for-python

<!--HONumber=35_1-->
