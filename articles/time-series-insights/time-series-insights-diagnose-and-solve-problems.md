---
title: Diagnosticar e solucionar problemas no Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como diagnosticar e solucionar problemas comuns que podem ser encontrados em seu ambiente do Azure Time Series Insights.
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/15/2017
ms.openlocfilehash: 4216b245fd480003cfa4a34452f87efade964f8d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Diagnosticar e resolver problemas no ambiente do Time Series Insights

## <a name="problem-1-no-data-is-shown"></a>Problema 1: Nenhum dado é mostrado
Há vários motivos comuns pelos quais você não pode ver seus dados no [explorer do Azure Time Series Insights](https://insights.timeseries.azure.com):

### <a name="possible-cause-a-event-source-data-is-not-in-json-format"></a>Causa possível A: os dados da origem do evento não estão no formato JSON
O Azure Time Series Insights dá suporte somente a dados JSON. Para obter exemplos do JSON, consulte [Formas de JSON com suporte](time-series-insights-send-events.md#supported-json-shapes).

### <a name="possible-cause-b-event-source-key-is-missing-a-required-permission"></a>Possível causa B: a chave de origem do evento não tem uma permissão necessária
* Para o Hub IoT, você precisa fornecer a chave com a permissão de **conexão de serviço**.

   ![Permissão de conexão de serviço do Hub IoT](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Conforme mostrado na imagem anterior, as políticas **iothubowner** e **service** funcionarão, pois ambas têm a permissão de **conexão de serviço**.
   
* Para o hub de eventos, você precisa fornecer a chave que tem a permissão de **Escuta**.

   ![Permissão de escuta do hub de eventos](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Conforme mostrado na imagem anterior, qualquer uma das políticas **read** e **manage** funcionará, pois ambas têm a permissão de **Escuta**.

### <a name="possible-cause-c-the-consumer-group-provided-is-not-exclusive-to-time-series-insights"></a>Causa possível C: o grupo de consumidores fornecido não é exclusivo do Time Series Insights
Durante o registro de um hub IoT ou de um hub de eventos, especifique o grupo de consumidores que deve ser usado para ler os dados. Esse grupo de consumidores **não** deve ser compartilhado. Se o grupo de consumidores for compartilhado, o hub de eventos subjacente desconectará automaticamente um dos leitores de forma aleatória. Forneça um grupo de consumidores exclusivo para o Time Series Insights para leitura.

## <a name="problem-2-some-data-is-shown-but-some-is-missing"></a>Problema 2: Alguns dados são exibidos, mas alguns estão ausentes
Quando você puder ver os dados parcialmente, e se os dados estiverem parcialmente escondidos, há várias possibilidades a serem consideradas:

### <a name="possible-cause-a-your-environment-is-getting-throttled"></a>Causa possível A: seu ambiente está ficando limitado
A limitação é imposta com base na capacidade e no tipo de SKU do ambiente. Todas as origens do evento no ambiente compartilham essa capacidade. Se a origem do evento do hub de eventos ou Hub IoT estiver enviando dados por push além dos limites impostos, você vê a limitação e um retardo.

O diagrama a seguir mostra um ambiente do Time Series Insights com um SKU S1 e uma capacidade 3. Ele pode ingressar 3 milhões de eventos por dia.

![Capacidade atual do SKU do ambiente](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Por exemplo, suponha que esse ambiente esteja ingerindo mensagens de um hub de eventos. Observe a taxa de ingresso mostrada no diagrama a seguir:

![Taxa de entrada de exemplo para um hub de eventos](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Conforme mostrado no diagrama, a taxa de entrada diária é de ~67.000 mensagens. Essa taxa converte aproximadamente 46 mensagens a cada minuto. Se cada mensagem do hub de eventos for nivelada para um único evento do Time Series Insights, esse ambiente não terá nenhuma limitação. Se cada mensagem do hub de eventos for nivelada para 100 eventos do Time Series Insights, 4.600 eventos deverão ser ingeridos a cada minuto. Um ambiente de SKU S1 que tem uma capacidade 3 pode ingressar apenas 2.100 eventos por minuto (1 milhão de eventos por dia = 700 eventos por minuto em 3 unidades = 2.100 eventos por minuto). Portanto, você observa um retardo devido à limitação. 

Para obter um entendimento de alto nível sobre como funciona a lógica de nivelamento, consulte a seção [Formas de JSON com suporte](time-series-insights-send-events.md#supported-json-shapes).

### <a name="recommended-resolution-steps-for-excessive-throttling"></a>Etapas de resolução recomendadas para limitação excessiva
Para corrigir o retardo, aumente a capacidade do SKU do ambiente. Para obter mais informações, consulte [Como dimensionar o ambiente do Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="possible-cause-b-initial-ingestion-of-historical-data-is-causing-slow-ingress"></a>Possível causa B: a ingestão inicial de dados históricos está causando o ingresso lento
Se você estiver se conectando a uma origem do evento existente, é provável que o Hub IoT ou o hub de eventos já contenha dados. O ambiente inicia o pull dos dados desde o início do período de retenção de mensagens da origem do evento.

Esse comportamento é o comportamento padrão e não pode ser substituído. Você pode acionar a limitação e pode levar alguns instantes para a atualização da ingestão de dados históricos.

#### <a name="recommended-resolution-steps-of-large-initial-ingestion"></a>Etapas de resolução recomendadas de ingestão inicial grande
Para corrigir o retardo, execute as seguintes etapas:
1. Aumente a capacidade do SKU para o valor máximo permitido (10, neste caso). Após o aumento da capacidade, o processo de entrada começa a ser atualizado muito mais rapidamente. É possível visualizar a rapidez da atualização por meio do gráfico de disponibilidade no [explorer do Time Series Insights](https://insights.timeseries.azure.com). Você é cobrado pelo aumento da capacidade.
2. Depois que o retardo for atualizado, diminua a capacidade do SKU novamente para a taxa de entrada normal.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problema 3: a configuração *nome da propriedade do carimbo de data/hora* da origem do evento não funciona
Verifique se o nome e o valor estão em conformidade com as seguintes regras:
* O nome da propriedade do carimbo de data/hora _diferencia maiúsculas de minúsculas_.
* O valor da propriedade do carimbo de data/hora obtido da origem do evento, como uma cadeia de caracteres JSON, deve ter o formato _yyyy-MM-ddTHH:mm:ss.FFFFFFFK_. Um exemplo de uma cadeia de caracteres desse tipo é “2008-04-12T12:53Z”.

## <a name="next-steps"></a>Próximas etapas
- Para obter mais assistência, inicie uma conversa no [fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) ou no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). 
- Use também o [suporte do Azure](https://azure.microsoft.com/support/options/) para obter opções de suporte assistido.
