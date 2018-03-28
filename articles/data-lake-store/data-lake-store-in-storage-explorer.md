---
title: Gerenciar recursos do Azure Data Lake Store no Gerenciador de Armazenamento do Azure
description: Saiba como acessar e gerenciar seus dados e recursos do Azure Data Lake Store no Gerenciador de Armazenamento do Azure
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: ''
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: ''
ms.service: Data Lake Store
ms.custom: Azure Data Lake Store
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: a02844c678c08d8aefbceb16d3908faeffd755fb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="manage-azure-data-lake-store-resources-by-using-storage-explorer"></a>Gerenciar recursos do Azure Data Lake Store usando o Gerenciador de Armazenamento

O [Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview) é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários. É possível obter acesso aos dados de qualquer lugar via HTTP ou HTTPS. O Data Lake Store no Gerenciador de Armazenamento do Azure permite acessar e gerenciar dados e recursos do Azure Data Lake Store juntamente com outras entidades do Azure, como blobs e filas. Agora você pode usar a mesma ferramenta para gerenciar suas diferentes entidades do Azure em um único local.

Outra vantagem é que não é preciso ter permissão de assinatura para gerenciar dados do Azure Data Lake Store. No Gerenciador de Armazenamento, é possível anexar o caminho do Data Lake Store para o nó **Local e Anexados**, desde que alguém conceda a permissão.

## <a name="prerequisites"></a>pré-requisitos
Para concluir as etapas neste artigo, você precisa dos seguintes pré-requisitos:

