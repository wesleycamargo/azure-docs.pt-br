<properties
	pageTitle="Guia técnico de Previsão de demanda em energia | Microsoft Azure"
	description="Um guia técnico para o Modelo de Solução com o Microsoft Cortana Intelligence para previsão de demanda de energia"
	services="cortana-analytics"
	documentationCenter=""
	authors="yijichen"
	manager="ilanr9"
	editor="yijichen"/>

<tags
	ms.service="cortana-analytics"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/24/2016"
	ms.author="inqiu;yijichen;ilanr9"/>

# Guia técnico para o Modelo de Solução do Cortana Intelligence para previsão de demanda em energia

## **Visão geral**

Os Modelos de Solução foram projetados para acelerar o processo de criação de uma demonstração E2E sobre o Cortana Intelligence Suite. Um modelo implantado provisionará à sua assinatura os componentes necessários do Cortana Intelligence e estabelecerá as relações entre eles. Ele também alimenta o pipeline de dados com amostras de dados geradas em um aplicativo de simulação de dados. Baixe o simulador de dados do link fornecido e instale-o em seu computador local. Consulte o arquivo readme.txt para obter instruções sobre o uso do simulador. Os dados gerados pelo simulador hidratarão o pipeline de dados e começarão a gerar previsões de aprendizado de máquina, que poderão ser visualizadas no painel do Power BI.

O processo de implantação guiará você pelas diversas etapas para configurar as credenciais da sua solução. Registre essas credenciais, como o nome da solução, o nome de usuário e a senha fornecidos durante a implantação.

A meta deste documento é explicar a arquitetura de referência e os diferentes componentes provisionados em sua assinatura como parte deste Modelo de Solução. O documento também fala sobre como substituir as amostras de dados por dados reais, para que você possa ver informações/previsões obtidas de seus próprios dados. Além disso, o documento discute as partes do Modelo de Solução que precisarão ser modificadas caso você queira personalizar a solução com seus próprios dados. As instruções sobre como criar o painel do Power BI para esse Modelo de Solução serão fornecidas ao final.

## **Visão global**

![](media\cortana-analytics-technical-guide-demand-forecast\ca-topologies-energy-forecasting.png)

### Arquitetura explicada
Quando a solução for implantada, diversos serviços do Azure no Cortana Analytics Suite serão ativados (*ou seja,* Hub de Eventos, Stream Analytics, HDInsight, Data Factory, Aprendizado de Máquina *etc.*). O diagrama da arquitetura acima mostra, em alto nível, como o Modelo de Solução de Previsão de demanda para energia é criado de ponta a ponta. Você poderá investigar esses serviços clicando neles no diagrama de modelo de solução criado com a implantação da solução. As seções a seguir descrevem cada parte.

## **Fonte de dados e ingestão**

### Fonte de dados sintética

