---
title: Manipulando a ordem e o atraso dos eventos com o Azure Stream Analytics | Microsoft Docs
description: Saiba como o Azure Stream Analytics funciona com eventos fora de ordem ou atrasados nos fluxos de dados
keywords: fora de ordem, atraso, eventos
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: cf9a43fbb82a32c92d66f25809916d3ccde1a20d
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---
# <a name="azure-stream-analytics-event-order-handling"></a>Manipulação da ordem dos eventos do Azure Stream Analytics

Em um fluxo de dados temporais de eventos, cada evento recebe um carimbo de data/hora. O Azure Stream Analytics atribui os carimbos de data/hora para cada evento, usando a hora de chegada ou a hora do aplicativo. 

A coluna **System.Timestamp** tem o carimbo de data/hora atribuído ao evento. A hora de chegada é atribuída na fonte de entrada, quando o evento chega à origem. A hora de chegada é **EventEnqueuedTime** para entrada de hub de eventos e [hora da última modificação do blob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) para a entrada de blob. A hora do aplicativo é atribuída quando o evento é gerado e é parte do conteúdo. 

Para processar eventos por hora do aplicativo, use a cláusula TIMESTAMP BY na consulta select. Se a cláusula TIMESTAMP BY estiver ausente, os eventos serão processados na hora de chegada. É possível acessar a hora de chegada usando a propriedade **EventEnqueuedTime** para o hub de eventos e a propriedade **BlobLastModified** para a entrada de blob. 

O Azure Stream Analytics produz a saída de carimbo de data/hora em ordem e fornece algumas configurações para lidar com os dados fora de ordem.

![Manipulação de eventos do Stream Analytics](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

Os fluxos de entrada que não estão em ordem são:
* Classificados (e, portanto, *atrasados*).
* Ajustados pelo sistema, de acordo com a política especificada pelo usuário.

O Stream Analytics tolera eventos atrasados e fora de ordem ao processar pela hora do aplicativo.

**Tolerância a atrasos**

* A configuração de tolerância a atrasos se aplica somente ao processar pelo tempo de aplicação. Caso contrário, será ignorada.
* A tolerância a atrasos é a diferença máxima entre a hora de chegada e o tempo de aplicação. Se o tempo de aplicação for anterior a *(Hora de Chegada – Janela de Atraso)*, ela será definida como *(Hora de Chegada – Janela de Atraso)*.
* Quando várias partições do mesmo fluxo de entrada ou de vários fluxos de entrada são combinadas, a tolerância a atrasos é a quantidade máxima de tempo que cada partição espera por novos dados. 

Em resumo, a janela de atraso é o atraso máximo entre a geração de eventos e o recebimento do evento na fonte de entrada.
O ajuste com base na tolerância a atrasos é feita pela primeiro e o fora de ordem depois. A coluna **System.Timestamp** tem o carimbo de data/hora final atribuído ao evento.

**Tolerância para trabalhos fora de ordem**

* Os eventos que chegam fora de ordem, mas dentro da “janela de tolerância para trabalhos fora de ordem”, são *reordenados pelo carimbo de data/hora*. 
* Os eventos que chegam após o tempo de tolerância são *removidos* ou *ajustados*.
    * **Ajustados**: para que pareçam ter chegado no último horário aceitável. 
    * **Removidos**: descartados.

Para reordenar eventos recebidos dentro da “janela de tolerância para trabalhos fora de ordem”, a saída da consulta é *atrasada pela janela de tolerância para trabalhos fora de ordem*.

**Exemplo**

Tolerância a atrasos = 10 minutos<br/>
Tolerância para eventos fora de ordem = 3 minutos<br/>
Processamento por tempo de aplicação<br/>

Eventos:

O Evento 1 _Tempo de Aplicação_ = 00:00:00, _Hora de Chegada_ = 00:10:01, _System.Timestamp_ = 00:00:01 é ajustado porque (_Hora de Chegada_ - _Hora do Aplicativo_) é maior do que a tolerância a atrasos.

O Evento 2 _Tempo de Aplicação_ = 00:00:01, _Hora de Chegada_ = 00:10:01, _System.Timestamp_ = 00:00:01 não é ajustado porque a hora do aplicativo está dentro da janela de atraso.

O Evento 3 _Tempo de Aplicação_ = 00:10:00, _Hora de Chegada_ = 00:10:02, _System.Timestamp_ = 00:10:00 não é ajustado porque a hora do aplicativo está dentro da janela de atraso.

O Evento 4 _Tempo de Aplicação_ = 00:09:00, _Hora de Chegada_ = 00:10:03, _System.Timestamp_ = 00:09:00 é aceito com o carimbo de data/hora original, pois o tempo de aplicação está dentro da tolerância a eventos fora de ordem.

O Evento 5 _Tempo de Aplicação_ = 00:06:00, _Hora de Chegada_ = 00:10:04, _System.Timestamp_ = 00:07:00 é ajustado porque o tempo de aplicação é anterior à tolerância a eventos fora de ordem.



## <a name="get-help"></a>Obter ajuda
Para obter mais ajuda, teste nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do gerenciamento do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

