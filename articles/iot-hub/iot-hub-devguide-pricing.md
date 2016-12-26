---
title: "Guia do desenvolvedor -Exemplo de preços | Microsoft Docs"
description: "Guia do desenvolvedor do Hub IoT do Azure - Informações e links para os vários SDKs de serviço e de dispositivo do Azure IoT."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 8b025872acd715878dab82e3fa02f0367f71420d


---

# <a name="pricing-information"></a>Informações sobre preço

O [Preço do Hub IoT do Azure][lnk-pricing] fornece as informações gerais sobre SKUs e preços diferentes para o Hub IoT. Este artigo contém detalhes adicionais sobre como as diversas funcionalidades do Hub IoT são medidas como mensagens pelo Hub IoT.

## <a name="charges-per-operation"></a>Encargos por operação

| Operação | Informações de cobrança | 
| --------- | ------------------- |
| Operações de registro de identidade <br/> (criar, recuperar, listar, atualizar e excluir) | Não será cobrado. |
| Mensagens do dispositivo para a nuvem | Mensagens enviadas com êxito são cobradas em blocos de 4 KB, por exemplo, uma mensagem de 6 KB é cobrada como 2 mensagens. |
| Mensagens da nuvem para o dispositivo | Mensagens enviadas com êxito são cobradas em blocos de 4 KB, por exemplo, uma mensagem de 6 KB é cobrada como 2 mensagens. |
| Carregamentos de arquivos | A transferência de arquivos para o Armazenamento do Azure não é limitada pelo Hub IoT. As mensagens de início e conclusão da transferência de arquivo são cobradas como mensagens medidas em incrementos de 4 KB. Por exemplo, a transferência de um arquivo de 10 MB é cobrada como duas mensagens além do custo do Armazenamento do Azure. |
| Métodos diretos | As solicitações de método cobradas em blocos de 4 KB, as respostas com corpos não vazios são cobradas como 4KB na forma de mensagens adicionais. As solicitações para dispositivos desconectados são cobradas como mensagens em blocos de 4 KB. Por exemplo, um método com um corpo de 6 KB que resulta em uma resposta sem corpo do dispositivo é cobrado como duas mensagens. Um método com um corpo de 6 KB que resulta em uma resposta de 1 KB do dispositivo é cobrado como duas mensagens para a solicitação mais outra mensagem para a resposta. |
| Leituras de dispositivo gêmeo | As leituras de dispositivo gêmeo e de back-end são cobradas como mensagens em blocos de 512 bytes. Por exemplo, a leitura de um dispositivo gêmeo de 6 KB é cobrada como 12 mensagens. |
| Atualizações do dispositivo gêmeo (marcas e propriedades) | As atualizações de dispositivo gêmeo de e para o dispositivo são cobradas como mensagens em blocos de 512 bytes. Por exemplo, a leitura de um dispositivo gêmeo de 6 KB é cobrada como 12 mensagens. |
| Consultas de dispositivo gêmeo | As consultas são cobradas como mensagens, dependendo do tamanho do resultado, em blocos de 512 bytes. |
| Operações de trabalhos <br/> (criar, atualizar, listar, excluir) | Não será cobrado. |
| Operações de trabalhos por dispositivo | As operações de trabalhos (como atualizações de dispositivos gêmeos e métodos) são cobradas de forma normal. Por exemplo, um trabalho que resulta em 1000 chamadas de método com solicitações de 1 KB e respostas de corpo vazio serão cobradas como 1000 mensagens. |

> AZURE.NOTE Todos os tamanhos são computados considerando o tamanho da carga em bytes (o enquadramento de protocolo é ignorado). No caso de mensagens (que têm propriedades e corpo), o tamanho é calculado independentemente do protocolo, conforme descrito no [Guia do desenvolvedor de mensagens do Hub IoT][lnk-message-size].

## <a name="example-1--"></a>Exemplo 1: 

Um dispositivo envia uma mensagem de dispositivo para nuvem de 1 KB por minuto para o Hub IoT, que é lida pelo Azure Stream Analytics. O back-end invoca um método (com carga de 512 bytes) no dispositivo a cada dez minutos para disparar uma ação específica. O dispositivo responde ao método com um resultado de 200 bytes.

O dispositivo consome 1 mensagem * 60 minutos * 24 horas = 1440 mensagens por dia para mensagens de dispositivo para nuvem e 2 solicitações mais respostas * 6 vezes por hora * 24 horas = 288 mensagens para os métodos, em um total de 1728 mensagens por dia.

## <a name="example-2"></a>Exemplo 2:

Um dispositivo envia uma mensagem de dispositivo para nuvem de 100 KB por hora. Ele também atualiza seu dispositivo gêmeo com cargas de 1 KB a cada 4 horas. O back-end, uma vez ao dia, lê o dispositivo gêmeo de 14 KB e o atualiza com cargas de 512 bytes para alterar as configurações.

O dispositivo consome 25 mensagens (100 KB/4 KB) * 24 horas para mensagens de dispositivo para nuvem, além de 1 mensagem * 6 vezes ao dia para atualizações de dispositivo gêmeo, em um total de 156 mensagens por dia.
O back-end consome 28 mensagens (14 KB/0,5 KB) para ler o dispositivo gêmeo, além de 1 mensagem para atualizá-lo, em um total de 29 mensagens.

No total, o dispositivo e o back-end consomem 185 mensagens por dia.


[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-message-size]: iot-hub-devguide-messaging.md#message-size


<!--HONumber=Nov16_HO5-->


