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
 ms.date="04/29/2016"
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

Depois de criar um hub IoT em uma região do Azure, o hub manterá o mesmo endereço IP para o tempo de vida desse hub. No entanto, para manter a qualidade do serviço, se a Microsoft mover o Hub IoT para uma unidade de escala diferente, ele receberá um novo endereço IP.

## Hub IoT e segurança

Somente os dispositivos registrados com um hub IoT têm permissão para se comunicar com esse hub IoT. Um dispositivo registrado deve ser concedido à permissão *DeviceConnect*. Um dispositivo identifica incluindo um token que encapsula a ID exclusiva de dispositivos em cada solicitação que faz, e o hub verifica a validade do token e se o dispositivo não está na lista negra (*DeviceConnect* permissão revogada).

O acesso a outros pontos de extremidade de gerenciamento em um hub IoT também é controlado por meio de um conjunto de permissões: *iothubowner*, *serviço*, *registryRead*, e *registryReadWrite*. Qualquer aplicativo de gerenciamento de cliente que se conecta a um hub IoT deve incluir um token com as permissões apropriadas.

## Próximas etapas

Este artigo contém informações específicas para os profissionais de TI e desenvolvedores para configurar seus ambientes de desenvolvimento e teste. Siga estes links para saber mais sobre o serviço do Hub IoT do Azure:

- [O que é o Hub IoT do Azure?][lnk-iothub]
- A [seção Segurança no guia do desenvolvedor do Hub IoT do Azure][lnk-devguide] fornece informações adicionais sobre os tokens e o sistema de permissão no Hub IoT.
- [Gerenciar o Hub IoT pelo portal do Azure][lnk-manage-portal] descreve como usar o portal do Azure para gerenciar seu hub IoT.

[lnk-iothub]: iot-hub-what-is-iot-hub.md
[lnk-devguide]: iot-hub-devguide.md#security
[lnk-manage-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0504_2016-->