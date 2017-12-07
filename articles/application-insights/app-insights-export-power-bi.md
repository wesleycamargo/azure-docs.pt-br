---
title: Exportar para o Power BI do Azure Application Insights | Microsoft Docs
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
ms.author: mbullwin
ms.openlocfilehash: 19595983ba49a88d9139c85afbf38d3106d4a81d
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="feed-power-bi-from-application-insights"></a>Alimentar o Power BI do Application Insights
O [Power BI](http://www.powerbi.com/) é um conjunto de ferramentas de negócios que ajudam a analisar dados e a compartilhar informações. Painéis avançados estão disponíveis em cada dispositivo. Você pode combinar dados de várias fontes, incluindo consultas do Analytics do [Azure Application Insights](app-insights-overview.md).

Há três métodos recomendados para exportar dados do Application Insights para o Power BI. Você pode usá-los separadamente ou em conjunto.

* [**Adaptador do Power BI**](#power-pi-adapter). Configure um painel completo de telemetria do seu aplicativo. O conjunto de gráficos é predefinido, mas você pode adicionar suas próprias consultas de outras fontes.
* [**Exportar consultas do Analytics**](#export-analytics-queries). Grave qualquer consulta que você deseja e exporte-a para o Power BI. Você pode escrever sua consulta usando o Analytics ou gravá-las nos Funis de Uso. Você pode colocar essa consulta em um painel com outros dados.
* [**A exportação contínua e o Azure Stream Analytics**](app-insights-export-stream-analytics.md). Esse método será útil se você quiser manter os dados por longos períodos. Caso contrário, use um dos outros métodos porque esse envolve mais trabalho para configurar.

## <a name="power-bi-adapter"></a>Adaptador do Power BI
Esse método cria um painel completo de telemetria para você. O conjunto de dados inicial é predefinido, mas você pode adicionar mais dados a ele.

### <a name="get-the-adapter"></a>Obter o adaptador
1. Entre no [Power BI](https://app.powerbi.com/).
2. Abra **Obter Dados**, **Serviços** e então **Application Insights**.
   
    ![Capturas de tela de Obter da fonte de dados do Application Insights](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Forneça os detalhes do seu recurso Application Insights.
   
    ![Captura de tela de Obter da fonte de dados do Application Insights](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Aguarde um minuto ou dois para que os dados sejam importados.
   
    ![Captura de tela de adaptador do Power BI](./media/app-insights-export-power-bi/010.png)

Você pode editar o painel, combinando os gráficos do Application Insights a outros de outras fontes e a consultas do Analytics. Você pode obter mais gráficos na galeria de visualização e cada um deles possui parâmetros que podem ser definidos.

Após a importação inicial, o painel e os relatórios continuarão a ser atualizados diariamente. Você pode controlar o agendamento de atualização no conjunto de dados.

## <a name="export-analytics-queries"></a>Exportar consultas do Analytics
Essa rota permite que você grave qualquer consulta do Analytics desejada ou que a exporte do Funil de Uso, e então fazer a exportação para um painel Power BI. (Você pode adicionar ao painel criado pelo adaptador).

### <a name="one-time-install-power-bi-desktop"></a>Uma vez: instalar o Power BI Desktop
Para importar sua consulta do Application Insights, você deve usar a versão da área de trabalho do Power BI. Em seguida, você poderá publicá-la na Web ou em seu espaço de trabalho de nuvem do Power BI. 

Instalar o [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportar uma consulta do Analytics
1. [Abra o Analytics e escreva sua consulta](app-insights-analytics-tour.md).
2. Teste e ajuste a consulta até ficar satisfeito com os resultados. Certifique-se de que a consulta seja executada corretamente no Analytics antes de ser exportada.
3. No menu **Exportar**, escolha **Power BI (M)**. Salve o arquivo de texto.
   
    ![Captura de tela do Analytics, com o menu Exportar realçado](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. No Power BI Desktop, selecione **Obter Dados** > **Consulta em Branco**. Em seguida, no editor de consultas, em **Exibir**, selecione **Editor Avançado**.

    Cole o script M Language exportado no Editor Avançado.

    ![Captura de tela do Power BI Desktop, com o Editor Avançado realçado](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Para permitir que o Power BI acesse o Azure, talvez seja necessário fornecer credenciais. Use a **conta Organizacional** para entrar com sua conta da Microsoft.
   
    ![Captura de tela da caixa de diálogo Configurações de Consulta do Power BI](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    Se você precisar verificar as credenciais, use o comando de menu **Configurações de Fonte de Dados** no editor de consultas. Especifique as credenciais usadas para o Azure, que podem ser diferentes das suas credenciais para o Power BI.
2. Escolha uma visualização para a sua consulta e selecione os campos dos eixos x, y e a dimensão de segmentação.
   
    ![Captura de tela de opções de visualização do Power BI Desktop](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Publique seu relatório em seu espaço de trabalho de nuvem do Power BI. A partir daí, você pode inserir uma versão sincronizada em outras páginas da Web.
   
    ![Captura de tela do Power BI Desktop, com o botão Publicar realçado](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Atualize o relatório manualmente em intervalos ou configure uma atualização agendada na página de opções.

### <a name="export-a-funnel"></a>Exportar um Funil
1. [Crie o seu funil](usage-funnels.md).
2. Selecione **Power BI**. 

   ![Captura de tela do botão do Power BI](./media/app-insights-export-power-bi/button.png)
   
3. No Power BI Desktop, selecione **Obter Dados** > **Consulta em Branco**. Em seguida, no editor de consultas, em **Exibir**, selecione **Editor Avançado**.

   ![Captura de tela do Power BI Desktop, com o botão Consulta em Branco realçado](./media/app-insights-export-power-bi/blankquery.png)

   Cole o script M Language exportado no Editor Avançado. 

   ![Captura de tela do Power BI Desktop, com o Editor Avançado realçado](./media/app-insights-export-power-bi/advancedquery.png)

4. Selecione os itens da consulta e escolha a visualização de Funil.

   ![Captura de tela de opções de visualização do Power BI Desktop](./media/app-insights-export-power-bi/selectsequence.png)

5. Altere o título para torná-lo significativo e publique seu relatório no seu espaço de trabalho de nuvem do Power BI. 

   ![Captura de tela do Power BI Desktop, com a alteração de título realçada](./media/app-insights-export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Solucionar problemas

Você pode encontrar erros relacionados às credenciais ou ao tamanho do conjunto de dados. Aqui estão algumas informações sobre o que fazer com esses erros.

### <a name="unauthorized-401-or-403"></a>Não Autorizado (401 ou 403)
Isso pode acontecer se o token de atualização não tiver sido atualizado. Repita estas etapas para garantir que você ainda terá acesso:

1. Faça logon no portal do Azure e certifique-se de que você pode acessar o recurso.
2. Tente atualizar as credenciais para o painel.

 Se você tiver acesso e a atualização das credenciais não funcionar, abra um tíquete de suporte.

### <a name="bad-gateway-502"></a>Gateway Incorreto (502)
Isso geralmente é causado por uma Consulta de análise que retorna um número de dados excessivo. Tente usar um intervalo de tempo menor para a consulta. 

Se reduzir o conjunto de dados da consulta de análise não atender às suas necessidades, considere o uso da [API](https://dev.applicationinsights.io/documentation/overview) para efetuar pull de um conjunto de dados maior. Veja como converter a exportação de Consulta M para usar a API.

1. Criar uma [chave de API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Atualize o script M do Power BI que você exportou do Analytics, substituindo a URL do Azure Resource Manager com a API do Application Insights.
   * Substituir **https://management.azure.com/subscriptions/...**
   * por **https://api.applicationinsights.io/beta/apps/...**
3. Por fim, atualize as credenciais para as básicas e use sua chave de API.
  

**Script existente**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Script atualizado**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Sobre amostragem
Se seu aplicativo envia muitos dados, convém usar o recurso de amostragem adaptável, que envia apenas uma porcentagem da sua telemetria. Isso também será verdadeiro se você tiver definido manualmente a amostragem no SDK ou na ingestão. [Saiba mais sobre a amostragem](app-insights-sampling.md).


## <a name="next-steps"></a>Próximas etapas
* [Power BI - Saiba mais](http://www.powerbi.com/learning/)
* [Tutorial do Analytics](app-insights-analytics-tour.md)

