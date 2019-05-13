---
title: Noções básicas do suporte do Azure IoT Hub AMQP | Microsoft Docs
description: Guia do desenvolvedor – suporte para dispositivos que se conectam ao IoT Hub voltados para o dispositivo e serviço voltado para os pontos de extremidade usando o protocolo AMQP. Inclui informações sobre suporte interno a AMQP nos SDKs do dispositivo IoT do Azure.
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: rezas
ms.openlocfilehash: 703e2c842fb42bad8aa112d84c516a29c2327378
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473500"
---
# <a name="communicate-with-your-iot-hub-using-the-amqp-protocol"></a>Se comunicar com o hub IoT usando o protocolo AMQP

Dá suporte ao IoT Hub [AMQP versão 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) para fornecer uma variedade de funcionalidades por meio de pontos de extremidade voltados para o dispositivo e para o serviço. Este documento descreve o uso de clientes AMQP para se conectar ao IoT Hub para usar a funcionalidade do IoT Hub.

## <a name="service-client"></a>Cliente de serviço

### <a name="connection-and-authenticating-to-iot-hub-service-client"></a>Conexão e autenticação no IoT Hub (cliente de serviço)
Para se conectar ao IoT Hub usando o AMQP, um cliente pode usar o [segurança com base em declarações (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) ou [Simple Authentication and Security Layer SASL () de autenticação](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

As seguintes informações são necessárias para o cliente de serviço:

| Informações | Value | 
|-------------|--------------|
| Nome do host do Hub IoT | `<iot-hub-name>.azure-devices.net` |
| Nome da chave | `service` |
| Chave de acesso | Chave primária ou secundária associada ao serviço |
| Assinatura de acesso compartilhado | SAS de curta duração no seguinte formato: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` (o código para gerar essa assinatura pode ser encontrado [aqui](./iot-hub-devguide-security.md#security-token-structure)).


O trecho de código abaixo usa [uAMQP biblioteca em Python](https://github.com/Azure/azure-uamqp-python) para se conectar ao hub IoT por meio de um link do remetente.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # e.g., '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoking-cloud-to-device-messages-service-client"></a>Invocar mensagens de nuvem para o dispositivo (cliente de serviço)
A troca de mensagens de nuvem para dispositivo entre o serviço e o IoT Hub, bem como entre o dispositivo e IoT Hub é descrita [aqui](iot-hub-devguide-messages-c2d.md). O cliente do serviço usa dois links descritos abaixo para enviar mensagens e receba comentários para mensagens enviadas anteriormente de dispositivos.

| Criado por | Tipo de link | Caminho do link | Descrição |
|------------|-----------|-----------|-------------|
| Serviço | Link do remetente | `/messages/devicebound` | Mensagens C2D destinadas a dispositivos são enviadas para este link pelo serviço. As mensagens enviadas por esse link têm suas `To` propriedade definida como o caminho do link de destinatário do dispositivo de destino: ou seja, `/devices/<deviceID>/messages/devicebound`. |
| Serviço | Link receptor | `/messages/serviceBound/feedback` | Mensagens de comentários abandono de conclusão e rejeição provenientes de dispositivos recebidos neste link pelo serviço. Ver [aqui](./iot-hub-devguide-messages-c2d.md#message-feedback) para obter mais informações sobre mensagens de comentários. |

O trecho de código a seguir demonstra como criar uma mensagem C2D e enviá-lo para um dispositivo usando [uAMQP biblioteca em Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the devicebound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message using the send client created and connected IoT Hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if not needed
send_client.close()
```

Para receber comentários, o cliente de serviço cria um link do receptor. O trecho de código a seguir demonstra como fazer isso usando [uAMQP biblioteca em Python](https://github.com/Azure/azure-uamqp-python).

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Recreate the URI using the feedback path above and authenticate
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
  print('received a message')
  # Check content_type in message property to identify feedback messages coming from device
  if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
    msg_body_raw = msg.get_data()
    msg_body_str = ''.join(msg_body_raw)
    msg_body = json.loads(msg_body_str)
    print(json.dumps(msg_body, indent=2))
    print('******************')
    for feedback in msg_body:
      print('feedback received')
      print('\tstatusCode: ' + str(feedback['statusCode']))
      print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
      print('\tdeviceId: ' + str(feedback['deviceId']))
      print
  else:
    print('unknown message:', msg.properties.content_type)
```

Como mostrado acima, uma mensagem de comentários C2D tem o tipo de conteúdo do `application/vnd.microsoft.iothub.feedback.json` e propriedades em seu corpo JSON podem ser usadas para inferir o status de entrega da mensagem original:
* Chave `statusCode` comentários corpo tem um destes valores: `['Success', 'Expired', 'DeliveryCountExceeded', 'Rejected', 'Purged']`.
* Chave `deviceId` comentários corpo tem a ID do dispositivo de destino.
* Chave `originalMessageId` comentários corpo tem a ID da mensagem C2D original enviada pelo serviço. Isso pode ser usado para correlacionar seus comentários para mensagens C2D.

### <a name="receive-telemetry-messages-service-client"></a>Receber mensagens de telemetria (cliente de serviço)


### <a name="additional-notes"></a>Observações adicionais
* As conexões AMQP podem ser interrompidas devido à falha ou expiração do token de autenticação (gerado no código) de rede. O cliente do serviço deve lidar com essas situações e restabelecer a conexão e os links se necessário. Para o caso de expiração do token de autenticação, o cliente também de forma proativa pode renovar o token antes de sua expiração para evitar uma queda de conexão.
* Em alguns casos, o cliente deve ser capaz de manipular corretamente os redirecionamentos de link. Consulte a documentação do cliente AMQP sobre como fazer isso.

### <a name="receive-cloud-to-device-messages-device-and-module-client"></a>Receber mensagens da nuvem para dispositivo (cliente de dispositivo e módulo)
Links do AMQP usados no lado do dispositivo são da seguinte maneira:

| Criado por | Tipo de link | Caminho do link | Descrição |
|------------|-----------|-----------|-------------|
| Dispositivos | Link receptor | `/devices/<deviceID>/messages/devicebound` | Mensagens C2D destinadas a dispositivos são recebidas nesse link por cada dispositivo de destino. |
| Dispositivos | Link do remetente | `/messages/serviceBound/feedback` | Comentários da mensagem C2D enviados ao serviço por esse link pelos dispositivos. |
| Módulos | Link receptor | `/devices/<deviceID>/modules/<moduleID>/messages/devicebound` | Mensagens C2D destinadas aos módulos são recebidas nesse link por cada módulo de destino. |
| Módulos | Link do remetente | `/messages/serviceBound/feedback` | Comentários da mensagem C2D enviados ao serviço por esse link por módulos. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o protocolo AMQP, consulte o [especificação do AMQP v1.0](http://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Para saber mais sobre mensagens do IoT Hub, consulte:

* [Mensagens da nuvem para dispositivo](./iot-hub-devguide-messages-c2d.md)
* [Suporte a protocolos adicionais](iot-hub-protocol-gateway.md)
* [Suporte para o protocolo MQTT](./iot-hub-mqtt-support.md)
