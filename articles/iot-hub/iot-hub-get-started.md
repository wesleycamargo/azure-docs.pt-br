---
title: "Hub IoT do Azure – introdução à conexão de dispositivos IoT à nuvem | Microsoft Docs"
description: Saiba como conectar dispositivos IoT ao Hub IoT do Azure. Os dispositivos podem enviar telemetria ao Hub IoT e o Hub IoT pode monitorar e gerenciar seus dispositivos.
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: tutorial do hub iot do azure
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: aeb0b665b8295bba30d8c6c47cc88e446693c91f
ms.contentlocale: pt-br
ms.lasthandoff: 05/01/2017


---
# <a name="azure-iot-hub-get-started-tutorials"></a>Tutoriais de introdução ao Hub IoT do Azure

Você pode usar o Hub IoT do Azure e os SDKs do dispositivo IoT do Azure para criar soluções de IoT (Internet das Coisas).

* O Hub IoT do Azure é um serviço completamente gerenciado na nuvem que se conecta com segurança aos seus dispositivos IoT, monitorando-os e gerenciando-os. Use os SDKs do dispositivo IoT do Azure para implementar os dispositivos IoT.
* Use um gateway IoT em cenários mais complexos de IoT, em que você precise considerar fatores como dispositivos herdados, custos de largura de banda, políticas de segurança e privacidade ou processamento de dados de borda. Nesses cenários, use o SDK do Gateway do Azure IoT para criar um dispositivo de gateway que conecte dispositivos ao Hub IoT.

## <a name="what-do-the-tutorials-cover"></a>O que os tutoriais abordam

Estes tutoriais apresentam a você o Hub IoT do Azure e os SDKs do dispositivo. Os tutoriais abordam os cenários comuns de IoT para demonstrar os recursos do Hub IoT. Os tutoriais também ilustram como combinar o Hub IoT com outros serviços e ferramentas do Azure para criar soluções de IoT mais eficazes. Nos tutoriais, você pode optar por usar dispositivos IoT simulados ou reais. Além disso, você pode aprender como usar um gateway de modo a habilitar dispositivos para conectar ao seu Hub IoT.

## <a name="device-setup-scenario-connect-iot-device-or-gateway-to-azure-iot-hub"></a>Cenário de configuração de dispositivo: conectar o dispositivo IoT ou gateway ao Hub IoT do Azure

Você pode escolher o dispositivo real ou simulado para começar.

| Dispositivo IoT                       | Linguagem de programação |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd], [C][Pi_C]           |
| Intel Edison                    | [Node.js][Ed_Nd], [C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |
| Dispositivo simulado                | [.NET][Sim_NET], [Java][Sim_Jav], [Node.js][Sim_Nd], [Python][Sim_Pyth]              |

Além disso, você pode usar um gateway de modo a habilitar dispositivos para conectar ao seu Hub IoT.

| Dispositivos de gateway               | Linguagem de programação | Plataforma         |
|------------------------------|----------------------|------------------|
| Intel NUC (modelo DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |
| Gateway simulado            | C                    | [Linux][Sim_Lnx], [Windows][Sim_Win] |

## <a name="extended-iot-scenarios-use-other-azure-services-and-tools"></a>Cenários de IoT estendidos: usar outros serviços e ferramentas do Azure

Depois de conectar o dispositivo ao Hub IoT, você poderá explorar cenários adicionais que usam outros serviços e ferramentas do Azure:

| Cenário                                    | Serviço ou ferramenta do Azure              |
|---------------------------------------------|------------------------------------|
| [Gerenciar mensagens do Hub IoT][Mg_IoT_Hub_Msg]                    | ferramenta iothub-explorer               |
| [Gerenciar seu dispositivo IoT][Mg_IoT_Dv]               | ferramenta iothub-explorer               |
| [Salvar mensagens do Hub IoT no armazenamento do Azure][Sv_IoT_Msg_Stor]                      | Armazenamento de tabelas do Azure               |
| [Visualizar dados do sensor][Vis_Data]             | Microsoft Power BI, Aplicativos Web do Azure |
| [Previsão meteorológica com dados de sensor][Weather_Forecast] | Azure Machine Learning             |
| [Detecção de anomalias e reação automáticas][Anomaly_Detect]    | Aplicativo Lógico do Azure                   |

## <a name="next-steps"></a>Próximas etapas

Após a conclusão desses tutoriais, você poderá explorar mais os recursos do Hub IoT no [Guia do desenvolvedor][lnk-dev-guide]. Você encontra mais tutoriais na seção de [instruções][lnk-how-to].


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-gateway-sdk-get-started.md
[Sim_Win]: iot-hub-windows-gateway-sdk-get-started.md
[Mg_IoT_Hub_Msg]: iot-hub-explorer-cloud-device-messaging.md
[Mg_IoT_Dv]: iot-hub-device-management-iothub-explorer.md
[Sv_IoT_Msg_Stor]: iot-hub-store-data-in-azure-table-storage.md
[Vis_Data]: iot-hub-live-data-visualization-in-power-bi.md
[Weather_Forecast]: iot-hub-weather-forecast-machine-learning.md
[Anomaly_Detect]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
[lnk-dev-guide]: iot-hub-devguide.md
[lnk-how-to]: iot-hub-how-to.md
