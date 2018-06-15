---
title: Solucionar problemas de eventos de entrada malformados no Azure Stream Analytics | Microsoft Docs
description: Como saber qual evento nos meus dados de entrada está causando o problema em um trabalho do Stream Analytics
keywords: ''
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/19/2018
ms.author: sngun
ms.openlocfilehash: 2b4f82bae20c3cb398848a89715bfdc1316e1ba1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2018
ms.locfileid: "29766375"
---
# <a name="troubleshoot-for-malformed-input-events"></a>Solucionar problemas de eventos de entrada malformados

Quando o fluxo de entrada de seu trabalho do Stream Analytics contém mensagens malformadas, ele causa problemas de serialização. As mensagens malformadas incluem serialização incorreta, como parênteses ausentes em um objeto JSON ou formato de carimbo de data/hora incorreto. Quando um trabalho do Stream Analytics recebe uma mensagem malformada, ele descarta a mensagem e notifica o usuário com um aviso. Um símbolo de aviso é exibido no bloco **Entradas** do seu trabalho do Stream Analytics:

![Bloco Entradas](media/stream-analytics-malformed-events/inputs_tile.png)

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

1. Navegue até o bloco de entrada e clique para exibir avisos.
2. O bloco de detalhes de entrada exibe um conjunto de avisos com detalhes sobre o problema. A seguir está uma mensagem de aviso de exemplo, a mensagem de aviso mostra a Partição, o Deslocamento e números de sequência em que há dados JSON malformados. 

   ![Mensagem de aviso com deslocamento](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Para obter os dados JSON com formato incorreto, execute o seguinte trecho de código. Este bloco de código lê a Id de partição, o deslocamento e imprime os dados. Você pode obter a amostra completa do [repositório de amostras do GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Depois de ler os dados, você pode analisar e corrigir o formato de serialização.

```csharp
static void PrintMessages(string partitionId, long offset, int numberOfEvents)
        {
            EventHubReceiver receiver;
            
            try
            {
                foreach (var e in receiver.Receive(numberOfEvents, TimeSpan.FromMinutes(1)))
                {
                    Console.WriteLine(Encoding.UTF8.GetString(e.GetBytes()));
                    Console.WriteLine("----");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.ToString());
                return;
            }

            receiver.Close();
        }
```
## <a name="next-steps"></a>Próximas etapas

* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
