Há alguns limites no número de métricas e eventos por aplicativo (isto é, por chave de instrumentação). 

Os limites dependem do [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) que você escolher.

| **Recurso** | **Limite padrão** | **Observação**
| --- | --- | --- |
| Total de dados por dia | 100 GB | Você pode reduzir, definindo um limite. Se precisar de mais, envie um email a AIDataCap@microsoft.com 
| Dados gratuitos por mês<br/> (Plano de preços básico) | 1 GB | Dados adicionais cobrados por GB
| Retenção de dados | 90 dias | Para [Pesquisa](../articles/application-insights/app-insights-diagnostic-search.md), [Análise](../articles/application-insights/app-insights-analytics.md) e [Metrics explorer](../articles/application-insights/app-insights-metrics-explorer.md)
| Retenção de resultados detalhados do [Teste de disponibilidade de várias etapas](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) | 90 dias | Resultados detalhados de cada etapa
| Contagem de nomes de [Propriedade](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) e [Métrica](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)<sup>2</sup> | 200 | 
| Tamanho dos nomes de propriedade e métrica | 150 |
| Tamanho da cadeia de caracteres do valor da propriedade | 8192 |
| Valores distintos para as propriedades<sup>3,4</sup> | 100 | >100 => não é possível usar a propriedade como filtro no Metrics Explorer
| Comprimento da mensagem de rastreamento e exceção | 10000 |
| Contagem de [testes de disponibilidade](../articles/application-insights/app-insights-monitor-web-app-availability.md) por aplicativo  | 10 |

1. Todos esses números são por chave de instrumentação.
2. Nomes de métrica são definidos no TrackMetric e no parâmetro de medição de outras chamadas Track*(). Os nomes de métricas são globais por chave de instrumentação.
3. As propriedades podem ser usadas para filtragem e agrupamento, somente enquanto tiverem menos de 100 valores exclusivos para cada propriedade. Depois que o número de valores exclusivos exceder 100, ainda é possível pesquisar a propriedade, mas não a use mais para filtros ou agrupamentos.
4. As propriedades padrão como Solicitar Nome e URL da Página estão limitadas a 1000 valores exclusivos por semana. Depois de 1000 valores exclusivos, os valores adicionais são marcados como "Outros valores". O valor original ainda pode ser usado para filtragem e pesquisa de texto completo.


[Sobre cotas e preços no Application Insights](../articles/application-insights/app-insights-pricing.md)

<!--HONumber=Dec16_HO3-->


