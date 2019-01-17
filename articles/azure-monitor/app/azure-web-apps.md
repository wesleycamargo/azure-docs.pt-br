---
title: Monitorar desempenho dos serviços de aplicativos do Azure | Microsoft Docs
description: Monitoramento do desempenho de aplicativos para serviços de aplicativos do Azure. Tempo de resposta e de carga, informações de dependência e alertas definidos sobre o desempenho do gráfico.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: mbullwin
ms.openlocfilehash: 17d8eff39eabb2f7b4968bf74d2482b980fe8060
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54116612"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorar o desempenho do Serviço de Aplicativo do Azure
No [Portal do Azure](https://portal.azure.com), você pode configurar o monitoramento de desempenho de dos seus aplicativos Web, back-end móveis e aplicativos de API no [Serviço de Aplicativo do Azure](../../app-service/overview.md). O [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) equipa seu aplicativo para enviar a telemetria sobre suas atividades para o serviço Application Insights, onde ela é armazenada e analisada. Lá, os gráficos de métricas e as ferramentas de pesquisa podem ser usados para ajudar a diagnosticar problemas, melhorar o desempenho e avaliar o uso.

## <a name="run-time-or-build-time"></a>Tempo de execução ou tempo de compilação
Você pode configurar o monitoramento por meio da instrumentação do aplicativo de duas maneiras:

* **Tempo de execução** – você poderá selecionar uma extensão de monitoramento de desempenho quando seu serviço de aplicativo já estiver ativo. Não é necessário recompilar ou reinstalar o aplicativo. Obtenha um conjunto padrão de pacotes que monitoram os tempos de resposta, taxas de êxito, exceções, dependências e assim por diante. 
* **Tempo de compilação** - você pode instalar um pacote em seu aplicativo em desenvolvimento. Essa opção é mais versátil. Além dos mesmos pacotes padrão, você pode escrever código para personalizar a telemetria ou para enviar sua própria telemetria. Você pode registrar eventos de registro de acordo com a semântica do seu domínio de aplicativo ou de atividades específicas. 

## <a name="run-time-instrumentation-with-application-insights"></a>Execute a instrumentação de tempo com o Application Insights
Se você já está executando um serviço de aplicativo no Azure, já está recebendo algum monitoramento: taxas de erro e de solicitação. Adicione o Application Insights para obter mais, como tempos de resposta, monitoramento de chamadas para dependências, detecção inteligente e a linguagem avançada de consulta do Log Analytics. 

1. **Selecione o Application Insights** no painel de controle do Azure para seu serviço de aplicativo.

    ![Em Configurações, escolha Insights do aplicativo](./media/azure-web-apps/settings-app-insights.png)

   * Opte por criar um novo recurso, a menos que você já tenha configurado um recurso do Application Insights para este aplicativo. 

    > [!NOTE]
    > Ao clicar em **OK** para criar o novo recurso, você será solicitado a **Aplicar configurações de monitoramento**. Se você selecionar **Continuar**, vinculará seu novo recurso do Application Insights ao seu serviço de aplicativo. Fazer isso também **disparará uma reinicialização do seu serviço de aplicativo**. 

    ![Instrumentar seu aplicativo Web](./media/azure-web-apps/create-resource.png)

2. Depois de especificar qual recurso usar, você pode escolher como deseja que o Application Insights colete dados por plataforma para seu aplicativo.

    ![Escolha as opções por plataforma](./media/azure-web-apps/choose-options.png)

3. **Instrumente seu serviço de aplicativo** após a instalação do Application Insights.

   **Habilite o monitoramento do lado do cliente** para telemetria de usuário e exibição de página.

   * Selecione Configurações > Configurações do Aplicativo
   * Em configurações do aplicativo, adicione um novo par de chave/valor:

    Chave: `APPINSIGHTS_JAVASCRIPT_ENABLED`

    Valor: `true`
   * **Salve** as configurações e **Reinicie** seu aplicativo.
4. Explore os dados de monitoramento de seu aplicativo selecionando **Configurações** >  **Insights de aplicativos** > **Veja mais em Insights de aplicativos**.

Posteriormente, você poderá compilar o aplicativo com o Application Insights, se desejar.

*Como remover o Application Insights ou alternar para envio para outro recurso?*

* No Azure, abra a folha de controle de aplicativo Web e, em Ferramentas de Desenvolvimento, abra **Application Insights**. Você pode desativar o Application Insights clicando em **Desabilitar** na parte superior, ou selecione um novo recurso na seção **Alterar o recurso**.

## <a name="build-the-app-with-application-insights"></a>Compilar seu aplicativo com o Application Insights
O Application Insights pode fornecer dados de telemetria mais detalhados instalando um SDK em seu aplicativo. Em particular, você pode coletar logs de rastreamento, [escrever telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md) e obter relatórios de exceção mais detalhados.

1. **No Visual Studio** (2013 atualização 2 ou posterior), adicione o Application Insights ao seu projeto.

    Clique com o botão direito do mouse no projeto da Web e selecione **Add> Application Insights** ou **Project** > **Application Insights** > **Configure Application Insights**.

    ![Clique com o botão direito do mouse no projeto da Web e escolha Adicionar ou Configurar o Application Insights](./media/azure-web-apps/03-add.png)

    Se receber uma solicitação para se conectar, use as credenciais da conta do Azure.

    A operação tem dois efeitos:

   1. Cria um recurso do Application Insights no Azure, onde a telemetria é armazenada, analisada e exibida.
   2. Adiciona o pacote NuGet do Application Insights ao seu código (se ainda não estiver lá) e o configura para enviar telemetria ao recurso do Azure.
2. **Teste a telemetria** executando o aplicativo no computador de desenvolvimento (F5).
3. **Publique o aplicativo** no Azure como de costume. 

*Como eu mudo para enviar dados para um recurso diferente do Application Insights?*

* No Visual Studio, clique com o botão direito do mouse no projeto, escolha **Configurar o Application Insights** e escolha o recurso desejado. Você obtém a opção para criar um novo recurso. Recompilar e reimplantar.

## <a name="more-telemetry"></a>Mais telemetria

* [Carregar dados da página da Web](../../azure-monitor/app/javascript.md)
* [Telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="troubleshooting"></a>solução de problemas

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED gera resposta HTML incompleta em aplicativos Web NET CORE.

Habilitar o Javascript por meio de Serviços de Aplicativos pode fazer respostas html serem cortadas.

- Solução alternativa 1: definir a Configuração de Aplicativo APPINSIGHTS_JAVASCRIPT_ENABLED como false ou removê-la completamente e reiniciar
- Solução alternativa 2: adicionar o sdk por meio de código e remover a extensão (o Depurador de Instantâneos e o Profiler não funcionam com essa configuração)

Estamos acompanhando esse problema [aqui](https://github.com/Microsoft/ApplicationInsights-Home/issues/277)

## <a name="next-steps"></a>Próximas etapas
* [Executar o criador de perfil em seu aplicativo ativo](../../azure-monitor/app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – monitorar o Azure Functions com o Application Insights
* [Permita que o diagnóstico do Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) seja enviado ao Application Insights.
* [Monitore as métricas de integridade do serviço](../../azure-monitor/platform/data-collection.md) para se certificar de que o serviço esteja disponível e responsivo.
* [Receba notificações de alerta](../../azure-monitor/platform/alerts-overview.md) sempre que ocorrerem eventos operacionais ou métricas ultrapassarem um limite.
* Use o [Application Insights para aplicativos JavaScript e páginas Web](../../azure-monitor/app/javascript.md) para obter a telemetria do cliente dos navegadores que visitam uma página da Web.
* [Configure testes de disponibilidade da Web](../../azure-monitor/app/monitor-web-app-availability.md) para ser alertado se o seu site for desativado.

