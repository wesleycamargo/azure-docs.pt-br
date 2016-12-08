---
title: Codificar um ativo usando o Media Encoder Standard com o portal do Azure | Microsoft Docs
description: "Este tutorial guia você pelas etapas de codificação de um ativo usando o Codificador de Mídia Padrão com o portal do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: dc30d0f6b885a1197ec41818e14b2e0b63386196


---
# <a name="encode-an-asset-using-media-encoder-standard-with-the-azure-portal"></a>Codificar um ativo usando o Codificador de Mídia Padrão com o portal do Azure
> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Ao trabalhar com os Serviços de Mídia do Azure, um dos cenários mais comuns é fornecer streaming com um streaming de taxa de bits adaptável aos clientes dos Serviços de Mídia do Azure. Os Serviços de Mídia dão suporte às seguintes tecnologias de streaming com taxa de bits adaptável: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS (apenas para licenciados Adobe PrimeTime/Access). Para preparar os vídeos para a transmissão da taxa de bits adaptável, você precisa codificar o vídeo de origem em arquivos de múltiplas taxas de bits. Você deve usar o codificador **Codificador de Mídia Padrão** para codificar seus vídeos.  

Os Serviços de Mídia também fornecem um empacotamento dinâmico que permite enviar seus MP4s de múltiplas taxas de bits nos seguintes formatos de transmissão: MPEG DASH, HLS, Smooth Streaming ou HDS, sem a necessidade de recolocar nesses formatos de transmissão. Com o empacotamento dinâmico, você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento, e os Serviços de Mídia criarão e fornecerão a resposta apropriada com base nas solicitações de um cliente.

Para aproveitar os benefícios do empacotamento dinâmico, você precisa fazer o seguinte:

* Codifique seu arquivo de origem em um conjunto de arquivos MP4 com múltiplas taxas de bits (as etapas da codificação são demonstradas mais tarde nesta seção).
* Obter pelo menos uma unidade de streaming para o ponto de extremidade de streaming do qual você planeja fornecer seu conteúdo. Para saber mais, confira [configurar pontos de extremidade de streaming](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

Para dimensionar o processamento de mídia, consulte [este](media-services-portal-scale-media-processing.md) tópico.

## <a name="encode-with-the-azure-portal"></a>Codificar com o portal do Azure
Esta seção descreve as etapas que você pode seguir para codificar o conteúdo com o Media Encoder Standard.

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia do Azure.
2. Na janela **Configurações**, selecione **Ativos**.  
3. Na janela **Ativos** , selecione o ativo que você gostaria de codificar.
4. Pressione o botão **Codificar** .
5. Na janela **Codificar um ativo** , selecione o processador "Media Encoder Standard" e uma predefinição. Por exemplo, se você souber que o vídeo de entrada tem uma resolução de 1920 x 1080 pixels, poderá usar a predefinição "H264 Múltiplas Taxas de Bits 1080p". Para obter mais informações sobre as predefinições, confira [este](https://msdn.microsoft.com/library/azure/mt269960.aspx) artigo – é importante selecionar a predefinição mais apropriada para seu vídeo de entrada. Se você tiver um vídeo de baixa resolução (640 x 360), não deverá usar a predefinição padrão "H264 Múltiplas Taxas de Bits 1080p".
   
   Para facilitar o gerenciamento, você tem a opção de editar o nome do ativo de saída e o nome do trabalho.
   
   ![Codificar ativos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
6. Pressione **Criar**.

## <a name="next-step"></a>Próxima etapa
Você pode monitorar o andamento do trabalho de codificação com o portal do Azure, conforme descrito [neste](media-services-portal-check-job-progress.md) artigo.  

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO3-->


