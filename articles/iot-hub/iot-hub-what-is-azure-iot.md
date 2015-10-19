<properties
 pageTitle="Hub IoT da Microsoft Azure e a Internet das coisas (IoT) | Microsoft Azure"
 description="Visão geral de IoT no Azure, incluindo uma arquitetura da solução do exemplo e como se relaciona com os Hub IoT do Azure, SDKs do dispositivo e soluções pré-configuradas"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-e-iot](../../includes/iot-azure-and-iot.md)]

## Escopo desta documentação

Esses artigos do Azure e IoT se concentram em dois conjuntos de recursos que podem ajudá-lo a implementar sua própria solução IoT baseada na plataforma Microsoft IoT.

- Hub IoT do Azure
- SDKs do dispositivo IoT do Azure

Você também pode estar interessado em [Azure IoT Suite][lnk-iot-suite], um conjunto de soluções pré-configuradas que permitem que você se familiarize rapidamente e dimensione os projetos de IoT para atribuir cenários de IoT comuns como monitoramento remoto, gerenciamento de ativo e manutenção preventiva.

### Hub IoT do Azure

O Hub IoT é um serviço do Azure que permite que você receba dados do dispositivo para a nuvem na escala de seus dispositivos e rotei esses dados para um processador de eventos de fluxo. O Hub IoT também pode enviar comandos da nuvem para o dispositivo para dispositivos específicos usando filas específicas do dispositivo.

Além disso, o serviço de HuB IoT inclui um Registro de identidade do dispositivo que ajuda a provisionar dispositivos e gerenciar os dispositivos que podem se conectar a um Hub IoT.

### SDKs do dispositivo IoT do Azure

A Microsoft fornece SDKs de dispositivo IoT que permitem implementar aplicativos cliente sejam executados em uma grande variedade de sistemas operacionais e plataformas de hardware do dispositivo. Os SDKs do dispositivo IoT incluem bibliotecas que facilitam enviar dados de telemetria do dispositivo para nuvem do Hub IoT e comandos de nuvem para o dispositivo de recebimento de Hub IoT. Esses SDKs de dispositivo IoT permitem que você escolha entre uma série de protocolos de rede diferentes para se comunicar com o Hub IoT da Azure.

## Próximas etapas

Para começar com o IoT no Azure, explore esses recursos:

- [Introdução ao Hub IoT][lnk-getstarted].
- [Hub IoT do Azure][lnk-iot-hub].
- [Azure IoT Suite][lnk-iot-suite].  


[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/

<!---HONumber=Oct15_HO2-->