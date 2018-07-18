---
title: Conectividade de dispositivo no Azure IoT Central | Microsoft Docs
description: Este artigo apresenta os principais conceitos relacionados à conectividade de dispositivo no Azure IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 00b621a4635ef1ceda26772ac5876fa2599b56f8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202638"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Conectividade de dispositivo no Azure IoT Central | Microsoft Docs

Este artigo apresenta os principais conceitos relacionados à conectividade de dispositivo no Microsoft Azure IoT Central.

Cada dispositivo que conecta ao aplicativo Azure IoT Central conecta aos [pontos de extremidades](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints) expostos pelo Hub IoT que o Azure IoT Central usa. O Hub IoT permite conexões escalonáveis, seguras e confiáveis dos dispositivos conectados.

## <a name="sdk-support"></a>Suporte a SDK

Os SDKs do Dispositivo do Azure oferecem a maneira mais fácil para implementar o código nos dispositivos que conectam ao aplicativo Azure IoT Central. Estão disponíveis os SDKs do dispositivo a seguir:

- [SDK do IoT do Azure para C](https://github.com/azure/azure-iot-sdk-c)
- [SDK do IoT do Azure para Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK do IoT do Azure para Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK do IoT do Azure para Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK do IoT do Azure para .NET](https://github.com/azure/azure-iot-sdk-csharp)

Cada dispositivo conecta usando uma cadeia de conexão única que identifica o dispositivo. Um dispositivo somente poderá conectar ao Hub IoT no qual estiver registrado. Ao criar um dispositivo real no aplicativo Azure IoT Central, o aplicativo gera uma cadeia de conexão a ser usada.

## <a name="sdk-features-and-iot-hub-connectivity"></a>Recursos de SDK e conectividade do Hub IoT

Toda a comunicação do dispositivo com o Hub IoT usa as opções de conectividade do Hub IoT a seguir:

- [Mensagens de dispositivo para nuvem](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Dispositivos gêmeos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

A tabela a seguir resume como os recursos do dispositivo Azure IoT Central são mapeados para os recursos do Hub IoT:

| Azure IoT Central | Hub IoT do Azure |
| ----------- | ------- |
| Medida: Telemetria | Mensagens do dispositivo para a nuvem |
| Propriedades do dispositivo | Propriedades relatadas do dispositivo gêmeo |
| Configurações | Propriedades desejadas e relatadas do dispositivo gêmeo |

Para saber mais sobre como usar os SDKs do Dispositivo, consulte um dos artigos a seguir para exemplo de código:

- [Conectar um cliente Node.js genérico ao aplicativo Azure IoT Central](howto-connect-nodejs.md)
- [Conectar um dispositivo Raspberry Pi ao aplicativo Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Conectar um dispositivo do kit de DevDiv ao aplicativo Azure IoT Central](howto-connect-devkit.md).

O vídeo a seguir apresenta uma visão geral de como conectar um dispositivo real ao Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Connect-real-devices-to-Microsoft-IoT-Central/Player]

## <a name="protocols"></a>Protocolos

Os SDKs do Dispositivo dão suporte aos protocolos de rede a seguir para conectar um Hub IoT:

- MQTT
- AMQP
- HTTPS

Para obter informações sobre esses diferentes protocolos e as diretrizes sobre como escolher um deles, consulte [Escolher um protocolo de comunicação](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Se o dispositivo não puder usar nenhum dos protocolos com suporte, será possível usar o Azure IoT Edge para fazer a conversão de protocolo. O IoT Edge dá suporte a outros cenários de inteligência na borda para transferir o processamento para a borda do aplicativo Azure IoT Central.

## <a name="security"></a>Segurança

Todos os dados trocados entre dispositivos e o Azure IoT Central são criptografados. O Hub IoT autentica todas as solicitações de um dispositivo que conecta a qualquer ponto de extremidade do Hub IoT voltado para o dispositivo. Para evitar a troca de credenciais pela rede, um dispositivo usa tokens assinados para autenticar. Para obter mais informações, consulte [Controlar o acesso ao Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> Atualmente, os dispositivos que conectam ao Azure IoT Central devem usar tokens de SAS. Certificados X.509 não têm suporte para dispositivos que conectam ao Azure IoT Central.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre a conectividade de dispositivo no Azure IoT Central, consulte as etapas a seguir sugeridas:

- [Preparar e conectar um dispositivo DevKit](howto-connect-devkit.md)
- [Preparar e conectar um Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Conectar um cliente Node.js genérico ao aplicativo Azure IoT Central](howto-connect-nodejs.md)
