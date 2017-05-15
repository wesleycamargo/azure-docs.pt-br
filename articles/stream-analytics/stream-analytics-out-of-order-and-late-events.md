---
title: Manipulando a ordem e o atraso dos eventos com o Azure Stream Analytics | Microsoft Docs
description: Saiba como o Stream Analytics funciona com eventos fora de ordem ou atrasados nos fluxos de dados.
keywords: fora de ordem, atraso, eventos
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 02c854727e5185968dba49233df82ea6ff336ec8
ms.contentlocale: pt-br
ms.lasthandoff: 05/01/2017


---
# <a name="azure-stream-analytics-event-order-handling"></a>Manipulação da ordem dos eventos do Azure Stream Analytics

Em um fluxo de dados temporal de eventos, cada evento é registrado com a hora em que ele foi recebido. Algumas condições podem fazer com que os fluxos de eventos às vezes recebam alguns eventos em uma ordem diferente da qual eles foram enviados. Uma retransmissão TCP simples ou, até mesmo, uma defasagem horária entre o dispositivo de envio e o hub de eventos de recebimento pode fazer com que isso aconteça. Os eventos de "pontuação" também são adicionados aos fluxos de eventos recebidos, de modo a antecipar o horário na ausência das chegadas de evento. Eles são necessários em cenários como "Notificar-me quando nenhum logon ocorrer por três minutos".

Os fluxos de entrada que não estão em ordem são:
* Classificados (e, portanto, **atrasados**).
* Ajustados pelo sistema, de acordo com uma política especificada pelo usuário.


## <a name="lateness-tolerance"></a>Tolerância de atraso
O Stream Analytics tolera esses tipos de cenários. O Stream Analytics tem tratamento para eventos "fora de ordem" e "em atraso". Ele lida com esses eventos das seguintes maneiras:

* Os eventos que chegam fora de ordem, mas dentro da tolerância definida são **reordenados pelo carimbo de data/hora**.
* Os eventos que chegam após o tempo de tolerância são **removidos ou ajustados**.
    * **Ajustados**: para que pareçam ter chegado no último horário aceitável.
    * **Removidos**: descartados.

![Manipulação de eventos do Stream Analytics](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

## <a name="reduce-the-number-of-out-of-order-events"></a>Reduzir o número de eventos fora de ordem

Uma vez que o Stream Analytics aplica uma transformação temporal quando processa eventos de entrada (por exemplo, para agregações com janela ou uniões temporais), ele classifica eventos de entrada pela ordem do carimbo de data/hora.

Quando a palavra-chave "carimbo de data/hora por" **não** é usada, a hora de enfileiramento do evento dos Hubs de Eventos do Azure é usada por padrão. Os Hubs de Eventos garantem monotonia do carimbo de data/hora em cada partição do hub de eventos. Eles também garantem que os eventos de todas as partições sejam mesclados na ordem do carimbo de data/hora. Essas duas garantias dos Hubs de Eventos asseguram que não haja eventos fora de ordem.

Às vezes, é importante usar o carimbo de data/hora do remetente. Nesse caso, um carimbo de data/hora do conteúdo do evento é escolhido usando "timestamp by". Nesses cenários, uma ou mais fontes de ordem incorreta podem ser introduzidas:

* Os produtores de eventos têm defasagem horária. Isso é comum quando produtores são de diferentes computadores, pois eles têm relógios diferentes.
* Há um atraso de rede da fonte dos eventos até o hub de eventos de destino.
* As defasagens horárias existem entre partições do hub de eventos. O Stream Analytics primeiro classifica os eventos de todas as partições do hub de eventos pela hora de enfileiramento do evento. Em seguida, ele examina o fluxo de dados em busca de eventos em ordem incorreta.

Na guia de configuração, você verá os seguintes padrões:

![Manipulação de eventos fora de ordem do Stream Analytics](media/stream-analytics-event-handling/stream-analytics-out-of-order-handling.png)

Caso use 0 segundos como a janela de tolerância para eventos fora de ordem, você estará afirmando que todos os eventos estão em ordem o tempo todo. Considerando as três fontes de eventos em ordem incorreta, é improvável que isso seja verdadeiro. 

Para permitir que o Stream Analytics corrija a ordem incorreta de um evento, você pode especificar uma janela de tolerância fora de ordem diferente de zero. O Stream Analytics armazena em buffer os eventos até essa janela e, em seguida, os reordena usando o carimbo de data/hora escolhido. Ele então aplica a transformação temporal. Você pode começar com uma janela de 3 segundos e ajustar o valor para reduzir o número de eventos que são ajustados por hora. 

Um efeito colateral do armazenamento em buffer é que a saída **tem o mesmo tempo de atraso**. Você pode ajustar o valor para reduzir o número de eventos fora de ordem e manter baixa a latência do trabalho.

## <a name="get-help"></a>Obter ajuda
Para obter mais ajuda, teste nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência da linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST de gerenciamento do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
