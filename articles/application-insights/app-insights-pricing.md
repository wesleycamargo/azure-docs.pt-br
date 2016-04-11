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
	ms.date="03/15/2016" 
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

* Em cada mês corrido, o aplicativo pode enviar no máximo quantidade especificada de telemetria ao Application Insights. Atualmente, a cota para o tipo de preços gratuito é de 5 milhões de pontos de dados por mês e muito mais para outros esquemas. Caso a cota seja atingida, é possível comprar mais pontos. Consulte o [esquema de preços][pricing] para ver os números reais. 
* A cota depende da camada de preços que você escolheu.
* A cota é contada a partir de meia-noite UTC até o primeiro dia de cada mês.
* O gráfico de pontos de dados mostra o quanto da sua cota foi usada neste mês.
* A cota é medida em *pontos de dados.* Um único ponto de dados é uma chamada para um dos métodos de Rastreamento, seja chamado explicitamente no seu código ou por um dos módulos padrão de telemetria. Ele pode ter várias propriedades e métricas anexadas.
* Pontos de dados são gerados por:
 * [Módulos SDK](app-insights-configuration-with-applicationinsights-config.md) que coletam dados automaticamente, por exemplo, para relatar uma solicitação ou falha ou para medir o desempenho.
 * Chamadas à [API](app-insights-api-custom-events-metrics.md) `Track...` que você escreveu, como `TrackEvent` ou `trackPageView`.
 * [Testes da Web de disponibilidade](app-insights-monitor-web-app-availability.md) que você configurou.
* Enquanto estiver depurando, é possível ver os pontos de dados sendo enviados de seu aplicativo na janela de saída do Visual Studio. Eventos de cliente podem ser vistos ao abrir a guia de rede no painel de depuração de seu navegador (geralmente F12).
* *Dados da sessão* não são contados na cota. Isso inclui contagens de usuários, sessões, ambiente e os dados do dispositivo.
* Se quiser fazer a contagem de pontos de dados por inspeção, é possível encontrá-los em vários locais:
 * Cada item exibido na [pesquisa de diagnóstico](app-insights-diagnostic-search.md), que inclui solicitações HTTP, exceções, rastreamentos de log, modos de exibição de página, eventos de dependência e eventos personalizados.
 * Cada medida bruta de uma [métrica](app-insights-metrics-explorer.md), como um contador de desempenho. (Os pontos que você vê nos gráficos normalmente são agregados de vários pontos de dados brutos.)
 * Cada ponto em um gráfico de disponibilidade da Web também é uma agregação de vários pontos de dados.
* Também é possível inspecionar os pontos de dados individuais na origem durante a depuração:
 * Caso execute seu aplicativo no modo de depuração no Visual Studio, os pontos de dados são registrados na janela de saída. 
 * Para exibir os pontos de dados do cliente, abra o painel de depuração do navegador (geralmente F12) e abra a guia de rede.
* A taxa de dados pode ser (por padrão) reduzida por [amostragem adaptável](app-insights-sampling). Isso significa que, conforme o uso de seu aplicativo aumenta, a taxa de telemetria não aumentará tanto quanto você poderia esperar.

### Excedente

Se seu aplicativo enviar mais do que a cota mensal, você pode:

* Pague por dados adicionais. Consulte o [esquema de preços][pricing] para obter detalhes. Você pode escolher essa opção com antecedência. Essa opção não está disponível na faixa de preços Gratuita.
* Atualize sua camada de preços.
* Não faça nada. Dados de sessão continuarão a ser gravados, mas outros dados não aparecerão na pesquisa de diagnóstico ou no Metrics Explorer.


### Quantos dados estou enviando?

O gráfico na parte inferior da folha de preços mostra o volume de ponto de dados do aplicativo, agrupados por tipo de ponto de dados. (Você também pode criar esse gráfico no Gerenciador de métricas).

![Na parte inferior da folha de preços](./media/app-insights-pricing/03-allocation.png)

Clique no gráfico para obter mais detalhes ou arraste sobre ele e clique em (+) para obter detalhes de um intervalo de tempo.

O gráfico mostra o volume de dados que chegam no serviço Application Insights após a [amostragem](app-insights-sampling).


## Taxa de dados

Além de cota mensal, existe limitação na taxa de dados. Para o [tipo de preços][pricing] gratuito o limite é de 200 dados de pontos/segundo na média em 5 minutos e para as camadas pagas, ele é 500/s na média depois de 1 minuto.

Há três classificações que são contados separadamente:

