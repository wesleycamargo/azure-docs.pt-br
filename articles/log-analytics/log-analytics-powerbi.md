---
title: Importar dados do Log Analytics do Azure para o Power BI | Microsoft Docs
description: O Power BI é um serviço de análise de negócios baseado em nuvem da Microsoft que fornece relatórios e visualizações avançadas para análise de diferentes conjuntos de dados.  Este artigo descreve como configurar e importar dados do Log Analytics para o Power BI e configurá-los para serem atualizados automaticamente.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: bwren
ms.openlocfilehash: 725828c2acc5ac4bb53c5e6af14d20578a3d3652
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
ms.locfileid: "30236141"
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Importar dados do Log Analytics do Azure para o Power BI


O [Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é um serviço de análise de negócios baseado em nuvem da Microsoft que fornece relatórios e visualizações avançadas para análise de diferentes conjuntos de dados.  Você pode importar os resultados de uma pesquisa de logs do Log Analytics para um conjunto de dados do Power BI para que possa tirar proveito de seus recursos, como combinar dados de fontes diferentes e compartilhar relatórios na Web e dispositivos móveis.

## <a name="overview"></a>Visão geral
Para importar dados de um espaço de trabalho do Log Analytics no Power BI, você deve criar um conjunto de dados no Power BI baseado em uma consulta de pesquisa de logs no Log Analytics.  A consulta é executada cada vez que o conjunto de dados é atualizado.  Depois você pode compilar relatórios do Power BI que usam dados do conjunto de dados.  Para criar o conjunto de dados no Power BI, exporte sua consulta do Log Analytics para a [linguagem do Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx).  Depois use-a para criar uma consulta no Power BI Desktop e publicá-la no Power BI como um conjunto de dados.  Os detalhes para esse processo são descritos abaixo.

![Log Analytics para Power BI](media/log-analytics-powerbi/overview.png)

## <a name="export-query"></a>Consulta de exportação
Comece criando uma [pesquisa de logs](log-analytics-log-search-new.md) que retorna os dados do Log Analytics que você quer usar para preencher o conjunto de dados do Power BI.  Depois exporte essa consulta para a [linguagem do Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx) que pode ser usado pelo Power BI Desktop.

1. Crie a pesquisa de logs no Log Analytics para extrair os dados para seu conjunto de dados.
2. Se estiver usando o portal de pesquisa de logs, clique em **Power BI**.  Se estiver usando o portal da Análise, selecione **Exportar** > **Power BI Query (M)**.  Ambas as opções exportam a consulta para um arquivo de texto chamado **PowerBIQuery.txt**. 

    ![Exportar pesquisa de logs](media/log-analytics-powerbi/export-logsearch.png) ![Exportar pesquisa de logs](media/log-analytics-powerbi/export-analytics.png)

3. Abra o arquivo de texto e copie o conteúdo.

## <a name="import-query-into-power-bi-desktop"></a>Importar a consulta no Power BI Desktop
O Power BI Desktop é um aplicativo de área de trabalho que permite criar conjuntos de dados e relatórios que podem ser publicados no Power BI.  Também é possível usá-lo para criar uma consulta usando a linguagem do Power Query exportada do Log Analytics. 

1. Instale o [Power BI Desktop](https://powerbi.microsoft.com/desktop/) se ainda não o tiver e depois abra o aplicativo.
2. Selecione **Obter Dados** > **Consulta em Branco** para abrir uma nova consulta.  Depois selecione **Editor Avançado** e cole o conteúdo do arquivo exportado na consulta. Clique em **Concluído**.

    ![Consulta do Power BI Desktop](media/log-analytics-powerbi/desktop-new-query.png)

5. A consulta é executada, e seus resultados são exibidos.  Pode ser que sejam solicitadas as credenciais para se conectar ao Azure.  
6. Digite um nome descritivo para a consulta.  O padrão é **Query1**. Clique em **Fechar e Aplicar** para adicionar o conjunto de dados ao relatório.

    ![Nome do Power BI Desktop](media/log-analytics-powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publicar no Power BI
Ao publicar no Power BI, serão criados um conjunto de dados e um relatório.  Se você criar um relatório no Power BI Desktop, isso será publicado com seus dados.  Se não, será criado um relatório em branco.  É possível modificar o relatório no Power BI ou criar um novo com base no conjunto de dados.

8. Crie um relatório baseado nos seus dados.  Use a [documentação do Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-report-view) se não estiver familiarizado com ele.  Quando você estiver pronto para enviá-lo ao Power BI, clique em **Publicar**.  Quando solicitado, selecione um destino em sua conta do Power BI.  A menos que tenha um destino específico em mente, use **Meu espaço de trabalho**.

    ![Publicação no Power BI Desktop](media/log-analytics-powerbi/desktop-publish.png)

3. Quando a publicação for concluída, clique em **Abrir no Power BI** para abrir o Power BI com seu novo conjunto de dados.


### <a name="configure-scheduled-refresh"></a>Configurar a atualização agendada
O conjunto de dados criado no Power BI terá os mesmos dados vistos anteriormente no Power BI Desktop.  Você precisa atualizar o conjunto de dados periodicamente para executar a consulta novamente e preenchê-lo com os dados mais recentes do Log Analytics.  

1. Clique no espaço de trabalho no qual você carregou o relatório e selecione o menu **Conjuntos de dados**. Selecione o menu de contexto ao lado do novo conjunto de dados e selecione **Configurações**. Em **Credenciais da fonte de dados** você deve ver uma mensagem de que as credenciais são inválidas.  Isso ocorre porque você ainda não forneceu as credenciais para o conjunto de dados usar quando ele atualiza os dados.  Clique em **Editar credenciais** e especifique as credenciais com acesso ao Log Analytics.

    ![Agenda do Power BI](media/log-analytics-powerbi/powerbi-schedule.png)

5. Em **Atualização agendada**, ative a opção **Manter seus dados atualizados**.  Outra opção é alterar a **Frequência de atualização** e um ou mais períodos específicos para executar a atualização.

    ![Atualização do Power BI](media/log-analytics-powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para criar consultas que podem ser exportadas para o Power BI.
* Saiba mais sobre o [Power BI](http://powerbi.microsoft.com) para criar visualizações baseadas em exportações do Log Analytics.
