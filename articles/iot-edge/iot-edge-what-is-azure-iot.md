---
title: "Soluções do Azure para a Internet das Coisas (IoT Edge) | Microsoft Docs"
description: "Visão geral de uma arquitetura de solução de IoT de exemplo e como ela se relaciona com dispositivos, o serviço de Hub IoT do Azure, SDKs de serviço IoT do Azure e outros serviços do Azure."
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
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 587b733106d511ec63d71f67a06e520324a3e594
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Próximas etapas

O Azure IoT Edge é um serviço do Azure que permite a análise e processamento de dados no Edge. Com o IoT Edge, você pode capacitar seus dispositivos com um código de contêiner que inclui a lógica extraída diretamente de serviços do Azure que você já usa ou seu próprio código de solução específica. Ela permite que os dispositivos:

* Atuem como dispositivos de gateway, agregando e processando dados de vários dispositivos folha.
* Executar a detecção de anomalias e reagir a alterações no ambiente sem a necessidade de aguardar instruções da nuvem.
* Minimizar o custo de armazenamento e a largura de banda de pré-processamento de dados e enviar os resultados. 

O IoT Edge também inclui uma interface de nuvem que permite o gerenciamento remoto de dispositivos. Sem a necessidade de acessar fisicamente seus dispositivos, implante o código, monitore o status e o atualize. É possível gerenciar remotamente dispositivos únicos ou criar implantações que afetem grandes conjuntos de dispositivos definidos por você. Para saber mais, veja [Noções básicas sobre implantações do IoT Edge para dispositivos únicos ou em escala][lnk-deployment].

Para saber mais sobre os componentes que habilitam o IoT Edge, veja [Como o Azure IoT Edge funciona][lnk-overview].

Se você não tiver usado o Azure IoT Hub antes, poderá começar com uma [Visão geral do serviço Azure IoT Hub][lnk-iot-hub].

[lnk-deployment]: module-deployment-monitoring.md
[lnk-overview]: how-iot-edge-works.md
[lnk-iot-hub]: ../iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