Para esse modelo, a fonte de dados usada é gerada de um aplicativo da área de trabalho que você baixará e executará localmente após a implantação bem-sucedida. Você encontrará as instruções para baixar e instalar esse aplicativo na barra de propriedades, quando selecionar o primeiro nó chamado Simulador de Dados de Previsão de Energia no diagrama do modelo de solução. Esse aplicativo alimenta o serviço [Hub de Eventos do Azure](#azure-event-hub) com pontos de dados, ou eventos, que serão usados no restante do fluxo de solução.

O aplicativo de geração de eventos só preencherá o Hub de Eventos do Azure enquanto ele estiver em execução no computador.

### Hub de Eventos do Azure

O serviço [Hub de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) é o destinatário da entrada fornecida pela Fonte de Dados Sintética descrita acima.

## **Preparação e análise de dados**


### Stream Analytics do Azure

O serviço [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/) é usado para fornecer análise quase em tempo real no fluxo de entrada do serviço [Hub de Eventos do Azure](#azure-event-hub) e para publicar os resultados em um painel do [Power BI](https://powerbi.microsoft.com), bem como para arquivar todos os eventos brutos de entrada no serviço [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) para processamento posterior pelo serviço [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

### Agregação personalizada do HDInsight

O serviço Azure HDInsight é usado para executar scripts do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (orquestrado pelo Azure Data Factory), de modo a fornecer as agregações nos eventos brutos arquivados usando o serviço Stream Analytics do Azure.

### Aprendizado de Máquina do Azure

O serviço [Aprendizado de Máquina do Azure](https://azure.microsoft.com/services/machine-learning/) é usado (orquestrado pelo Azure Data Factory) para fazer previsões sobre o consumo futuro de energia de uma região específica com base nos dados recebidos.

## **Publicação de dados**


### Serviço Banco de Dados SQL do Azure

O serviço [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) é usado para armazenar (gerenciado pelo Azure Data Factory) as previsões recebidas pelo serviço Aprendizado de Máquina do Azure que serão consumidas no painel do [Power BI](https://powerbi.microsoft.com).

## **Consumo de dados**

### Power BI

O serviço [Power BI](https://powerbi.microsoft.com) é usado para mostrar um painel que contém as agregações fornecidas pelo serviço [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/), bem como os resultados das previsões de demanda no [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) que foram produzidos usando o serviço [Aprendizado de Máquina do Azure](https://azure.microsoft.com/services/machine-learning/). Para obter instruções sobre como criar o painel do Power BI para esse Modelo de Solução, consulte a seção abaixo.

## **Como inserir seus próprios dados**

Esta seção descreve como inserir seus próprios dados no Azure e quais áreas exigiriam alterações nos dados trazidos para essa arquitetura.

É improvável que algum conjunto de dados que você coloque corresponda ao conjunto de dados usado para esse modelo de solução. Compreender seus dados e os requisitos será fundamental para a forma como você modificará esse modelo para que ele funcione com seus próprios dados. Se esta for sua primeira experiência com o serviço Aprendizado de Máquina do Azure, você poderá obter uma introdução a ele usando o exemplo em [Como criar sua primeira experiência](machine-learning\machine-learning-create-experiment.md).

As seções a seguir discutirão as seções do modelo que exigirão modificações quando um novo conjunto de dados for introduzido.

### Hub de Eventos do Azure

O serviço [Hub de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) é muito genérico, já que esses dados podem ser publicados no hub no formato CSV ou JSON. Não ocorrerá qualquer processamento especial no Hub de Eventos do Azure, mas é importante que você compreenda os dados inseridos nele.

Esse documento não descreve como incluir seus dados, mas é possível enviar com facilidade eventos ou dados para um Hub de Eventos do Azure usando a [API do Hub de Eventos](event-hubs\event-hubs-programming-guide.md).

### Stream Analytics do Azure

O serviço [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/) é usado para fornecer análise em quase tempo real ao ler fluxos de dados e produzir como saída dados para qualquer número de fontes.

Para o Modelo de Solução de Previsão de demanda para energia, a consulta do Stream Analytics do Azure é formada por duas subconsultas, cada uma consumindo eventos do serviço Hub de Eventos do Azure e produzindo saídas para dois locais distintos. Essas saídas são formadas por um conjunto de dados do Power BI e em um local do Armazenamento do Azure.

A consulta do [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/) pode ser encontrada por meio de:

-   Entrando no [portal de gerenciamento do Azure](https://manage.windowsazure.com/).

-   Localizando os trabalhos do Stream Analytics ![](media\cortana-analytics-technical-guide-demand-forecast\icon-stream-analytics.png) que foram gerados durante a implantação da solução. Um serve para enviar dados ao armazenamento de blobs (por exemplo, mytest1streaming432822asablob) e o outro é para publicar dados no Power BI (por exemplo, mytest1streaming432822asapbi).


-   Seleção

    -   ***INPUTS*** para exibir a entrada da consulta

    -   ***QUERY*** para exibir a consulta

    -   ***OUTPUTS*** para exibir as saídas diferentes

As informações sobre a criação da consulta do Stream Analytics do Azure podem ser encontradas na [Referência de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx), no MSDN.

Nesta solução, o trabalho do Stream Analytics do Azure que produz como saída um conjunto de dados com informações de análise quase em tempo real sobre o fluxo de dados de entrada para um painel do Power BI, é fornecido como parte desse modelo de solução. Como há um conhecimento implícito sobre o formato de dados de entrada, essas consultas precisariam ser alteradas com base em seu formato de dados.

O outro trabalho do Stream Analytics gera como saída todos os eventos do [Hub de Eventos](https://azure.microsoft.com/services/event-hubs/) para o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) e, portanto, não exige alteração, independentemente do formato dos dados, já que as informações completas do evento são transmitidas para o armazenamento.

### Fábrica de dados do Azure

O serviço [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orquestra a movimentação e o processamento de dados. No Modelo de Previsão de Demanda para Solução de Energia, o data factory é composto por doze [pipelines](data-factory\data-factory-create-pipelines.md) que movem e processam os dados usando diversas tecnologias.

  Você pode acessar seu data factory abrindo o nó Data Factory na parte inferior do diagrama do modelo de solução criado com a implantação da solução. Você será levado ao data factory no portal de gerenciamento do Azure. Se você encontrar erros em seus conjuntos de dados, poderá ignorá-los, já que eles ocorrem porque a implantação do data factory foi anterior ao início do gerador de dados. Esses erros não impedem o funcionamento do seu data factory.

Esta seção discute os [pipelines](data-factory\data-factory-create-pipelines.md) e as [atividades](data-factory\data-factory-create-pipelines.md) necessários contidos no [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). A seguir, o modo de exibição de diagrama da solução.

![](media\cortana-analytics-technical-guide-demand-forecast\ADF2.png)

Cinco dos pipelines desse data factory contêm scripts do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) usados para particionar e agregar os dados. Quando observado o contrário, os scripts estarão localizados na conta do [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) criada durante a instalação. A localização deles será: demandforecasting\\\script\\\hive\\\ (ou nome da solução do https://[Your].blob.core.windows.net/demandforecasting).

Da mesma forma como acontece com as consultas do [Stream Analytics do Azure](#azure-stream-analytics-1) , os scripts do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) têm conhecimento implícito sobre o formato de dados de entrada. Essas consultas precisam ser alteradas com base em seu formato de dados e nos requisitos de [engenharia de recursos](machine-learning\machine-learning-feature-selection-and-engineering.md).

#### *AggregateDemandDataTo1HrPipeline*

Esse [pipeline](data-factory\data-factory-create-pipelines.md) contém uma única atividade - uma atividade [HDInsightHive](data-factory\data-factory-hive-activity.md) usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para agregar os dados de demanda transmitidos a cada 10 segundos no nível da subestação para o nível de região por hora, e colocar no [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) por meio do trabalho do Stream Analytics do Azure.

O script do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para essa tarefa de particionamento é ***AggregateDemandRegion1Hr.hql***


#### *LoadHistoryDemandDataPipeline*

Esse [pipeline](data-factory\data-factory-create-pipelines.md) contém duas atividades:
- A atividade [HDInsightHive](data-factory\data-factory-hive-activity.md), que usa um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script do Hive para agregar os dados de demanda histórica por hora no nível de subestação ao nível de região por hora, e os coloca no Armazenamento do Azure durante o trabalho do Stream Analytics do Azure

- A atividade [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os dados agregados do Blob de Armazenamento do Azure para o Banco de Dados SQL do Azure provisionado como parte da instalação do modelo de solução.

O script do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para essa tarefa é ***AggregateDemandHistoryRegion.hql***.


#### *MLScoringRegionXPipeline*

Esses [pipelines](data-factory\data-factory-create-pipelines.md) contêm várias atividades cujos resultados finais são as previsões pontuadas do experimento do Aprendizado de Máquina do Azure associados a esse modelo de solução. Eles são quase idênticos, exceto pelo fato de que cada um trata apenas da região diferente, que está sendo concluída por uma RegionID diferente passada no pipeline do ADF e no script do Hive para cada região. As atividades contidas aqui são:
-	A atividade [HDInsightHive](data-factory\data-factory-hive-activity.md), que usa um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script do Hive para executar agregações e recursos de engenharia necessários ao experimento do Aprendizado de Máquina do Azure. Os scripts de Hive para essa tarefa são os respectivos ***PrepareMLInputRegionX.hql***.

-	A atividade [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx), que move os resultados da atividade [HDInsightHive](data-factory\data-factory-hive-activity.md) para um único blob do Armazenamento do Azure, que pode ser acessado pela atividade [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx).

-	A atividade [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx), que chama o experimento do Aprendizado de Máquina do Azure, que faz com que os resultados sejam colocados em um único blob do Armazenamento do Azure.

#### *CopyScoredResultRegionXPipeline*
Esses [pipelines](data-factory\data-factory-create-pipelines.md) contêm uma única atividade - uma atividade [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os resultados do experimento do Aprendizado de Máquina do Azure, do respectivo ***MLScoringRegionXPipeline*** para o Banco de Dados SQL do Azure que foi provisionado como parte da instalação do modelo de solução.

#### *CopyAggDemandPipeline*
Esses [pipelines](data-factory\data-factory-create-pipelines.md) contêm uma única atividade - uma atividade [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os dados de demanda contínua agregados do ***LoadHistoryDemandDataPipeline*** para o Banco de Dados SQL do Azure que foi provisionado como parte da instalação do modelo de solução.

#### *CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Esses [pipelines](data-factory\data-factory-create-pipelines.md) contêm uma única atividade - uma atividade [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os dados de referência da Região/Subestação/Topologygeo que são carregados no blob de Armazenamento do Azure como parte da instalação do modelo de solução para o Banco de Dados SQL que foi provisionado como parte da instalação do modelo de solução.

### Aprendizado de Máquina do Azure
O experimento do [Aprendizado de Máquina do Azure](https://azure.microsoft.com/services/machine-learning/) usado para esse modelo de solução fornece a previsão de demanda da região. O experimento é específico ao conjunto de dados consumido e, portanto, exigirá modificação ou substituição específica para os dados trazidos.

## **Monitorar o progresso**
Depois que o Gerador de Dados é iniciado, o pipeline começa a obter os hidratados e os diferentes componentes de sua solução começam a entrar em ação seguindo os comandos emitidos pelo Data Factory. Há duas maneiras possíveis de monitorar o pipeline.

1. Verificar os dados do Armazenamento de Blobs do Azure

	Um dos trabalhos do Stream Analytics grava os dados brutos de entrada no armazenamento de blobs. Se você clicar no componente **Armazenamento de Blobs do Azure ** de sua solução na tela na qual você implantou a solução com êxito, e clicar em **Abrir** no painel direito, você será levado ao [Portal de gerenciamento do Azure](https://portal.azure.com). No portal, clique em **Blobs**. No painel seguinte, você verá uma lista de Contêineres. Clique em **"energysadata"**. No painel seguinte, você verá a pasta **"demandongoing"**. Dentro da pasta rawdata, você verá pastas com nomes como date=2016-01-28 etc. Caso você visualize essas pastas, isso indica que os dados brutos estão sendo gerados com êxito no computador e armazenados no armazenamento de blobs. Você deverá ver arquivos com tamanhos finitos em MB nessas pastas.

2. Verifique os dados do Banco de Dados SQL do Azure.

	A última etapa do pipeline é gravar dados (por exemplo, previsões do aprendizado de máquina) no Banco de Dados SQL. Talvez seja necessário esperar até duas horas no máximo para que os dados apareçam no Banco de Dados SQL. Uma forma de monitorar o volume de dados disponível no Banco de Dados SQL é pelo [portal de gerenciamento do Azure](https://manage.windowsazure.com/). No painel esquerdo, localize BANCOS DE DADOS SQL![](media\cortana-analytics-technical-guide-demand-forecast\SQLicon2.png) e clique nele. Em seguida, localize seu banco de dados (ou seja, demo123456db) e clique nele. Na próxima página, na seção **"Conectar-se ao banco de dados"**, clique em **"Executar consultas Transact-SQL em seu Banco de Dados SQL"**.

	Aqui, você poderá clicar em Nova Consulta e consultar o número de linhas (por exemplo, "select count(*) de DemandRealHourly)". À medida que seu banco de dados cresce, o número de linhas na tabela aumenta).

3. Verifique os dados no painel do Power BI.

	Você pode configurar o painel de afunilamento do Power BI para monitorar os dados brutos de entrada. Siga as instruções na seção "Painel do Power BI".



## **Painel do Power BI**

### Visão geral

Esta seção descreve como configurar o painel do Power BI para visualizar os dados em tempo real do Stream Analytics do Azure (afunilamento), bem como os resultados de previsão do Aprendizado de Máquina do Azure (dados em lote/caminho frio).


### Configurar o painel de afunilamento

As etapas a seguir mostrarão como visualizar a saída de dados em tempo real de trabalhos do Stream Analytics gerados no momento da implantação da solução. Será necessária uma conta do [Power BI online](http://www.powerbi.com/) para executar as etapas a seguir. Se não tiver uma conta, você poderá [criar uma](https://powerbi.microsoft.com/pricing).

1.  Adicione a saída do Power BI ao Stream Analytics (ASA).

    -  Você precisará seguir as instruções em [Stream Analytics do Azure e Power BI: um painel de análise em tempo real para dados de streaming](stream-analytics\stream-analytics-power-bi-dashboard.md) para configurar a saída do seu trabalho do Stream Analytics do Azure como painel do Power BI.

	- Localize o trabalho do stream analytics em seu [portal de gerenciamento do Azure](https://manage.windowsazure.com). O nome do trabalho deve ser: NomeDaSuaSolução+"trabalhodestreaming"+número aleatório+"asapbi" (ou seja, demostreamingjob123456asapbi).

	- Configure a saída da consulta ASA, que é **PBIoutput**. Verifique se o **Alias de Saída** é igual ao de sua consulta. Você pode nomear o **Nome do Conjunto de Dados** e o **Nome da Tabela** como **'EnergyStreamData'**. Depois de adicionar a saída, clique em **"Iniciar"** na parte inferior da página para iniciar o trabalho do Stream Analytics. Você deverá receber uma mensagem de confirmação (*por exemplo*, "O trabalho do Stream Analytics myteststreamingjob12345asablob foi iniciado com êxito").

2. Faça logon no [Power BI online](http://www.powerbi.com)

    -   Na seção Conjuntos de Dados do painel esquerdo em Meu Espaço de Trabalho, você deverá ver um novo conjunto de dados no painel esquerdo do Power BI. Esses são os dados de streaming enviados do Stream Analytics do Azure na etapa anterior.

    -   Verifique se o painel ***Visualizações*** está aberto e se é mostrado no lado direito da tela.

3. Crie o bloco "Demanda por Carimbo de Data/Hora":
	-	Clique no conjunto de dados **'EnergyStreamData'** na seção Conjuntos de Dados do painel esquerdo.

	-	Clique no ícone **"Gráfico de Linhas"** ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic8.png).

	-	Clique em 'EnergyStreamData' no campo **Campos**.

	-	Clique em **"Carimbo de Data/Hora"** e verifique se isso é exibido em "Eixo". Clique em **"Carregar"** e verifique se isso é exibido em "Valores".

	-	Clique em **SALVAR** na parte superior e dê ao relatório o nome "EnergyStreamDataReport". O relatório chamado "EnergyStreamDataReport" será exibido na seção Relatórios do painel Navegador à esquerda.

	-	Clique no ícone **"Fixação Visual"**![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) no canto superior direito deste gráfico de linha, uma janela de "Fixar ao Painel" pode aparecer para que você escolha um painel. Selecione "EnergyStreamDataReport" e clique em "Fixar".

	-	Passe o mouse sobre esse bloco no painel, clique no ícone "editar" no canto superior direito para alterar seu título para "Demandar por Carimbo de Data/Hora"

4.	Crie outros blocos de painel com base em conjuntos de dados apropriados. O modo de exibição de painel final é exibido abaixo. ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic5.png)


### Configurar o painel de caminho frio
No pipeline de dados de caminho frio, o principal objetivo é obter a previsão de demanda de cada região. O Power BI se conecta a um banco de dados SQL do Azure como fonte de dados, onde os resultados de previsão são armazenados.

> [AZURE.NOTE] 1) Demora algumas horas para coletar resultados de previsão suficientes para o painel. Recomendamos o início desse processo duas a três horas após a inicialização do gerador de dados. 2) Nesta etapa, o pré-requisito é baixar e instalar o software gratuito [Power BI desktop](https://powerbi.microsoft.com/desktop).



1.  Obtenha as credenciais do banco de dados.

    Você precisará do **nome do servidor de banco de dados, do nome do banco de dados, do nome de usuário e da senha** antes de passar para as próximas etapas. Veja algumas etapas para mostrar como encontrá-las.

    -   Quando **"Banco de Dados SQL do Azure"** ficar verde no diagrama do modelo da solução, clique nele e clique em **"Abrir"**. Você será orientado até o Portal de Gerenciamento do Azure, e sua página de informações do banco de dados também será aberta.

    -   Na página, você pode encontrar uma seção "Banco de dados". Ela lista o banco de dados que você criou. O nome de seu banco de dados deve ser **"Nome da sua Solução+Número Aleatório+db"** (por exemplo, "meuteste12345db").

	-	Clique em seu banco de dados e, no novo painel pop-up, você pode encontrar o nome do seu servidor de banco de dados na parte superior. O nome de seu servidor de banco de dados deve ser **"Nome de sua Solução + Número aleatório + 'database.windows.net,1433'"** (por exemplo, "mytest12345.database.windows.net,1433").

	-   O **nome de usuário** e a **senha** do banco de dados são iguais ao nome de usuário e à senha previamente registrados durante a implantação da solução.

2.	Atualizar a fonte de dados do arquivo de dados em lote (caminho frio) do Power BI
	-  Verifique se você instalou a versão mais recente do [Power BI desktop](https://powerbi.microsoft.com/desktop).

	-	Na pasta **"DemandForecastingDataGeneratorv1.0"** que você baixou, clique duas vezes no arquivo **'Power BI Template\\DemandForecastPowerBI.pbix'**. As visualizações inicias se baseiam em dados fictícios. **Observação:** se você vir uma mensagem de erro, verifique se instalou a versão mais recente do Power BI desktop

		Depois de abri-lo, na parte superior do arquivo, clique em **"Editar Consultas"**. Na janela pop-up, clique duas vezes em **"Origem"** no painel à direita. ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic1.png)

	-   Na janela pop-out, substitua **"Servidor"** e **"Banco de dados"** por seus próprios nomes de servidor e de banco de dados e clique em **"OK"**. Para o nome do servidor, especifique a porta 1433 (**NomeDaSuaSolução.database.windows.net, 1433**). Ignore as mensagens de aviso que aparecem na tela.

	-   Na próxima janela pop-out, você verá duas opções no painel esquerdo (**Windows** e **Banco de dados**). Clique em **"Banco de dados"**, preencha o **"Nome de usuário"** e a **"Senha"** (são o nome de usuário e a senha inseridos quando você implantou a solução e criou um banco de dados SQL do Azure). Em ***Selecione o nível ao qual aplicar essas configurações***, marque a opção de nível de banco de dados. E clique em **”Conectar”**.

	-   Depois de ser guiado de volta à página anterior, feche a janela. Será exibida uma mensagem - clique em **Aplicar**. Por fim, clique no botão **Salvar** para salvar as alterações. Seu arquivo do Power BI agora estabeleceu uma conexão com o servidor. Se suas visualizações estiverem vazias, limpe as seleções nas visualizações para visualizar todos os dados clicando no ícone de borracha no canto superior direito das legendas. Use o botão de atualização para refletir os novos dados nas visualizações. Inicialmente, você só verá os dados de propagação em suas visualizações, já que a atualização do Data Factory está agendada para a cada três horas. Após três horas, você verá novas previsões refletidas nas visualizações quando atualizar os dados.

3. (Opcional) Publique o painel de caminho frio no [Power BI online](http://www.powerbi.com/). Observe que esta etapa precisa de uma conta do Power BI (ou de uma conta do Office 365).

	-   Clique em **"Publicar"** e, depois de alguns segundos, será exibida uma janela mostrando "Êxito ao publicar no Power BI!", com uma marca de seleção verde. Clique no link abaixo "Abrir demoprediction.pbix abrir no Power BI". Para obter instruções detalhadas, confira [Publicar por meio do Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

	-   Para criar um novo painel: clique no sinal de **+** ao lado da seção **Painéis** no painel esquerdo. Insira o nome "Demonstração de previsão de demanda" para esse novo painel.

	-   Depois de abrir o relatório, clique em ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) para fixar todas as visualizações no painel. Para obter instruções detalhadas, confira [Fixar um bloco em um painel do Power BI por meio de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report). Vá para a página do painel e ajuste o tamanho e o local de suas visualizações e edite os títulos delas. Para obter instruções detalhadas sobre como editar seus blocos, confira [Editar um bloco — redimensionar, mover, renomear, fixar, excluir, adicionar hiperlink](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Veja um painel de exemplo com algumas visualizações de caminho frio fixadas nele.

		![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic7.png)

4. (Opcional) Agendar a atualização da fonte de dados.
	-	  Para agendar a atualização dos dados, passe o mouse sobre o conjunto de dados **EnergyBPI-Final**, clique em ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic3.png) e escolha **Agendar Atualização**. **Observação**: se for exibida uma mensagem de aviso, clique em **Editar Credenciais** e verifique se as credenciais de banco de dados são iguais às descritas na etapa 1

	![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic4.png)

	-   Expanda a seção **Agendar Atualização**. Ative "manter os dados atualizados".

	-   Agende a atualização com base em suas necessidades. Para saber mais, confira [Atualizar dados no Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).


## **Ferramentas de estimativa de custo**

As duas ferramentas a seguir estão disponíveis para ajudar você a entender melhor os custos totais envolvidos do Modelo de Solução de Previsão de demanda para energia em sua assinatura:

-   [Ferramenta Calculadora de Preço do Microsoft Azure (online)](https://azure.microsoft.com/pricing/calculator/)

-   [Ferramenta Calculadora de Preço do Microsoft Azure (área de trabalho)](http://www.microsoft.com/download/details.aspx?id=43376)

<!---HONumber=AcomDC_0406_2016-->