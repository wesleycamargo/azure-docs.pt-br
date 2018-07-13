---
title: Traduzir fala usando os serviços de Fala | Microsoft Docs
description: Saiba como usar a Tradução de Fala no serviço de Fala.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 7f39f284998489574049d82c44b3d3a0a3797adb
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "35364956"
---
# <a name="translate-speech-using-speech-service"></a>Traduzir fala usando o serviço de Fala

O [SDK de Fala](speech-sdk.md) é a maneira mais simples de usar a tradução de fala em seu aplicativo. O SDK fornece a funcionalidade completa do serviço. O processo básico para executar a tradução de fala inclui as seguintes etapas:

1. Crie um alocador de fala e forneça uma chave de assinatura do serviço de Fala ou um token de autorização. Aqui, você também configurar os idiomas de tradução de origem e de destino, bem como especificar se deseja uma saída de fala ou de texto.

2. Obter um reconhecedor do alocador. Para a tradução, selecione um reconhecedor de tradução. Os outros reconhecedores são para *Conversão de Fala em Texto*. Há várias versões do reconhecedor de tradução com base na fonte de áudio que você está usando.

4. Associe eventos para operação assíncrona, se você quiser. O reconhecedor chama seus manipuladores de eventos quando houver resultados intermediários e finais. Caso contrário, o aplicativo receberá um resultado de tradução final.

5. Inicie o reconhecimento e a tradução.

# <a name="sdk-samples"></a>Amostras do SDK

Para obter o último conjunto de exemplos, consulte o [Repositório GitHub de exemplo do SDK de Fala dos Serviços Cognitivos](https://aka.ms/csspeech/samples).

# <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Reconhecer fala no C#](quickstart-csharp-windows.md)