* [Chamadas do TrackTrace](app-insights-api-custom-events-metrics.md#track-trace) e [logs capturados](app-insights-asp-net-trace-logs.md)
* [Exceções](app-insights-api-custom-events-metrics.md#track-exception), limitado a 50 pontos/s.
* Todos os outros dados de telemetria (exibições de página, sessões, solicitações, dependências, métricas, eventos personalizados, resultados de teste da Web).





*O que acontece se o aplicativo exceder a taxa por segundo?*

* O volume de dados que seu aplicativo envia é avaliado a cada minuto. Se ele exceder a taxa por segundo média por minuto, o servidor recusa algumas solicitações. Algumas versões do SDK tentam reenviar, criando uma sobretensão por alguns minutos; outras, como o SDK do JavaScript, simplesmente descartam os dados recusados.

Caso ocorra uma limitação, será exibido um aviso de notificação que isso aconteceu.

*Como posso saber quantos pontos de dados meu aplicativo está enviando?*

* Abra Configurações/Cotas e Preços para ver o gráfico de Volume de dados.
* Ou, no Metrics Explorer, adicione um novo gráfico e selecione **Volume de pontos de dados** como a métrica. Ative o Agrupamento e agrupe por **Tipo de dados**.


### Dicas para reduzir a sua taxa de dados

Se você encontrar as limitações, aqui há algumas coisas que você pode fazer:

* Use a [amostragem](app-insights-sampling.md). Essa tecnologia reduz a taxa de dados sem distorcer suas métricas e sem afetar a capacidade de navegar entre itens relacionados na Pesquisa.
* [Limite o número de chamadas do Ajax que podem ser informadas](app-insights-javascript.md#detailed-configuration) em cada modo de exibição de página ou desative o relatório de Ajax.
* Desative os módulos de coleção que você não precisa [editando o ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Por exemplo, você pode decidir que os contadores de desempenho ou dados de dependência não são essenciais.
* Métricas de pré-agregação. Se você colocou chamadas ao TrackMetric no seu aplicativo, você pode reduzir o tráfego usando a sobrecarga que aceita o cálculo da média e o desvio padrão de um lote de medições. Ou então, você pode usar um [pacote de pré-agregação](https://www.myget.org/gallery/applicationinsights-sdk-labs). 


### Limites de nome

1.	Máximo de 200 nomes de métrica exclusivos e 200 nomes de propriedade exclusivos para seu aplicativo. As métricas incluem dados enviados por meio de TrackMetric, bem como as medidas em outros tipos de dados, como Eventos. Os[ nomes de propriedades e métricas][api] são globais por chave de instrumentação, mas não têm escopo definido segundo o tipo de dados.
2.	As [propriedades][apiproperties] podem ser usadas para filtragem e agrupamento, somente enquanto tiverem menos de 100 valores exclusivos para cada propriedade. Depois que os valores exclusivos excederem 100, a propriedade ainda pode ser usada para pesquisa e filtragem, mas não para filtros.
3.	As propriedades padrão como Solicitar Nome e URL da Página estão limitadas a 1000 valores exclusivos por semana. Depois de 1000 valores exclusivos, os valores adicionais são marcados como "Outros valores". O valor original ainda pode ser usado para filtragem e pesquisa de texto completo.

## Retenção de dados

A camada de preços determina quanto tempo os dados são mantidos no portal e, portanto, quanto você pode definir para atrás os intervalos de tempo.


* Pontos de dados brutos (ou seja, instâncias que você pode inspecionar na Pesquisa de Diagnóstico): entre sete dias.
* Os dados agregados (ou seja, contagens, médias e outros dados estatísticos que você vê no Gerenciador de Métricas) são mantidos em um detalhamento de um minuto por 30 dias e de uma hora ou de um dia (dependendo do tipo) por 90 dias.


## Amostragem

[Amostragem](app-insights-sampling.md) é um método de redução do volume de telemetria retida pelo seu aplicativo, ao mesmo tempo que ainda retém a capacidade de encontrar eventos relacionados durante as pesquisas de diagnóstico, retendo também as contagens de eventos corretas. A amostragem o ajuda a se manter dentro de sua cota mensal.

Há várias formas de amostragem. Recomendamos a [amostragem adaptável](app-insights-sampling.md), que se ajusta automaticamente ao volume de telemetria enviado por seu aplicativo. Ela opera no SDK em seu aplicativo Web, para que o tráfego de telemetria na rede seja reduzido. É possível usá-la se a estrutura do aplicativo Web for o .NET: basta instalar a versão (beta) mais recente do SDK.

Como alternativa, é possível definir a *amostragem de ingestão* na folha Cotas e Preço. Essa forma de amostragem opera no ponto em que a telemetria de seu aplicativo entra no serviço do Application Insights. Ela não afeta o volume de telemetria enviado de seu aplicativo, mas reduz o volume retido pelo serviço.

![Na folha Cota e preço, clique no bloco Exemplos e selecione uma fração de amostragem.](./media/app-insights-sampling/04.png)

A amostragem é uma maneira eficiente de reduzir encargos e permanecer dentro de sua cota mensal. O algoritmo de amostragem retém itens de telemetria relacionados, para que, por exemplo, quando a Pesquisa for utilizada, seja possível encontrar a solicitação relacionada a uma exceção específica. O algoritmo também retém contagens corretas, para que você veja os valores corretos no Gerenciador de Métricas referentes a taxas de solicitação, taxas de exceção e outras contagens.


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

 

<!---HONumber=AcomDC_0330_2016-->