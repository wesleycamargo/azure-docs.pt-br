<properties linkid="web-sites-python-ptvs-bottle-table-storage" title="Bottle and Azure Table Storage on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Bottle e armazenamento de tabela do Azure no Azure com ferramentas 2.1 do Python para Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Bottle application that stores data in Azure Table Storage and can be deployed to a web site." metaKeywords="" services="web-sites" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Bottle e armazenamento de tabela do Azure no Azure com ferramentas 2.1 do Python para Visual Studio 

Neste tutorial, vamos criar um aplicativo de pesquisa simples usando um dos modelos de exemplo PTVS.

O aplicativo de pesquisa define um resumo para seu repositório, assim você pode alternar facilmente entre diferentes tipos de repositórios (na memória, armazenamento de tabela do Azure, MongoDB).

Iremos aprender como criar uma conta de armazenamento do Azure, como configurar o aplicativo para usar o armazenamento de tabela do Azure e como publicar o aplicativo em um site do Azure.

Consulte o [Python Developer Center][] para obter mais artigos que abordam o desenvolvimento de sites do Azure com o PTVS usando estruturas web Bottle, Flask e Django, com serviços MongoDB, armazenamento de tabela do Azure, MySQL e banco de dados SQL.  Embora este artigo se concentre em sites do Azure, as etapas são semelhantes ao desenvolvimento de [serviços de nuvem do Azure][].

