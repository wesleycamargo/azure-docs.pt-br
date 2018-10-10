---
title: Introdução à API de Reconhecimento de Fala do Bing usando bibliotecas de clientes | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use as bibliotecas de clientes de Fala do Bing nos Serviços Cognitivos da Microsoft para desenvolver aplicativos que convertem áudio falado em texto.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: e9d1bf1a6a2383a58a890ce9add816f9e9060273
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948115"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Introdução às bibliotecas de clientes do Serviço de Fala do Bing

Além de fazer solicitações HTTP diretas por meio de uma API REST, o Serviço de Fala do Bing fornece aos desenvolvedores bibliotecas de clientes de Fala em diferentes linguagens. As bibliotecas de cliente de voz:

- Suportam recursos mais avançados em reconhecimento de voz, como resultados intermediários em tempo real, streaming de áudio longo (até 10 minutos) e reconhecimento contínuo.
- Fornece uma API simples e idiomática no idioma de sua preferência.
- Oculte detalhes de comunicação de baixo nível.

Atualmente, as seguintes bibliotecas de clientes de Fala do Bing estão disponíveis:

- [Biblioteca de área de trabalho do C#](GetStartedCSharpDesktop.md)
- [Biblioteca de serviço do C#](GetStartedCSharpServiceLibrary.md)
- [Biblioteca do JavaScript](GetStartedJSWebsockets.md)
- [Biblioteca do Java para Android](GetStartedJavaAndroid.md)
- [Biblioteca do Objective-C para iOS](Get-Started-ObjectiveC-iOS.md)

> [!NOTE] 
Em maio de 2018, também lançamos o novo [Serviço de Fala](../../speech-service/index.yml) em visualização pública. É recomendável [experimentá-lo gratuitamente](../../speech-service/get-started.md). 

## <a name="additional-resources"></a>Recursos adicionais

- A página de [exemplos](../samples.md) fornece exemplos completos para usar as bibliotecas de cliente de fala.
- Se você precisar de uma biblioteca de cliente que ainda não tem suporte, você pode criar seu próprio SDK. Implemente o [protocolo WebSocket de fala](../API-Reference-REST/websocketprotocol.md) na plataforma e use a linguagem de sua escolha.

## <a name="license"></a>Licença

Todos os SDKs dos Serviços Cognitivos da Microsoft e exemplos são licenciados com a licença do MIT. Para obter mais informações, consulte [Licença](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).

