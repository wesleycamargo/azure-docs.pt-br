Há alguns limites no número de métricas e eventos por aplicativo (isto é, por chave de instrumentação). Os limites dependem do [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) que você escolher.

| **Recurso** | **Limite padrão** | **Observação**
| --- | --- | --- |
| Total de dados por dia | 500 GB | Você pode reduzir os dados ao definir um limite. Se precisar de mais, envie um email para AIDataCap@microsoft.com.
| Dados gratuitos por mês<br/> (Plano de preços básico) | 1 GB | Dados adicionais são cobrados por gigabyte.
| Limitação | 32.000 eventos/s | O limite é medido em um minuto.
| Retenção de dados | 90 dias | Este recurso destina-se a [Pesquisa](../articles/application-insights/app-insights-diagnostic-search.md), [Análise](../articles/application-insights/app-insights-analytics.md) e [Metrics Explorer](../articles/application-insights/app-insights-metrics-explorer.md).
| Retenção de resultados detalhados do [Teste de disponibilidade de várias etapas](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) | 90 dias | Esse recurso fornece resultados detalhados de cada etapa.
| Tamanho dos nomes de propriedade e métrica | 150 |
| Tamanho da cadeia de caracteres do valor da propriedade | 8.192 |
| Comprimento da mensagem de rastreamento e de exceção | 10.000 |
| Contagem de [testes de disponibilidade](../articles/application-insights/app-insights-monitor-web-app-availability.md) por aplicativo  | 10 |

Para obter mais informações, consulte [sobre preços e cotas no Application Insights](../articles/application-insights/app-insights-pricing.md).


<!--HONumber=Feb17_HO2-->


