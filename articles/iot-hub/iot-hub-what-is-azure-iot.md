<properties
 pageTitle="Soluções do Azure para a Internet das coisas | Microsoft Azure"
 description="Visão geral de IoT no Azure, incluindo uma arquitetura da solução do exemplo e como se relaciona com os Hub IoT do Azure, SDKs do dispositivo e soluções pré-configuradas"
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
 ms.date="11/05/2015"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-e-iot](../../includes/iot-azure-and-iot.md)]

## Próximas etapas

Você pode usar o Hub IoT do Azure, um serviço do Azure que recebe a telemetria em escala de seus dispositivos e realiza o roteamento desses dados a um processador de eventos de fluxo, para implementar sua própria solução de back-end. O Hub IoT também pode enviar comandos do tipo “da nuvem para o dispositivo” para dispositivos específicos. Além disso, o Hub IoT inclui um registro de identidade do dispositivo que pode ajudar a provisionar dispositivos e gerenciar os dispositivos que podem se conectar ao hub. Para obter mais informações, consulte:

- [O que é o Hub IoT?][lnk-iot-hub]
- [Introdução ao Hub IoT][lnk-getstarted]

Para implementar aplicativos cliente em uma grande variedade de sistemas operacionais e plataformas de hardware do dispositivo, você pode usar os SDKs do dispositivo IoT. Os SDKs do dispositivo IoT incluem bibliotecas que facilitam o envio de telemetria para um hub IoT e o recebimento de comandos do tipo “nuvem para o dispositivo”. Ao usar os SDKs, você pode escolher entre vários protocolos de rede para comunicar-se com o Hub IoT. Para saber mais, consulte [O que são SDKs de dispositivo?][lnk-device-sdks].

Você também pode estar interessado no [Azure IoT Suite][lnk-iot-suite], um conjunto de soluções pré-configuradas que permitem que você se familiarize rapidamente e dimensione os projetos de IoT para tratar de cenários de IoT comuns como monitoramento remoto, gerenciamento de ativos e manutenção preventiva.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=Nov15_HO3-->