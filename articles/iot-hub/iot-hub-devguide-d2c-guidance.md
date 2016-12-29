---
title: "Orientação para comunicações do dispositivo para a nuvem do Hub IoT do Azure | Microsoft Docs"
description: "Guia do desenvolvedor do Hub IoT do Azure - orientação sobre quando usar mensagens do dispositivo para a nuvem, as propriedades relatadas ou carregamento do arquivo."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 53f14e6fe115ed5f96d25b9ec5ab04abe23712d5
ms.openlocfilehash: 6d5608cd86e3f042a7d63bd4e43e75a06141dfab


---
# <a name="device-to-cloud-communications-guidance"></a>Orientação de comunicações do dispositivo para a nuvem
Ao enviar informações do aplicativo do dispositivo para o back-end, o Hub IoT expõe três opções:

* [Mensagens do dispositivo para a nuvem (D2C)][lnk-d2c], para telemetria de série de tempo e alertas;
* [Propriedades relatadas][lnk-twins], para relatar informações de estado do dispositivo, como recursos disponíveis, condições e estado de fluxos de trabalho de longa duração (por exemplo, configuração e atualizações de software);
* [Carregar arquivos][lnk-fileupload], para arquivos de mídia e lotes grandes de telemetria carregados por dispositivos conectados de forma intermitente ou compactados para economizar largura de banda.

Aqui está uma comparação detalhada das várias opções de comunicação do dispositivo para a nuvem.

|  | Mensagens D2C | Propriedades reportadas | Carregamentos de arquivos |
| ---- | ------- | ---------- | ---- |
| Cenário | Série de tempo de telemetria e alertas, por exemplo, lotes de dados de sensor de 256 KB enviados cada cinco minutos. | Recursos disponíveis e condições, por exemplo, modo de conectividade do dispositivo atual (celular ou Wi-Fi). Sincronização dos fluxos de trabalho de longa duração, como atualizações de software e configuração. | Arquivos de mídia. Lotes grandes de telemetria (geralmente compactados). |
| Armazenamento e recuperação | Armazenado temporariamente pelo Hub IoT, até sete dias. Somente leitura sequencial. | Armazenados pelo Hub IoT no dispositivo gêmeo. Recuperáveis usando a [linguagem de consulta do Hub IoT][lnk-query]. | Armazenados na conta de Armazenamento do Azure fornecida pelo usuário. |
| Tamanho | Até 256 KB de mensagens. | O tamanho máximo relatado das propriedades é de 8 KB. | Tamanho máximo de arquivo com suporte pelo Armazenamento de Blobs do Azure. |
| Frequência | Alta. Consulte [Limites do Hub IoT][lnk-quotas] para saber mais. | Média. Consulte [Limites do Hub IoT][lnk-quotas] para saber mais. | Baixa. Consulte [Limites do Hub IoT][lnk-quotas] para saber mais. |
| Protocolo | Disponível em todos os protocolos. | Disponível atualmente somente ao usar MQTT. | Disponível ao usar qualquer protocolo, mas requer HTTP no dispositivo. |

> [!NOTE]
> É possível que um aplicativo exija o envio de informações como uma série de tempo de telemetria ou alerta, e também a disponibilização no dispositivo gêmeo. Nesses casos, o aplicativo do dispositivo pode enviar uma mensagem D2C e relatar uma alteração de propriedade, ou o back-end pode armazenar as informações nas marcas do dispositivo gêmeo quando ele recebe a mensagem. Como as mensagens D2C permitem uma taxa de transferência muito mais alta do que as atualizações do dispositivo gêmeo, às vezes é desejável evitar a atualização do dispositivo gêmeo para cada mensagem D2C.
> 
> 

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages



<!--HONumber=Nov16_HO5-->


