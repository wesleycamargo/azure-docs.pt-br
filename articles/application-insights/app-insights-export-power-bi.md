---
title: Exportar para o Power BI do Application Insights | Microsoft Docs
description: As consultas do Analytics podem ser exibidas no Power BI.
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 24ccafb4df95e0010416485199e19f81e1ae31aa
ms.openlocfilehash: 11017c7c0a761569892aebcd085d5d3fb2d67a69
ms.contentlocale: pt-br
ms.lasthandoff: 02/14/2017


---
# <a name="feed-power-bi-from-application-insights"></a>Alimentar o Power BI do Application Insights
O [Power BI](http://www.powerbi.com/) é um conjunto de ferramentas de análise de negócios que ajudam a analisar dados e a compartilhar informações. Painéis avançados estão disponíveis em cada dispositivo. Você pode combinar dados de várias fontes, incluindo consultas do Analytics do [Azure Application Insights](app-insights-overview.md).

Há três métodos recomendados para exportar dados do Application Insights para o Power BI. Você pode usá-los separadamente ou em conjunto.

* [**Adaptador do Power BI**](#power-pi-adapter) -configure um painel completo de telemetria do seu aplicativo. O conjunto de gráficos é predefinido, mas você pode adicionar suas próprias consultas de outras fontes.
* [**Exportar as consultas do Analytics**](#export-analytics-queries) -escreva qualquer consulta que quiser usando o Analytics e exporte-a para o Power BI. Você pode colocar essa consulta em um painel com outros dados.
* [**Exportação contínua e Stream Analytics**](app-insights-export-stream-analytics.md) – isso envolve mais trabalho para configurar. Será útil se você quiser manter os dados por longos períodos. Caso contrário, os outros métodos serão recomendados.

## <a name="power-bi-adapter"></a>Adaptador do Power BI
Esse método cria um painel completo de telemetria para você. O conjunto de dados inicial é predefinido, mas você pode adicionar mais dados a ele.

### <a name="get-the-adapter"></a>Obter o adaptador
1. Entre no [Power BI](https://app.powerbi.com/).
2. Abra **Obter Dados**, **Serviços**, **Application Insights**
   
    ![Obter da fonte de dados do Application Insights](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Forneça os detalhes do seu recurso Application Insights.
   
    ![Obter da fonte de dados do Application Insights](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Aguarde um minuto ou dois para que os dados sejam importados.
   
    ![Adaptador do Power BI](./media/app-insights-export-power-bi/010.png)

Você pode editar o painel, combinando os gráficos do Application Insights a outros de outras fontes e a consultas do Analytics. Há uma galeria de visualização onde você pode obter mais gráficos e cada um deles possui parâmetros que podem ser definidos.

Após a importação inicial, o painel e os relatórios continuarão a ser atualizados diariamente. Você pode controlar o agendamento de atualização no conjunto de dados.

## <a name="export-analytics-queries"></a>Exportar consultas do Analytics
Essa rota permite escrever qualquer consulta do Analytics desejada e então exportá-la para um painel. (Você pode adicionar ao painel criado pelo adaptador).

### <a name="one-time-install-power-bi-desktop"></a>Uma vez: instalar o Power BI Desktop
Para importar sua consulta do Application Insights, você deve usar a versão da área de trabalho do Power BI. Mas, em seguida, você poderá publicá-la na Web ou em seu espaço de trabalho de nuvem do Power BI. 

Instalar o [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportar uma consulta do Analytics
1. [Abra o Analytics e escreva sua consulta](app-insights-analytics-tour.md).
2. Teste e ajuste a consulta até ficar satisfeito com os resultados.

   **Certifique-se de que a consulta seja executada corretamente no Analytics antes de ser exportada.**
3. No menu **Exportar**, escolha **Power BI (M)**. Salve o arquivo de texto.
   
    ![Exportar a consulta do Power BI](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. No Power BI Desktop, selecione **Obter Dados, Consulta em Branco** e, então, no editor de consultas, em **Exibir**, selecione **Editor Avançado de Consultas**.

    Cole o script M Language exportado no Editor Avançado de Consultas.

    ![Editor avançado de consultas](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Talvez seja necessário fornecer credenciais para permitir que o Power BI acesse o Azure. Use a ‘conta organizacional’ para entrar com sua conta da Microsoft.
   
    ![Forneça credenciais do Azure para permitir que o Power BI execute sua consulta do Application Insights](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    (Se você precisar verificar as credenciais, use o comando de menu Configurações de Fonte de Dados no Editor de Consultas. Tome cuidado para especificar as credenciais usadas para o Azure, que podem ser diferentes das suas credenciais para o Power BI.)
2. Escolha uma visualização para a sua consulta e selecione os campos dos eixos x, y e a dimensão de segmentação.
   
    ![Selecionar visualização](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Publique seu relatório em seu espaço de trabalho de nuvem do Power BI. A partir daí, você pode inserir uma versão sincronizada em outras páginas da Web.
   
    ![Selecionar visualização](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Atualize o relatório manualmente em intervalos ou configure uma atualização agendada na página de opções.

## <a name="about-sampling"></a>Sobre amostragem
Se seu aplicativo enviar muitos dados, a funcionalidade de amostragem adaptável poderá operar e enviar apenas uma porcentagem da sua telemetria. Isso também será verdadeiro se você tiver definido manualmente a amostragem no SDK ou na ingestão. [Saiba mais sobre amostragem.](app-insights-sampling.md)

## <a name="next-steps"></a>Próximas etapas
* [Power BI - Saiba mais](http://www.powerbi.com/learning/)
* [Tutorial do Analytics](app-insights-analytics-tour.md)