+ [Pré-requisitos](#prerequisites)
+ [Criar o projeto](#create-the-project)
+ [Criar uma conta de armazenamento do Azure](#create-an-azure-storage-account)
+ [Configurar o projeto](#configure-the-project)
+ [Explore o armazenamento de tabela do Azure](#explore-the-azure-table-storage)
+ [Publicar em um Site do Azure](#publish-to-an-azure-website)
+ [Configure o site do Azure](#configure-the-azure-website)
+ [Próximas etapas](#next-steps)

##<a name="prerequisites"></a>Pré-requisitos

 - Visual Studio 2012 ou 2013
 - [Python Tools 2,1 para Visual Studio][]
 - [Ferramentas Python 2.1 para amostras VSIX do Visual Studio][]
 - [Azure SDK Tools para VS 2013][] ou [Azure SDK Tools para VS 2012][]
 - [Python 2.7 de 32 bits][] ou [Python 3.4 de 32 bits][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Criar o projeto

Nesta seção, vamos criar um projeto do Visual Studio usando um modelo de exemplo.  Vamos criar um ambiente virtual e instalar os pacotes necessários.  Em seguida, vamos executar o aplicativo localmente usando o repositório na memória padrão.

1.  No Visual Studio, selecione **Arquivo**, **Novo projeto**.

1.  Os modelos de projeto do VSIX de exemplos PTVS estão disponíveis em **Python**, **Exemplos**.  Selecione o **Projeto Web Bottle de pesquisas** e clique em OK para criar o projeto.

  	![New Project Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleNewProject.png)

1.  Você precisará instalar pacotes externos.  Selecione **Instalar em um ambiente virtual**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleExternalPackages.png)

1.  Selecione **Python 2.7** ou **Python 3.4** como o interpretador de base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAddVirtualEnv.png)

1.  Confirme se o aplicativo funciona pressionando <kbd>F5</kbd>.  Por padrão, o aplicativo usa um repositório na memória que não exige nenhuma configuração.  Todos os dados são perdidos quando o servidor Web é interrompido.

1.  Clique em **Criar pesquisas de exemplo**, clique em uma pesquisa e vote.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleInMemoryBrowser.png)

##<a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

Para usar as operações de armazenamento, você precisa de uma conta de armazenamento do Azure. Você pode criar uma conta de armazenamento seguindo essas etapas.

1.  Faça logon no [Portal de Gerenciamento do Azure][].

1.  Na parte inferior do painel de navegação, clique em **NOVO**.

  	![New Button](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzurePlusNew.png)

1.  Clique em **SERVIÇOS DE DADOS**, em **ARMAZENAMENTO** e,em seguida, clique em **CRIAÇÃO RÁPIDA**.

  	![Quick Create](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzureStorageCreate.png)

1.    No URL, digite um nome de subdomínio a ser usado no URI para a    conta de armazenamento.  A entrada pode conter de 3 a 24 letras minúsculas e números. Esse valor se torna o nome do host no URI que é usado para lidar com os recursos Blob, Fila ou Tabela da assinatura.

1.    Escolha um Grupo de Região/Afinidade no qual deseja localizar o    armazenamento. Se você usar o armazenamento de seu aplicativo do Azure, selecione a mesma região onde você implantará seu aplicativo.

1.  Opcionalmente, você pode habilitar a replicação geográfica.  Com a replicação geográfica, o armazenamento do Azure agora mantém seus dados duráveis em dois locais. Em ambos os locais, o armazenamento do Azure mantém constantemente várias réplicas íntegras de seus dados.

1.  Clique em **CRIAR CONTA DE ARMAZENAMENTO**.

##<a name="configure-the-project"></a>Configurar o projeto

Nesta seção, configuraremos nosso aplicativo para usar a conta de armazenamento que acabamos de criar.  Veremos como obter as configurações de conexão do portal do Azure.  Em seguida, vamos executar o aplicativo localmente.

1.  No [Portal de Gerenciamento do Azure][], clique na conta de armazenamento criada na seção anterior.

1.  Clique em **GERENCIAR CHAVES DE ACESSO**.

  	![Manage Access Keys Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  No Visual Studio, clique com o botão direito do mouse no nó do projeto no Gerenciador de Soluções e selecione **Propriedades**.  Clique na guia **Depuração**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageProjectDebugSettings.png)

1.  Defina os valores das variáveis de ambiente exigidas pelo aplicativo no **Comando de depuração do servidor**, **Ambiente**.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    Isso definirá as variáveis de ambiente quando você **iniciar a depuração**.  Se você quiser que as variáveis sejam definidas quando você **Iniciar sem depuração**, defina os mesmos valores também em **Executar comando de servidor**.

    Como alternativa, você pode definir variáveis de ambiente usando o painel de controle do Windows.  Essa é uma opção melhor se você quiser evitar armazenar credenciais no código-fonte/arquivo de projeto.  Observe que você precisará reiniciar o Visual Studio para os novos valores de ambiente para ficar disponível para o aplicativo.

1.  O código que implementa o repositório de armazenamento de tabela do Azure está em **models/azuretablestorage.py**.  Consulte a [Documentação] para obter mais informações sobre como usar o serviço Tabela do Python.

1.  Execute o aplicativo com <kbd>F5</kbd>.  As pesquisas que são criadas com **Criar pesquisas de exemplo** e os dados enviados por votação poderão ser serializados no armazenamento de tabela do Azure.

1.  Navegue até a página **Sobre** para verificar se o aplicativo está usando o repositório **Armazenamento de tabela do Azure**.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageAbout.png)

##<a name="explore-the-azure-table-storage"></a>Explore o armazenamento de tabela do Azure

É fácil exibir e editar tabelas de armazenamento usando o Gerenciador de Servidores no Visual Studio.  Nesta seção, usaremos o Gerenciador de Servidores para exibir o conteúdo das tabelas de aplicativo de pesquisas.

> [WACOM.NOTE] Isso requer que as ferramentas do Microsoft Azure Tools estejam instaladas. Elas estão disponíveis como parte do [SDK do Azure para .NET][].

1.  Abra o **Gerenciador de Servidores**.  Expanda o **Azure**, **Armazenamento**, sua conta de armazenamento e **Tabelas**.

  	![Server Explorer](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png)

1.  Clique duas vezes na tabela de **pesquisas** ou **opções** para exibir o conteúdo da tabela em uma janela do documento, bem como adicionar/remover/editar entidades.

  	![Table Query Results](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png)

##<a name="publish-to-an-azure-website"></a>Publicar em um Site do Azure

O PTVS fornece uma maneira fácil de implantar seu aplicativo Web em um site do Azure.

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto de nó e selecione **Publicar**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  Clique em **Sites do Microsoft Azure**.

1.  Clique em **Novo** para criar um novo site.

1.  Selecione um **Nome de site** e uma **Região** e clique em **Criar**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonCreateWebSite.png)

1.  Aceite todos os outros padrões e clique em **Publicar**.

1.  Seu navegador da Web será aberto automaticamente no site publicado.  Se navegar até a página Sobre, você verá que ele usa o repositório **Na memória**, não o repositório de **Armazenamento de tabela do Azure**.

    Isso ocorre porque as variáveis de ambiente não são definidas no site do Azure, portanto, ele usa os valores padrão especificados em **settings.py**.

##<a name="configure-the-azure-website"></a>Configure o site do Azure

Nesta seção, configuraremos variáveis de ambiente para o site.

1.  No [Portal de Gerenciamento do Azure][], clique no site criado na seção anterior.

1.  No menu superior, clique em **CONFIGURAR**.

  	![Top Menu](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteTopMenu.png)

1.  Role para baixo até a seção de **configurações do aplicativo** e defina os valores para **REPOSITORY\_NAME**, **STORAGE\_NAME** e **STORAGE\_KEY**, conforme descrito na seção acima.

  	![App Settings](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. No menu inferior, clique em **SALVAR**, **REINICIAR** e em **PROCURAR**.

  	![Bottom Menu](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Você deve ver o aplicativo funcionando conforme o esperado, utilizando o repositório de **Armazenamento de tabela do Azure**.

    Parabéns!

  	![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureBrowser.png)

##<a name="next-steps"></a>Próximas etapas

Siga estes links para saber mais sobre as ferramentas Python para Visual Studio, Bottle e armazenamento de tabela do Azure.

- [Ferramentas Python para documentação do Visual Studio][]
  - [Projetos da Web][]
  - [Projetos de serviços de nuvem][]
  - [Depuração remota no Microsoft Azure][]
- [Documentação do Bottle][]
- [Armazenamento do Azure][]
- [SDK do Azure para Python][]
- [Como usar o Serviço de Armazenamento de Tabela no Python][]


<!--Link references-->
[Centro de desenvolvedores do Python]: /pt-br/develop/python/
[Serviços de nuvem do Azure]: ../cloud-services-python-ptvs/
[documentação]: ../storage-python-how-to-use-table-storage/
[Como usar o Serviço de Armazenamento de Tabela no Python]: ../storage-python-how-to-use-table-storage/

<!--External Link references-->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
[SDK do Azure para .NET]: http://azure.microsoft.com/pt-br/downloads/
[Python Tools 2,1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Ferramentas Python 2.1 para amostras VSIX do Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Ferramentas do SDK do Azure para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Ferramentas do SDK do Azure para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3,4 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517191
[Ferramentas Python para documentação do Visual Studio]: http://pytools.codeplex.com/documentation
[Documentação do Bottle]: http://bottlepy.org/docs/dev/index.html
[Depuração remota no Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projetos da Web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projetos de serviços de nuvem]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Armazenamento do Azure]: http://azure.microsoft.com/pt-br/documentation/services/storage/
[SDK do Azure para Python]: https://github.com/Azure/azure-sdk-for-python
