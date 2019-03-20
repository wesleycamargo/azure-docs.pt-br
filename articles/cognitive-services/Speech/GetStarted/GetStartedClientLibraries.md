---
title: Introdução à API de Reconhecimento de Fala do Bing usando bibliotecas de clientes | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use as bibliotecas de clientes de Fala do Bing nos Serviços Cognitivos da Microsoft para desenvolver aplicativos que convertem áudio falado em texto.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 89eb18a2b4af76f6489442dc66ab12d0840e92c7
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669475"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Introdução às bibliotecas de clientes do Serviço de Fala do Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

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

## <a name="additional-resources"></a>Recursos adicionais

- A página de [exemplos](../samples.md) fornece exemplos completos para usar as bibliotecas de cliente de fala.
- Se você precisar de uma biblioteca de cliente que ainda não tem suporte, você pode criar seu próprio SDK. Implemente o [protocolo WebSocket de fala](../API-Reference-REST/websocketprotocol.md) na plataforma e use a linguagem de sua escolha.

## <a name="license"></a>Licença

Todos os SDKs dos Serviços Cognitivos da Microsoft e exemplos são licenciados com a licença do MIT. Para obter mais informações, consulte [Licença](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
