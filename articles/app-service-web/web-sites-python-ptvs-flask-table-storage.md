---
title: Flask e Tabela de Armazenamento do Azure com Ferramentas Python 2.2 para Visual Studio
description: "Aprenda a usar o Python Tools para Visual Studio para criar um aplicativo Flask que armazena dados Armazenamento de Dados do Azure e o implanta em Aplicativos Web do Serviço de Aplicativo do Azure."
services: app-service\web
tags: python
documentationcenter: python
author: huguesv
manager: erikre
editor: 
ms.assetid: d8e70a29-aca1-4010-95f5-cfe769e3be06
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 07/07/2016
ms.author: huvalo
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 2e1bc8eebd0b67b965cc70ac4b5dfe03c4720ddf
ms.contentlocale: pt-br
ms.lasthandoff: 08/21/2017

---
# <a name="flask-and-azure-table-storage-on-azure-with-python-tools-22-for-visual-studio"></a>Flask e Tabela de Armazenamento do Azure com Ferramentas Python 2.2 para Visual Studio
Neste tutorial, usaremos o [Python Tools para Visual Studio] para criar um aplicativo Web de votação simples, usando um dos modelos de exemplo de PTVS. Este tutorial também está disponível como um [vídeo](https://www.youtube.com/watch?v=qUtZWtPwbTk).

O aplicativo Web de votação define uma abstração para seu repositório, para que você possa alternar facilmente entre diferentes tipos de repositórios (In-Memory, Azure Table Storage, MongoDB).

Aprenderemos como criar uma conta de Armazenamento do Azure, como configurar o aplicativo Web para usar o Armazenamento de Tabela do Azure e como publicar o aplicativo Web nos [Aplicativos Web do Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Confira o [Python Developer Center] para obter mais artigos que abrangem o desenvolvimento de Aplicativos Web do Serviço de Aplicativo do Azure com PTVS usando estruturas da Web Bottle, Flask e Django, com serviços MongoDB, Azure Table Storage, MySQL e banco de dados SQL. Embora este artigo se concentre no Serviço de Aplicativo, as etapas são semelhantes ao desenvolvimento de [Serviços de Nuvem do Azure].

## <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2015
* [Ferramentas Python 2.2 para Visual Studio]
* [VSIX de amostra de Ferramentas Python 2.2 para Visual Studio]
* [Ferramentas do SDK do Azure para VS 2015]
* [Python 2.7 de 32 bits] ou [Python 3.4 de 32 bits]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## <a name="create-the-project"></a>Criar o projeto
Nesta seção, criaremos um projeto Visual Studio usando um modelo de amostra. Criaremos um ambiente virtual e instalaremos pacotes necessários. Em seguida, executaremos o aplicativo localmente usando o repositório da memória padrão.

1. No Visual Studio, selecione **Arquivo**, **Novo Projeto**.
2. Os modelos de projeto das [VSIX de amostra de Ferramentas Python 2.2 para Visual Studio] estão disponíveis em **Python**, **Amostras**. Selecione **Projeto Web Flask de Votações** e clique em OK para criar o projeto.
   
     ![Caixa de diálogo Novo Projeto](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)
3. Você será solicitado a instalar pacotes externos. Selecione **Instalar em um ambiente virtual**.
   
     ![Caixa de diálogo Pacotes Externos](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)
4. Selecione **Python 2.7** ou **Python 3.4** como o interpretador base.
   
     ![Caixa de diálogo Adicionar Ambiente Virtual](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)
5. Confirme se o aplicativo funciona pressionando `F5`. Por padrão, o aplicativo usa um repositório da memória que não requer configuração. Todos os dados são perdidos quando o servidor Web é interrompido.
6. Clique em **Criar Votações de Exemplo**e, em seguida, clique em uma votação e vote.
   
     ![Navegador da Web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure
Para usar as operações de armazenamento, você precisa de uma conta de armazenamento do Azure. Você pode criar uma conta de armazenamento seguindo essas etapas.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Clique no ícone **Novo** no canto superior esquerdo do Portal. Em seguida, clique em **Dados + Armazenamento** > **Conta de Armazenamento**. Clique em **Criar**, dê um nome exclusivo à conta de armazenamento e crie um novo [grupo de recursos](../azure-resource-manager/resource-group-overview.md) para ela.
   
      ![Criação Rápida](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)
   
    Quando a conta de armazenamento for criada, o botão **Notificações** piscará **ÊXITO** em verde e a folha da conta de armazenamento será aberta para mostrar que pertence ao novo grupo de recursos criado.
3. Clique na parte **Chaves de Acesso** na folha da conta de armazenamento. Anote o nome da conta e a key1.
   
      ![simétricas](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageKeys.png)
   
    Precisaremos dessas informações para configurar seu projeto na próxima seção.

## <a name="configure-the-project"></a>Configurar o projeto
Nesta seção, configuraremos nosso aplicativo para usar a conta de armazenamento que acabamos de criar. Veremos como obter configurações de conexão do Portal do Azure. Em seguida, executaremos o aplicativo localmente.

1. No Visual Studio, clique com o botão direito do mouse no nó do projeto no Gerenciador de Soluções e selecione **Propriedades**. Clique na guia **Depurar** .
   
     ![Configurações de depuração do projeto](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)
2. Defina os valores de variáveis de ambiente necessários para o aplicativo em **Depurar Comando do Servidor**, **Ambiente**.
   
       REPOSITORY_NAME=azuretablestorage
       STORAGE_NAME=<storage account name>
       STORAGE_KEY=<primary access key>
   
   Isso definirá as variáveis de ambiente quando você **Iniciar a Depuração**. Se quiser que as variáveis sejam definidas quando você **Iniciar sem Depurar**, configure também os mesmos valores em **Executar Comando do Servidor**.
   
   Como alternativa, é possível definir variáveis de ambiente usando o Painel de Controle do Windows. Esta é uma opção melhor se desejar evitar armazenar credenciais no código-fonte/arquivo de projeto. Observe que você precisará reiniciar o Visual Studio para que os novos valores de ambiente estejam disponíveis ao aplicativo.
3. O código que implementa o repositório do Armazenamento de Tabela do Azure está em **models/azuretablestorage.py**. Consulte a [documentação] para obter mais informações sobre como usar o Serviço de Tabela do Python.
4. Execute o aplicativo com `F5`. As votações que são criadas com **Criar Votações de Exemplo** e os dados enviados por voto serão serializados no Armazenamento de Tabela do Azure.
   
   > [!NOTE]
   > O Ambiente Virtual do Python 2.7 pode causar uma interrupção de exceção no Visual Studio.  Pressione `F5` para continuar carregando o projeto Web.
   > 
   > 
5. Navegue até a página **Sobre** para verificar se o aplicativo está usando o repositório do **Armazenamento de Tabelas do Azure**.
   
     ![Navegador da Web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

## <a name="explore-the-azure-table-storage"></a>Explorar o Armazenamento de tabela do Azure
É fácil exibir e editar tabelas de armazenamento usando o Cloud Explorer no Visual Studio. Nesta seção, usaremos o Gerenciador de Servidores para visualizar o conteúdo das tabelas do aplicativo de pesquisas.

> [!NOTE]
> Isso requer que o Microsoft Azure Tools seja instalado, disponível como parte do [SDK do Azure para .NET].
> 
> 

1. Abra o **Cloud Explorer**. Expanda **Contas de Armazenamento**, sua conta de armazenamento e, em seguida, **Tabelas**.
   
     ![Cloud Explorer](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png)
2. Clique duas vezes na tabela **votações** ou **opções** para exibir o conteúdo da tabela em uma janela do documento, bem como adicionar/remover/editar entidades.
   
     ![Resultados da consulta de tabela](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Publicar aplicativo Web para Serviço de Aplicativo do Azure
O SDK .NET do Azure fornece uma forma fácil de implantar seu aplicativo Web no Serviço de Aplicativo do Azure.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nó do projeto e selecione **Publicar**.
   
     ![Caixa de diálogo Web Publicar](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png)
2. Clique em **Aplicativos Web do Microsoft Azure**.
3. Clique em **Novo** para criar um novo aplicativo Web.
4. Preencha os campos a seguir e clique em **Criar**.
   
   * **Nome do aplicativo Web**
   * **Plano do Serviço de Aplicativo**
   * **Grupo de recursos**
   * **Região**
   * Deixe **Servidor de banco de dados** definido como **Nenhum banco de dados**
5. Aceite todos os outros padrões e clique em **Publicar**.
6. Seu navegador da Web será aberto automaticamente para o aplicativo Web publicado. Se navegar até a página Sobre, você verá que ela usa o repositório **Na memória**, não o repositório do **Armazenamento de Tabelas do Azure**.
   
   Isso ocorre porque as variáveis de ambiente não estão configuradas na instância Aplicativos Web no Serviço de Aplicativo do Azure. Portanto, ele usa os valores padrão especificados em **settings.py**.

## <a name="configure-the-web-apps-instance"></a>Configurar a instância Aplicativos Web
Nesta seção, configuraremos variáveis do ambiente para a instância de Aplicativos Web.

1. No [Portal do Azure](https://portal.azure.com), abra a folha do aplicativo Web clicando em **Procurar** > **Serviços de Aplicativos** > nome do aplicativo Web.
2. Na folha do seu aplicativo Web, clique em **Todas as configurações** depois clique em **Configurações do Aplicativo**.
3. Role para baixo até a seção **Configurações do aplicativo** e defina os valores para **REPOSITORY\_NAME**, **STORAGE\_NAME** e **STORAGE\_KEY**, conforme descrito na seção **Configurar o projeto** acima.
   
     ![Configurações do aplicativo](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)
4. Clique em **Save**. Depois de receber as notificações indicando que as alterações foram aplicadas, clique em **Procurar** na folha principal do aplicativo Web.
5. Você deve ver o aplicativo funcionando conforme o esperado, usando o repositório do **Armazenamento de Tabela do Azure** .
   
   Parabéns!
   
     ![Navegador da Web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

## <a name="next-steps"></a>Próximas etapas
Siga estes links para saber mais sobre as ferramentas Python para Visual Studio, Flask e armazenamento de tabela do Azure.

* [Ferramentas Python para documentação do Visual Studio]
  * [Projetos da Web]
  * [Projetos do serviço de nuvem]
  * [Depuração remota no Microsoft Azure]
* [Documentação do Flask]
* [Armazenamento do Azure]
* [SDK do Azure para Python]
* [Como usar o serviço de armazenamento de tabela por meio do Python]

## <a name="whats-changed"></a>O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!--Link references-->
[Python Developer Center]: /develop/python/
[Serviços de Nuvem do Azure]: ../cloud-services/cloud-services-python-ptvs.md
[documentação]:../cosmos-db/table-storage-how-to-use-python.md
[Como usar o serviço de armazenamento de tabela por meio do Python]:../cosmos-db/table-storage-how-to-use-python.md

<!--External Link references-->
[Azure Portal]: https://portal.azure.com
[SDK do Azure para .NET]: http://azure.microsoft.com/downloads/
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Ferramentas Python 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[VSIX de amostra de Ferramentas Python 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Ferramentas do SDK do Azure para VS 2015]: http://go.microsoft.com/fwlink/?linkid=518003
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Ferramentas Python para documentação do Visual Studio]: http://aka.ms/ptvsdocs
[Documentação do Flask]: http://flask.pocoo.org/
[Depuração remota no Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Projetos da Web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Projetos do serviço de nuvem]: http://go.microsoft.com/fwlink/?LinkId=624028
[Armazenamento do Azure]: http://azure.microsoft.com/documentation/services/storage/
[SDK do Azure para Python]: https://github.com/Azure/azure-sdk-for-python

