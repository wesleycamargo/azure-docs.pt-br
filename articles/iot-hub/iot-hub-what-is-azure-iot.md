---
title: "Soluções do Azure para a Internet das coisas | Microsoft Docs"
description: "Visão geral de IoT no Azure, incluindo uma arquitetura da solução do exemplo e como se relaciona com os Hub IoT do Azure, SDKs do dispositivo e soluções pré-configuradas"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2dbf639abfa505eb329769bcc346efb5f1db443e


---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Próximas etapas
O Hub IoT do Azure é um serviço do Azure que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de aplicativo. Ele permite que o aplicativo back-end:

* Receba telemetria em escala de seus dispositivos.
* Encaminhe os dados de seus dispositivos para um processador de eventos de fluxo.
* Receba carregamentos de arquivos de dispositivos.
* Envie comandos da nuvem para o dispositivo para dispositivos específicos.

Você pode usar o Hub IoT para implementar seu próprio back-end de solução. Além disso, o Hub IoT inclui um registro de identidade do dispositivo usado para provisionar dispositivos, suas credenciais de segurança e os direitos de conexão ao hub. Para saber mais sobre o Hub IoT, confira [O que é o Hub IoT?][lnk-iot-hub].

Para saber como o Hub IoT do Azure permite o gerenciamento de dispositivos baseado em padrões para gerenciar, configurar e atualizar os dispositivos remotamente, consulte [Visão geral do gerenciamento de dispositivos com o Hub IoT][lnk-device-management].

Para implementar aplicativos cliente em uma grande variedade de sistemas operacionais e plataformas de hardware do dispositivo, você pode usar os SDKs do dispositivo IoT. Os SDKs do dispositivo IoT incluem bibliotecas que facilitam o envio de telemetria para um hub IoT e o recebimento de comandos da nuvem para o dispositivo. Ao usar os SDKs, você poderá escolher entre vários protocolos de rede para comunicar-se com o Hub IoT. Para saber mais, consulte as [informações sobre os SDKs do dispositivo][lnk-device-sdks].

Para começar a escrever algum código e executar alguns exemplos, consulte o tutorial [Introdução ao Hub IoT][lnk-getstarted].

Você também pode se interessar pelo [Azure IoT Suite][lnk-iot-suite], que é um conjunto de soluções pré-configuradas. O IoT Suite permite que você comece a trabalhar rapidamente e dimensiona projetos IoT para lidar com cenários comuns de IoT, como o monitoramento remoto, o gerenciamento de ativos e a manutenção preditiva.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md



<!--HONumber=Nov16_HO2-->


