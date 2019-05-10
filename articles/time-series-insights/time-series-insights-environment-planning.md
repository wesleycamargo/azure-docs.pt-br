---
title: Planejar a escala do ambiente do Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como seguir as práticas recomendadas ao planejar um ambiente do Azure Time Series Insights, incluindo capacidade de armazenamento, retenção de dados, capacidade de entrada e monitoramento, e recuperação de desastre de neócios (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: bf1f570319370fab99e2f52086bc81df259e3d35
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236516"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planejar seu ambiente de GA de Insights de série de tempo do Azure

Este artigo descreve como planejar seu ambiente de disponibilidade geral (GA) do Azure Time Series Insights com base na taxa de entrada esperada e os requisitos de retenção de dados.

## <a name="video"></a>Vídeo

### <a name="learn-more-about-data-retention-in-azuretime-series-insights-and-how-to-plan-for-itbr"></a>Saiba mais sobre retenção de dados na análise de séries AzureTime e como planejar para ele.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Práticas recomendadas

Para começar a usar o Time Series Insights, é melhor saber a quantidade de dados esperada para envio por push por minuto, bem como por quanto tempo é necessário armazenar os dados.  

Para saber mais sobre capacidade e retenção para ambas as SKUs do Time Series Insights, confira [Preços do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Considere os seguintes atributos para planejar melhor o ambiente para o sucesso de longo prazo:

- <a href="#understand-storage-capacity">Capacidade de armazenamento</a>
- <a href="#understand-data-retention">Período de retenção de dados</a>
- <a href="#understand-ingress-capacity">Capacidade de entrada</a>
- <a href="#shape-your-events">Formação de seus eventos</a>
- <a href="#ensure-you-have-reference-data">Garantir que você tenha dados de referência no local</a>

## <a name="understand-storage-capacity"></a>Entender a capacidade de armazenamento

Por padrão, o Azure Time Series Insights retém dados com base na quantidade de armazenamento que você provisionou (unidades vezes a quantidade de armazenamento por unidade) e a entrada.

## <a name="understand-data-retention"></a>Entender a retenção de dados

Você pode definir a configuração de **Tempo de retenção de dados** do ambiente do Time Series Insights, habilitando até 400 dias de retenção. O Time Series Insights tem dois modos: um que realiza a otimização para garantir que o ambiente tenha os dados mais atualizados (ativado por padrão) e outro que realiza a otimização para garantir que os limites retenção sejam cumpridos, em que a entrada é pausada se a capacidade de armazenamento geral do ambiente é atingida.  Você pode ajustar a retenção e alternar entre os dois modos na página de configuração do ambiente no portal do Azure.

Você pode configurar no máximo 400 dias de retenção de dados no ambiente do Time Series Insights.

### <a name="configure-data-retention"></a>Configurar retenção de dados

1. No [portal do Azure](https://portal.azure.com), selecione o ambiente do Time Series Insights.

1. Na **página do ambiente do Time Series Insights**, sob o título **Configurações**, selecione **Configurar**.

1. Na caixa **Tempo de retenção de dados (em dias)**, digite um valor de 1 a 400.

   [![Configurar retenção](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Saiba mais sobre como implementar uma política de retenção de dados apropriado, revisando [como configurar a retenção](./time-series-insights-how-to-configure-retention.md).

## <a name="understand-ingress-capacity"></a>Entender a capacidade de entrada

Outra área na qual se concentrar para o planejamento é a capacidade de entrada, que é um derivativo de alocação por minuto.

De uma perspectiva de limitação, um pacote de dados recebido com um tamanho de pacote igual a 32 KB é tratado como 32 eventos, cada um com um tamanho de 1 KB. O tamanho máximo permitido do evento é de 32 KB; pacotes de dados maiores que 32 KB são truncados.

A tabela a seguir resume a capacidade de entrada para cada SKU:

|SKU  |Contagem de eventos / mês / unidade  |Tamanho de eventos / mês / unidade  |Contagem de eventos / minuto / unidade  | Tamanho / minuto / unidade   |
|---------|---------|---------|---------|---------|
|S1     |   30 milhões     |  30 GB     |  720    |  720 KB   |
|S2     |   300 milhões    |   300 GB   | 7.200   | 7.200 KB  |

Você pode aumentar a capacidade de uma SKU S1 ou S2 para até 10 unidades em um único ambiente. Você não pode migrar de um ambiente S1 para um S2 ou de um ambiente S2 para um S1.

Para a capacidade de entrada, primeiro você deve determinar a entrada total necessária por mês. Em seguida, determine quais são suas necessidades por minuto, pois a limitação e latência desempenham uma função em relação a isso.

Se houver um pico na entrada de dados com duração de menos de 24 horas, o Time Series Insights poderá "se recuperar" a uma taxa de entrada que é do dobro das taxas listadas acima.

Por exemplo, se você tiver uma única SKU S1 e dados de entrada a uma taxa de 720 eventos por minuto e o pico for de menos de 1 hora a uma taxa de 1440 eventos ou menos, não haverá latência perceptível para o ambiente. No entanto, se exceder 1440 eventos por minuto por mais de uma hora, provavelmente haverá latência nos dados visualizados e disponíveis para consulta no ambiente.

Você pode não saber com antecedência a quantidade de dados que espera enviar por push. Nesse caso, você pode encontrar a telemetria de dados do [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) e de [Hubs de Eventos do Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) no portal do Azure. Essa telemetria pode ajudar a determinar como provisionar o ambiente. Use a página **Métricas** no portal do Azure da respectiva origem de evento exibir sua telemetria. Se você compreender suas métricas de origem de eventos, poderá planejar e provisionar com mais eficiência o ambiente do Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calcular os requisitos de entrada

- Confirme se sua capacidade de entrada está acima da taxa média por minuto e se o ambiente é grande o suficiente para lidar com a entrada antecipada equivalente ao dobro da capacidade para menos de uma hora.

- Se ocorrerem picos de entrada com duração de mais de uma hora, use a taxa de pico como média e provisione um ambiente com capacidade para lidar com a taxa de pico.

### <a name="mitigate-throttling-and-latency"></a>Reduzir a latência e a limitação

Para obter informações sobre como evitar a limitação e latência, leia sobre como [reduzir a latência e limitação](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Formatar os eventos

É importante garantir a maneira como você envia eventos para TSI compatível com o tamanho do ambiente você está Provisionando (por outro lado, você pode mapear o tamanho do ambiente para quantos eventos o TSI lê e o tamanho de cada evento). Da mesma forma, é importante pensar sobre os atributos que talvez você queira dividir e filtrar ao consultar seus dados.

> [!TIP]
> Examine o documentação de formação de JSON [enviando eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-you-have-reference-data"></a>Verifique se que você tem dados de referência

Um **conjunto de dados de referência** é uma coleção de itens que aumentam os eventos da origem do evento. O mecanismo de entrada da Análise de Séries Temporais une cada evento da fonte de evento à linha de dados correspondentes em seu conjunto de dados de referência. Esse evento aumentado é disponibilizado para consulta. Essa junção baseia-se na(s) coluna(s) de chave primária definidas no conjunto de dados de referência.

Observação: os dados de referência não são unidos retroativamente. Isso significa que somente os dados de entrada atuais e futuros são correspondidos e associados ao conjunto de dados de referência, depois que foi configurado e carregado.  Se você planeja enviar muitos dados históricos para TSI, mas não carregar ou criar dados de referência no TSI antes, talvez seja necessário realizar o trabalho novamente (dica: não é divertido).  

Para saber mais sobre como criar, fazer upload e gerenciar os dados de referência no TSI, acesse nossa documentação [Conjunto de dados de referência](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Próximas etapas

- Introdução à criação [um novo ambiente do Time Series Insights no portal do Azure](time-series-insights-get-started.md).

- Saiba como [adicionar uma origem de evento do Hub de eventos](time-series-insights-how-to-add-an-event-source-eventhub.md) para análise de séries Temporais.

- Leia sobre como [configurar uma origem de evento do IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
