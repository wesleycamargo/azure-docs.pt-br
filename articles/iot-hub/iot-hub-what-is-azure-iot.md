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
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 00b2e90901d763d218d1118f1d7ef2bf617a2d2f


---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Próximas etapas
O Hub IoT do Azure é um serviço do Azure que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de aplicativo. Ele permite que o aplicativo back-end:

* Receba telemetria em escala de seus dispositivos.
* Encaminhe os dados de seus dispositivos para um processador de eventos de fluxo.
* Receba carregamentos de arquivos de dispositivos.
* Envie comandos da nuvem para o dispositivo para dispositivos específicos.

Você pode usar o Hub IoT para implementar seu próprio back-end de solução. Além disso, o Hub IoT inclui um Registro de identidade usado para provisionar dispositivos, suas credenciais de segurança e os direitos de conexão ao Hub IoT. Para saber mais sobre o Hub IoT, confira [O que é o Hub IoT?][lnk-iot-hub].

Para saber como o Hub IoT do Azure permite o gerenciamento de dispositivos baseado em padrões para gerenciar, configurar e atualizar os dispositivos remotamente, consulte [Visão geral do gerenciamento de dispositivos com o Hub IoT][lnk-device-management].

Para implementar aplicativos cliente em uma grande variedade de sistemas operacionais e plataformas de hardware do dispositivo, você pode usar os SDKs do dispositivo IoT do Azure. Os SDKs do dispositivo incluem bibliotecas que facilitam o envio de telemetria para um hub IoT e o recebimento de comandos da nuvem para o dispositivo. Ao usar os SDKs de dispositivo, você poderá escolher entre vários protocolos de rede para comunicar-se com o Hub IoT. Para saber mais, confira as [informações sobre SDKs de dispositivo][lnk-device-sdks].

Para começar a escrever código e executar alguns exemplos, confira o tutorial [Introdução ao Hub IoT][lnk-getstarted].

Você também pode se interessar pelo [Azure IoT Suite][lnk-iot-suite], que é um conjunto de soluções pré-configuradas. O IoT Suite permite que você comece a trabalhar rapidamente e dimensiona projetos IoT para lidar com cenários comuns de IoT, como o monitoramento remoto, o gerenciamento de ativos e a manutenção preditiva.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md



<!--HONumber=Nov16_HO5-->


