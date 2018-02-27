---
title: "Configurar análise de aplicativo Web do ASP.NET com o Azure Application Insights | Microsoft Docs"
description: "Configurar análise de desempenho, disponibilidade e uso para seu site ASP.NET, hospedado no local ou no Azure."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/19/2018
ms.author: mbullwin
ms.openlocfilehash: 198cd5164ce3840503aca359767638e135f164bc
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Configurar o Application Insights para seu site ASP.NET

Este procedimento configura seu aplicativo da Web ASP.NET para enviar telemetria para o serviço [Azure Application Insights](app-insights-overview.md). Ele funciona para aplicativos ASP.NET hospedados em seu próprio servidor IIS local ou na nuvem. Você obtém gráficos e uma linguagem de consulta eficiente que ajudarão a compreender o desempenho de seu aplicativo e como as pessoas estão usando-o, além de alertas automáticos sobre falhas ou problemas de desempenho. Muitos desenvolvedores acham esses recursos excelentes como estão, mas você também pode estender e personalizar a telemetria se for necessário.

A instalação leva apenas alguns cliques no Visual Studio. Você tem a opção de evitar cobranças limitando o volume de telemetria. Isso permite testar e depurar ou monitorar um site com poucos usuários. Quando você decidir que deseja prosseguir e monitorar seu site de produção, é fácil aumentar o limite mais tarde.

## <a name="prerequisites"></a>pré-requisitos
Para adicionar o Application Insights ao seu site ASP.NET, você precisa:

