---
title: "Instruções para o Hub IoT do Azure | Microsoft Docs"
description: "Como desenvolvedor, como usar os vários recursos do Hub IoT?"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 6fbdebd316cd00e7dd762487848e616fdd2317e8
ms.openlocfilehash: 358fd0888049f97e5fde6e2a6303ea6c4cf65da9
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-use-azure-iot-hub"></a>Como usar o Hub IoT do Azure

Você tem várias opções para aprender a desenvolver para o serviço do Hub IoT. Você pode ler os artigos conceituais que descrevem detalhadamente os recursos do Hub IoT ou seguir um dos tutoriais que abordam os vários recursos do Hub IoT.

## <a name="the-developer-guide"></a>O guia do desenvolvedor

Como desenvolvedor, você pode ler diretrizes conceituais detalhadas sobre o Hub IoT no [Guia do Desenvolvedor][lnk-devguide]. Este guia inclui descrições detalhadas de todos os recursos do Hub IoT que o ajudam a aprender a usá-los e a escolher entre eles quando há várias opções disponíveis


## <a name="the-tutorials"></a>Os tutoriais

Se você prefere aprender sobre recursos específicos do Hub IoT trabalhando em exercícios práticos, há vários tutoriais para escolher. Muitos desses tutoriais estão disponíveis em várias linguagens de programação. Os tutoriais incluem:

- [Processar mensagens do dispositivo para nuvem do Hub IoT usando rotas][lnk-routes-tutorial]. Este tutorial mostra como usar regras de roteamento do Hub IoT para enviar mensagens de dispositivo à nuvem de maneira fácil, com base em configuração.

- [Enviar mensagens da nuvem para o dispositivo com o Hub IoT][lnk-c2d-tutorial]. Este tutorial mostra como enviar mensagens de nuvem ao dispositivo por meio do Hub IoT e receber mensagens da nuvem para um dispositivo.

- [Carregar arquivos de dispositivos para a nuvem com o Hub IoT][lnk-upload-tutorial]. Este tutorial mostra como usar os recursos de carregamento de arquivo do Hub IoT.

- [Introdução a dispositivos gêmeos][lnk-twin-tutorial]. Este tutorial apresenta gêmeos de dispositivos, propriedades relatadas, propriedades desejadas e marcas. Você usa gêmeos de dispositivo para sincronizar valores com os dispositivos.

- [Usar métodos diretos][lnk-methods-tutorial]. Este tutorial mostra como usar métodos diretos. Você adiciona um manipulador a um método direto no dispositivo simulado e invoca o método direto do Hub IoT.

- [Introdução ao gerenciamento de dispositivos][lnk-dm-tutorial]. Este tutorial mostra como usar recursos-chave de gerenciamento de dispositivos, como gêmeos e métodos diretos, para reiniciar remotamente o dispositivo simulado.

- [Usar as propriedades desejadas para configurar os dispositivos][lnk-properties-tutorial]. Este tutorial mostra como usar propriedades desejadas do gêmeo do dispositivo, juntamente com propriedades relatadas, para configurar o dispositivo remotamente.

- [Usar trabalhos de dispositivo para iniciar uma atualização de firmware do dispositivo][lnk-jobs-tutorial]. Este tutorial mostra como usar recursos-chave de gerenciamento de dispositivos, como gêmeos e métodos diretos, para atualizar remotamente o firmware do dispositivo.

- [Agendar e transmitir trabalhos][lnk-schedule-tutorial]. Este tutorial mostra como usar propriedades desejadas e métodos diretos para interagir com vários dispositivos em um horário agendado.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o serviço do Hub IoT, confira o [Guia do Desenvolvedor][lnk-devguide].

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-routes-tutorial]: ./iot-hub-csharp-csharp-process-d2c.md
[lnk-c2d-tutorial]: ./iot-hub-csharp-csharp-c2d.md
[lnk-upload-tutorial]: ./iot-hub-csharp-csharp-file-upload.md
[lnk-twin-tutorial]: ./iot-hub-node-node-twin-getstarted.md
[lnk-methods-tutorial]: ./iot-hub-node-node-direct-methods.md
[lnk-dm-tutorial]: ./iot-hub-node-node-device-management-get-started.md
[lnk-properties-tutorial]: ./iot-hub-node-node-twin-how-to-configure.md
[lnk-jobs-tutorial]: ./iot-hub-node-node-firmware-update.md
[lnk-schedule-tutorial]: ./iot-hub-node-node-schedule-jobs.md
