---
title: " Carregar arquivos em uma conta dos Serviços de Mídia usando o Portal do Azure | Microsoft Docs"
description: "Este tutorial orienta você pelas etapas de carregamento de arquivos em uma conta do Serviços de Mídia do Azure usando o portal do Azure"
services: media-services
documentationcenter: 
author: Juliako
manager: SyntaxC4
editor: 
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 8b9a7a266259077d49a38d51d7f801f420cc6315
ms.contentlocale: pt-br
ms.lasthandoff: 08/08/2017

---
# <a name="upload-files-into-a-media-services-account-using-the-azure-portal"></a>Carregar arquivos em uma conta do Serviços de Mídia usando o portal do Azure
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 


Nos serviços de mídia, você pode carregar seus arquivos digitais em um ativo. O Ativo pode conter vídeo, áudio, imagens, coleções de miniaturas, sequências de texto e arquivos de legendas (e os metadados sobre esses arquivos). Depois que os arquivos são carregados, o conteúdo é armazenado com segurança na nuvem para processamento adicional e transmissão.


## <a name="upload-files"></a>Carregar arquivos

>[!NOTE]
>Há um limite no tamanho máximo de arquivo com suporte para o processamento nos Serviços de Mídia. Confira [este](media-services-quotas-and-limitations.md) tópico para obter detalhes sobre a limitação de tamanho de arquivo.
>

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia do Azure.
2. Na folha **Configurações**, clique em **Ativos**.
   
    ![Carregar arquivos](./media/media-services-portal-vod-get-started/media-services-upload.png)
3. Clique no botão **Carregar** .
   
    A janela **Carregar um ativo de vídeo** é exibida.
   
   > [!NOTE]
   > Não há nenhuma limitação de tamanho do arquivo.
   > 
   > 
4. Navegue até o vídeo desejado no seu computador, selecione-o e clique em OK.  
   
    O carregamento é iniciado e você pode ver o progresso abaixo do nome de arquivo.  

Quando o carregamento for concluído, você verá o novo ativo listado na janela **Ativos** . 

## <a name="next-steps"></a>Próximas etapas
Agora você pode codificar seus ativos carregados. Para saber mais, veja [Codificar ativos](media-services-portal-encode.md).

Você também pode usar as Azure Functions para disparar um trabalho de codificação baseado em um arquivo que chega no contêiner configurado. Para obter mais informações, confira [este exemplo](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


