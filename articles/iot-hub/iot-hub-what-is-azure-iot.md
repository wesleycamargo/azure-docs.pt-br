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

O Hub IoT do Azure é um serviço do Azure que recebe telemetria na escala de seus dispositivos e roteia esses dados para um processador de eventos de fluxo. Você pode usar o Hub IoT para implementar seu próprio back-end de solução. O Hub IoT também pode enviar comandos da nuvem para o dispositivo para dispositivos específicos. Além disso, o Hub IoT inclui um registro de identidade do dispositivo que pode ajudar a provisionar dispositivos e gerenciar os dispositivos que podem se conectar ao hub. Para obter mais informações, consulte:

- [O que é o Hub IoT?][lnk-iot-hub]
- [Introdução ao Hub IoT][lnk-getstarted]

Para implementar aplicativos cliente em uma grande variedade de sistemas operacionais e plataformas de hardware do dispositivo, você pode usar os SDKs do dispositivo IoT. Os SDKs do dispositivo IoT incluem bibliotecas que facilitam o envio de telemetria para um hub IoT e o recebimento de comandos da nuvem para o dispositivo. Ao usar os SDKs, você poderá escolher entre vários protocolos de rede para comunicar-se com o Hub IoT. Para saber mais, confira as [informações sobre SDKs de dispositivo][lnk-device-sdks].

Você também pode se interessar pelo [Azure IoT Suite][lnk-iot-suite], que é um conjunto de soluções pré-configuradas. O IoT Suite permite que você comece a trabalhar rapidamente e dimensiona projetos IoT para lidar com cenários comuns de IoT, como o monitoramento remoto, o gerenciamento de ativos e a manutenção preditiva.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_1223_2015-->