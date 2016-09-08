<properties
 pageTitle="Informações de Hub IoT para os profissionais de TI | Microsoft Azure"
 description="Informações para ajudar os profissionais de TI a trabalhar com o Hub IoT do Azure como requisitos de porta e fundo de segurança."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/09/2016"
 ms.author="dobett"/>

# Configurando e gerenciando o acesso ao Hub IoT

Este artigo fornece informações para ajudar os profissionais de TI a configurar um ambiente no qual os dispositivos de IoT se comunicam com o Hub IoT em uma infraestrutura de rede.

## Conectividade de rede

Os dispositivos podem se comunicar com o Hub IoT no Azure usando uma variedade de protocolos. Normalmente, a opção de protocolo é orientada por requisitos específicos da solução. A tabela a seguir lista as portas de saída que devem ser abertas para um dispositivo para poder usar um protocolo específico:

| Protocolo | Porta(s) |
| -------- | ------- |
| HTTPS | 443 |
| AMQP | 5671 |
| AMQP sobre WebSockets | 443 |
| MQTT | 8883 |
| LWM2M (Gerenciamento de dispositivo) | 5684 |

Depois de criar um hub IoT em uma região do Azure, o hub manterá o mesmo endereço IP durante a vida útil desse hub. No entanto, para manter a qualidade do serviço, se a Microsoft mover o Hub IoT para uma unidade de escala diferente, ele receberá um novo endereço IP.

## Hub IoT e segurança

Somente os dispositivos registrados com um hub IoT têm permissão para se comunicar com esse hub IoT. Um dispositivo registrado deve ser concedido à permissão *DeviceConnect*. Um dispositivo se identifica incluindo um token que encapsula a ID exclusiva do dispositivo em cada solicitação efetuada. Em seguida, o hub verifica a validade do token e se o dispositivo não está numa lista de negação (permissão *DeviceConnect* revogada). Para obter informações sobre os tokens compatíveis com o Hub IoT, confira [Usar tokens de segurança do Hub IoT e certificados X.509][lnk-tokens]

O acesso a outros pontos de extremidade de gerenciamento em um hub IoT também é controlado por meio de um conjunto de permissões: *iothubowner*, *serviço*, *registryRead*, e *registryReadWrite*. Qualquer aplicativo de gerenciamento de cliente que se conecta a um hub IoT deve incluir um token com as permissões apropriadas.

## Próximas etapas

Este artigo contém informações específicas para os profissionais de TI e desenvolvedores para configurar seus ambientes de desenvolvimento e teste. A [seção Segurança no guia do desenvolvedor do Hub IoT do Azure][lnk-devguide] fornece informações adicionais sobre os tokens e o sistema de permissão no Hub IoT.

Para explorar melhor as funcionalidades do Hub IoT, consulte:

- [Projetar sua solução][lnk-design]
- [Guia do desenvolvedor][lnk-devguide]
- [Explorar o gerenciamento de dispositivo usando a interface do usuário de exemplo][lnk-dmui]
- [Simular um dispositivo com o SDK do Gateway][lnk-gateway]

[lnk-devguide]: iot-hub-devguide.md#security

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-tokens]: iot-hub-sas-tokens.md

<!---HONumber=AcomDC_0824_2016-->