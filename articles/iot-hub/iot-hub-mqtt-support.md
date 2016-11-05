---
title: Suporte ao MQTT do Hub IoT | Microsoft Docs
description: Descrição do suporte ao MQTT no nível do Hub IoT
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: dobett

---
# <a name="iot-hub-mqtt-support"></a>Suporte ao MQTT do Hub IoT
O Hub IoT permite que dispositivos se comuniquem com os pontos de extremidade do dispositivo do Hub IoT usando o protocolo [MQTT v3.1.1][lnk-mqtt-org] na porta 8883. O Hub IoT exige que todas as comunicações do dispositivo sejam protegidas usando o TLS/SSL.

## <a name="connecting-to-iot-hub"></a>Conectando-se ao Hub IoT
Um dispositivo pode usar o protocolo MQTT para se conectar a um hub IoT usando as bibliotecas de [SDKs do IoT do Microsoft Azure][lnk-device-sdks] ou usando o protocolo MQTT diretamente.

## <a name="using-the-device-client-sdks"></a>Usando os SDKs do cliente do dispositivo
Os [SDKs do cliente do dispositivo][lnk-device-sdks] que são compatíveis com o protocolo MQTT estão disponíveis para Java, Node.js, C e C#. Os SDKs do cliente do dispositivo usam a cadeia de conexão do Hub IoT padrão para estabelecer uma conexão com um Hub IoT. Para usar o protocolo MQTT, o parâmetro do protocolo do cliente deve ser definido como **MQTT**. Por padrão, os SDKs do cliente do dispositivo se conectam a um Hub IoT com o sinalizador **CleanSession** definido como **0** e usam **QoS 1** para troca de mensagens com o Hub IoT.

Quando um dispositivo é conectado a um Hub IoT, os SDKs do cliente do dispositivo fornecem métodos que permitem que o dispositivo envie mensagens para um Hub IoT e receba mensagens dele.

A tabela a seguir contém links para exemplos de código de cada linguagem compatível e especifica o parâmetro a ser usado para estabelecer uma conexão com o Hub IoT usando o protocolo MQTT.

| Linguagem | Parâmetro do protocolo |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrate-a-device-app-from-amqp-to-mqtt"></a>Migrar um aplicativo de dispositivo de AMQP para MQTT
Se você estiver usando o [SDKs de cliente de dispositivo][lnk-device-sdks], a mudança do uso de AMQP para MQTT exige alteração do parâmetro de protocolo na inicialização do cliente, conforme mencionado acima.

Ao fazer isso, verifique os seguintes itens:

* AMQP retorna erros para várias condições, enquanto MQTT encerra a conexão. Como resultado, sua lógica de manipulação de exceções pode exigir algumas alterações.
* MQTT não oferece suporte a *rejeitar* operações quando recebe [mensagens C2D][lnk-messaging]. Se o back-end precisar receber uma resposta do aplicativo do dispositivo, considere o uso de [métodos diretos][lnk-methods].
* Neste momento, MQTT não está disponível por meio de WebSockets.

## <a name="using-the-mqtt-protocol-directly"></a>Usando o protocolo MQTT diretamente
Se um dispositivo não puder usar os SDKs do cliente do dispositivo, ele poderá se conectar com os pontos de extremidade públicos do dispositivo usando o protocolo MQTT. No pacote **CONNECT** , o dispositivo deve usar os seguintes valores:

* No campo **ClientId**, use o **deviceId**. 
* No campo **Username**, use `{iothubhostname}/{device_id}`, em que {iothubhostname} é o CName completo do Hub IoT.
  
    Por exemplo, se o nome de seu Hub IoT for **contoso.azure-devices.net** e se o nome do dispositivo for **MyDevice01**, o campo **Username** completo deverá conter `contoso.azure-devices.net/MyDevice01`.
* No campo **Senha** use um token SAS. O formato do token SAS é o mesmo, conforme descrito para os protocolos HTTP e AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.
  
    Para saber mais sobre como gerar tokens SAS, confira a seção de dispositivo [Usar tokens de segurança do Hub IoT][lnk-sas-tokens].
  
    Durante o teste, você também pode usar a ferramenta [Gerenciador de Dispositivo][lnk-device-explorer] para gerar rapidamente um token SAS que pode ser copiado e colado em seu próprio código:
  
  1. Acesse a guia **Gerenciamento** no Gerenciador de Dispositivo.
  2. Clique em **Token SAS** (parte superior direita).
  3. Em **SASTokenForm**, selecione seu dispositivo no menu suspenso **DeviceID**. Defina o **TTL**.
  4. Clique em **Gerar** para criar o token.
     
     O token SAS gerado tem esta estrutura:   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.
     
     A parte disso que deve ser usada, como no campo **Senha**, para se conectar usando MQTT é:   `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Para que o MQTT conecte e desconecte pacotes, o Hub IoT emite um evento no canal **Monitoramento de Operações** .

### <a name="sending-messages-to-iot-hub"></a>Enviando mensagens ao Hub IoT
Depois de fazer uma conexão bem-sucedida, um dispositivo pode enviar mensagens ao IoT Hub usando `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` como um **nome de tópico**. O elemento `{property_bag}` habilita o dispositivo a enviar mensagens com propriedades adicionais em um formato codificado de URL. Por exemplo:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Esse elemento `{property_bag}` usa a mesma codificação para cadeias de caracteres de consulta do protocolo HTTP.
> 
> 

O aplicativo cliente do dispositivo também pode usar `devices/{device_id}/messages/events/{property_bag}` como o **nome do tópico Will** para definir *mensagens Will* a serem encaminhadas como uma mensagem de telemetria.

### <a name="receiving-messages"></a>Recebendo mensagens
Para receber mensagens do Hub IoT, um dispositivo deve fazer uma assinatura usando `devices/{device_id}/messages/devicebound/#”` como um **Filtro do Tópico**. IoT Hub entrega mensagens com o **Nome do Tópico** `devices/{device_id}/messages/devicebound/` ou `devices/{device_id}/messages/devicebound/{property_bag}` se houver propriedades de mensagem. `{property_bag}` contém pares de chave/valor codificados de URL das propriedades da mensagem. Somente propriedades de aplicativo e propriedades do sistema definível pelo usuário (como **messageId** ou **correlationId**) estão incluídas no recipiente de propriedades. Os nomes de propriedade do sistema têm o prefixo **$**; as propriedades de aplicativo usam o nome da propriedade original sem prefixo.

## <a name="next-steps"></a>Próximas etapas
Para obter informações adicionais, confira [Observações sobre o suporte a MQTT][lnk-mqtt-devguide] na guia do desenvolvedor do Hub IoT do Azure.

Para saber mais sobre o protocolo MQTT, consulte a [documentação do MQTT][lnk-mqtt-docs].

Para saber mais sobre como planejar sua implantação do Hub IoT, consulte:

* [Dispositivos com suporte][lnk-devices]
* [Suporte a protocolos adicionais][lnk-protocols]
* [Comparar com Hubs de Eventos][lnk-compare]
* [Escala, alta disponibilidade e recuperação de desastre][lnk-scaling]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia de desenvolvedor][lnk-devguide]
* [Simular um dispositivo com o SDK do Gateway][lnk-gateway]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-devguide-security.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support

[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md



<!--HONumber=Oct16_HO2-->


