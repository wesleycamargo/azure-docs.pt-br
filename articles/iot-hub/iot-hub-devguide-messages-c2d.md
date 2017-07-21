---
title: Entender mensagens de nuvem para o dispositivo Hub IoT do Azure| Microsoft Docs
description: "Guia do desenvolvedor - como usar mensagens de nuvem para o dispositivo com o Hub IoT. Inclui informações sobre o ciclo de vida da mensagem e as opções de configuração."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 04ac46498c912b0503036f70b7f3d0e28e5a82b8
ms.contentlocale: pt-br
ms.lasthandoff: 05/31/2017


---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>Enviar mensagens de nuvem para o dispositivo do Hub IoT

Para enviar notificações unidirecionais para o aplicativo do dispositivo do back-end de solução, envie mensagens de nuvem para o dispositivo do seu Hub IoT para seu dispositivo. Para uma discussão sobre outras opções de nuvem para o dispositivo com suporte do Hub IoT, consulte [Diretrizes de comunicação de nuvem para o dispositivo][lnk-c2d-guidance].

Você pode enviar mensagens da nuvem para o dispositivo por meio de um ponto de extremidade voltado para o serviço (**/messages/devicebound**). Um dispositivo recebe as mensagens através de um ponto de extremidade específico do dispositivo (**/devices/{deviceId}/messages/devicebound**).

Cada mensagem da nuvem para o dispositivo é direcionada a um único dispositivo definindo a propriedade **to** como **/devices/{deviceId}/messages/devicebound**.

Cada fila de dispositivo contém no máximo 50 mensagens da nuvem para o dispositivo. Tentar enviar mais mensagens para o mesmo dispositivo resultará em um erro.

## <a name="the-cloud-to-device-message-lifecycle"></a>O ciclo de vida da mensagem de nuvem para o dispositivo

Para garantir uma entrega de mensagem pelo menos uma vez, o Hub IoT persiste com mensagens de nuvem para o dispositivo em filas por dispositivo. Os dispositivos devem reconhecer explicitamente a *conclusão* para que o Hub IoT as remova da fila. Essa abordagem garante a resiliência contra conectividade e falhas de dispositivo.

O diagrama a seguir mostra o gráfico de estado do ciclo de vida para uma mensagem de nuvem para o dispositivo no Hub IoT.

![Ciclo de vida de mensagens da nuvem para o dispositivo][img-lifecycle]

Quando o serviço do Hub IoT envia uma mensagem para um dispositivo, o serviço define o estado da mensagem para **Enfileirada**. Quando um dispositivo deseja *receber* uma mensagem, o Hub IoT *bloqueia* a mensagem (definindo o estado para **Invisível**), permitindo que outros threads no dispositivo comecem a receber outras mensagens. Quando um thread de dispositivo conclui o processamento de uma mensagem, ele notifica o Hub IoT *concluindo* a mensagem. O Hub IoT, em seguida, define o estado para **Concluído**.

Um dispositivo também pode escolher:

* *Rejeitar* a mensagem, o que faz com que o Hub IoT a defina no estado **Morto** . Os dispositivos que se conectam através do protocolo MQTT não podem rejeitar mensagens de nuvem para o dispositivo.
* *Abandonar* a mensagem, o que faz com que o Hub IoT coloque a mensagem de volta na fila com o estado definido como **Enfileirada**.

Um thread pode falhar ao processar uma mensagem sem notificar o Hub IoT. Nesse caso, as mensagens passam automaticamente do estado **Invisível** de volta para o estado **Enfileirada** após um *tempo limite de visibilidade (ou de bloqueio)*. O valor padrão desse tempo limite é um minuto.

Uma mensagem pode transitar entre os estados **Enfileirada** e **Invisível**, no máximo, pelo número de vezes especificado na propriedade **contagem máx. de entregas** no Hub IoT. Após esse número de transições, o Hub IoT definirá o estado da mensagem como **Morto**. Da mesma forma, o Hub IoT define o estado de uma mensagem como **Morto** após o tempo de expiração (confira [Vida útil][lnk-ttl]).

O [Como enviar mensagens de nuvem para o dispositivo com o Hub IoT][lnk-c2d-tutorial] mostra como enviar mensagens de nuvem a dispositivo da nuvem e recebê-las em um dispositivo.

Normalmente, um dispositivo completa uma mensagem de nuvem para o dispositivo quando a perda da mensagem não afeta a lógica de aplicativo. Por exemplo, quando o dispositivo persistiu o conteúdo da mensagem localmente ou executou uma operação com êxito. A mensagem também poderia portar informações transitórias cuja perda não afetaria a funcionalidade do aplicativo. Às vezes, para tarefas de longa duração, você pode concluir a mensagem do dispositivo para a nuvem depois de manter a descrição da tarefa no armazenamento local. Em seguida, você pode notificar o back-end da solução com uma ou mais mensagens de dispositivo para a nuvem em vários estágios de progresso da tarefa.

## <a name="message-expiration-time-to-live"></a>Expiração da mensagem (vida útil)

Todas as mensagens da nuvem para o dispositivo têm um tempo de expiração. Esse tempo é definido pelo serviço (na propriedade **ExpiryTimeUtc**) ou pelo Hub IoT usando a *vida útil* padrão especificada como uma propriedade do Hub IoT. Consulte [Opções de configuração da nuvem para o dispositivo][lnk-c2d-configuration].

