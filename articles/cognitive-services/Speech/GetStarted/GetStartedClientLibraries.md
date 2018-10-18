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
ms.openlocfilehash: 2682236ae9f9256b91d1259248e72139c907c8e5
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345009"
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

