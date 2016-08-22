<properties 
	pageTitle="Aproveite melhor o Application Insights" 
	description="Após começar a usar o Application Insights, veja um resumo dos recursos que você pode explorar." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/10/2016" 
	ms.author="awills"/>

# Mais telemetria do Application Insights

Depois que você [adiciona o Application Insights a seu código ASP.NET](app-insights-asp-net.md), existem algumas coisas que pode fazer para obter ainda mais telemetria.

## Se seu aplicativo for executado no servidor IIS...

Se seu aplicativo estiver hospedado em servidores IIS sob seu controle, instale o Monitor de Estado do Application Insights nos servidores. Se ele já estiver instalado, você não precisará fazer nada.

1. Em cada servidor Web IIS, entre com as credenciais de administrador.
2. Baixe e execute o [instalador do Monitor de Status](http://go.microsoft.com/fwlink/?LinkId=506648).
3. No assistente de instalação, entre no Microsoft Azure.

Você não precisa fazer mais nada, mas pode confirmar se o monitoramento está habilitado para seu aplicativo.

![Estender no Azure](./media/app-insights-asp-net-more/025.png)

(Você também pode usar o Monitor de Status para [habilitar o monitoramento em tempo de execução](app-insights-monitor-performance-live-website-now.md), mesmo que não instrumente seus aplicativos no Visual Studio.)

### O que você obtém?

Se o Monitor de Status estiver instalado em seus computadores servidores, você poderá obter telemetria adicional:

* Telemetria de dependência (chamadas SQL e chamadas REST feitas por seu aplicativo) para aplicativos do .NET 4.5. (Para versões posteriores do .NET, o Monitor de Status não é necessário para a telemetria de dependência.)
* Os rastreamentos de pilha de exceção mostram mais detalhes.
* Contadores de desempenho. No Application Insights, esses contadores aparecem na folha de Servidores.

![Estender no Azure](./media/app-insights-asp-net-more/070.png)

Para ver mais ou menos contadores, [edite os gráficos](app-insights-metrics-explorer.md). Se o contador de desempenho que você deseja não estiver no conjunto disponível, você poderá [adicioná-lo ao conjunto coletado pelo módulo de contador de desempenho](app-insights-web-monitor-performance.md#system-performance-counters).

## Se for um aplicativo Web do Azure...

Se seu aplicativo for executado como um aplicativo Web, vá para o painel de controle do Azure do aplicativo ou VM e adicione a extensão do Application Insights. Em **Ferramentas**, abra **Monitoramento de desempenho** e configure o **Application Insights**. Quando solicitado, escolha o recurso Application Insights que você já criou.

![Estender no Azure](./media/app-insights-asp-net-more/05-extend.png)

### O que você obtém?

* Os rastreamentos de pilha de exceção mostram mais detalhes.
* Telemetria de dependência (chamadas SQL e chamadas REST feitas por seu aplicativo) para aplicativos do .NET 4.5. (Para versões posteriores do .NET, a extensão não é necessária para a telemetria de dependência.)

![Estender no Azure](./media/app-insights-asp-net-more/080.png)

(Você também pode usar esse método para [habilitar o monitoramento do desempenho em tempo de execução](app-insights-monitor-performance-live-website-now.md), mesmo que não instrumente seu aplicativo no Visual Studio.)


## Desempenho do navegador

Adicione o trecho de JavaScript do Application Insights para páginas da Web, a fim de obter a telemetria de navegadores cliente.

1. No Azure, abra o recurso Application Insights para seu aplicativo.
2. Abra Início Rápido, Monitor do Lado do Cliente e copie o trecho de código.
3. Cole-o para que ele apareça na parte superior de cada página da Web. Normalmente, você pode fazer isso colando na página de layout mestre.

![Estender no Azure](./media/app-insights-asp-net-more/100.png)

### O que você obtém?

* Você pode escrever JavaScript para enviar [telemetria personalizada de suas páginas da Web](app-insights-api-custom-events-metrics.md), por exemplo, para rastrear cliques de botão.
* Em [Análise](app-insights-analytics.md), dados em `pageViews` e dados AJAX em `dependencies`.
* [Dados de desempenho e de uso do cliente](app-insights-javascript.md) na folha de Navegadores.

![Estender no Azure](./media/app-insights-asp-net-more/090.png)

## Testes de disponibilidade na Web

Envie solicitações HTTP para seu aplicativo Web de todo o mundo em intervalos regulares. Alertaremos se a resposta estiver lenta ou não for confiável.

No recurso Application Insights para seu aplicativo, clique no bloco de Disponibilidade para adicionar, editar e exibir testes da Web.

Você pode adicionar vários testes que são executados em vários locais.

![Estender no Azure](./media/app-insights-asp-net-more/110.png)

[Saiba mais](app-insights-monitor-web-app-availability.md)

## Registro em log e telemetria personalizada

Os pacotes do Application Insights que você adicionou ao código fornecem uma API que você pode chamar de seu aplicativo.

* [Gere seus próprios eventos e métricas](app-insights-api-custom-events-metrics.md), por exemplo, para monitorar o desempenho ou contar eventos de negócios.
* [Capture rastreamentos de log](app-insights-asp-net-trace-logs.md) de Log4Net, NLog ou System.Diagnostics.Trace.
* [Filtre, modifique ou aumente](app-insights-api-filtering-sampling.md) a telemetria padrão enviada do seu aplicativo gravando Processadores de Telemetria.


## Análise e apresentação avançadas

Há várias maneiras de explorar seus dados. Se você tiver começado a usar o Application Insights recentemente, confira estes artigos:

||
|---|---
|[**Encontre dados de instância com a pesquisa de diagnóstico**](app-insights-visual-studio.md)<br/>pesquise e filtre eventos como solicitações, exceções, chamadas de dependência, rastreamentos de log e exibições de página. No Visual Studio, vá até o código dos rastreamentos de pilha.|![Visual Studio](./media/app-insights-asp-net/61.png)
|[**Metrics Explorer para dados agregados**](app-insights-metrics-explorer.md)<br/>explore, filtre e segmente dados agregados, como taxas de solicitações, falhas e exceções; tempos de resposta e tempos de carregamento de página.|![Visual Studio](./media/app-insights-asp-net-more/060.png)
|[**Painéis**](app-insights-dashboards.md#dashboards)<br/>faça um mashup de dados de vários recursos e compartilhe com outras pessoas. Excelente para aplicativos com vários componentes e para exibição contínua no ambiente de equipe. |![Exemplos de painéis](./media/app-insights-asp-net/62.png)
|[**Live Metrics Stream**](app-insights-metrics-explorer.md#live-metrics-stream)<br/>quando implantar um novo build, acompanhe esses indicadores de desempenho quase em tempo real para verificar se tudo está funcionando conforme esperado.|![Exemplo de análise](./media/app-insights-asp-net-more/050.png)
|[**Análise**](app-insights-analytics.md)<br/>responda perguntas difíceis sobre o desempenho e o uso do seu aplicativo usando essa poderosa linguagem de consulta.|![Exemplo de análise](./media/app-insights-asp-net-more/010.png)
|[**Alertas automáticos e manuais**](app-insights-alerts.md)<br/>alertas automáticos se adaptam aos padrões normais de telemetria do seu aplicativo e são disparados quando há algo fora do padrão normal. Você também pode definir alertas em níveis específicos de métricas padrão ou personalizadas.|![Exemplo de alerta](./media/app-insights-asp-net-more/020.png)

## Gerenciamento de dados

|||
|---|---|
|[**Exportação contínua**](app-insights-export-telemetry.md)<br/>copie toda a telemetria para um armazenamento para poder analisá-la como quiser.|
|**API de acesso a dados**<br/>em breve.|
|[**Amostragem**](app-insights-sampling.md)<br/>reduz a taxa de dados e ajuda você a permanecer dentro do limite de seu tipo de preço.|![Bloco de amostragem](./media/app-insights-asp-net-more/030.png)

<!---HONumber=AcomDC_0810_2016-->