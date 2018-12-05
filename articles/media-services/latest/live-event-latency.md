---
title: Latência LiveEvent nos Serviços de Mídia do Microsoft Azure | Microsoft Docs
description: Este tópico fornece uma visão geral de latência de LiveEvent e mostra como definir a baixa latência.
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
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: a74f2e53127b506f42ff49018c3df2985396646d
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619802"
---
# <a name="liveevent-latency-in-media-services"></a>Latência do LiveEvent nos Serviços de Mídia

Este artigo mostra como definir a baixa latência em um **LiveEvent**. Esta seção também discute os resultados comuns que você vê ao usar as configurações de baixa latência e de vários players. Os resultados variam com base na latência de rede e da CDN. 

Para usar o novo recurso **LowLatency**, você pode definir as **StreamOptionsFlag** à **LowLatency** sobre o **LiveEvent**. Depois que o fluxo está em execução, você pode usar o [Player de Mídia do Azure](http://ampdemo.azureedge.net/) (AMP) página de demonstração e defina as opções de reprodução para usar a baixa latência heurística "perfil".

O exemplo de .NET a seguir mostra como definir **LowLatency** sobre o **LiveEvent**:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second GOP size instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Veja o exemplo completo: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="pass-through-liveevents-latency"></a>Latência do LiveEvents de passagem

A tabela a seguir mostra os resultados comuns de latência (quando o sinalizador LowLatency estiver habilitado) nos Serviços de Mídia, medido desde a hora em que o feed de contribuição atingir o serviço quando um player puder solicitar a reprodução.

||2s GOP baixa latência habilitada|1s GOP baixa latência habilitada|
|---|---|---|
|TRAÇO no AMP|10s|8s|
|HLS no player de iOS nativo|14s|10s|

> [!NOTE]
> A latência de ponta a ponta pode variar, dependendo das condições de rede local ou com a introdução de uma camada de cache da CDN. Você deve testar as configurações exatas.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Tutorial de live streaming](stream-live-tutorial-with-api.md)

