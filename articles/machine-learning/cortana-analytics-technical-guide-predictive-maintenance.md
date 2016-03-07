<properties
	pageTitle="Guia técnico do Modelo de Solução do Cortana Analytics para a manutenção preventiva no setor aeroespacial e em outras empresas | Microsoft Azure"
	description="Um guia técnico para o Modelo de Solução com o Microsoft Cortana Analytics para a manutenção preventiva no setor aeroespacial, de utilitários e de transportes."
	services="cortana-analytics"
	documentationCenter=""
	authors="fboylu"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cortana-analytics"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="fboylu" />

# Guia técnico do Modelo de Solução do Cortana Analytics para a manutenção preventiva no setor aeroespacial e em outras empresas

## **Confirmações**
Este artigo foi autorizado pelos cientistas de dados Yan Zhang, Gauher Shaheen, Fidan Boylu Uz e o engenheiro de software Dan Grecoe da Microsoft.

## **Visão geral**

Os Modelos de Solução foram projetados para acelerar o processo de criação de uma demonstração E2E sobre o Cortana Analytics Suite. Um modelo implantado provisionará sua assinatura com os componentes necessários do Cortana Analytics e criará os relacionamentos entre eles. Ele também propaga o pipeline de dados com dados de amostra gerados por um aplicativo gerador de dados que você vai baixar e instalar no computador local depois de implantar o modelo de solução. Os dados gerados pelo gerador hidratarão o pipeline de dados e iniciarão a geração das previsões de aprendizado de máquina, que poderão ser visualizadas no painel do Power BI. O processo de implantação guiará você pelas diversas etapas para configurar as credenciais da sua solução. Registre essas credenciais, como o nome da solução, o nome de usuário e a senha fornecidos durante a implantação.

A meta deste documento é explicar a arquitetura de referência e os diferentes componentes provisionados em sua assinatura como parte deste modelo de solução. O documento também fala sobre como substituir os dados de exemplo por dados reais para que você possa ver informações e previsões obtidas de seus próprios dados. Além disso, o documento discute as partes do Modelo de Solução que precisarão ser modificadas caso você queira personalizar a solução com seus próprios dados. As instruções sobre como criar o painel do Power BI para esse Modelo de Solução serão fornecidas ao final.

>[AZURE.TIP] Você pode baixar e imprimir uma [versão em PDF deste documento](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).

## **Visão global**

![](media/cortana-analytics-technical-guide-predictive-maintenance\predictive-maintenance-architecture.png)

