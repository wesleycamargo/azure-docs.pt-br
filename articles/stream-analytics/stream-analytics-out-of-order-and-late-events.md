---
title: Manipulando a ordem e o atraso dos eventos com o Azure Stream Analytics | Microsoft Docs
description: Saiba como o Stream Analytics funciona com eventos fora de ordem ou atrasados nos fluxos de dados.
keywords: fora de ordem, atraso, eventos
documentationcenter: 
services: stream-analytics
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 208dfa14d5d18e106d654539cd80bafdeb90cdf8
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2017
---
# <a name="azure-stream-analytics-event-order-consideration"></a>Consideração sobre a ordem dos eventos do Azure Stream Analytics

## <a name="understand-arrival-time-and-application-time"></a>Entenda a hora de chegada e a hora do aplicativo.

Em um fluxo de dados temporais de eventos, cada evento recebe um carimbo de data/hora. O Azure Stream Analytics atribui os carimbos de data/hora para cada evento, usando a Hora de chegada ou a Hora do aplicativo. A coluna “System.Timestamp” tem o carimbo de data/hora atribuído ao evento. A hora de chegada é atribuída na fonte de entrada, quando o evento chega à origem. A hora de chegada é EventEnqueuedTime para entrada de Hub de Eventos e [hora da última modificação do blob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) para a entrada de blob. A Hora do aplicativo é atribuída quando o evento é gerado e é parte do conteúdo. Para processar eventos por hora do aplicativo, use a cláusula “Timestamp by” na consulta select. Se a cláusula “Timestamp by” estiver ausente, os eventos serão processados na Hora de chegada. A hora de chegada pode ser acessada usando a propriedade EventEnqueuedTime para o hub de eventos e a propriedade BlobLastModified para a entrada de blob. O Azure Stream Analytics produz a saída de carimbo de data/hora em ordem e fornece algumas configurações para lidar com os dados fora de ordem.


## <a name="azure-stream-analytics-handling-of-multiple-streams"></a>Manipulação de vários fluxos do Azure Stream Analytics

O trabalho do Azure Stream Analytics combina eventos de várias linhas do tempo em alguns casos, incluindo

* Produzindo saída de várias partições. Consultas que não têm uma "Partição por PartitionId" explícita precisariam combinar eventos de todas as partições.
* União de duas ou mais fontes de entrada diferentes.
* Unindo fontes de entrada.

Nos cenários em que várias linhas do tempo são combinadas, o Azure Stream Analytics produzirá saída para um carimbo de data/hora *t1* somente depois que todas as fontes combinadas estiverem pelo menos na hora *t1*.
Por exemplo, se a consulta fizer a leitura por meio de uma partição do *Hub de eventos* que tem duas partições, e uma das partições *P1* tiver eventos até a hora *t1* e a outra *P2* até a hora *t1 + x*, a saída será produzida até a hora *t1*.
No entanto, se houver uma cláusula *"Partição por PartitionId"* explícita, as duas partições progredirão de maneira independente.
A configuração Tolerância da Chegada Tardia é usada para lidar com a ausência de dados em algumas partições.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>Configurando a Tolerância da chegada tardia e a tolerância para trabalhos fora de ordem
Os fluxos de entrada que não estão em ordem são:
* Classificados (e, portanto, **atrasados**).
* Ajustados pelo sistema, de acordo com a política especificada pelo usuário.

O Stream Analytics tolera eventos tardios e em fora de ordem ao processar por **hora do aplicativo**. As configurações a seguir estão disponíveis na opção **Ordenação de eventos** no Portal do Azure: 

