<properties title="Application Insights" pageTitle="Application Insights - iniciar o monitoramento de integridade e uso do seu aplicativo" description="Analise o uso, disponibilidade e desempenho de seu local ou um aplicativo Web do Microsoft Azure com o Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />

# Application Insights - Iniciar o monitoramento de integridade e uso do seu aplicativo

*O Application Insights está em modo de visualização.*

O Application Insights permite que você monitore seu aplicativo ao vivo para:

* **Disponibilidade** - Testaremos suas URLs a cada poucos minutos em todo o mundo.
* **Desempenho** - Detecte e diagnostique problemas de desempenho e exceções.
* **Uso** - Descubra o que os usuários estão fazendo com o seu aplicativo, para que você possa fazer melhor do que eles.

A configuração é muito fácil, e você verá os resultados em minutos. Atualmente, suportamos aplicativos de web ASP.NET (em seus próprios servidores ou no Azure).


## Introdução

Inicie com qualquer combinação, em qualquer ordem, dos pontos de entrada à esquerda desta tabela. Escolha as rotas que funcionam para você. Se estiver desenvolvendo um aplicativo Web ASP.NET, inicie ao adicionar o Application Insights ao seu projeto Web - é fácil adicionar outros elementos posteriormente.

Você precisará de uma conta no [Microsoft Azure](http://azure.com) (a não ser que esteja usando a versão VSO).

<table >
<tr valign="top"><th>O que você precisa</th><th colspan="2">O que fazer</th><th>O que você ganha</th></tr>
<tr valign="top"><td>Obter análise de desempenho e uso para meu aplicativo ASP.NET</td><td colspan="2"><a href="../app-insights-start-monitoring-app-health-usage/">Adicionar o Application Insights ao seu projeto Web</a></td><td>Métricas de desempenho: contagens de carga, tempos de resposta, ...</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Enviar eventos e métricas do código de seu servidor</a></td><td>Análise empresarial personalizada</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">Enviar telemetria de rastreamento e de exceção de seu servidor ou capturar dados de log de terceiros.</td><td>Diagnósticos dos aplicativos do servidor. Dados de log de pesquisa e de filtro.</a></td></tr>
<tr valign="top"><td>Obter análise de uso para minhas páginas da Web (em qualquer plataforma)</td><td colspan="2"><a href="../app-insights-web-track-usage/">Inserir script AI em suas páginas da Web</a></td><td>Análises de uso: visualizações de uso, usuários que retornam, contagens de sessão</td></tr>
<tr valign="top"><td></td><td>&nbsp;&nbsp;</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Gravar chamadas de eventos e métricas em scripts de sua página da Web</a></td><td>Análises de experiência do usuário personalizada</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">Gravar chamadas de rastreamento e diagnóstico nos scripts de sua página da Web</a></td><td>Dados de log de pesquisa e de filtro.</td></tr>
<tr valign="top"><td>Diagnosticar problemas em um aplicativo ASP.NET já em execução em meu servidor Web</td><td colspan="2"><a href="../app-insights-monitor-performance-live-website-now/">Instalar Monitor de Status em seu servidor Web</a></td><td>Durações e contagens de chamadas de dependência; contadores de CPU, memória e rede; contagens de carga, tempos de resposta</td></tr>
<tr valign="top"><td>Monitorar a disponibilidade de qualquer página da Web</td><td colspan="2"><a href="../app-insights-monitor-web-app-availability/">Configurar testes da Web no Application Insights</a></td><td>Monitor e alertas de disponibilidade</td></tr>
<tr valign="top"><td>Obter análise de desempenho e uso para aplicativos do Windows Phone, da Windows Store ou para sites Java</td><td colspan="2"><a href="http://msdn.microsoft.com/library/dn481095.aspx">Por enquanto, use a versão VSO mais antiga do Application Insights</a></td><td>Análises de uso e desempenho. <a href="http://msdn.microsoft.com/library/dn793604.aspx">Estamos aumentando gradativamente os recursos na versão do Azure.</a></td></tr>
</table>


## <a name="video"></a>Vídeos

#### Introdução

> [AZURE.VIDEO application-insights-introduction]

#### Introdução

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]


