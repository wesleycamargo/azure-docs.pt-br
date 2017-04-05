---
title: Entender os pontos de extremidade do Hub IoT do Azure | Microsoft Docs
description: "Guia do desenvolvedor ‑ Informações de referência sobre pontos de extremidade do Hub IoT voltados para o dispositivo e para o serviço."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 57ba52ae-19c6-43e4-bc6c-d8a5c2476e95
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: cdc50ec4bf98d7924ad5bf97c9a757f0bac01173
ms.lasthandoff: 03/23/2017


---
# <a name="reference---iot-hub-endpoints"></a>Referência - Pontos de extremidade do Hub IoT
## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista de pontos de extremidade internos do Hub IoT
O Hub IoT do Azure é um serviço multilocatário que expõe suas funcionalidades a vários atores. O diagrama a seguir mostra os diversos pontos de extremidade que o Hub IoT expõe.

![Pontos de extremidade do Hub IoT][img-endpoints]

A seguir, uma descrição dos pontos de extremidade:

* **Provedor de recursos**. O provedor de recursos do Hub IoT expõe uma interface do [Azure Resource Manager][lnk-arm] que permite aos proprietários de assinaturas do Azure criar e excluir Hubs IoT e atualizar propriedades do Hub IoT. As propriedades do Hub IoT regem as [políticas de segurança no nível do hub][lnk-accesscontrol], ao contrário do controle de acesso no nível do dispositivo, e as opções funcionais para sistema de mensagens da nuvem para o dispositivo e do dispositivo para a nuvem. O provedor de recursos do Hub IoT também permite [exportar identidades do dispositivo][lnk-importexport].
* **Gerenciamento de identidades dos dispositivos**. Cada Hub IoT expõe um conjunto de pontos de extremidade HTTP REST para o gerenciamento de identidades do dispositivo (criar, recuperar, atualizar e excluir). [As identidades do dispositivo][lnk-device-identities] são usadas para controle de acesso e autenticação do dispositivo.
* **Gerenciamento do dispositivo gêmeo**. Cada hub IoT expõe um conjunto de ponto de extremidade voltado para o serviço HTTP REST para consultar e atualizar [gêmeos de dispositivo][lnk-twins] (atualizar marcas e propriedades).
* **Gerenciamento de trabalhos**. Cada hub IoT expõe um conjunto de ponto de extremidade voltado para o serviço HTTP REST para consultar e gerenciar [trabalhos][lnk-jobs].
* **Pontos de extremidade do dispositivo**. Para cada dispositivo provisionado no registro de identidade, o Hub IoT expõe um conjunto de pontos de extremidade que pode ser usado por um dispositivo para enviar e receber mensagens:
  
  * *Enviar mensagens do dispositivo para a nuvem*. Use este ponto de extremidade para [enviar mensagens do dispositivo para a nuvem][lnk-d2c].
  * *Receber mensagens da nuvem para o dispositivo*. Um dispositivo usa este ponto de extremidade para receber [mensagens direcionadas da nuvem para o dispositivo][lnk-c2d].
  * *Inicie os uploads de arquivos*. Um dispositivo usa esse ponto de extremidade para receber um URI de SAS do Armazenamento do Azure do Hub IoT para [carregar um arquivo][lnk-upload].
  * *Recupere e atualize as propriedades do dispositivo gêmeo*. Um dispositivo usa esse ponto de extremidade para acessar as propriedades do [dispositivo gêmeo][lnk-twins].
  * *Receber solicitações de métodos diretos*. Um dispositivo usa esse ponto de extremidade para escutar as solicitações dos [métodos diretos][lnk-methods].
    
    Estes pontos de extremidade são expostos usando os protocolos [MQTT v3.1.1][lnk-mqtt], HTTP 1.1 e [AMQP 1.0][lnk-amqp]. Observe que o AMQP também está disponível sobre [WebSockets][lnk-websockets] na porta 443.
    
    Os pontos de extremidade de gêmeos e métodos estão disponíveis somente usando [MQTT v3.1.1][lnk-mqtt].