![Manipulação de eventos do Stream Analytics](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

**Tolerância a atrasos**
* Essa configuração se aplica somente ao processar pela Hora do aplicativo, caso contrário ela é ignorada.
* A tolerância a atrasos é a diferença máxima entre a Hora de chegada e a Hora do aplicativo. Se a Hora do aplicativo for anterior (Hora de chegada – Janela de atraso), ela será definida como (Hora de chegada – Janela de Atraso)
* Quando várias partições do mesmo fluxo de entrada ou de vários fluxos de entrada são combinadas, a tolerância a atrasos é a quantidade máxima de tempo que cada partição espera por novos dados. 

Em resumo, a janela de atraso é o atraso máximo entre a geração de eventos e o recebimento do evento na fonte de entrada.
O ajuste com base na tolerância a atrasos é feito primeiro e o fora de ordem depois. A coluna **System.Timestamp** tem o carimbo de data/hora final atribuído ao evento.

**Tolerância para trabalhos fora de ordem**
* Os eventos que chegam fora de ordem, mas dentro da configuração da “janela de tolerância a eventos fora de ordem”, são **reordenados pelo carimbo de data/hora**. 
* Os eventos que chegam após o tempo de tolerância são **removidos ou ajustados**.
    * **Ajustados**: para que pareçam ter chegado no último horário aceitável. 
    * **Removidos**: descartados.

Para reordenar eventos recebidos dentro da "janela de tolerância a eventos fora de ordem", a saída da consulta é **atrasado conforme a janela de tolerância a eventos fora de ordem**.

**Exemplo**

* Tolerância a atrasos = 10 minutos<br/>
* Tolerância para eventos fora de ordem = 3 minutos<br/>
* Processamento por tempo de aplicação<br/>
* Eventos:
   * O Evento 1 _Hora do aplicativo_ = 00:00:00, _Hora de chegada_ = 00:10:01, _System.Timestamp_ = 00:00:01 é ajustado porque (_Hora de chegada_ - _Hora do aplicativo_) é maior do que a tolerância a atrasos.
   * O Evento 2 _Hora do aplicativo_ = 00:00:01, _Hora de Chegada_ = 00:10:01, _System.Timestamp_ = 00:00:01 não é ajustado porque a hora do aplicativo está dentro da janela de atraso.
   * O Evento 3 _Hora do aplicativo_ = 00:10:00, _Hora de chegada_ = 00:10:02, _System.Timestamp_ = 00:10:00 não é ajustado porque a hora do aplicativo está dentro da janela de atraso.
   * O Evento 4 _Hora do aplicativo_ = 00:09:00, _Hora de chegada_ = 00:10:03, _System.Timestamp_ = 00:09:00 é aceito com o carimbo de data/hora original, pois a hora do aplicativo está dentro da tolerância a eventos fora de ordem.
   * O Evento 5 _Hora do aplicativo_ = 00:06:00, _Hora de chegada_ = 00:10:04, _System.Timestamp_ = 00:07:00 é ajustado pois a hora do aplicativo é anterior à tolerância a eventos fora de ordem.

## <a name="practical-considerations"></a>Considerações práticas
Conforme mencionado acima, a *tolerância da chegada tardia* é a diferença máxima entre a hora do aplicativo e a hora da chegada.
Além disso, ao processar por hora do aplicativo, os eventos posteriores à *tolerância da chegada tardia* configurada serão ajustados antes da aplicação da configuração *tolerância para trabalhos fora de ordem*. Portanto, a tolerância fora de ordem efetiva é o mínimo da tolerância da chegada tardia e da tolerância para trabalhos fora de ordem.

Os eventos fora de ordem dentro de um fluxo acontecem devido a alguns motivos, incluindo
* Defasagem horária entre os remetentes.
* Latência variável entre a origem do evento de entrada e o remetente.

A chegada tardia acontece devido a alguns motivos, incluindo
* Lote dos remetentes e envio dos eventos para um intervalo mais tarde, após o intervalo.
* Latência entre o envio do evento pelo remetente e o recebimento do evento na fonte de entrada.

Ao configurar a *tolerância da chegada tardia* e a *tolerância para trabalhos fora de ordem* para um trabalho específico, devem ser considerados a exatidão, os requisitos de latência e os fatores acima.

Veja alguns exemplos

### <a name="example-1"></a>Exemplo 1: 
A consulta tem a cláusula "Partição por PartitionId" e, dentro de uma única partição, os eventos são enviados usando métodos de envio síncronos. Os métodos de envio síncronos são bloqueados até que os eventos sejam enviados.
Nesse caso, a tolerância para trabalhos fora de ordem é zero, porque os eventos são enviados em ordem com confirmação explícita antes do envio do próximo evento. A chegada tardia é o atraso máximo entre a geração do evento e o envio do evento + a latência máxima entre o remetente e a fonte de entrada

### <a name="example-2"></a>Exemplo 2:
A consulta tem a cláusula "Partição por PartitionId" e, dentro de uma única partição, os eventos são enviados usando o método de envio assíncrono. Os métodos de envio assíncronos podem iniciar vários envios ao mesmo tempo, o que pode ser a causa de eventos fora de ordem.
Nesse caso, a tolerância para trabalhos fora de ordem e da chegada tardia estão com pelo menos o atraso máximo entre a geração do evento e o envio do evento + a latência máxima entre o remetente e a fonte de entrada.

### <a name="example-3"></a>Exemplo 3:
A consulta não tem "Partição por PartitionId" e há pelo menos duas partições.
A configuração é a mesma do exemplo 2. No entanto, a ausência de dados em uma das partições pode atrasar a saída em uma janela de "tolerância da chegada tardia" adicional.

## <a name="to-summarize"></a>Para resumir
* A tolerância da chegada tardia e a janela fora de ordem devem ser configuradas com base em requisitos de latência e precisão e devem considerar a maneira como os eventos são enviados.
* É recomendável que a tolerância para trabalhos fora de ordem seja menor do que a tolerância da chegada tardia.
* Ao combinar várias linhas do tempo, a falta de dados em uma das fontes ou partições pode atrasar a saída em uma janela de tolerância da chegada tardia adicional.

## <a name="get-help"></a>Obter ajuda
Para obter mais ajuda, teste nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência da linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST de gerenciamento do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
