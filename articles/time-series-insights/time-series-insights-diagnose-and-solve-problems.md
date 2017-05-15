---
title: Diagnosticar e resolver problemas | Microsoft Docs
description: Este tutorial aborda como diagnosticar e resolver problemas no ambiente do Time Series Insights
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/24/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 46d58e94f3fede3f7f19de8d97e23ac6910bc12a
ms.contentlocale: pt-br
ms.lasthandoff: 05/01/2017

---
# <a name="diagnose-and-solve-problems"></a>Diagnosticar e resolver problemas

## <a name="i-do-not-see-my-data"></a>Não consigo ver meus dados
Estes são alguns motivos pelos quais talvez você não consiga ver seus dados no ambiente do [Portal do Time Series Insights](https://insights.timeseries.azure.com).

### <a name="does-your-event-source-have-data-in-json-format"></a>A origem do evento tem dados no formato JSON?
Atualmente, o Azure Time Series Insights dá suporte apenas a dados JSON. Para obter amostras de JSON, consulte a seção *Formas de JSON com suporte* [aqui](time-series-insights-send-events.md#supported-json-shapes).

### <a name="when-registering-your-event-source-did-you-provide-the-key-with-required-permissions"></a>Ao registrar a origem do evento, você forneceu a chave com as permissões necessárias?
1. Para o Hub IoT, você precisa fornecer a chave com a permissão de *conexão de serviço*.

   ![Permissão de conexão de serviço do Hub IoT](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Conforme mostrado na imagem anterior, a política “iothubowner” ou “service” funcionará, pois ambas têm a permissão de “conexão de serviço”.
2. Para o Hub de Eventos, você precisa fornecer a chave com a permissão de *Escuta*.

   ![Permissão de escuta do hub de eventos](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Conforme mostrado na imagem anterior, a política “read” ou “manage” funcionará, pois ambas têm a permissão de “leitura”

### <a name="are-you-sure-that-the-consumer-group-provided-is-exclusive-to-time-series-insights"></a>Você tem certeza de que o grupo de consumidores fornecido é exclusivo ao Time Series Insights?
Para o Hub IoT ou o Hub de Eventos, durante o registro, solicitamos que você especifique o grupo de consumidores que deve ser usado durante a leitura dos dados. Esse grupo de consumidores não deve ser compartilhado. Se ele for compartilhado, o hub de eventos subjacente desconectará automaticamente um dos leitores de forma aleatória.

## <a name="i-see-my-data-but-there-is-a-lag"></a>Consigo ver meus dados, mas há um retardo
Estes são alguns motivos pelos quais você possa ver dados parciais no ambiente do [Portal do Time Series Insights](https://insights.timeseries.azure.com).

### <a name="your-environment-might-be-getting-throttled"></a>O ambiente pode estar ficando restrito
A limitação é imposta de acordo com a capacidade e o tipo de SKU do ambiente. Todas as origens do evento no ambiente compartilham essa capacidade. Se a origem do evento do hub de eventos/hub IoT estiver enviando dados por push além dos limites impostos, você observará uma limitação e um retardo.

O diagrama a seguir mostra um ambiente do Time Series Insights com um SKU S1 e uma capacidade 3. Ele pode ingressar 3 milhões de eventos por dia.

![Capacidade atual do SKU do ambiente](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Suponha que esse ambiente estava ingerindo mensagens de um hub de eventos com uma taxa de entrada, conforme mostrado no seguinte diagrama:

![Capacidade atual do SKU do ambiente](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Conforme mostrado no diagrama, a taxa de entrada diária é de ~67.000 mensagens. Essa taxa converte aproximadamente 46 mensagens a cada minuto. Se cada mensagem do hub de eventos for nivelada para um único evento do Time Series Insights, esse ambiente não terá nenhuma limitação. Se cada mensagem do hub de eventos for nivelada para 100 eventos do Time Series Insights, 4.600 eventos deverão ser ingeridos a cada minuto. Um ambiente do SKU S1 com uma capacidade igual a três pode apenas ingressar 2.100 eventos a cada minuto. (1 milhão de eventos por dia => 700 eventos por minuto, 3 unidades => 2.100 eventos por minuto). Portanto, você observa um retardo devido à limitação. Para obter um entendimento de alto nível sobre como funciona a lógica de nivelamento, consulte a seção *Formas de JSON com suporte* [aqui](time-series-insights-send-events.md#supported-json-shapes).

#### <a name="recommended-steps"></a>Etapas recomendadas
Para corrigir o retardo, aumente a capacidade do SKU do ambiente. [Como dimensionar o ambiente do Time Series Insights](time-series-insights-how-to-scale-your-environment.md)

### <a name="you-might-be-pushing-historical-data-and-hence-the-slow-ingress"></a>Você pode estar enviando dados históricos por push e, por essa razão, a entrada lenta
Se você estiver se conectando a uma origem do evento existente, é provável que o hub de eventos/hub IoT já contenha dados. Portanto, o ambiente inicia o pull dos dados desde o início do período de retenção de mensagens da origem do evento. Esse comportamento é o comportamento padrão e não pode ser substituído. A limitação pode ser ativada e pode levar alguns instantes para a atualização da ingestão de dados históricos.

#### <a name="recommended-steps"></a>Etapas recomendadas
Para corrigir o retardo, realize as seguintes etapas:
1. Aumente a capacidade do SKU para o valor máximo permitido (10, neste caso). Após o aumento da capacidade, o processo de entrada começa a ser atualizado muito mais rapidamente. É possível visualizar a rapidez da atualização por meio do gráfico de disponibilidade no [Portal do Time Series Insights](https://insights.timeseries.azure.com). Você é cobrado pelo aumento da capacidade.
2. Depois que o retardo for atualizado, diminua a capacidade do SKU novamente para a taxa de entrada normal.

## <a name="my-event-source-timestamp-property-name-setting-does-not-work"></a>A configuração *nome da propriedade do carimbo de data/hora* de minha origem do evento não funciona
Verifique se o nome e o valor seguem estas regras:
1. O nome da propriedade do carimbo de data/hora __diferencia maiúsculas de minúsculas__.
2. O valor da propriedade do carimbo de data/hora obtido da origem do evento, como uma cadeia de caracteres JSON, deve ter o formato __yyyy-MM-ddTHH:mm:ss.FFFFFFFK__. Um exemplo de uma cadeia de caracteres desse tipo é “2008-04-12T12:53Z”.
