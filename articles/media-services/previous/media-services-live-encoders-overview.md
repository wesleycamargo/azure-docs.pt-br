---
title: Configurar codificadores locais ao usar os Serviços de Mídia do Azure para criar fluxos de múltiplas taxas de bits | Microsoft Docs
description: Este tópico lista os codificadores ativos locais que podem ser usados para capturar os eventos ao vivo e enviar uma transmissão ao vivo de taxa de bits única para os canais do AMS (que são habilitados para a codificação ativa) para mais processamento. O tópico contém links para tutoriais que mostram como configurar os codificadores listados.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 360b822bc78d353e91612459a70334a0bf3a56e6
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189624"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Como configurar os codificadores locais ao usar os Serviços de Mídia do Azure para criar fluxos de múltiplas taxas de bits
Este tópico lista os codificadores ativos locais que podem ser usados para capturar os eventos ao vivo e enviar uma transmissão ao vivo de taxa de bits única para os canais do AMS (que são habilitados para a codificação ativa) para mais processamento. O tópico também contém links para os tutoriais que mostram como configurar os codificadores listados.

> [!NOTE]
> Ao transmitir via RTMP, verifique as configurações de firewall e/ou proxy para confirmar se as portas TCP de saída 1935 e 1936 estão abertas.

## <a name="flash-media-live-encoder"></a>Flash Media Live Encoder
Para saber mais sobre como configurar o codificador FMLE ([Flash Media Encoder Live](https://www.adobe.com/products/flash-media-encoder.html)) para enviar uma transmissão ao vivo de taxa de bits única para um Canal do AMS, veja [Configurando o FMLE](media-services-configure-fmle-live-encoder.md).

## <a name="haivision-kb-encoder"></a>Codificador Haivision KB
Para saber mais sobre como configurar o [Codificador Haivision KB](https://www.haivision.com/products/kb-series/) para enviar uma transmissão ao vivo de taxa de bits única para um Canal do AMS, veja [Configurando o Codificador Haivision KB](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Telestream Wirecast
Para obter informações sobre como configurar o codificador [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) para enviar uma transmissão ao vivo de taxa de bits única para um Canal do AMS, veja [Configurando o Wirecast](media-services-configure-wirecast-live-encoder.md).

## <a name="newtek-tricaster"></a>NewTek TriCaster
Para obter informações sobre como configurar o codificador [Tricaster](http://newtek.com/products/tricaster-40.html) para enviar uma transmissão ao vivo de taxa de bits única para um Canal do AMS, veja [Configurando o Tricaster](media-services-configure-tricaster-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Para obter mais informações, consulte [Elemental Live](https://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Próximas etapas

[Transmissão ao vivo usando os Serviços de Mídia do Azure para criar fluxos de múltiplas taxas de bits](media-services-manage-live-encoder-enabled-channels.md).

