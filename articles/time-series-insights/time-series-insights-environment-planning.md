---
title: Planejar a escala do ambiente do Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como seguir as práticas recomendadas ao planejar um ambiente do Azure Time Series Insights, incluindo capacidade de armazenamento, retenção de dados, capacidade de entrada e monitoramento.
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 45f081c4e1dbd32b46c8a69f32b0b205b948f9b5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652314"
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Planejar o ambiente do Azure Time Series Insights

Este artigo descreve como planejar o ambiente do Azure Time Series Insights com base na taxa de entrada esperada e nos requisitos de retenção de dados.

## <a name="best-practices"></a>Práticas recomendadas

Para começar a usar o Time Series Insights, é melhor saber a quantidade de dados esperada para envio por push por minuto, bem como por quanto tempo é necessário armazenar os dados.  

Para saber mais sobre capacidade e retenção para ambas as SKUs do Time Series Insights, confira [Preços do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Considere os seguintes atributos para planejar melhor o ambiente para o sucesso de longo prazo: 
- Capacidade de armazenamento
- Período de retenção de dados
- Capacidade de entrada 
- Formação de seus eventos
- Garantia de que você tem dados de referência em vigor

## <a name="understand-storage-capacity"></a>Entender a capacidade de armazenamento
Por padrão, Time Series Insights retém a dados com base na quantidade de armazenamento que você provisionou (unidades vezes a quantidade de armazenamento por unidade) e a entrada.

## <a name="understand-data-retention"></a>Entender a retenção de dados
Você pode definir a configuração de **Tempo de retenção de dados** do ambiente do Time Series Insights, habilitando até 400 dias de retenção.  O Time Series Insights tem dois modos: um que realiza a otimização para garantir que o ambiente tenha os dados mais atualizados (ativado por padrão) e outro que realiza a otimização para garantir que os limites retenção sejam cumpridos, em que a entrada é pausada se a capacidade de armazenamento geral do ambiente é atingida.  Você pode ajustar a retenção e alternar entre os dois modos na página de configuração do ambiente no portal do Azure.

Você pode configurar no máximo 400 dias de retenção de dados no ambiente do Time Series Insights.

## <a name="configure-data-retention"></a>Configurar retenção de dados

1. No [portal do Azure](https://portal.azure.com), selecione o ambiente do Time Series Insights.

2. Na **página do ambiente do Time Series Insights**, sob o título **Configurações**, selecione **Configurar**. 

3. Na caixa **Tempo de retenção de dados (em dias)**, digite um valor de 1 a 400.

   ![Configurar retenção](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>Entender a capacidade de entrada

Outra área na qual se concentrar para o planejamento é a capacidade de entrada, que é um derivativo de alocação por minuto. 

De uma perspectiva de limitação, um pacote de dados recebido com um tamanho de pacote igual a 32 KB é tratado como 32 eventos, cada um com um tamanho de 1 KB. O tamanho máximo permitido do evento é de 32 KB; pacotes de dados maiores que 32 KB são truncados.

A tabela a seguir resume a capacidade de entrada para cada SKU:

|SKU  |Contagem de eventos por mês, por unidade  |Tamanho de eventos por mês, por unidade  |Contagem de eventos por minuto, por unidade  | Tamanho por minuto, por unidade   |
|---------|---------|---------|---------|---------|
|S1     |   30 milhões     |  30 GB     |  700    |  700 KB   |
|S2     |   300 milhões    |   300 GB   | 7.000   | 7.000 KB  |

Você pode aumentar a capacidade de uma SKU S1 ou S2 para até 10 unidades em um único ambiente. Você não pode migrar de um ambiente S1 para um S2 ou de um ambiente S2 para um S1. 

Para a capacidade de entrada, primeiro você deve determinar a entrada total necessária por mês. Em seguida, determine quais são suas necessidades por minuto, pois a limitação e latência desempenham uma função em relação a isso.

Se houver um pico na entrada de dados com duração de menos de 24 horas, o Time Series Insights poderá "se recuperar" a uma taxa de entrada que é do dobro das taxas listadas acima. 

Por exemplo, se você tiver uma única SKU S1 e dados de entrada a uma taxa de 700 eventos por minuto, e o pico for de menos de uma hora a uma taxa de 1400 eventos ou menos, não haverá latência perceptível para o ambiente. No entanto, se você exceder 1400 eventos por minuto por mais de uma hora, provavelmente haverá latência nos dados visualizados e disponíveis para consulta no ambiente. 

Você pode não saber com antecedência a quantidade de dados que espera enviar por push. Nesse caso, você pode encontrar a telemetria de dados do [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) e de [Hubs de Eventos do Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) no portal do Azure. Essa telemetria pode ajudar a determinar como provisionar o ambiente. Use a página **Métricas** no portal do Azure da respectiva origem de evento exibir sua telemetria. Se você compreender suas métricas de origem de eventos, poderá planejar e provisionar com mais eficiência o ambiente do Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calcular os requisitos de entrada

- Confirme se sua capacidade de entrada está acima da taxa média por minuto e se o ambiente é grande o suficiente para lidar com a entrada antecipada equivalente ao dobro da capacidade para menos de uma hora.

- Se ocorrerem picos de entrada com duração de mais de uma hora, use a taxa de pico como média e provisione um ambiente com capacidade para lidar com a taxa de pico.
 
### <a name="mitigate-throttling-and-latency"></a>Reduzir a latência e a limitação

Para obter informações sobre como evitar a limitação e a latência, confira [Reduzir a latência e a limitação](time-series-insights-environment-mitigate-latency.md). 

## <a name="shaping-your-events"></a>Formação de seus eventos
É importante garantir que a maneira como você envia eventos para TSI dê suporte ao tamanho do ambiente que você está provisionando (por outro lado, você pode mapear o tamanho do ambiente a quantos eventos o TSI lê e o tamanho de cada evento).  Da mesma forma, é importante pensar sobre os atributos que talvez você queira dividir e filtrar ao consultar seus dados.  Com isso em mente, é recomendável revisar a seção de formação de JSON de nossa documentação *Enviar eventos* [documentação] (https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).  Está perto do final da página.  

## <a name="ensuring-you-have-reference-data-in-place"></a>Garantia de que você tem dados de referência em vigor
Um conjunto de dados de referência é uma coleção de itens que aumentam os eventos da fonte de evento. O mecanismo de entrada da Análise de Séries Temporais une cada evento da fonte de evento à linha de dados correspondentes em seu conjunto de dados de referência. Esse evento aumentado é disponibilizado para consulta. Essa junção baseia-se na(s) coluna(s) de chave primária definidas no conjunto de dados de referência.

Observação: os dados de referência não são unidos retroativamente. Isso significa que somente os dados de entrada atuais e futuros são correspondidos e associados ao conjunto de dados de referência, depois que foi configurado e carregado.  Se você planeja enviar muitos dados históricos para TSI, mas não carregar ou criar dados de referência no TSI antes, talvez seja necessário realizar o trabalho novamente (dica: não é divertido).  

Para saber mais sobre como criar, carregar e gerenciar os dados de referência no TSI, acesse nossa documentação *dados de referência* [documentação] (https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).


## <a name="next-steps"></a>Próximas etapas
- [Como adicionar uma origem do evento do Hub de Eventos](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [Como adicionar uma origem do evento do Hub IoT](time-series-insights-how-to-add-an-event-source-iothub.md)
