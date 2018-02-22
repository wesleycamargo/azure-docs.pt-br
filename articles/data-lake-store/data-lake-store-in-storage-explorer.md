---
title: Gerenciar recursos do Azure Data Lake Store no Gerenciador de Armazenamento do Azure
description: "Permite aos usuários acessar e gerenciar seus dados e recursos do ADLS no Gerenciador de Armazenamento do Azure"
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: 
ms.service: Data Lake Store
ms.custom: Azure Data Lake Store
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 4c69acebceac6719cf3f12ff0a93879f1b5a8943
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="manage-azure-data-lake-store-resources-with-storage-explorer-preview"></a>Gerenciar os recursos do Azure Data Lake Store com o Gerenciador de Armazenamento (versão prévia)

O [ADLS (Azure Data Lake Store)](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview) é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários. O usuário pode obter acesso aos dados de qualquer lugar via HTTP ou HTTPS. O ADLS no Gerenciador de Armazenamento do Azure permite aos usuários acessar e gerenciar dados e recursos do ADLS juntamente com outras entidades do Azure, como blobs e filas. Agora, seus usuários podem usar a mesma ferramenta para gerenciar suas diferentes entidades do Azure em um único local.

Outra vantagem é que os usuários não precisam ter permissão de assinatura para gerenciar dados do ADLS. No Gerenciador de Armazenamento, os usuários poderão anexar o caminho do ADLS ao nó "Local e Conectado" se outros concederem a permissão.

## <a name="prerequisites"></a>pré-requisitos
Para concluir as etapas neste artigo, você precisa dos seguintes pré-requisitos:

*   Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial).
*   Uma conta do Azure Data Lake Store. Para obter instruções sobre como criar uma, confira [Introdução ao Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal)

## <a name="installation"></a>Instalação

Instale os bits mais recentes do Gerenciador de Armazenamento do Azure aqui: [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/). Agora há suporte para versões do Windows, do Linux e do MAC.

## <a name="connect-to-an-azure-subscription"></a>Conectar-se a uma assinatura do Azure

