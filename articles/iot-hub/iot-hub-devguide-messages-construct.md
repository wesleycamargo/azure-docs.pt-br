---
title: Entender o formato de mensagem do Hub IoT do Azure| Microsoft Docs
description: Guia do desenvolvedor - descreve o formato e o conteúdo esperado de mensagens do Hub IoT.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: edea20343c2a261902c082dbc5c96b78db6b470d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973197"
---
# <a name="create-and-read-iot-hub-messages"></a>Criar e ler mensagens do Hub IoT

Para suportar a interoperabilidade contínua entre protocolos, o Hub IoT define um formato de mensagem comum para todos os protocolos voltados para o dispositivo. Esse formato de mensagem é usado para mensagens de [roteamento de dispositivo para nuvem](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) e de [nuvem para dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-c2d). 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O Hub IoT implementa mensagens de dispositivo para nuvem usando um padrão de sistema de mensagens de streaming. As mensagens de dispositivo para nuvem do Hub IoT são mais semelhantes a *eventos* de [Hubs de Eventos](https://docs.microsoft.com/azure/event-hubs/) do que a *mensagens* do [Barramento de Serviço](https://docs.microsoft.com/azure/service-bus-messaging/) no sentido de que há um volume alto de eventos passando pelo serviço que podem ser lidos por vários leitores.

Uma mensagem do Hub IoT consiste em:
* Um conjunto predeterminado de *propriedades do sistema*, conforme listado abaixo.
* Um conjunto de *propriedades do aplicativo*. Um dicionário de propriedades de cadeia de caracteres que o aplicativo pode definir e acessar sem precisar desserializar o corpo da mensagem. O Hub IoT nunca modifica essas propriedades.
* Um corpo de binário opaco.

Os valores e nomes de propriedade podem conter apenas caracteres alfanuméricos ASCII mais ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}``` quando você envia mensagens de dispositivo para nuvem usando o protocolo HTTPS ou envia mensagens da nuvem para dispositivo.

As mensagens de dispositivo para a nuvem com o Hub IoT têm as seguintes características:

* As mensagens do dispositivo para a nuvem são duráveis e mantidas em um ponto de extremidade de **mensagens/eventos** padrão do Hub IoT por até sete dias.
* As mensagens de dispositivo para a nuvem podem ter no máximo 256 KB e podem ser agrupadas em lotes para otimizar os envios. Os lotes podem ter no máximo 256 KB.
* O Hub IoT não permite o particionamento arbitrário. As mensagens do dispositivo para a nuvem são particionadas com base em sua **deviceId**de origem.
* Como explicado na seção [Controlar o acesso ao Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security), o Hub IoT permite a autenticação e o controle de acesso por dispositivo.

Para obter mais informações de como codificar e decodificar mensagens enviadas usando diferentes protocolos, confira [SDKs de IoT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

A tabela a seguir lista o conjunto de propriedades do sistema em mensagens do Hub IoT.

| Propriedade | DESCRIÇÃO | O usuário é configurável? |
| --- | --- | --- |
| MessageId |Um identificador configurável pelo usuário para a mensagem utilizada para padrões de resposta à solicitação. Formato: uma cadeia de caracteres que diferencia maiúsculas de minúsculas (com até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | SIM |
| Número de sequência |Um número (exclusivo por fila de dispositivos) atribuído pelo Hub IoT a cada mensagem da nuvem para o dispositivo. | Não para mensagens C2D, caso contrário, sim. |
| Para |Um destino especificado em mensagens [nuvem para dispositivo][lnk-c2d]. | Não para mensagens C2D, caso contrário, sim. |
| ExpiryTimeUtc |Data e hora de expiração da mensagem. | SIM |
| EnqueuedTime |Data e hora do recebimento da mensagem [nuvem para dispositivo][lnk-c2d] pelo Hub IoT. | Não para mensagens C2D, caso contrário, sim. |
| CorrelationId |Uma cadeia de propriedade em uma mensagem de resposta que geralmente contém a MessageId da solicitação em padrões de solicitação-resposta. | SIM |
| UserId |Uma ID usada para especificar a origem das mensagens. Quando as mensagens são geradas pelo Hub IoT, são definidas como `{iot hub name}`. | Não  |
| Ack |Um gerador de mensagem de comentários. Essa propriedade é usada em mensagens da nuvem para o dispositivo para solicitar ao Hub IoT a geração de mensagens de comentários como resultado do consumo da mensagem pelo dispositivo. Valores possíveis: **nenhum** (padrão): nenhuma mensagem de comentários é gerada, **positivo**: ocorrerá o recebimento de uma mensagem de comentários se a mensagem estiver completa, **negativo**: ocorrerá o recebimento de uma mensagem de comentários se a mensagem expirar (ou se a contagem máxima de entrega tiver sido atingida) sem ser concluída pelo dispositivo ou **total**: positivos e negativos. Para obter mais informações, consulte [Comentários da mensagem][lnk-feedback]. | SIM |
| ConnectionDeviceId |Uma ID definida pelo Hub IoT em mensagens do dispositivo para a nuvem. Contém a **deviceId** do dispositivo que enviou a mensagem. | Não para mensagens D2C, caso contrário, sim. |
| ConnectionDeviceGenerationId |Uma ID definida pelo Hub IoT em mensagens do dispositivo para a nuvem. Contém a **generationId** (conforme [Propriedades de identidade do dispositivo][lnk-device-properties] ) do dispositivo que enviou a mensagem. | Não para mensagens D2C, caso contrário, sim. |
| ConnectionAuthMethod |Um método de autenticação definido pelo Hub IoT em mensagens do dispositivo para a nuvem. Essa propriedade contém informações sobre o método de autenticação usado para autenticar o dispositivo que envia a mensagem. Para obter mais informações, consulte [Dispositivo para nuvem antifalsificação][lnk-antispoofing] . | Não para mensagens D2C, caso contrário, sim. |
| CreationTimeUtc | Data e hora em que a mensagem foi criada em um dispositivo. Um dispositivo deve definir esse valor explicitamente. | SIM |

## <a name="message-size"></a>Tamanho da mensagem

O Hub IoT mede o tamanho da mensagem independentemente do protocolo, considerando apenas a carga real. O tamanho em bytes é calculado como a soma do seguinte:

* O tamanho do corpo em bytes.
* O tamanho em bytes de todos os valores das propriedades do sistema de mensagens.
* O tamanho em bytes de todos os nomes e valores de propriedade do usuário.

Valores e nomes de propriedade são limitados a caracteres ASCII, portanto, o comprimento das cadeias de caracteres é igual ao tamanho em bytes.

## <a name="anti-spoofing-properties"></a>Propriedades antifalsificação

Para evitar a falsificação em mensagens do dispositivo para a nuvem, o Hub IoT carimba todas as mensagens com as seguintes propriedades:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

As duas primeiras contêm a **deviceId** e a **generationId** do dispositivo de origem, conforme as [Propriedades de identidade do dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#device-identity-properties).

A propriedade **ConnectionAuthMethod** contém um objeto JSON serializado com as seguintes propriedades:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre limites de tamanho de mensagem no Hub IoT, confira [Cotas e limitação do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-quotas-throttling).
* Para saber como criar e ler mensagens do Hub IoT em várias linguagens de programação, confira os [Inícios Rápidos](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-node).