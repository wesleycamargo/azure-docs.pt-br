---
title: "Guia de início rápido do Azure – Transferir objetos para/do Armazenamento de blobs do Azure usando o portal do Azure | Microsoft Docs"
description: Saiba como usar o portal do Azure para carregar, baixar e listar blobs no Armazenamento de blobs do Azure.
services: storage
documentationcenter: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.topic: quickstart
ms.date: 12/12/2017
ms.author: tamram
ms.openlocfilehash: f647a5b78ee2fa362c4dea6ee9003ac56e0f7f7d
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-the-azure-portal"></a>Transferir objetos de/para o Armazenamento de blobs do Azure usando o portal do Azure

Neste guia de início rápido, você aprenderá a usar o [portal do Azure](https://portal.azure.com/) para criar um contêiner no Armazenamento do Azure e a carregar e baixar blobs de blocos nesse contêiner.

## <a name="prerequisites"></a>pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="create-a-container"></a>Criar um contêiner

Para criar um contêiner no portal do Azure, siga estas etapas:

1. Navegue até sua nova conta de armazenamento no portal do Azure.
2. No menu à esquerda da conta de armazenamento, role até a seção **Serviço de Blobs** e selecione **Procurar Blobs**.
3. Clique no botão **Adicionar Contêiner**.
4. Digite um nome para seu novo contêiner. O nome do contêiner deve estar em caixa baixa, começar com uma letra ou número e pode conter apenas letras, números e o caractere traço (-). Consulte [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Nomenclatura e referência de contêineres, blobs e metadados) para obter mais informações sobre nomes de contêiner e de blobs.
5. Defina o nível de acesso público ao contêiner. O nível padrão é **Privado (sem acesso anônimo)**.
6. Clique em **OK** para criar o contêiner.

    ![Captura de tela mostrando como criar um contêiner no portal do Azure](media/storage-quickstart-blobs-portal/create-container.png)

## <a name="upload-a-block-blob"></a>Carregar um blob de blocos

Blobs de bloco consistem em blocos de dados montados para criar um blob. A maioria dos cenários que usa o Armazenamento de blobs emprega blobs de blocos. Os blobs de blocos são ideais para armazenar texto e dados binários na nuvem, como arquivos, imagens e vídeos. Este guia de início rápido mostra como trabalhar com blobs de blocos. 

Para carregar um blob de blocos para seu novo contêiner no portal do Azure, siga estas etapas:

1. No portal do Azure, navegue até o contêiner que você criou na seção anterior.
2. Selecione o contêiner para mostrar uma lista de blobs que ele contém. Nesse caso, uma vez que você criou um novo contêiner, ele ainda não contém nenhum blob.
3. Clique no botão **Carregar** para carregar um blob no contêiner.
4. Procure seu sistema de arquivos local para encontrar um arquivo a ser carregado como um blob de blocos e depois clique em **Carregar**.
     
    ![Captura de tela mostrando como carregar um blob a partir da sua unidade local](media/storage-quickstart-blobs-portal/upload-blob.png)

5. Carregue quantos blobs desejar usando essa maneira. Você verá que os novos blobs agora estão listados dentro do contêiner.

    ![Captura de tela mostrando a lista de blobs no contêiner](media/storage-quickstart-blobs-portal/list-blobs.png)

## <a name="download-a-block-blob"></a>Baixar um blob de blocos

Você pode baixar um blob de blocos para exibir no navegador ou salvar em seu sistema de arquivos local. Para baixar um blob de blocos, siga estas etapas:

1. Navegue até a lista de blobs que você carregou na seção anterior. 
2. Selecione o blob a ser baixado.
3. Clique com botão direito do mouse no botão **Mais** (**...**) e selecione **Baixar**. 

![Captura de tela mostrando como baixar um blob no portal do Azure](media/storage-quickstart-blobs-portal/download-blob.png)

## <a name="clean-up-resources"></a>Limpar recursos

Para remover os recursos criados neste guia de início rápido, você pode simplesmente excluir o contêiner. Todos os blobs no contêiner também serão excluídos.

Para excluir o contêiner:

1. No portal do Azure, navegue até a lista de contêineres na sua conta de armazenamento.
2. Selecione o contêiner a ser excluído.
3. Clique com botão direito do mouse no botão **Mais** (**...**) e selecione **Excluir**.
4. Confirme que você realmente deseja excluir o contêiner.

    ![Captura de tela mostrando como excluir um contêiner no portal do Azure](media/storage-quickstart-blobs-portal/delete-container.png)   

## <a name="next-steps"></a>Próximas etapas

Nesse guia de início rápido, você aprendeu a transferir arquivos entre o disco local e Armazenamento de Blobs do Azure usando o .NET. Para saber mais sobre como trabalhar com o Armazenamento de Blobs, prossiga para as instruções do Armazenamento de Blobs.

> [!div class="nextstepaction"]
> [Instruções de operações do Armazenamento de Blobs](storage-dotnet-how-to-use-blobs.md)