1. Depois de instalar o **Gerenciador de Armazenamento do Azure**, clique no ícone de **plug-in** à esquerda, conforme mostrado na imagem a seguir.
       
   ![Ícone do plug-in](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. Selecione **Adicionar uma Conta do Azure** e, em seguida, clique em **Entrar**.

   ![Conectar-se a uma assinatura do Azure](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. Na caixa de diálogo **Entrar no Azure**, selecione **Entrar** e insira suas credenciais do Azure.

    ![Entrar](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. Selecione sua assinatura na lista e, em seguida, clique em **Aplicar**.

    ![Aplicar](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    O painel do Explorer atualiza e exibe as contas na assinatura selecionada.

    ![Lista de contas](./media/data-lake-store-in-storage-explorer/account-list.png)

    Você conectou sua **Conta do Azure Data Lake Store** à sua assinatura do Azure com êxito.

## <a name="connect-to-data-lake-store"></a>Conectar-se ao Repositório Data Lake
Se você deseja obter acesso aos recursos que não existem na sua assinatura. Mas outras pessoas concedem a você para obter o URI para os recursos. Nesse caso, você pode se conectar ao Data Lake Store usando o URI depois de se conectar. Consulte as etapas a seguir.
1. Abra o Gerenciador de Armazenamento (Preview).
2. No painel esquerdo, expanda **Local e Conectado**.
3. Clique com o botão direito do mouse em **Data Lake Store**e, no menu de contexto, selecione **Conectar-se ao Data Lake Store...**.

      ![Menu de contexto Conectar-se ao Data Lake Store](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Digite o URI e a ferramenta navegará até o local da URL inserida.

      ![Diálogo Conectar-se ao Data Lake Store](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Resultado Conectar-se ao Data Lake Store](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-an-azure-data-lake-store-accounts-contents"></a>Exibir o conteúdo de uma conta do Azure Data Lake Store
Os recursos de uma conta do Azure Data Lake Store contêm arquivos e pastas.

As etapas a seguir ilustram como exibir o conteúdo de uma conta do ADLS com o Gerenciador de Armazenamento (Versão Prévia):

1. Abra o Gerenciador de Armazenamento (Preview).
2. No painel esquerdo, expanda a assinatura contendo a conta do Azure Data Lake Store que você deseja exibir.
3. Expanda o **Data Lake Store**.
4. Clique com o botão direito do mouse no nó de conta do Azure Data Lake Store que você deseja exibir e, no menu de contexto, selecione **Abrir**. Você também pode clicar duas vezes na conta do ADLS para abri-la. 
5. O painel principal exibe o conteúdo da conta do ADLS.

     ![painel principal](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="azure-data-lake-store-resources-management"></a>Gerenciamento de recursos do Azure Data Lake Store

Você pode gerenciar recursos do Azure Data Lake Store executando estas operações:
*   Navegar por recursos do ADLS entre várias contas do ADL.  
*   Usar a cadeia de conexão para conectar e gerenciar o ADLS diretamente. 
*   Exibir recursos do ADLS compartilhados por outras pessoas por meio de ACL em Local e Anexados.
*   Executar operações CRUD de arquivo/pasta: suporte a pastas recursivas e vários arquivos selecionados. 
*   Arrastar, soltar e adicionar pasta para acesso rápido e locais recentes, que refletem a experiência de explorador de arquivos da área de trabalho. 
*   Copiar e abrir o hiperlink do ADL com o Gerenciador de Armazenamento em um clique. 
*   Exibir o log de atividades no painel inferior direito para exibir o status da atividade.
*   Exibir propriedades de arquivos e estatísticas da pasta.

## <a name="manage-resources-in-azure-storage-explorer"></a>Gerenciar recursos no Gerenciador de Armazenamento do Azure
Depois de criar uma conta do Azure Data Lake Store, você pode carregar pastas e arquivos, baixar e abrir recursos no computador local. E há recursos de fixar no acesso rápido, nova pasta, copiar URL, Selecionar tudo. Além disso, é possível copiar, colar, renomear, excluir, exibir estatísticas de pasta, atualizar.

Os itens a seguir ilustram como gerenciar recursos em uma conta do Azure Data Lake Store. Execute estas etapas, dependendo da tarefa que deseja executar:
   * **Carregar arquivos**

     1. Na barra de ferramentas do painel principal, escolha **Carregar** e **Carregar Arquivos...** no menu suspenso.

        ![Menu Carregar arquivos](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

     2. No diálogo **Selecionar arquivos para Carregar**, selecione os arquivos que você deseja carregar.

        ![Diálogo Carregar pasta](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

     3. Selecione **Abrir** para começar a carregar.
   * **Carregar uma pasta**

     1. Na barra de ferramentas do painel principal, escolha **Carregar** e **Carregar Pasta...** no menu suspenso.

        ![Menu Carregar pasta](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     2. No diálogo **Selecione a pasta para Carregar**, selecione uma pasta que você deseja carregar.

        ![Diálogo Carregar pasta](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

     3. Selecione **Selecionar pasta** para começar a carregar as pastas.

        ![Diálogo Carregar pasta](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

      > [!NOTE] 
          > Você pode arrastar diretamente as pastas e arquivos no computador local para implementar o upload. 
       
   * **Baixar arquivos ou pastas no computador local**

     1. Selecione as pastas ou arquivos que você deseja baixar.
     2. Na barra de ferramentas do painel principal, escolha **Baixar**.
     3. No diálogo **Selecionar uma pasta para salvar os arquivos baixados**, especifique o local onde você quer baixar pastas ou arquivos. E especifique o nome que você deseja atribuir a ela.
     4. Selecione **Salvar**.
   * **Abri arquivo ou pasta do computador local**

     1. Selecione a pasta ou arquivo que deseja abrir.
     2. Na barra de ferramentas do painel principal, selecione **Abrir** ou clique com o botão direito do mouse na pasta ou no arquivo selecionado e, no menu de contexto, clique em **Abrir**.
     3. O arquivo é baixado e aberto usando o aplicativo associado ao tipo de arquivo subjacente. Ou a pasta é aberta no painel principal.

        ![abrir arquivo](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

   * **Copiar pastas ou arquivos para a área de transferência**

     1. Selecione as pastas ou arquivos que você deseja copiar.
     2. Na barra de ferramentas do painel principal, selecione **Copiar** ou clique com o botão direito do mouse nas pastas ou nos arquivos selecionados e, no menu de contexto, clique em **Copiar**.
     3. No painel esquerdo, navegue até outra conta do ADLS e clique duas vezes nele para exibi-lo no painel principal.
     4. Na barra de ferramentas do painel principal, escolha **Colar** para criar uma cópia. Ou clique no menu de contexto **Colar** no destino.

        ![copiar colar pasta ou arquivo](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

      > [!NOTE] 
          > + O recurso de copiar e colar entre tipos de armazenamento **não** tem suporte. Você pode copiar arquivos ou pastas do ADLS e colar para outra conta do ADLS. Mas **não é possível** copiar arquivos ou pastas do ADLS e colar em um armazenamento de blobs ou vice-versa. 
          > + O recurso copiar e colar funciona baixando pastas ou arquivos no local e, em seguida, carregando para o destino. A ferramenta **não** executa a ação no back-end. Copiar e colar em arquivos grandes é um processo lento. A otimização da movimentação de cópia de arquivos de alto desempenho está em andamento.
   * **Excluir arquivos ou pastas**

     1. Selecione pastas ou arquivos que você deseja excluir.
     2. Na barra de ferramentas do painel principal, selecione **Excluir** ou clique com o botão direito do mouse nas pastas ou nos arquivos selecionados e, no menu de contexto, clique em **Excluir**.
     3. Escolha **Sim** no diálogo de confirmação.

        ![copiar colar pasta ou arquivo](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

   * **Fixar no Acesso rápido**

     1. Selecione a pasta que você deseja fixar.
     2. Na barra de ferramentas do painel principal, selecione **Fixar no acesso rápido**.
     3. No painel esquerdo, veja a pasta selecionada adicionada ao nó **Acesso Rápido**.

        ![fixar no acesso rápido](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)
     4. Depois de criar o acesso rápido, você pode obter acesso aos recursos facilmente.
   * **Links Profundos**
     1. Se você tem uma URL, pode inserir apenas a URL no caminho de endereço no **Explorador de Arquivos** ou no navegador.
     2. Em seguida, **Storage Explorer.exe** é iniciado automaticamente para navegar até o local da URL que você acabou de digitar.

        ![link profundo no explorador de arquivos](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>Próximas etapas
* Veja as [Notas de versão e vídeos mais recentes do Gerenciador de Armazenamento (Preview)](http://www.storageexplorer.com).
* Aprenda a [Gerenciar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer)
* Saiba mais sobre o Gerenciador de Armazenamento[Introdução ao Gerenciador de Armazenamento (Versão Prévia)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* Comece a usar o Azure Data Lake Store (ADLS) conferindo a [Visão Geral do Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview)
* Assista ao vídeo a seguir para ver[como usar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)
