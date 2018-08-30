---
title: Introdução à API de Reconhecimento de Fala da Microsoft usando bibliotecas de clientes de Fala do Bing | Microsoft Docs
description: Use a as bibliotecas de cliente dos Serviços de Fala da Microsoft nos Serviços Cognitivos da Microsoft para desenvolver aplicativos que convertem áudio falado para texto.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 6fb490def6807204943a1ce3ba3c53186055102b
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048450"
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