Quando a solução for implantada, diversos serviços do Azure no Cortana Analytics Suite serão ativados (*ou seja,* Hub de Eventos, Stream Analytics, HDInsight, Data Factory, Aprendizado de Máquina, *etc.*). O diagrama da arquitetura acima mostra, em um alto nível, como a Manutenção Preventiva do Modelo de Solução Aeroespacial é criada de ponta a ponta. Você poderá investigar esses serviços no portal do Azure clicando neles no diagrama do modelo de solução criado com a implantação da solução, com exceção do HDInsight, pois esse serviço é provisionado sob demanda quando as atividades do pipeline relacionado são obrigatoriamente executadas e mais tarde excluídas. É possível baixar uma [versão em tamanho original do diagrama](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

As seções a seguir descrevem cada parte.

## **Fonte de dados e ingestão**

### Fonte de dados sintética

Para esse modelo, a fonte de dados usada é gerada de um aplicativo da área de trabalho que você baixará e executará localmente após a implantação bem-sucedida. Você encontrará as instruções para baixar e instalar esse aplicativo na barra de propriedades quando selecionar o primeiro nó chamado Gerador de Dados de Manutenção Preditiva no diagrama do modelo de solução. Esse aplicativo alimenta o serviço [Hub de Eventos do Azure](#azure-event-hub) com pontos de dados, ou eventos, que serão usados no restante do fluxo de solução. Essa fonte de dados é composta por dados, ou é derivada deles, publicamente disponíveis no [repositório de dados da NASA](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/) usando o [Turbofan Engine Degradation Simulation Data Set](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

O aplicativo de geração de eventos só preencherá o Hub de Eventos do Azure enquanto ele estiver em execução no computador.

### Hub de Eventos do Azure

O serviço [Hub de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) é o destinatário da entrada fornecida pela Fonte de Dados Sintética descrita acima.

## **Preparação e análise de dados**


### Stream Analytics do Azure

O serviço [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/) é usado para fornecer análise quase em tempo real no fluxo de entrada do serviço [Hub de Eventos do Azure](#azure-event-hub) e para publicar os resultados em um painel do [Power BI](https://powerbi.microsoft.com), bem como para arquivar todos os eventos brutos de entrada no serviço [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) para processamento posterior pelo serviço [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

### Agregação personalizada do HDInsight

O serviço Azure HDInsight é usado para executar scripts do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (orquestrado pelo Azure Data Factory), de modo a fornecer as agregações nos eventos brutos arquivados usando o serviço Stream Analytics do Azure.

### Aprendizado de Máquina do Azure

O serviço [Aprendizado de Máquina do Azure](https://azure.microsoft.com/services/machine-learning/) é usado (orquestrado pelo Azure Data Factory) para fazer previsões sobre a RUL (vida útil restante) de um motor de aeronave específico considerando as entradas recebidas.

## **Publicação de dados**


### Serviço Banco de Dados SQL do Azure

O serviço [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) é usado para armazenar (gerenciado pelo Azure Data Factory) as previsões recebidas pelo serviço Aprendizado de Máquina do Azure que serão consumidas no painel do [Power BI](https://powerbi.microsoft.com).

## **Consumo de dados**

### Power BI

O serviço [Power BI](https://powerbi.microsoft.com) é usado para mostrar um painel que contém as agregações e os alertas fornecidos pelo serviço [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/), bem como as previsões de RUL armazenadas no [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) que foram produzidas usando o serviço [Aprendizado de Máquina do Azure](https://azure.microsoft.com/services/machine-learning/). Para obter instruções sobre como criar o painel do Power BI para esse Modelo de Solução, consulte a seção abaixo.

## **Como inserir seus próprios dados**

Esta seção descreve como inserir seus próprios dados no Azure e quais áreas exigiriam alterações nos dados trazidos para essa arquitetura.

É improvável que algum conjunto de dados que você coloque corresponda ao conjunto de dados usado pelo [Turbofan Engine Degradation Simulation Data Set](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) usado para esse modelo de solução. Compreender seus dados e os requisitos será fundamental para a forma como você modificará esse modelo para que ele funcione com seus próprios dados. Se esta for sua primeira experiência com o serviço Aprendizado de Máquina do Azure, você poderá obter uma introdução a ele usando o exemplo em [Como criar sua primeira experiência](machine-learning-create-experiment.md).

As seções a seguir discutirão as seções do modelo que exigirão modificações quando um novo conjunto de dados for introduzido.

### Hub de Eventos do Azure

O serviço Hub de Eventos do Azure é muito genérico, já que esses dados podem ser publicados no hub no formato CSV ou JSON. Não ocorrerá nenhum processamento especial no Hub de Eventos do Azure, mas é importante que você compreenda os dados inseridos nele.

Esse documento não descreve como incluir seus dados, mas você pode facilmente enviar eventos ou dados para um Hub de Eventos do Azure usando as APIs do Hub de Eventos.

### Stream Analytics do Azure

O serviço Stream Analytics do Azure é usado para fornecer análise em quase tempo real ao ler fluxos de dados e produzir como saída dados para qualquer número de fontes.

Para a Manutenção Preditiva para o Modelo de Solução Aeroespacial, a consulta do Stream Analytics do Azure consiste em quatro subconsultas, cada uma consumindo eventos do serviço Hub de Eventos do Azure e produzindo saídas para quatro locais distintos. Essas saídas consistem em três conjuntos de dados do Power BI e em um local do Armazenamento do Azure.

A consulta do Stream Analytics do Azure pode ser encontrada por meio de:

-   Logon no portal do Azure

-   Localização dos trabalhos do Stream Analytics ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-stream-analytics.png) gerados quando a solução foi implantada (*por exemplo*, **maintenancesa02asapbi** e **maintenancesa02asablob** para a solução de manutenção preditiva)

-   Seleção

    -   ***INPUTS*** para exibir a entrada da consulta

    -   ***QUERY*** para exibir a consulta

    -   ***OUTPUTS*** para exibir as diferentes saídas

As informações sobre a criação da consulta do Stream Analytics do Azure podem ser encontradas em [Stream Analytics Query Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx), no MSDN.

Nesta solução, as consultas produzem como saída três conjuntos de dados com informações de análise quase em tempo real sobre o fluxo de dados de entrada para um painel fornecido como parte deste modelo de solução. Como há um conhecimento implícito sobre o formato de dados de entrada, essas consultas precisariam ser alteradas com base em seu formato de dados.

A consulta do segundo trabalho do Stream Analytics, **maintenancesa02asablob**, simplesmente produz como saída todos os eventos do [Hub de Eventos](https://azure.microsoft.com/services/event-hubs/) para o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) e, portanto, não requer alteração, independentemente do formato dos dados, já que as informações completas do evento são transmitidas para o armazenamento.

### Fábrica de dados do Azure

O serviço [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orquestra a movimentação e o processamento de dados. Na Manutenção Preditiva para o Modelo de Solução Aeroespacial, o data factory é composto por três [pipelines](../data-factory/data-factory-create-pipelines.md) que movem e processam os dados usando diversas tecnologias. Você pode acessar seu data factory abrindo o nó Data Factory na parte inferior do diagrama do modelo de solução criado com a implantação da solução. Você será levado ao data factory no portal do Azure. Se você encontrar erros em seus conjuntos de dados, poderá ignorá-los, já que eles ocorrem porque a implantação do data factory foi anterior ao início do gerador de dados. Esses erros não impedem o funcionamento do seu data factory.

![](media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Esta seção discute os [pipelines](../data-factory/data-factory-create-pipelines.md) e as [atividades](../data-factory/data-factory-create-pipelines.md) necessários contidos no [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). A seguir, o modo de exibição de diagrama da solução.

![](media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Dois dos pipelines desse data factory contêm scripts do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) usados para particionar e agregar os dados. Quando observado o contrário, os scripts estarão localizados na conta do [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) criada durante a instalação. A localização deles será: maintenancesascript\\\script\\\hive\\\ (ou https://[Your nome da solução].blob.core.windows.net/maintenancesascript).

Da mesma forma que acontece com as consultas do [Stream Analytics do Azure](#azure-stream-analytics-1) , os scripts do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) têm conhecimento implícito sobre o formato de dados de entrada. Essas consultas precisam ser alteradas com base em seu formato de dados e nos requisitos de [engenharia de recursos](machine-learning-feature-selection-and-engineering.md).

#### *AggregateFlightInfoPipeline*

Esse [pipeline](../data-factory/data-factory-create-pipelines.md) contém uma única atividade - um atividade [HDInsightHive](../data-factory/data-factory-hive-activity.md) que usa um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para particionar os dados colocados no [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) durante o trabalho do [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/).

O script do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para essa tarefa de particionamento é ***AggregateFlightInfo.hql***

#### *MLScoringPipeline*

Esse [pipeline](../data-factory/data-factory-create-pipelines.md) contém várias atividades e seu resultado final é composto pelas previsões pontuadas do experimento do [Aprendizado de Máquina do Azure](https://azure.microsoft.com/services/machine-learning/) associado a esse modelo de solução.

As atividades contidas aqui são:

-   A atividade [HDInsightHive](../data-factory/data-factory-hive-activity.md), que usa um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para executar agregações e recursos de engenharia necessários ao experimento do [Aprendizado de Máquina do Azure](https://azure.microsoft.com/services/machine-learning/). O script do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para essa tarefa de particionamento é ***PrepareMLInput.hql***.

-   A atividade [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx), que move os resultados da atividade [HDInsightHive](../data-factory/data-factory-hive-activity.md) para um único blob do [Armazenamento do Azure](https://azure.microsoft.com/services/storage/), que pode ser acessado pela atividade [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx).

-   A atividade [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx), que chama o experimento do [Aprendizado de Máquina do Azure](https://azure.microsoft.com/services/machine-learning/), que faz com que os resultados sejam colocados em um único blob do [Armazenamento do Azure](https://azure.microsoft.com/services/storage/).

#### *CopyScoredResultPipeline*

Esse [pipeline](../data-factory/data-factory-create-pipelines.md) contém uma única atividade - uma atividade [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os resultados do experimento do [Aprendizado de Máquina do Azure](#azure-machine-learning) do ***MLScoringPipeline*** para o [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) provisionado como parte da instalação do modelo de solução.

### Aprendizado de Máquina do Azure

O experimento do [Aprendizado de Máquina do Azure](https://azure.microsoft.com/services/machine-learning/) usado para esse modelo de solução fornece a RUL (Vida Útil Restante) de um motor de aeronave. O experimento é específico do conjunto de dados consumido e, portanto, exigirá modificação ou substituição específicas para os dados trazidos.

Para saber mais sobre como o experimento do Aprendizado de Máquina do Azure foi criado, confira [Manutenção preventiva: etapa 1 de 3, preparação de dados e engenharia de recursos](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## **Monitorar o progresso**
 Depois que o Gerador de Dados é iniciado, o pipeline começa a obter os hidratados e os diferentes componentes de sua solução começam a entrar em ação seguindo os comandos emitidos pelo Data Factory. Há duas maneiras possíveis de monitorar o pipeline.

1. Um dos trabalhos do Stream Analytics grava os dados brutos de entrada no armazenamento de blobs. Se você clicar no componente Armazenamento de Blobs da solução na tela, a solução foi implantada com êxito. Desse modo, clicar em Abrir, no painel direito, exibirá o [portal de gerenciamento](https://portal.azure.com/). No portal, clique em Blobs. No painel seguinte, você verá uma lista de Contêineres. Clique em **maintenancesadata**. No painel seguinte, você verá a pasta **rawdata**. Dentro da pasta rawdata, você verá pastas como hour=17, hour=18, etc. Caso você visualize essas pastas, isso indica que os dados brutos estão sendo gerados com êxito no computador e armazenados no armazenamento de blobs. Você deve ver arquivos csv que devem ter tamanhos finitos em MB nessas pastas.

2. A última etapa do pipeline é gravar dados (por exemplo, previsões do aprendizado de máquina) no Banco de Dados SQL. Talvez seja preciso esperar até três horas para que os dados apareçam no Banco de Dados SQL. Uma forma de monitorar o volume de dados disponível no Banco de Dados SQL é pelo [portal do Azure](https://manage.windowsazure.com/). No painel, localize BANCOS DE DADOS SQL ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-SQL-databases.png) e clique nele. Em seguida, localize o banco de dados **pmaintenancedb** e clique nele. Na próxima página, na parte inferior, clique em GERENCIAR

	![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-manage.png).

	Aqui, é possível clicar em Nova Consulta e consular o número de linhas (por exemplo, selecione count(*) em PMResult ). À medida que o banco de dados aumenta, o número de linhas na tabela deve aumentar.


## **Painel do Power BI**

### Visão geral

Esta seção descreve como configurar o painel do Power BI para visualizar os dados em tempo real do Stream Analytics do Azure (afunilamento), bem como resultados de previsão do Aprendizado de Máquina do Azure (caminho frio).

### Painel de caminho frio de instalação

No pipeline de dados do caminho frio, o principal objetivo é obter a RUL de previsão (vida útil restante) de cada motor da aeronave após o término de um voo (ciclo). O resultado da previsão é atualizado a cada três horas para prever os motores da aeronave que concluiu um voo durante as três últimas horas.

O Power BI se conecta a um banco de dados SQL do Azure como fonte de dados, onde os resultados de previsão são armazenados. Observação: 1) Depois de implantar sua solução, uma previsão real será mostrada no banco de dados dentro de três horas. O arquivo pbix que acompanha o download do Gerador contém alguns dados de propagação para que você possa criar o painel do Power BI imediatamente. 2) Nesta etapa, o pré-requisito é baixar e instalar o software gratuito [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

As etapas a seguir mostrarão como conectar o arquivo pbix ao Banco de Dados SQL envolvido no momento da implantação da solução com dados (*por exemplo,* resultados da previsão) para visualização.

1.  Obtenha as credenciais do banco de dados.

    Você precisará do **nome do servidor de banco de dados, do nome do banco de dados, do nome de usuário e da senha** antes de passar para as próximas etapas. Veja algumas etapas para mostrar como encontrá-las.

    -   Quando **'Banco de Dados SQL do Azure'** ficar verde no diagrama do modelo da solução, clique nele e clique em **'Abrir'**.

    -   Você verá uma nova guia/janela do navegador exibindo a página do portal do Azure. Clique em **'Grupos de recursos'** no painel esquerdo.

    -   Selecione a assinatura que você está usando para implantar a solução e selecione **'YourSolutionName\_ResourceGroup'**.

    -   No novo painel pop-out, clique no ícone ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-sql.png) para acessar o banco de dados. O nome do banco de dados fica ao lado desse ícone (*por exemplo*, **'pmaintenancedb'**) e o **nome do servidor de banco de dados** está listado sob a propriedade Nome do servidor e deve ser parecido com **YourSoutionName.database.windows.net**.

	-   O **nome de usuário** e a **senha** do banco de dados são iguais ao nome de usuário e à senha previamente registrados durante a implantação da solução.

2.  Atualize a fonte de dados do arquivo de relatório de caminho frio com o Power BI Desktop.

    -   Na pasta do computador em que você baixou e descompactou o arquivo Gerador, clique duas vezes no arquivo **PowerBI\\PredictiveMaintenanceAerospace.pbix**. Se houver mensagens de aviso quando você abrir o arquivo, ignore-as. Na parte superior do arquivo, clique em **'Editar Consultas'**.

	    ![](media\cortana-analytics-technical-guide-predictive-maintenance\edit-queries.png)

	-	Você verá duas tabelas, **RemainingUsefulLife** e **PMResult**. Selecione a primeira tabela e clique em ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-query-settings.png) ao lado de **'Fonte'** em **'ETAPAS APLICADAS'** no painel **'Configurações da Consulta'** à direita. Ignore as mensagens de aviso que aparecerem.

    -   Na janela pop-out, substitua **'Servidor'** e **'Banco de dados'** por seus próprios nomes de servidor e de banco de dados e clique em **'OK'**. Para o nome do servidor, especifique a porta 1433 (**NomeDaSuaSolução.database.windows.net, 1433**). Deixe o campo Banco de Dados como **pmaintenancedb**. Ignore as mensagens de aviso que aparecem na tela.

    -   Na próxima janela pop-out, você verá duas opções no painel esquerdo (**Windows** e **Banco de dados**). Clique em **'Banco de dados'**, preencha o **'Nome de usuário'** e a **'Senha'** (são o nome de usuário e a senha inseridos quando você implantou a solução e criou um banco de dados SQL do Azure). Em ***Selecione o nível ao qual aplicar essas configurações***, marque a opção de nível de banco de dados. Clique em **'Conectar'**.

    -   Clique na segunda tabela **PMResult** e clique em ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-navigation.png) ao lado de **'Fonte'** em **'ETAPAS APLICADAS'** no painel **'Configurações da Consulta'** à direita e atualize os nomes do servidor e do banco de dados, como nas etapas acima, e clique em OK.

    -   Depois de ser guiado de volta à página anterior, feche a janela. Será exibida uma mensagem - clique em **Aplicar**. Por fim, clique no botão **Salvar** para salvar as alterações. Seu arquivo do Power BI agora estabeleceu uma conexão com o servidor. Se suas visualizações estiverem vazias, limpe as seleções nas visualizações para visualizar todos os dados clicando no ícone de borracha no canto superior direito das legendas. Use o botão de atualização para refletir os novos dados nas visualizações. Inicialmente, você só verá os dados de propagação em suas visualizações, já que a atualização do Data Factory está agendada para a cada três horas. Após três horas, você verá novas previsões refletidas nas visualizações quando atualizar os dados.

3.  (Opcional) Publique o painel de caminho frio no [Power BI online](http://www.powerbi.com/). Observe que esta etapa precisa de uma conta do Power BI (ou de uma conta do Office 365).

    -   Clique em **'Publicar'** e, depois de alguns segundos, será exibida uma janela mostrando "Êxito ao publicar no Power BI!", com uma marca de seleção verde. Clique no link abaixo, "Abrir PredictiveMaintenanceAerospace.pbix no Power BI". Para obter instruções detalhadas, confira [Publicar por meio do Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Para criar um novo painel: clique no sinal de **+** ao lado da seção **Painéis** no painel esquerdo. Insira o nome "Demonstração de manutenção preditiva" para esse novo painel.

    -   Depois de abrir o relatório, clique em ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-pin.png) para fixar todas as visualizações no painel. Para obter instruções detalhadas, confira [Fixar um bloco em um painel do Power BI por meio de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report). Vá para a página do painel e ajuste o tamanho e o local de suas visualizações e edite os títulos delas. Para obter instruções detalhadas sobre como editar seus blocos, confira [Editar um bloco — redimensionar, mover, renomear, fixar, excluir, adicionar hiperlink](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Veja um painel de exemplo com algumas visualizações de caminho frio fixadas nele. Dependendo da duração da execução do gerador de dados, os números nas visualizações poderão ser diferentes. <br/> ![](media\cortana-analytics-technical-guide-predictive-maintenance\final-view.png) <br/>
    -   Para agendar a atualização dos dados, passe o mouse sobre o conjunto de dados **PredictiveMaintenanceAerospace**, clique em ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-elipsis.png) e escolha **Agendar Atualização**. <br/> **Observação:** se for exibida uma mensagem de aviso, clique em **Editar Credenciais** e verifique se as credenciais de banco de dados são iguais às descritas na etapa 1. <br/> ![](media\cortana-analytics-technical-guide-predictive-maintenance\schedule-refresh.png) <br/>
    -   Expanda a seção **Agendar Atualização**. Ative "manter os dados atualizados". <br/>
    -   Agende a atualização com base em suas necessidades. Para saber mais, confira [Atualizar dados no Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### Painel de afunilamento de instalação

As etapas a seguir mostrarão como visualizar a saída de dados em tempo real de trabalhos do Stream Analytics gerados no momento da implantação da solução. Será necessária uma conta do [Power BI online](http://www.powerbi.com/) para executar as etapas a seguir. Se não tiver uma conta, você poderá [criar uma](https://powerbi.microsoft.com/pricing).

1.  Adicione a saída do Power BI ao Stream Analytics (ASA).

    -  Você precisará seguir as instruções em [Stream Analytics do Azure e Power BI: um painel de análise em tempo real para dados de streaming](stream-analytics-power-bi-dashboard.md) para configurar a saída do seu trabalho do Stream Analytics do Azure como painel do Power BI.
	- Configure as três saídas da consulta do ASA, **aircraftmonitor**, **aircraftalert** e **flightsbyhour**. Você pode exibir a consulta clicando na guia Consulta. Correspondendo a cada uma dessas tabelas, você precisará adicionar uma saída ao ASA. Quando você adicionar a primeira saída (*por exemplo,* **aircraftmonitor**) verifique se **Alias de Saída**, **Nome do Conjunto de Dados** e **Nome da Tabela** são iguais (**aircraftmonitor**). Repita as etapas para adicionar saídas para **aircraftalert** e **flightsbyhour**. Depois de adicionar as três tabelas de saída e de iniciar o trabalho do ASA, você obterá uma mensagem de confirmação (*por exemplo*, "Êxito ao iniciar o trabalho maintenancesa02asapbi do Stream Analytics").

2. Faça logon no [Power BI online](http://www.powerbi.com)

    -   Na seção Conjuntos de dados do painel esquerdo, em Meu Espaço de Trabalho, os nomes de ***CONJUNTO DE DADOS*** **aircraftmonitor**, **aircraftalert** e **flightsbyhour** devem aparecer. Esse são os dados de streaming que você enviou por push do Azure Stream Analytics na etapa anterior. O conjunto de dados **flightsbyhour** pode não ser exibido ao mesmo tempo que os outros dois devido à natureza da consulta SQL por trás dele. No entanto, ele deve aparecer após uma hora.
    -   Verifique se o painel ***Visualizações*** está aberto e se é mostrado no lado direito da tela.

3. Depois que os dados fluírem para o Power BI, você poderá começar a visualizar os dados de streaming. Veja abaixo um painel de exemplo com algumas visualizações de afunilamento fixadas nele. É possível criar outros blocos de painel com base em conjuntos de dados apropriados. Dependendo da duração da execução do gerador de dados, os números nas visualizações poderão ser diferentes.


    ![](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

4. Veja algumas etapas para criar um dos blocos acima — o bloco "Exibição de frota do sensor 11 versus limite 48,26":

    -   Clique no conjunto de dados **aircraftmonitor** na seção Conjuntos de dados do painel esquerdo.

    -   Clique no ícone **Gráfico de Linhas**.

    -   Clique em **Processado** no painel **Campos** para mostrá-lo em "Eixo" no painel **Visualizações**.

    -   Clique em "s11" e em "s11\_alert" para que ambos sejam exibidos em "Valores". Clique na pequena seta ao lado de **s11** e de **s11\_alert**, altere "Soma" para "Média".

    -   Clique em **SALVAR** na parte superior e chame o relatório de "aircraftmonitor". O relatório chamado "aircraftmonitor" será exibido na seção **Relatórios** do painel **Navegador** à esquerda.

    -   Clique no ícone **Fixar Visual** no canto superior direito desse gráfico de linha. Uma janela "Fixar ao painel" poderá aparecer para que você escolha um painel. Selecione "Demonstração de manutenção preditiva" e clique em "Fixar".

    -   Passe o mouse sobre esse bloco no painel, clique no ícone "editar" no canto superior direito para alterar o título para "Exibição de frota do sensor 11 em relação ao limite 48,26" e o subtítulo"Média na frota ao longo do tempo".

## **Como excluir a solução**
Não se esqueça de parar o gerador de dados quando não estiver usando ativamente a solução, pois a execução dele incorrerá em custos mais altos. Se não estiver usando a solução, exclua-a. A exclusão da solução excluirá todos os componentes provisionados em sua assinatura quando você implantou a solução. Para excluir a solução, clique com botão no nome da solução no painel esquerdo do modelo de solução e clique em Excluir.

## **Ferramentas de estimativa de custo**

As duas ferramentas a seguir estão disponíveis para ajudar você a entender melhor os custos totais envolvidos na execução da Manutenção Preditiva para o Modelo de Solução Aeroespacial em sua assinatura:

-   [Ferramenta Calculadora de Preço do Microsoft Azure (online)](https://azure.microsoft.com/pricing/calculator/)

-   [Ferramenta Calculadora de Preço do Microsoft Azure (área de trabalho)](http://www.microsoft.com/download/details.aspx?id=43376)

<!---HONumber=AcomDC_0224_2016-->