*   Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial).
*   Uma conta do Azure Data Lake Store. Para obter instruções sobre como criar uma, consulte [Introdução ao Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="install-storage-explorer"></a>Instalar o Gerenciador de Armazenamento

Instalar os bits mais recentes do Gerenciador de Armazenamento do Azure a partir da [página da Web do produto](https://azure.microsoft.com/features/storage-explorer/). A instalação tem suporte para as versões do Windows, Linux e Mac.

## <a name="connect-to-an-azure-subscription"></a>Conectar-se a uma assinatura do Azure

1. No Gerenciador de Armazenamento, selecione o ícone de plug-in à esquerda.
       
   ![Ícone de plug-in](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. Selecione **Adicionar uma Conta do Azure** e, em seguida, selecione **Entrar**.

   ![Caixa de diálogo “Conectar ao Armazenamento do Azure”](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. Na caixa de diálogo caixa **Entrar na sua conta**, insira suas credenciais do Azure.

    ![Caixa de diálogo para entrar no Azure](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. Selecione sua assinatura na lista e, em seguida, clique em **Aplicar**.

    ![Informações de assinatura e o botão “Aplicar”](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    O painel **EXPLORER** é atualizado e exibe as contas na assinatura selecionada.

    ![Lista de contas](./media/data-lake-store-in-storage-explorer/account-list.png)

Você conectou o Azure Data Lake Store à sua assinatura do Azure com êxito.

## <a name="connect-to-data-lake-store"></a>Conectar-se ao Repositório Data Lake
É possível acessar os recursos que não existem na sua assinatura se alguém lhe fornecer o URI para os recursos. Nesse caso, você pode se conectar ao Data Lake Store usando o URI depois de se conectar.
1. Abra o Explorer do Armazenamento.
2. No painel esquerdo, expanda **Local e Conectado**.
3. Clique com o botão direito do mouse em **Data Lake Store** e selecione **Conectar-se ao Data Lake Store**.

      ![“Conectar-se ao Data Lake Store” no menu de atalho](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Inserir o URI. A ferramenta procura o local da URL que você acabou de inserir.

      ![Caixa de diálogo “Conectar-se ao Data Lake Store” com a caixa de texto para digitar o URI](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Resultado de se conectar ao Data Lake Store](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-an-azure-data-lake-store-accounts-contents"></a>Exibir o conteúdo de uma conta do Azure Data Lake Store
Os recursos de uma conta do Azure Data Lake Store contêm arquivos e pastas.

As etapas a seguir ilustram como exibir o conteúdo de uma conta do Data Lake Store no Gerenciador de Armazenamento:

1. Abra o Explorer do Armazenamento.
2. No painel esquerdo, expanda a assinatura contendo a conta do Azure Data Lake Store que você deseja exibir.
3. Expanda o **Data Lake Store**.
4. Clique com o botão direito do mouse no nó de conta do Azure Data Lake Store que você deseja exibir, depois selecione **Abrir**. Também é possível clicar duas vezes na conta do Azure Data Lake Store para abri-la. 
   
   O painel principal exibe o conteúdo da conta do Data Lake Store.

   ![Painel principal com uma lista de pastas](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="manage-resources-in-azure-data-lake-store"></a>Gerenciar recursos no Azure Data Lake Store

Você pode gerenciar recursos do Azure Data Lake Store executando estas operações:
*   Navegar pelos recursos do Data Lake Store entre várias contas do Azure Data Lake.  
*   Usar uma cadeia de conexão para se conectar ao Data Lake Store e gerenciá-lo diretamente. 
*   Exibir recursos do Data Lake Store compartilhados por outras pessoas por meio de um ACL em **Local e Anexados**.
*   Executar operações CRUD de arquivo e pasta: suporte a pastas recursivas e vários arquivos selecionados. 
*   Arrastar, soltar e adicionar uma pasta para acessar rapidamente os locais recentes. Essa operação reflete a experiência do Explorador de Arquivos do desktop. 
*   Copiar e abrir, em um clique, o hiperlink do Azure Data Lake com o Gerenciador de Armazenamento. 
*   Exibir o Log de atividades no painel inferior direito para exibir o status da atividade.
*   Exibir estatísticas da pasta e propriedades de arquivos.

## <a name="manage-resources-in-azure-storage-explorer"></a>Gerenciar recursos no Gerenciador de Armazenamento do Azure
Depois de criar uma conta do Azure Data Lake Store, é possível:

* Carregar ou baixar arquivos e pastas e abrir recursos no computador local.
* Fixar no **Acesso rápido**, criar uma nova pasta, copiar uma URL e selecionar tudo.
* Copiar e colar, renomear, excluir, obter estatísticas de pasta e atualizar.

Os itens a seguir ilustram como gerenciar recursos em uma conta do Azure Data Lake Store. Siga as etapas da tarefa que deseja executar.

### <a name="upload-files"></a>Carregar arquivos

1. Na barra de ferramentas do painel principal, selecione **Carregar** e, depois, **Carregar Arquivos** no menu suspenso.

   ![Item de menu “Carregar Arquivos”](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

2. Na caixa de diálogo **Selecionar arquivos para carregar**, selecione os arquivos que deseja carregar.

   ![Caixa de diálogo para carregar arquivos](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

3. Selecione **Abrir** para começar a carregar.

### <a name="upload-a-folder"></a>Carregar uma pasta

1. Na barra de ferramentas do painel principal, selecione **Carregar** e, depois, **Carregar Pasta** no menu suspenso.

   ![Item de menu “Carregar Pasta”](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     
2. Na caixa de diálogo **Selecionar pastas para carregar**, selecione uma pasta que deseja carregar. Em seguida, clique em **Selecionar Pasta**.

   ![Caixa de diálogo para carregar pastas](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

   O upload é iniciado.

   ![Caixa de diálogo com o carregamento em andamento](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

> [!NOTE] 
> É possível arrastar as pastas e arquivos diretamente em um computador local para iniciar o upload. 
       
### <a name="download-folders-or-files-to-your-local-computer"></a>Baixar pastas ou arquivos para o computador local

1. Selecione as pastas ou os arquivos que deseja baixar.
2. Na barra de ferramentas do painel principal, escolha **Baixar**.
3. Na caixa diálogo **Selecionar uma pasta na qual salvar os arquivos baixados**, especifique o local e o nome.
4. Selecione **Salvar**.

### <a name="open-a-folder-or-file-from-your-local-computer"></a>Abrir uma pasta ou um arquivo do computador local

1. Selecione a pasta ou o arquivo que deseja abrir.
2. Na barra de ferramentas do painel principal, escolha **Abrir**. Ou clique com o botão direito do mouse na pasta ou no arquivo selecionado e selecione **Abrir** no menu de atalho.

O arquivo é baixado e aberto por meio do aplicativo associado ao tipo de arquivo subjacente. Ou a pasta é aberta no painel principal.

![Arquivo aberto](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

### <a name="copy-folders-or-files-to-the-clipboard"></a>Copiar pastas ou arquivos para a área de transferência

1. Selecione as pastas ou os arquivos que deseja copiar.
2. Na barra de ferramentas do painel principal, escolha **Copiar**. Ou clique com o botão direito do mouse nas pastas ou nos arquivos selecionados e selecione **Copiar** no menu de atalho.
3. No painel esquerdo, navegue até outra conta do Data Lake Store e clique duas vezes nela para exibi-la no painel principal.
4. Na barra de ferramentas do painel principal, escolha **Colar** para criar uma cópia. Ou selecione **Colar** no menu de atalho do destino.

![Seleções para copiar uma pasta](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

> [!NOTE] 
> Não há suporte para operações de copiar/colar em todos os tipos de armazenamento. É possível copiar pastas ou arquivos do Data Lake Store e colá-las em outra conta do Data Lake Store. Mas *não é possível* copiar pastas ou arquivos do Data Lake Store e colá-los em um armazenamento de blobs do Azure e vice-versa.
> 
> A operação copiar/colar funciona baixando pastas ou arquivos no computador local e, em seguida, carregando-os para o destino. A ferramenta *não* executa a ação no back-end. A operação copiar/colar em arquivos grandes é um processo lento. A otimização da movimentação/cópia de arquivos de alto desempenho está a caminho.

### <a name="delete-folders-or-files"></a>Excluir pastas ou arquivos

1. Selecione as pastas ou os arquivos que deseja excluir.
2. Na barra de ferramentas do painel principal, escolha **Excluir**. Ou clique com o botão direito do mouse nas pastas ou nos arquivos selecionados e selecione **Excluir** no menu de atalho.
3. Escolha **Sim** na caixa de diálogo de confirmação.

![Seleções para excluir uma pasta](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

### <a name="pin-to-quick-access"></a>Fixar no acesso rápido

1. Selecione a pasta que deseja fixar.
2. Na barra de ferramentas do painel principal, selecione **Fixar no acesso rápido**.

   No painel esquerdo, a pasta selecionada é adicionada ao nó **Acesso Rápido**.

   ![Seleções de fixação de uma pasta de “Acesso Rápido”](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

Depois de fixar uma pasta no nó **Acesso Rápido**, é possível acessar facilmente os recursos.

### <a name="use-deep-links"></a>Usar links profundos
Se tiver uma URL, você pode inserir apenas ela no caminho de endereço do Explorador de Arquivos ou de um navegador. Em seguida, o Storage Explorer.exe é executado automaticamente para ir até o local da URL.

![Link profundo no Explorador de Arquivos](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>Próximas etapas
* Exibir as [notas de versão e os vídeos mais recentes do Gerenciador de Armazenamento](http://www.storageexplorer.com).
* Saiba como [gerenciar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer).
* [Introdução ao Gerenciador de Armazenamento](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer).
* [Introdução ao Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview).
* Assista a um [vídeo do YouTube sobre como usar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
