Há alguns limites no número de métricas e eventos por aplicativo (isto é, por chave de instrumentação).

Os limites dependem da [camada de preços](https://azure.microsoft.com/pricing/details/application-insights/) que você escolher.

**Recurso** | **Limite padrão** | **Limite máximo**
-------- | ------------- | -------------
Pontos de dados da sessão<sup>1, 2</sup> por mês | ilimitado | 
Total de pontos de dados por mês de solicitação, evento, dependência, rastreamento, exceção e exibição de página | 5 milhões | 50 milhões<sup>3</sup>
Taxa de dados de [Log e Rastreamento](../articles/application-insights/app-insights-search-diagnostic-logs.md) | 200 dp/s | 500 dp/s
Taxa de dados de [exceção](../articles/application-insights/app-insights-asp-net-exceptions.md) | 50 dp/s | 50 dp/s
Taxa total de dados de solicitação, evento, dependência e telemetria de exibição de página | 200 dp/s | 500 dp/s
Retenção de dados brutos para [Pesquisa](../articles/application-insights/app-insights-diagnostic-search.md) e [Análise](../articles/application-insights/app-insights-analytics.md) | 7 dias
Retenção de dados agregados para [Metrics Explorer](../articles/application-insights/app-insights-metrics-explorer.md) | 90 dias
Contagem de nome de [propriedade](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Comprimento do nome da propriedade | 150 | 
Comprimento do valor da propriedade | 8192 | 
Comprimento da mensagem de rastreamento e exceção | 10000 |
Contagem de nome de [métrica](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Comprimento do nome da métrica | 150 | 
[Testes de disponibilidade](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> Um ponto de dados é um valor ou evento de métrica individual, com propriedades e medidas anexadas.

<sup>2</sup> Um ponto de dados da sessão registra o início ou término de uma sessão e a identidade do usuário.

<sup>3</sup> Você pode comprar capacidade adicional além de 50 milhões.
 
[Sobre cotas e preços no Application Insights](../articles/application-insights/app-insights-pricing.md)

<!---HONumber=AcomDC_0803_2016-->