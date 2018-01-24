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
ms.openlocfilehash: 71929b449f2a0fa55327fd3f9741208506859e85
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="azure-stream-analytics-event-order-considerations"></a>Considerações sobre a ordem dos eventos do Azure Stream Analytics

## <a name="arrival-time-and-application-time"></a>Hora de chegada e hora do aplicativo

Em um fluxo de dados temporais de eventos, cada evento recebe um carimbo de data/hora. O Azure Stream Analytics atribui um carimbo de data/hora para cada evento, usando a hora de chegada ou a hora do aplicativo. A coluna **System.Timestamp** tem o carimbo de data/hora atribuído ao evento. 

A hora de chegada é atribuída na fonte de entrada, quando o evento chega à origem. É possível acessar o tempo de chegada usando a propriedade **EventEnqueuedTime** para a entrada do hub de eventos e a propriedade [BlobProperties.LastModified](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) para a entrada de Blob. 

A hora do aplicativo é atribuída quando o evento é gerado e é parte do conteúdo. Para processar eventos por hora do aplicativo, use a cláusula **Timestamp by** na consulta selecionada. Se a cláusula **Timestamp by** estiver ausente, os eventos serão processados na hora de chegada. 

O Azure Stream Analytics produz a saída de carimbo de data/hora em ordem e fornece as configurações para lidar com os dados fora de ordem.


## <a name="handling-of-multiple-streams"></a>Tratamento de vários fluxos

O trabalho do Azure Stream Analytics combina eventos de várias linhas do tempo em casos como:

* Produzindo saída de várias partições. Consultas que não têm uma cláusula**Partition by PartitionId** explícita devem combinar eventos de todas as partições.
* União de duas ou mais fontes de entrada diferentes.
* Unindo fontes de entrada.

Nos cenários em que várias linhas do tempo são combinadas, o Azure Stream Analytics produz saída para o carimbo de data/hora *t1* somente depois que todas as fontes combinadas estiverem pelo menos na hora *t1*. Por exemplo, suponha que a consulta faça a leitura de uma partição do hub de eventos que tem duas partições. Uma das partições, *P1*, tem eventos até o tempo de *t1*. A outra partição, *P2*, tem eventos até o tempo *t1 + x*. Em seguida, a saída é produzida até o tempo *t1*. Mas, se houver uma cláusula **Partition by PartitionId** explícita, ambas as partições progredirão de maneira independente.

A configuração para tolerância de chegada em atraso é usada para lidar com a ausência de dados em algumas partições.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>Configurando a tolerância da chegada em atraso e a tolerância para trabalhos fora de ordem
Os fluxos de entrada que não estão em ordem são:
* Classificados (e, portanto, atrasados)
* Ajustados pelo sistema, de acordo com a política especificada pelo usuário

O Stream Analytics tolera eventos atrasados e fora de ordem ao processar por hora do aplicativo. As configurações a seguir estão disponíveis na opção **Ordenação de eventos** no Portal do Azure: 

