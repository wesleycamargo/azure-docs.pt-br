---
title: Mover dados de e para o Armazenamento de Blobs do Azure usando o Gerenciador de Armazenamento do Azure | Microsoft Docs
description: Mover dados de e para o Armazenamento de Blobs do Azure usando o Gerenciador de Armazenamento do Azure
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 10bd283f-0875-4c67-af63-6492270b7656
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: a67f2e77d3bc7da35a03b68d7f32fd3a2a42bfcd
ms.openlocfilehash: 18c05ade1ee0c0c716307613a3c0daf1c8d853aa


---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Mover dados de e para o Armazenamento de Blobs do Azure usando o Gerenciador de Armazenamento do Azure
O Gerenciador de Armazenamento do Azure é um ferramenta gratuita da Microsoft que possibilita o trabalho com dados do Armazenamento do Azure no Windows, MacOS e Linux. Este tópico descreve como usá-lo para carregar e baixar os dados do armazenamento de blobs do Azure. A ferramenta pode ser baixada em [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Se você estiver usando a VM configurada com os scripts fornecidos pelas [Máquinas virtuais de ciências de dados no Azure](machine-learning-data-science-virtual-machines.md), o Gerenciador de armazenamento do Azure já estará instalado na VM.
> 
> [!NOTE]
> Para obter uma introdução completa ao armazenamento de blobs do Azure, consulte [Noções básicas de Blob do Azure](../storage/storage-dotnet-how-to-use-blobs.md) e [Serviço de Blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que você tenha uma assinatura, uma conta de armazenamento do Azure e a chave de armazenamento correspondente dessa conta. Antes de carregar/baixar os dados, você deve conhecer o nome e a chave da sua conta do Armazenamento do Azure. 

* Para configurar uma assinatura do Azure, consulte [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma conta de armazenamento e obter informações sobre conta e chave, consulte [Sobre contas do armazenamento do Azure](../storage/storage-create-storage-account.md). Anote a chave de acesso para sua conta de armazenamento, pois você precisa dessa chave para conectar-se à conta com a ferramenta Gerenciador de Armazenamento do Azure.
* A ferramenta Gerenciador de Armazenamento do Azure pode ser baixada em [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/). Aceite os padrões durante a instalação.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Usar o Gerenciador de Armazenamento do Azure
As etapas a seguir mostram como carregar/baixar os dados usando o Gerenciador de Armazenamento do Azure. 

1. Inicie o Gerenciador de Armazenamento do Microsoft Azure.
2. Para abrir o assistente **Entrar em sua conta...**, selecione o ícone **Configurações de conta do Azure**, depois **Adicionar uma conta** e insira suas credenciais. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Para abrir o assistente **Conectar-se ao Armazenamento do Azure**, selecione o ícone **Conectar-se ao Armazenamento do Azure**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Insira a chave de acesso da sua conta de armazenamento do Azure no assistente **Conectar-se ao Armazenamento do Azure** e, em seguida, **Avançar**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Insira o nome da conta de armazenamento na caixa **Nome da conta** e selecione **Avançar**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. A conta de armazenamento adicionada deve estar listada. Para criar um contêiner de blob em uma conta de armazenamento, clique com o botão direito no nó **Contêineres de Blob** na conta, selecione **Criar Contêiner de Blob** e insira um nome.
7. Para carregar dados em um contêiner, selecione o contêiner de destino e clique no botão **Carregar**.![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Clique em **...** à direita da caixa **Arquivos**, selecione um ou vários arquivos para carregar no sistema de arquivos e clique em **Carregar** para começar a carregar os arquivos.![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Para baixar os dados, selecione o blob no contêiner correspondente para baixar e clique em **Download**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)




<!--HONumber=Nov16_HO3-->