* **Pontos de extremidade do serviço**. Cada Hub IoT expõe um conjunto de pontos de extremidade que o seu back-end de solução pode usar para se comunicar com os dispositivos. Esses pontos de extremidade são atualmente expostos usando apenas o protocolo [AMQP][lnk-amqp], exceto para o ponto de extremidade de invocação de método que é exposto por meio de HTTP 1.1.
  
  * *Receber mensagens do dispositivo para a nuvem*. Esse ponto de extremidade é compatível com os [Hubs de Eventos do Azure][lnk-event-hubs]. Um serviço de back-end pode usá-lo para ler todas as [mensagens do dispositivo para a nuvem][lnk-d2c] enviadas por seus dispositivos. Você pode criar pontos de extremidade personalizados em seu hub IoT, além desse ponto de extremidade interno.
  * *Enviar mensagens da nuvem para o dispositivo e receber confirmações de entrega*. Esses pontos de extremidade permitem que o seu back-end de solução envie [mensagens da nuvem para o dispositivo][lnk-c2d] confiáveis e receba as confirmações de entrega ou de vencimento correspondentes.
  * *Receba notificações de arquivo*. Esse ponto de extremidade de mensagens permite que você receba notificações quando os dispositivos carregarem com êxito um arquivo. 
  * *Invocação direta de método*. Esse ponto de extremidade permite que um serviço de back-end invoque um [método direto][lnk-methods] em um dispositivo.
  * *Eventos de monitoramento de operações de recebimento*. Esse ponto de extremidade permitirá que você receba eventos de monitoramento de operações se o hub IoT tiver sido configurado para emiti-los. Confira [Monitoramento de operações do Hub IoT][lnk-operations-mon] para obter mais detalhes.

O artigo [SDKs do Azure IoT][lnk-sdks] descreve as várias maneiras de acessar esses pontos de extremidade.

Por fim, é importante observar que todos os pontos de extremidade do Hub IoT usam o protocolo [TLS][lnk-tls] e que nenhum ponto de extremidade é exposto em canais sem criptografia/desprotegidos.

## <a name="custom-endpoints"></a>Pontos de extremidade personalizados
Você pode vincular os serviços existentes do Azure em sua assinatura ao Hub IoT para serem usados como pontos de extremidade no direcionamento de mensagens. Esses agem como pontos de extremidade de serviço e são usados como "coletores" para rotas de mensagens. Os dispositivos não podem gravar diretamente nos pontos de extremidade adicionais. Para saber mais sobre rotas de mensagens, confira a entrada do guia do desenvolvedor sobre [como enviar e receber mensagens com o hub IoT][lnk-devguide-messaging].

Atualmente, o Hub IoT é compatível com os seguintes serviços do Azure como pontos de extremidade adicionais:

* Hubs de Eventos
* Filas de barramento de serviço
* Tópicos do Service Bus

O Hub IoT precisa de acesso de gravação a esses pontos de extremidade para que o direcionamento de mensagens funcione. Se você configurar os pontos de extremidade por meio do Portal do Azure, as permissões necessárias serão adicionadas para você. Certifique-se de configurar os serviços para dar suporte à taxa de transferência esperada. Talvez seja necessário monitorar seus pontos de extremidade adicionais ao configurar a solução IoT e depois fazer os ajustes necessários para a carga real.

Se uma mensagem corresponder a várias rotas e todas apontarem para o mesmo ponto de extremidade, o Hub IoT entregará mensagens para esse ponto apenas uma vez. Portanto, você não precisa configurar a eliminação de duplicação nem no tópico, nem na fila do Barramento de Serviço. Em filas particionados, a afinidade de partição garante a ordenação das mensagens. Não há suporte para filas com sessões habilitadas como pontos de extremidade. Filas particionadas e tópicos com eliminação de duplicação habilitada também não têm suporte.

Para conhecer os limites para a quantidade de pontos de extremidade que você pode adicionar, confira [Cotas e limitação][lnk-devguide-quotas].

## <a name="field-gateways"></a>Gateways de campo
Em uma solução IoT, um *gateway de campo* fica entre os dispositivos e os pontos de extremidade do hub IoT. Normalmente, ele se localiza perto dos dispositivos. Os dispositivos se comunicam diretamente com o gateway de campo usando um protocolo com suporte dos dispositivos. O gateway de campo conecta-se com um ponto de extremidade Hub IoT usando um protocolo que tem suporte do Hub IoT. Um gateway de campo pode ser um hardware altamente especializado ou um computador com baixo consumo de energia que executa um software que realiza o cenário de ponta a ponta ao qual o gateway se destina.

É possível usar o [SDK do Gateway do Azure IoT][lnk-gateway-sdk] para implementar um gateway de campo. Esse SDK oferece uma funcionalidade específica, como a capacidade de multiplexar a comunicação de vários dispositivos na mesma conexão do Hub IoT.

## <a name="next-steps"></a>Próximas etapas
Outros tópicos de referência neste Guia do desenvolvedor do Hub IoT incluem:

* [Linguagem de consulta do Hub IoT para gêmeos e trabalhos][lnk-devguide-query]
* [Cotas e limitações][lnk-devguide-quotas]
* [Suporte ao MQTT do Hub IoT][lnk-devguide-mqtt]

[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md

