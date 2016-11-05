---
title: Como criar um processador de mídia | Microsoft Docs
description: Saiba como criar um componente de processador de mídia para codificar, converter o formato, criptografar ou descriptografar conteúdo de mídia dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako

---
# <a name="how-to:-get-a-media-processor-instance"></a>Como obter uma instância do processador de mídia
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Visão geral
Nos Serviços de Mídia, um processador de mídia é um componente que manipula uma tarefa de processamento específica, como codificação, conversão de formato, criptografia ou descriptografia de conteúdo de mídia. Normalmente, você cria um processador de mídia quando está criando uma tarefa para codificar, criptografar ou converter o formato do conteúdo de mídia.

A tabela a seguir fornece o nome e a descrição de cada processador de mídia disponível.

| Nome do processador de mídia | Descrição | Mais informações |
| --- | --- | --- |
| Media Encoder Standard |Fornece funcionalidades padrão para codificação sob demanda. |[Visão Geral e Comparação de Codificadores de Mídia sob Demanda do Azure](media-services-encode-asset.md) |
| Fluxo de trabalho do Media Encoder Premium |Permite que você execute tarefas de codificação usando o fluxo de trabalho do Media Encoder Premium. |[Visão Geral e Comparação de Codificadores de Mídia sob Demanda do Azure](media-services-encode-asset.md) |
| Indexador de Mídia do Azure |Permite tornar arquivos e conteúdo de mídia pesquisáveis, bem como gerar faixas de legenda e palavras-chave. |[Indexador de Mídia do Azure](media-services-index-content.md) |
| Azure Media Hyperlapse (visualização) |Permite suavizar "impactos" no vídeo com estabilização do vídeo. Também permite que você a acelere o seu conteúdo em um clipe de consumo. |[Azure Media Hyperlapse](media-services-hyperlapse-content.md) |
| Codificador de Mídia do Azure |Substituído | |
| Descriptografia do armazenamento |Substituído | |
| Gerenciador de mídia do Azure |Substituído | |
| Criptografador de Mídia do Azure |Substituído | |

## <a name="get-mediaprocessor"></a>Obter MediaProcessor
> [!NOTE]
> Ao trabalhar com a API REST dos serviços de mídia, as seguintes considerações se aplicam:
> 
> Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md).
> 
> Depois de se conectar com êxito em https://media.windows.net, você receberá um redirecionamento 301 especificando outro URI dos serviços de mídia. Você deve fazer chamadas subsequentes para o novo URI, conforme descrito em [Conectando-se aos Serviços de Mídia usando a API REST](media-services-rest-connect-programmatically.md). 
> 
> 

A chamada REST a seguir mostra como obter uma instância do processador de mídia por nome (neste caso, **Codificador de Mídia Padrão**). 

Solicitação:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.11
    Host: media.windows.net

Resposta:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Próximas etapas
Agora que você já sabe como obter uma instância do processador de mídia, vá para o tópico [Como Codificar um Ativo](media-services-rest-get-started.md) , que mostrará como usar o Codificador de Mídia Standard para codificar um ativo.

<!--HONumber=Oct16_HO2-->


