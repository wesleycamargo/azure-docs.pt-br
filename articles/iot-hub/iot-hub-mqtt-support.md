<properties
 pageTitle="Suporte ao MQTT do Hub IoT | Microsoft Azure"
 description="Descrição do suporte ao MQTT no nível do Hub IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# Suporte ao MQTT do Hub IoT

O Hub IoT permite que dispositivos se comuniquem com os pontos de extremidade do dispositivo do Hub IoT usando o protocolo [MQTT v3.1.1][lnk-mqtt-org] a na porta 8883. O Hub IoT exige que todas as comunicações do dispositivo sejam protegidas usando o TLS/SSL.

Para obter informações adicionais, confira [Observações sobre o suporte a MQTT][lnk-mqtt-devguide] no guia do desenvolvedor do Hub IoT do Azure.

## Conectando-se ao Hub IoT

Um dispositivo pode se conectar a um Hub IoT com o protocolo MQTT usando as bibliotecas nos [SDKs do IoT do Microsoft Azure][lnk-device-sdks] ou usando o protocolo MQTT diretamente.

## Usando os SDKs do cliente do dispositivo

Os [SDKs do cliente do dispositivo][lnk-device-sdks] que são compatíveis com o protocolo MQTT estão disponíveis para Java, Node.js, C e C#. Os SDKs do cliente do dispositivo usam a cadeia de conexão do Hub IoT padrão para estabelecer uma conexão com um Hub IoT. Para usar o protocolo MQTT, o parâmetro do protocolo do cliente deve ser definido como **MQTT**. Por padrão, os SDKs do cliente do dispositivo se conectam a um Hub IoT com o sinalizador **CleanSession** definido como **0** e usam **QoS 1** para troca de mensagens com o Hub IoT.

Quando um dispositivo é conectado a um Hub IoT, os SDKs do cliente do dispositivo fornecem métodos que permitem que o dispositivo envie mensagens para um Hub IoT e receba mensagens dele.

A tabela a seguir contém links para exemplos de código de cada linguagem compatível e especifica o parâmetro a ser usado para estabelecer uma conexão com o Hub IoT usando o protocolo MQTT.

| Linguagem | Parâmetro do protocolo |
| -------------------------- | ------------------------- |
| [Node.js][lnk-sample-node] | azure-iot-device-mqtt |
| [Java][lnk-sample-java] | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] | MQTT\_Protocol |
| [C#][lnk-sample-csharp] | TransportType.Mqtt |

## Usando o protocolo MQTT diretamente

Se um dispositivo não puder usar os SDKs do cliente do dispositivo, ele poderá se conectar com os pontos de extremidade públicos do dispositivo usando o protocolo MQTT. No pacote **CONNECT**, o dispositivo deve usar os seguintes valores:

- Para o campo **ClientId** use o **deviceId**. 
- No campo **Username**, use `{iothubhostname}/{device_id}`, em que {iothubhostname} é o CName completo do Hub IoT.

    Por exemplo, se o nome de seu Hub IoT for **contoso.azure devices.net** e se o nome do dispositivo for **MyDevice01**, o campo **Username** completo deverá conter `contoso.azure-devices.net/MyDevice01`.

- Para o campo **Senha** use um token SAS. O [formato do token SAS][lnk-iothub-security] é o mesmo, conforme descrito para os protocolos HTTP e AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`.

    Para saber mais sobre como gerar tokens SAS, confira [Uso de tokens de segurança do Hub IoT][lnk-sas-tokens].
    
    Durante o teste, você também pode usar a ferramenta [Gerenciador de Dispositivo][lnk-device-explorer] para gerar rapidamente um token SAS, que pode copiar e colar em seu próprio código:
    
    1. Acesse a guia **Gerenciamento** no Gerenciador de Dispositivo.
    2. Clique em **Token SAS** (parte superior direita).
    3. Em **SASTokenForm**, selecione seu dispositivo no menu suspenso **DeviceID**. Defina o **TTL**.
    4. Clique em **Gerar** para criar o token.
    
    O token SAS gerado é semelhante ao seguinte: `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

    A parte disso que deve ser usada, como no campo **Senha**, para se conectar usando MQTT é: `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Para que o MQTT conecte e desconecte pacotes, o Hub IoT emite um evento no canal **Monitoramento de Operações**.

### Enviando mensagens ao Hub IoT

Depois de fazer uma conexão bem-sucedida, um dispositivo pode enviar mensagens ao Hub IoT usando `devices/{did}/messages/events/` ou `devices/{did}/messages/events/{property_bag}` como um **Nome de Tópico**. O elemento `{property_bag}` habilita o dispositivo a enviar mensagens com propriedades adicionais em um formato codificado de URL. Por exemplo:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] Essa é a mesma codificação que foi usada para cadeias de caracteres de consulta no protocolo HTTP.

O aplicativo cliente do dispositivo também pode usar `devices/{did}/messages/events/{property_bag}` com o **nome do tópico Will** para definir *mensagens Will* a serem encaminhadas como uma mensagem de telemetria.

### Recebendo mensagens

Para receber mensagens do Hub IoT, um dispositivo deve fazer uma assinatura usando `devices/{did}/messages/devicebound/#”` como um **Filtro de Tópico**. O Hub IoT entrega mensagens com o **Nome do Tópico** `devices/{did}/messages/devicebound/`, ou `devices/{did}/messages/devicebound/{property_bag}` se houver alguma propriedade de mensagem. `{property_bag}` contém pares de chave/valor codificados em URL das propriedades da mensagem. Somente propriedades de aplicativo e propriedades do sistema definível pelo usuário (como **messageId** ou **correlationId**) estão incluídas no recipiente de propriedades. Os nomes de propriedade do sistema têm o prefixo **$**; as propriedades de aplicativo usam o nome da propriedade original sem prefixo.

## Próximas etapas

Para saber mais sobre como usar os SDKs do cliente do dispositivo para se comunicar com o Hub IoT, confira [Introdução ao Hub IoT do Azure][lnk-iot-get-stated].

Para saber mais sobre o protocolo MQTT, confira a [documentação do MQTT][lnk-mqtt-docs].

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-iot-get-stated]: iot-hub-csharp-csharp-getstarted.md
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-iothub-security]: iot-hub-devguide.md#security
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-sas-tokens.md
[lnk-mqtt-devguide]: iot-hub-devguide.md#mqtt-support

<!---HONumber=AcomDC_0406_2016-->