- Instale o [Visual Studio 2017 para Windows](https://www.visualstudio.com/downloads/) com as cargas de trabalho a seguir:
    - Desenvolvimento Web e ASP.NET
    - Desenvolvimento do Azure

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="ide"></a>Etapa 1: Adicionar o SDK do Application Insights

Clique com o botão direito no nome de seu aplicativo Web no Gerenciador de Soluções e escolha **Configurar o Application Insights**

![Captura de tela do Gerenciador de Soluções, com Configurar o Application Insights realçado](./media/app-insights-asp-net/0001-configure-application-insights.png)

(Dependendo da versão de seu SDK do Application Insights, você pode receber uma solicitação para atualizar para a versão mais recente do SDK. Se isso acontecer, selecione **Atualizar SDK**.)

![Captura de tela: uma nova versão do SDK do Microsoft Application Insights está disponível. Atualizar SDK realçado](./media/app-insights-asp-net/0002-update-sdk.png)

Tela de Configuração do Application Insights:

Selecione **Iniciar Gratuitamente**.

![Captura de tela de registrar seu aplicativo com o Application Insights](./media/app-insights-asp-net/0004-start-free.png)

Se você deseja definir o grupo de recursos ou o local onde os dados estão armazenado, clique **configurações**. Grupos de recursos são usados para controlar o acesso aos dados. Por exemplo, se você tiver vários aplicativos que fazem parte do mesmo sistema, você pode colocar seus dados do Application Insights no mesmo grupo de recursos.

 Selecione **Registrar**. 

![Captura de tela de registrar seu aplicativo com o Application Insights](./media/app-insights-asp-net/0005-register-ed.png)

 Telemetria será enviada para o [portal do Azure](https://portal.azure.com), durante a depuração e depois de ter publicado seu aplicativo.
> [!NOTE]
> Se você não quiser enviar telemetria para o portal durante a depuração, adicione o SDK do Application Insights ao seu aplicativo, mas não configure um recurso no portal. Você pode ver a telemetria no Visual Studio enquanto você está depurando. Posteriormente, você pode retornar a esta página de configuração, ou você poderia esperar até depois de implantar seu aplicativo e [ative telemetria em tempo de execução](app-insights-monitor-performance-live-website-now.md).

## <a name="run"></a>Etapa 2: Executar seu aplicativo
Execute o aplicativo com F5. Abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, você verá uma contagem dos eventos que foram registrados.

![Captura de tela do Visual Studio. O botão Application Insights é exibido durante a depuração.](./media/app-insights-asp-net/0006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Etapa 3: conferir sua telemetria
Você pode ver sua telemetria no Visual Studio ou no portal da web Application Insights. Pesquise a telemetria no Visual Studio para ajudá-lo a depurar seu aplicativo. Monitore o desempenho e o uso no portal da Web quando o sistema estiver ativo. 

### <a name="see-your-telemetry-in-visual-studio"></a>Confira sua telemetria no Visual Studio

No Visual Studio, para ver os dados do Application Insights.  Selecione **Gerenciador de Soluções** > **Serviços Conectados** > clique com o botão direito em **Application Insights** e, em seguida, clique em **Pesquisar Telemetria em Tempo Real**.

Na janela de pesquisa do Application Insights do Visual Studio, você verá os dados de seu aplicativo para a telemetria gerada no lado do servidor de seu aplicativo. Experimente os filtros e clique em qualquer evento para ver mais detalhes.

![Captura de tela dos dados da exibição da sessão de depuração na janela do Application Insights.](./media/app-insights-asp-net/55.png)

> [!Tip]
> Caso você não veja os dados, verifique se o intervalo de tempo está correto e clique no ícone Pesquisa.

[Saiba mais sobre as ferramentas do Application Insights no Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Conferir telemetria no portal da Web

Você também pode ver a telemetria no portal da Web do Application Insights (a menos que você opte por instalar somente o SDK). O portal tem mais gráficos, ferramentas analíticas e modos de exibição entre componentes do que o Visual Studio. O portal também fornece alertas.

Abra seu recurso do Application Insights. Entre no [Portal do Azure](https://portal.azure.com/) e encontre-o lá, ou selecione **Gerenciador de Soluções** > **Serviços Conectados** > clique com o botão direito em  **Application Insights** > **Abrir Portal do Application Insights** e ele o levará até lá.

O portal é aberto em uma exibição da telemetria do aplicativo.

![Captura de tela da página de visão geral do Application Insights](./media/app-insights-asp-net/66.png)

No portal, clique em qualquer bloco ou gráfico para ver mais detalhes.

[Saiba mais sobre como usar o Application Insights no portal do Azure](app-insights-dashboards.md).

## <a name="step-4-publish-your-app"></a>Etapa 4: Publicar seu aplicativo
Publica seu aplicativo no servidor IIS ou no Azure. Observe o [Fluxo de Métricas Ativo](app-insights-metrics-explorer.md#live-metrics-stream) para verificar se tudo está funcionando corretamente.

A telemetria se acumula no portal do Application Insights, em que você pode monitorar as métricas, pesquisar a telemetria e configurar [painéis](app-insights-dashboards.md). Você também pode usar a poderosa [linguagem de consulta do Log Analytics](https://docs.loganalytics.io/) para analisar o uso e o desempenho ou para encontrar eventos específicos.

Você também pode continuar a analisar a telemetria no [Visual Studio](app-insights-visual-studio.md) com ferramentas como pesquisa de diagnóstico e de [tendências](app-insights-visual-studio-trends.md).

> [!NOTE]
> Se seu aplicativo enviar telemetria suficiente para se aproximar das [limitações](app-insights-pricing.md#limits-summary), a [amostragem](app-insights-sampling.md) automática será ativada. A amostragem reduz a quantidade de telemetria enviada do seu aplicativo, preservando dados correlacionados para fins de diagnóstico.
>
>

## <a name="land"></a> Você está pronto

Parabéns! Você instalou o pacote do Application Insights em seu aplicativo e o configurou para enviar telemetria para o serviço Application Insights no Azure.

![Diagrama de movimentação de telemetria](./media/app-insights-asp-net/01-scheme.png)

O recurso do Azure que recebe a telemetria do seu aplicativo é identificado por uma *chave de instrumentação*. Você encontrará essa chave no arquivo applicationinsights.config.


## <a name="upgrade-to-future-sdk-versions"></a>Atualizar para versões futuras do SDK
Para atualizar para uma [nova versão do SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), abra o **gerenciador de pacotes do NuGet** e filtre os pacotes instalados. Selecione **Microsoft.ApplicationInsights.Web** e escolha **Atualizar**.

Se você fez todas as personalizações no ApplicationInsights.config, salve uma cópia dele antes de atualizar. Em seguida, mescle suas alterações para a nova versão.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Próximas etapas

Há tópicos alternativos para conferir se você está interessado em:

* [Instrumentar um aplicativo Web em tempo de execução](app-insights-monitor-performance-live-website-now.md)
* [Serviços de Nuvem do Azure](app-insights-cloudservices.md)

### <a name="more-telemetry"></a>Mais telemetria

* **[Navegador e página carregam dados](app-insights-javascript.md)**  -insira um trecho de código em suas páginas da Web.
* **[Obtenha monitoramento de dependência e de exceção mais detalhado](app-insights-monitor-performance-live-website-now.md)**  -instale o Monitor de Status no seu servidor.
* **[Eventos personalizados de código](app-insights-api-custom-events-metrics.md)** para contagem, tempo ou medição de ações do usuário.
* **[Obter dados de log](app-insights-asp-net-trace-logs.md)**  - correlacionar dados de log com a telemetria.

### <a name="analysis"></a>Análise

* **[Trabalhar com o Application Insights no Visual Studio](app-insights-visual-studio.md)**<br/>Inclui informações sobre a depuração de telemetria, pesquisa de diagnóstico e análise por meio de código.
* **[Trabalhando com o portal do Application Insights](app-insights-dashboards.md)**<br/> Inclui informações sobre painéis, poderosas ferramentas de diagnóstico e análise, alertas, um mapa de dependências em tempo real de seu aplicativo e a exportação de telemetria.
* **[Analytics](app-insights-analytics-tour.md)** - a linguagem de consulta poderosa.

### <a name="alerts"></a>Alertas

* [Testes de disponibilidade](app-insights-monitor-web-app-availability.md): criar testes para verificar se seu site está visível na web.
* [Inteligente diagnóstico](app-insights-proactive-diagnostics.md): esses testes são executados automaticamente, portanto você não precisa fazer nada para configurá-los. Eles informam se o aplicativo tem uma taxa incomum de solicitações com falha.
* [Alertas de métrica](app-insights-alerts.md): defina-os para avisar se uma métrica ultrapassar um limite. Você pode defini-los em métricas personalizadas que você codifica em seu aplicativo.

### <a name="automation"></a>Automação

* [Automatizar a criação de um recurso adicional do Application Insights](app-insights-powershell.md)
