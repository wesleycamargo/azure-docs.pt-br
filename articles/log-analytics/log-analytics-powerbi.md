---
title: Importar dados do Log Analytics do Azure para o Power BI | Microsoft Docs
description: "O Power BI é um serviço de análise de negócios baseado em nuvem da Microsoft que fornece relatórios e visualizações avançadas para análise de diferentes conjuntos de dados.  Este artigo descreve como configurar a importação de dados do Log Analytics para o Power BI e configurá-los para serem atualizados automaticamente."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: bwren
ms.openlocfilehash: e687a1ee8ac4f565062e57b07cdfa9ac5e6bbf4f
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2018
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Importar dados do Log Analytics do Azure para o Power BI


O [Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é um serviço de análise de negócios baseado em nuvem da Microsoft que fornece relatórios e visualizações avançadas para análise de diferentes conjuntos de dados.  Você pode importar os resultados de uma pesquisa de logs do Log Analytics para um conjunto de dados do Power BI para que possa tirar proveito de seus recursos, como combinar dados de fontes diferentes e compartilhar relatórios na Web e dispositivos móveis.

Este artigo fornece detalhes sobre como importar dados do Log Analytics para o Power BI e programá-los para serem atualizados automaticamente.  Processos diferentes são incluídos para um espaço de trabalho [atualizado](#upgraded-workspace) e um [herdado](#legacy-workspace).

## <a name="upgraded-workspace"></a>Espaço de trabalho atualizado


Para importar dados de um [espaço de trabalho atualizado do Log Analytics](log-analytics-log-search-upgrade.md) para o Power BI, você deve criar um conjunto de dados no Power BI baseado em uma consulta de pesquisa de logs no Log Analytics.  A consulta é executada cada vez que o conjunto de dados é atualizado.  Depois você pode compilar relatórios do Power BI que usam dados do conjunto de dados.  Para criar o conjunto de dados no Power BI, exporte sua consulta do Log Analytics para a [linguagem do Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx).  Depois use-a para criar uma consulta no Power BI Desktop e publicá-la no Power BI como um conjunto de dados.  Os detalhes para esse processo são descritos abaixo.

![Log Analytics para Power BI](media/log-analytics-powerbi/overview.png)

### <a name="export-query"></a>Consulta de exportação
Comece criando uma [pesquisa de logs](log-analytics-log-search-new.md) que retorna os dados do Log Analytics que você quer usar para preencher o conjunto de dados do Power BI.  Depois exporte essa consulta para a [linguagem do Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx) que pode ser usado pelo Power BI Desktop.

1. Crie a pesquisa de logs no Log Analytics para extrair os dados para seu conjunto de dados.
2. Se estiver usando o portal de pesquisa de logs, clique em **Power BI**.  Se estiver usando o portal da Análise, selecione **Exportar** > **Power BI Query (M)**.  Ambas as opções exportam a consulta para um arquivo de texto chamado **PowerBIQuery.txt**. 

    ![Exportar pesquisa de logs](media/log-analytics-powerbi/export-logsearch.png) ![Exportar pesquisa de logs](media/log-analytics-powerbi/export-analytics.png)

3. Abra o arquivo de texto e copie o conteúdo.

### <a name="import-query-into-power-bi-desktop"></a>Importar a consulta no Power BI Desktop
O Power BI Desktop é um aplicativo de área de trabalho que permite criar conjuntos de dados e relatórios que podem ser publicados no Power BI.  Também é possível usá-lo para criar uma consulta usando a linguagem do Power Query exportada do Log Analytics. 

1. Instale o [Power BI Desktop](https://powerbi.microsoft.com/desktop/) se ainda não o tiver e depois abra o aplicativo.
2. Selecione **Obter Dados** > **Consulta em Branco** para abrir uma nova consulta.  Depois selecione **Editor Avançado** e cole o conteúdo do arquivo exportado na consulta. Clique em **Concluído**.

    ![Consulta do Power BI Desktop](media/log-analytics-powerbi/desktop-new-query.png)

5. A consulta é executada, e seus resultados são exibidos.  Pode ser que sejam solicitadas as credenciais para se conectar ao Azure.  
6. Digite um nome descritivo para a consulta.  O padrão é **Query1**. Clique em **Fechar e Aplicar** para adicionar o conjunto de dados ao relatório.

    ![Nome do Power BI Desktop](media/log-analytics-powerbi/desktop-results.png)



### <a name="publish-to-power-bi"></a>Publicar no Power BI
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

## <a name="legacy-workspace"></a>Espaço de trabalho herdado
Ao configurar o Power BI com um [espaço de trabalho herdado do Log Analytics](log-analytics-powerbi.md), você cria consultas de log que exportam seus resultados para conjuntos de dados correspondentes no Power BI.  A consulta e a exportação continuarão a ser executadas automaticamente em uma agenda definida por você para manter o conjunto de dados atualizado com os últimos dados coletados pelo Log Analytics.

![Log Analytics para Power BI](media/log-analytics-powerbi/overview-legacy.png)

### <a name="power-bi-schedules"></a>Agendas do Power BI
Um *Agendamento do Power BI* inclui uma pesquisa de logs que exporta um conjunto de dados do Log Analytics para um conjunto de dados correspondente no Power BI e um agendamento que define a frequência com que essa pesquisa é executada para manter o conjunto de dados atual.

Os campos no conjunto de dados corresponderão às propriedades dos registros retornados pela pesquisa de log.  Se a pesquisa retornar registros de diferentes tipos, o conjunto de dados incluirá todas as propriedades de cada um dos tipos de registro incluídos.  

### <a name="connecting-log-analytics-workspace-to-power-bi"></a>Conectando o espaço de trabalho do Log Analytics ao Power BI
Antes de exportar do Log Analytics para o Power BI, você precisa conectar o espaço de trabalho à sua conta do Power BI usando o procedimento a seguir.  

1. No console do OMS, clique no bloco **Configurações** .
2. Selecione **Contas**.
3. Na seção **Informações sobre o espaço de trabalho**, clique em **Conectar à Conta do Power BI**.
4. Insira as credenciais da sua conta do Power BI.

### <a name="create-a-power-bi-schedule"></a>Criar uma agenda do Power BI
Crie uma agenda do Power BI para cada conjunto de dados usando o procedimento a seguir.

1. No console do OMS, clique no bloco **Pesquisa de Log** .
2. Digite uma nova consulta ou selecione uma pesquisa salva que retorna os dados que você deseja exportar para o **Power BI**.  
3. Clique no botão **Power BI** na parte superior da página para abrir o diálogo **Power BI**.
4. Forneça as informações na tabela a seguir e clique em **Salvar**.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| NOME |Nome para identificar a agenda ao exibir a lista de agendamentos do Power BI. |
| Pesquisa Salva |A pesquisa de log a ser executada.  Você pode selecionar a consulta atual ou uma pesquisa salva existente na lista suspensa. |
| Agenda |A frequência de execução da pesquisa salva e exportação para o conjunto de dados do Power BI.  O valor deve ser entre 15 minutos e 24 horas. |
| Nome do conjunto de dados |O nome do conjunto de dados no Power BI.  Ele será criado se ele não existir e atualizado se existir. |

### <a name="viewing-and-removing-power-bi-schedules"></a>Exibir e remover agendas do Power BI
Veja a lista de agendamentos do Power BI existentes com o procedimento a seguir.

1. No console do OMS, clique no bloco **Configurações** .
2. Selecione **Power BI**.

Além dos detalhes da agenda, são exibidos o número de vezes que a agenda foi executada na última semana e o status da última sincronização.  Se a sincronização encontrou erros, você pode clicar no link para executar uma pesquisa de log de registros com detalhes do erro.

É possível remover um agendamento clicando no **X** na **coluna Remover**.  É possível desabilitar um agendamento selecionando **Desativado**.  Para modificar uma agenda, você deve removê-la e recriá-la com as novas configurações.

![Agendas do Power BI](media/log-analytics-powerbi/schedules.png)

### <a name="sample-walkthrough"></a>Passo a passo de exemplo
A seção a seguir demonstra o passo a passo de um exemplo de como criar uma agenda do Power BI e usar seu conjunto de dados para criar um relatório simples.  Neste exemplo, todos os dados de desempenho para um conjunto de computadores são exportado para o Power BI e um grafo de linha é criado para exibir a utilização do processador.

#### <a name="create-log-search"></a>Criar pesquisa de log
Começamos criando uma pesquisa de log para os dados que desejamos enviar para o conjunto de dados.  Neste exemplo, usaremos uma consulta que retorna todos os dados de desempenho de computadores com um nome que começa com *srv*.  

![Agendas do Power BI](media/log-analytics-powerbi/walkthrough-query.png)

#### <a name="create-power-bi-search"></a>Criar a pesquisa do Power BI
Clicamos no botão **Power BI** para abrir o diálogo Power BI e fornecemos as informações necessárias.  Queremos que essa pesquisa seja executada uma vez por hora e crie um conjunto de dados chamado *Contoso Perf*.  Como já temos aberta uma pesquisa que cria os dados que queremos, mantemos a opção padrão *Usar consulta de pesquisa atual* para **Pesquisa Salva**.

![Pesquisa do Power BI](media/log-analytics-powerbi/walkthrough-schedule.png)

#### <a name="verify-power-bi-search"></a>Verifique a pesquisa do Power BI
Para verificar se criamos o agendamento corretamente, exibimos a lista de Pesquisas do Power BI sob o bloco **Configurações** no painel do OMS.  Aguarde alguns minutos e atualize essa exibição até que ela informe que a sincronização foi executada.  Normalmente, você vai programar o conjunto de dados para atualizar automaticamente.

![Pesquisa do Power BI](media/log-analytics-powerbi/walkthrough-schedules.png)

#### <a name="verify-the-dataset-in-power-bi"></a>Verificar o conjunto de dados no Power BI
Fazemos logon em nossa conta em [powerbi.microsoft.com](http://powerbi.microsoft.com/) e rolamos até **Conjuntos de Dados** na parte inferior do painel esquerdo.  Podemos observar que o conjunto de dados *Contoso Perf* é listado, indicando que nossa exportação foi executada com êxito.

![Conjunto de dados do Power BI](media/log-analytics-powerbi/walkthrough-datasets.png)

#### <a name="create-report-based-on-dataset"></a>Criar um relatório com base no conjunto de dados
Selecionamos o conjunto de dados **Contoso Perf** e clicamos em **Resultados** no painel **Campos** à direita para exibir os campos que fazem parte desse conjunto de dados.  Para criar um grafo de linha mostrando a utilização do processador para cada computador, executamos as seguintes ações.

1. Selecione a visualização de Gráfico de linha.
2. Arraste**ObjectName** para **Filtro no nível de relatório** e marque **Processor**.
3. Arraste **CounterName** para **Filtro no nível de relatório** e marque **% Processor Time**.
4. Arraste **CounterValue** para **Valores**.
5. Arraste **Computer** para **Legenda**.
6. Arraste **TimeGenerated** para **Eixo**.

Podemos ver que o grafo de linhas resultante é exibido com os dados do nosso conjunto de dados.

![Grafos de linha do Power BI](media/log-analytics-powerbi/walkthrough-linegraph.png)

#### <a name="save-the-report"></a>Salvar o relatório
Salvamos o relatório clicando no botão Salvar na parte superior da tela e validamos se agora ele está listado na seção Relatórios no painel esquerdo.

![Relatórios do Power BI](media/log-analytics-powerbi/walkthrough-report.png)



## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para criar consultas que podem ser exportadas para o Power BI.
* Saiba mais sobre o [Power BI](http://powerbi.microsoft.com) para criar visualizações baseadas em exportações do Log Analytics.
