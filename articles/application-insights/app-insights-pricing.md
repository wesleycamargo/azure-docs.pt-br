<properties 
	pageTitle="Gerenciar cotas e preços do Application Insights" 
	description="Escolher o plano de preço certo para você" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2015" 
	ms.author="awills"/>

# Gerenciar cotas e preços do Application Insights

*O Application Insights está em modo de visualização.*

Os [preços][pricing] do [Visual Studio Application Insights][start] baseiam-se no volume de dados por aplicativo. Há uma camada gratuita significativa na qual você obtém a maioria dos recursos com algumas limitações.

Cada recurso Application Insights é cobrado como um serviço separado e contribui para a conta da sua assinatura do Azure.

[Consulte o esquema de preços][pricing].

## Examine o plano de preços e cotas para o recurso do Application Insights

Você pode abrir a folha de Cota + Preços nas Configurações do recurso do aplicativo.

![Escolha Configurações, Cota + Preços.](./media/app-insights-pricing/01-pricing.png)

A sua opção de esquema de preços afeta à:

* [Cota mensal](#monthly-quota) - a quantidade de telemetria que você pode analisar a cada mês.
* [Taxa de dados](#data-rate) - a taxa máxima em que os dados do seu aplicativo podem ser processados.
* [Retenção](#data-retention) - quanto tempo os dados são mantidos no portal do Application Insights para exibição.
* [Exportação contínua](#continuous-export)- se que você pode exportar dados para outras ferramentas e serviços.

Esses limites são definidos separadamente para cada recurso do Application Insights.

### Avaliação gratuita Premium

Quando você cria um novo recurso do Application Insights pela primeira vez, ele começa na camada Gratuita.

A qualquer momento, você pode alternar para a avaliação Premium gratuita de 30 dias. Isso concede a você os benefícios da camada Premium. Após 30 dias, ela será revertida automaticamente para a camada anterior, a menos que você escolha explicitamente outra camada. Você pode selecionar a camada que você deseja a qualquer momento durante o período de avaliação, mas você ainda terá a avaliação gratuita até o término do período de 30 dias.


## Cota mensal

* Em cada mês corrido, o aplicativo pode enviar no máximo quantidade especificada de telemetria ao Application Insights. Consulte o [esquema de preços][pricing] para ver os números reais. 
* A cota depende da camada de preços que você escolheu.
* A cota é contada a partir de meia-noite UTC até o primeiro dia de cada mês.
* O gráfico de pontos de dados mostra o quanto da sua cota foi usada neste mês.
* A cota é medida em *pontos de dados.* Um único ponto de dados é uma chamada para um dos métodos de Rastreamento, seja chamado explicitamente no seu código ou por um dos módulos padrão de telemetria. Os pontos de dados incluem:
 * Cada linha que você vê na [pesquisa de diagnóstico](app-insights-diagnostic-search.md). 
 * Cada medida bruta de uma [métrica](app-insights-metrics-explorer.md), como um contador de desempenho. (Os pontos que você vê nos gráficos normalmente são agregados de vários pontos de dados brutos.)
 * Cada ponto nos gráficos de [teste da Web (disponibilidade)](app-insights-monitor-web-app-availability.md). 
* *Dados da sessão* não são contados na cota. Isso inclui contagens de usuários, sessões, ambiente e os dados do dispositivo.


### Excedente

Se seu aplicativo enviar mais do que a cota mensal, você pode:

* Pague por dados adicionais. Consulte o [esquema de preços][pricing] para obter detalhes. Você pode escolher essa opção com antecedência. Essa opção não está disponível na faixa de preços Gratuita.
* Atualize sua camada de preços.
* Não faça nada. Dados de sessão continuarão a ser gravados, mas outros dados não aparecerão na pesquisa de diagnóstico ou no Metrics Explorer.


### Quantos dados estou enviando?

O gráfico na parte inferior da folha de preços mostra o volume de ponto de dados do aplicativo, agrupados por tipo de ponto de dados. (Você também pode criar esse gráfico no Gerenciador de métricas).

![Na parte inferior da folha de preços](./media/app-insights-pricing/03-allocation.png)

Clique no gráfico para obter mais detalhes ou arraste sobre ele e clique em (+) para obter detalhes de um intervalo de tempo.


## Taxa de dados

Além de cota mensal, existe limitação na taxa de dados. Para a [camada de preços][pricing] gratuita o limite é de 200 dados de pontos/segundo na média em 5 minutos e para as camadas pagas, ele é 500/s na média depois de 1 minuto.

Há três classificações que são contados separadamente:

* [Chamadas do TrackTrace](app-insights-api-custom-events-metrics.md#track-trace) e [logs capturados](app-insights-asp-net-trace-logs.md)
* [Exceções](app-insights-api-custom-events-metrics.md#track-exception), limitado a 50 pontos/s.
* Todos os outros dados de telemetria (exibições de página, sessões, solicitações, dependências, métricas, eventos personalizados, resultados de teste da Web).

Se seu aplicativo enviar acima do limite durante alguns minutos, alguns dados poderão ser descartados. Você verá um aviso de notificação que isso aconteceu.

### Dicas para reduzir a sua taxa de dados

Se você encontrar as limitações, aqui há algumas coisas que você pode fazer:

* Use [Amostragem](app-insights-sampling.md). Essa tecnologia reduz a taxa de dados sem distorcer suas métricas e sem afetar a capacidade de navegar entre itens relacionados na Pesquisa.
* Desative os módulos de coleção que você não precisa [editando o ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Por exemplo, você pode decidir que os contadores de desempenho ou dados de dependência não são essenciais.
* Métricas de pré-agregação. Se você colocou chamadas ao TrackMetric no seu aplicativo, você pode reduzir o tráfego usando a sobrecarga que aceita o cálculo da média e o desvio padrão de um lote de medições. Ou então, você pode usar um [pacote de pré-agregação](https://www.myget.org/gallery/applicationinsights-sdk-labs). 


### Limites de nome

1.	Máximo de 200 nomes exclusivos de métrica e 200 nomes de propriedade exclusivo para seu aplicativo. As métricas incluem o envio de dados por meio de TrackMetric, bem como as medidas em outros tipos de dados, como Eventos. Os[ nomes de propriedades e métricas][api] são globais por chave de instrumentação, mas não têm escopo definido segundo o tipo de dados.
2.	As [propriedades][apiproperties] podem ser usadas para filtragem e agrupamento, somente enquanto tiverem menos de 100 valores exclusivos para cada propriedade. Depois que os valores exclusivos excederem 100, a propriedade ainda pode ser usada para pesquisa e filtragem, mas não para filtros.
3.	As propriedades padrão como Solicitar Nome e URL da Página estão limitadas a 1000 valores exclusivos por semana. Depois de 1000 valores exclusivos, os valores adicionais são marcados como "Outros valores". O valor original ainda pode ser usado para filtragem e pesquisa de texto completo.

## Retenção de dados

A camada de preços determina quanto tempo os dados são mantidos no portal e, portanto, quanto você pode definir para atrás os intervalos de tempo.


* Pontos de dados brutos (ou seja, instâncias que você pode inspecionar na pesquisa de diagnóstico): entre 7 e 30 dias.
* Os dados agregados (ou seja, contagens, médias e outros dados estatísticos que você vê no Metric Explorer) são mantidos em um detalhamento de 1 minuto por 30 dias, e 1 hora ou 1 dia (dependendo do tipo) por 13 meses.




## Examine a conta da sua assinatura do Azure

Encargos do Application Insights são adicionados à sua conta do Azure. Você pode ver os detalhes da sua conta do Azure cobrar na seção de Cobrança do Portal do Azure ou no [Portal de Cobrança do Azure](https://account.windowsazure.com/Subscriptions).

![No menu lateral, escolha Cobrança.](./media/app-insights-pricing/02-billing.png)

## Resumo de limites

[AZURE.INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=AcomDC_1125_2015-->