![Manipulação de eventos do Stream Analytics](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

### <a name="late-arrival-tolerance"></a>Tolerância a atrasos
A tolerância de chegada em atraso é aplicável somente quando processar por hora do aplicativo. Caso contrário, a configuração será ignorada.

A tolerância da chegada em atraso é a diferença máxima entre a hora de chegada e a hora do aplicativo. Se um evento chegar depois da tolerância da chegada em atraso (por exemplo, hora do aplicativo *app_t* < hora de chegada *arr_t* - tolerância à política de chegada em atraso *late_t*), o evento será ajustado para o máximo da tolerância da chegada em atraso (*arr_t* - *late_t*). A janela da chegada em atraso é o atraso máximo entre a geração de eventos e o recebimento do evento na fonte de entrada. 

Quando várias partições do mesmo fluxo de entrada ou de vários fluxos de entrada são combinadas, a tolerância da chegada em atraso é a quantidade máxima de tempo que cada partição espera por novos dados. 

O ajuste com base na tolerância da chegada em atraso ocorre primeiro. O ajuste com base na tolerância para trabalhos fora de ordem acontece em seguida. A coluna **System.Timestamp** tem o carimbo de data/hora final atribuído ao evento.

### <a name="out-of-order-tolerance"></a>Tolerância para trabalhos fora de ordem
Os eventos que chegam fora de ordem, mas dentro da configuração da janela de tolerância a eventos fora de ordem, são reordenados pelo carimbo de data/hora. Os eventos que após a janela de tolerância são:
* **Ajustados**: para que pareçam ter chegado no último horário aceitável. 
* **Removidos**: descartados.

Quando o Stream Analytics reordena eventos recebidos dentro da janela de tolerância para trabalhos fora de ordem, a saída da consulta é atrasada pela janela de tolerância para trabalhos fora de ordem.

### <a name="example"></a>Exemplo

* Tolerância a atrasos = 10 minutos<br/>
* Tolerância para eventos fora de ordem = 3 minutos<br/>
* Processamento por tempo de aplicação<br/>
* Eventos:
   1. **Hora do Aplicativo** = 00:00:00, **Hora de Chegada** = 00:10:01, **System.Timestamp** = 00:00:01, é ajustado porque (**Hora de Chegada - Tempo de Aplicação**) é maior do que a tolerância da chegada em atraso.
   2. **Hora do Aplicativo** = 00:00:01, **Hora de Chegada** = 00:10:01, **System.Timestamp** = 00:00:01, não é ajustado porque a hora do aplicativo está dentro da janela da chegada em atraso.
   3. **Hora do Aplicativo** = 00:10:00, **Hora de Chegada** = 00:10:02, **System.Timestamp** = 00:10:00, não é ajustado porque a hora do aplicativo está dentro da janela da chegada em atraso.
   4. **Hora do Aplicativo** = 00:09:00, **Hora de Chegada** = 00:10:03, **System.Timestamp** = 00:09:00, aceito com o carimbo de data/hora original porque a hora do aplicativo está dentro da tolerância para trabalhos fora de ordem.
   5. **Hora do Aplicativo** = 00:06:00, **Hora de Chegada** = 00:10:04, **System.Timestamp** = 00:07:00, é ajustado porque a hora do aplicativo é mais antiga do que a tolerância para trabalhos fora de ordem.

### <a name="practical-considerations"></a>Considerações práticas
Conforme mencionado anteriormente, a tolerância da chegada em atraso é a diferença máxima entre a hora do aplicativo e a hora de chegada. Ao processar por hora do aplicativo, os eventos posteriores à tolerância da chegada em atraso configurada são ajustados antes que a configuração de tolerância fora de ordem seja aplicada. Portanto, a tolerância para trabalhos fora de ordem efetiva é o mínimo da tolerância da chegada em atraso e da tolerância para trabalhos fora de ordem.

Razões para eventos fora de ordem dentro de um fluxo incluem:
* Distorção horária entre os remetentes.
* Latência variável entre o remetente e a origem do evento de entrada.

As razões para a chegada em atraso incluem:
* Envio em lote e envio dos eventos para um intervalo mais tarde, após o intervalo.
* Latência entre enviar o evento pelo remetente e receber o evento na fonte de entrada.

Ao configurar a tolerância de chegada em atraso e a tolerância para trabalhos fora de ordem para um trabalho específico, considere a correção, os requisitos de latência e os fatores anteriores.

Seguem alguns exemplos.

#### <a name="example-1"></a>Exemplo 1 
A consulta tem uma cláusula **Partition by PartitionId**. Dentro de uma única partição, os eventos são enviados por meio de métodos de envio síncrono. Os métodos de envio síncronos são bloqueados até que os eventos sejam enviados.

Nesse caso, para trabalhos fora de ordem é zero porque os eventos são enviados em ordem com confirmação explícita, antes que o próximo evento seja enviado. A chegada em atraso é o atraso máximo entre gerar o evento e enviar o evento, além da latência máxima entre o remetente e a fonte de entrada.

#### <a name="example-2"></a>Exemplo 2
A consulta tem uma cláusula **Partition by PartitionId**. Dentro de uma única partição, os eventos são enviados através de métodos de envio assíncrono. Os métodos de envio assíncrono podem iniciar múltiplos envios ao mesmo tempo, o que pode causar eventos fora de ordem.

Nesse caso, tanto trabalhos fora de ordem como de chegada em atraso são pelo menos o atraso máximo entre gerar o evento e enviar o evento, além da latência máxima entre o remetente e a fonte de entrada.

#### <a name="example-3"></a>Exemplo 3
A consulta não tem uma cláusula **Partition by PartitionId** e existem pelo menos duas partições.

A configuração é a mesma do exemplo 2. No entanto, a ausência de dados em uma das partições pode atrasar a saída por uma janela adicional de tolerância da chegada em atraso.

## <a name="handling-event-producers-with-differing-timelines"></a>Manipulação de produtores de eventos com diferentes linhas do tempo
Um único fluxo de eventos de entrada geralmente contém eventos originados por vários produtores de eventos, como dispositivos individuais. Esses eventos podem chegar em fora de ordem devido às razões discutidas anteriormente. Nesses cenários, embora a desordem entre os produtores de eventos possa ser grande, a desordem dentro dos eventos de um único produtor é pequena (ou mesmo inexistente).

O Azure Stream Analytics fornece mecanismos gerais para lidar com eventos fora de ordem. Esses mecanismos resultam em atrasos de processamento (enquanto aguardam eventos difíceis de alcançar o sistema), eventos soltos ou ajustados, ou ambos.

No entanto, em muitos cenários, a consulta desejada é processar eventos de diferentes produtores de eventos de forma independente. Por exemplo, é possível agregar eventos por janela, por dispositivo. Nesses casos, não há necessidade de atrasar o resultado que corresponde a um produtor de eventos enquanto espera que os outros produtores de eventos se recuperem. Ou seja, não há necessidade de lidar com a distorção de tempo entre os produtores. Você pode ignorá-la.

Certamente, isso significa que os próprios eventos de saída estão fora de ordem em relação aos seus carimbos de data/hora. O consumidor de downstream deve ser capaz de lidar com esse comportamento. Mas cada evento na saída está correto.

O Azure Stream Analytics implementa essa funcionalidade usando a cláusula [TIMESTAMP BY OVER](https://msdn.microsoft.com/library/azure/mt573293.aspx).

## <a name="summary"></a>Resumo
* Configure a tolerância da chegada em atraso e a janela fora de ordem com base nos requisitos de correção e latência. Considere também como os eventos são enviados.
* É recomendável que a tolerância para trabalhos fora de ordem seja menor do que a tolerância da chegada em atraso.
* Ao combinar várias linhas do tempo, a falta de dados em uma das fontes ou partições pode atrasar a saída em uma janela de tolerância da chegada tardia adicional.

## <a name="get-help"></a>Obter ajuda
Para obter assistência adicional, teste o [Fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência da linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST de gerenciamento do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