Uma maneira comum de tirar proveito da expiração da mensagem e evitar enviar mensagens para dispositivos desconectados é definir valores de vida útil baixos. Essa abordagem proporciona o mesmo resultado que a manutenção do estado de conexão do dispositivo, enquanto é mais eficiente. Quando você solicita confirmações de mensagem, o Hub IoT notifica você sobre quais dispositivos podem receber mensagens e quais dispositivos não estão online ou apresentam falha.

## <a name="message-feedback"></a>Comentários da mensagem

Quando você envia uma mensagem da nuvem para o dispositivo, o serviço pode solicitar a entrega de um comentário por mensagem sobre o estado final dessa mensagem.

| Propriedade Ack | Comportamento |
| ------------ | -------- |
| **positivo** | O Hub IoT gera uma mensagem de comentários se, e somente se, a mensagem da nuvem para o dispositivo tenha alcançado o estado **Concluído**. |
| **negativo** | O Hub IoT gera uma mensagem de comentários, se e somente se, a mensagem da nuvem para o dispositivo alcançar o estado **Morto**. |
| **completo**     | O Hub IoT gera uma mensagem de comentários em ambos os casos. |

Se **Ack** estiver definida como **total** e você não receber uma mensagem de comentários, isso significará que a mensagem de comentários expirou. O serviço não pode saber o que aconteceu com a mensagem original. Na prática, um serviço deve garantir que possa processar os comentários antes que eles expirem. O tempo máximo de expiração é de dois dias, o que permite tempo suficiente para restabelecer a execução do problema caso ocorra uma falha.

Como explicado em [Pontos de extremidade][lnk-endpoints], o Hub IoT fornece comentários por meio de um ponto de extremidade voltado para o serviço (**/messages/servicebound/feedback**) como mensagens. A semântica de recebimento dos comentários é a mesma das mensagens da nuvem para o dispositivo e tem o mesmo [Ciclo de vida da mensagem][lnk-lifecycle]. Sempre que possível, os comentários de mensagem são feitos em lotes em uma única mensagem, com o seguinte formato:

| Propriedade     | Descrição |
| ------------ | ----------- |
| EnqueuedTime | Carimbo de data/hora que indica quando a mensagem foi criada. |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

O corpo é uma matriz de registros serializada em JSON, cada um com as seguintes propriedades:

| Propriedade           | Descrição |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Carimbo de data e hora que indica quando ocorreu a saída da mensagem. Por exemplo, o dispositivo foi concluído ou a mensagem expirou. |
| OriginalMessageId  | **MessageId** da mensagem de nuvem para o dispositivo para qual essa informação de comentários se relaciona. |
| StatusCode         | Cadeia de caracteres obrigatória. Usado em mensagens de comentários geradas pelo Hub IoT. <br/> 'Êxito' <br/> 'Expirado' <br/> 'DeliveryCountExceeded' <br/> 'Rejeitado' <br/> 'Limpo' |
| Descrição        | Valores de cadeia de caracteres para **StatusCode**. |
| deviceId           | **DeviceId** do dispositivo de destino da mensagem de nuvem para o dispositivo para qual esse comentário se relaciona. |
| DeviceGenerationId | **DeviceGenerationId** do dispositivo de destino da mensagem de nuvem para o dispositivo para qual esse comentário se relaciona. |

O serviço deve especificar uma **MessageId** para a mensagem da nuvem para o dispositivo, para poder correlacionar seus comentários com a mensagem original.

O exemplo a seguir mostra o corpo de uma mensagem de comentários.

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>Opções de configuração da nuvem para o dispositivo

Cada Hub IoT expõe as seguintes opções de configuração para mensagens de nuvem para o dispositivo:

| Propriedade                  | Descrição | Intervalo e padrão |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | TTL padrão para mensagens da nuvem para o dispositivo. | Intervalo ISO_8601 de até 2D (mínimo de 1 minuto). Padrão: 1 hora. |
| maxDeliveryCount          | Contagem máxima de entrega para filas de nuvem para o dispositivo por dispositivo. | 1 a 100. Padrão: 10. |
| feedback.ttlAsIso8601     | Retenção de mensagens informativas do serviço associado. | Intervalo ISO_8601 de até 2D (mínimo de 1 minuto). Padrão: 1 hora. |
| feedback.maxDeliveryCount |Contagem máxima de entrega para a fila de comentários. | 1 a 100. Padrão: 100. |

Para obter mais informações sobre como configurar essas opções de configuração, consulte [Criar Hubs IoT][lnk-portal].

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre os SDKs que você pode utilizar para receber mensagens de nuvem para o dispositivo, consulte [SDKs do IoT do Azure][lnk-sdks].

Para experimentar a recepção de mensagens de nuvem para o dispositivo, consulte o tutorial [Enviar de nuvem para o dispositivo ][lnk-c2d-tutorial].

[img-lifecycle]: ./media/iot-hub-devguide-messages-c2d/lifecycle.png

[lnk-portal]: iot-hub-create-through-portal.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-ttl]: #message-expiration-time-to-live
[lnk-c2d-configuration]: #cloud-to-device-configuration-options
[lnk-lifecycle]: #message-lifecycle
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

