---
title: Criando Filtros com a API REST dos Serviços de Mídia do Azure | Microsoft Docs
description: Este tópico descreve como criar filtros para que seu cliente possa usá-los na transmissão de seções específicas de um fluxo. Os Serviços de Mídia criam manifestos dinâmicos para atingir esse streaming seletivo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 6b0ef646ba9ea535038f181ebfff5bf7639afdf8
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633615"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Criando filtros com a API REST de Serviços de Mídia do Microsoft Azure

Ao entregar seu conteúdo aos clientes (streaming de eventos ao vivo ou vídeo por demanda), seu cliente pode precisar de mais flexibilidade do que o descrito no arquivo de manifesto do ativo padrão. Os Serviços de Mídia do Azure permitem definir filtros de conta e filtros de recursos para o seu conteúdo. Para obter mais informações, consulte [Filtros e manifestos dinâmicos](filters-dynamic-manifest-overview.md).

Este tópico mostra como definir um filtro para um ativo vídeo por demanda e usar APIs REST para criar [Filtros de Conta](https://docs.microsoft.com/rest/api/media/accountfilters) e [Filtros de Ativos](https://docs.microsoft.com/rest/api/media/assetfilters). 

## <a name="prerequisites"></a>Pré-requisitos 

Para concluir as etapas descritas neste tópico, você precisa:

- Analise [Filtros e manifestos dinâmicos](filters-dynamic-manifest-overview.md).
- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md). Lembre-se de lembrar o nome do grupo de recursos e o nome da conta do Media Services. 
- [Configurar o Postman para chamadas da API REST dos Serviços de Mídia do Azure](media-rest-apis-with-postman.md)/

## <a name="define-a-filter"></a>Definir um filtro  

A seguir, o exemplo **Request body**, que define as condições de seleção de faixas adicionadas ao manifesto. Este filtro inclui todas as faixas de áudio inglesas com EC-3 e quaisquer faixas de vídeo com taxa de bits na faixa de 0 a 1000000.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "Language",
                        "value": "en",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "NotEqual"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Crie filtros de conta

Na coleção do Postman que você fez o download, selecione **Account Filters**-> **Crie ou atualize um Filtro de Conta**.

O método de solicitação HTTP **PUT** é semelhante a:

PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01

Selecione a guia **Body** e cole o código json [definido anteriormente](#define-a-filter).

Selecione **Enviar**. 

O filtro foi criado.

Para mais informações, consulte [Criar ou atualizar](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Além disso, veja [exemplos de JSON para filtros](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Crie filtros de ativos  

Na coleção do Postman "Serviços de Mídia v3" que você fez o download, selecione **Ativos** -> ** Criar ou atualizar o Filtro de Ativos.

O método de solicitação HTTP **PUT** é semelhante a:

PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01

Selecione a guia **Body** e cole o código json [definido anteriormente](#define-a-filter).

Selecione **Enviar**. 

O filtro de ativos foi criado.

Para detalhes sobre como criar ou atualizar filtros de recursos, consulte [Criar ou atualizar](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Além disso, veja [exemplos de JSON para filtros](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter). 

## <a name="next-steps"></a>Próximas etapas

[Vídeos do Stream](stream-files-tutorial-with-rest.md) 
