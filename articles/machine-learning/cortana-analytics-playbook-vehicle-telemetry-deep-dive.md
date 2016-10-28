<properties 
	pageTitle="Guia estratégico de solução da análise de telemetria do veículo: aprofunde-se na solução | Microsoft Azure" 
	description="Use os recursos do Cortana Intelligence para obter informações preditivas em tempo real sobre a integridade do veículo e hábitos de condução." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="bradsev" />


# Guia estratégico de solução da análise de telemetria do veículo: aprofunde-se na solução

Este **menu** tem links com as seções deste guia estratégico:

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Esta seção faz uma busca detalhada em cada um dos estágios descritos na Arquitetura da Solução com instruções e indicações para a personalização.

## Fontes de dados

A solução usa duas fontes de dados diferentes:

- **sinais de veículo simulados, conjunto de dados de diagnóstico** e
- **catálogo do veículo**

Um simulador de telemática do veículo é incluído como parte desta solução. Ele emite informações de diagnóstico e sinais correspondentes ao estado do veículo e ao padrão de condução em um determinado ponto no tempo. Clique em [Simulador de Telemática do Veículo](http://go.microsoft.com/fwlink/?LinkId=717075) para baixar a **Solução do Simulador de Telemática do Veículo do Visual Studio** para personalizações de acordo com suas necessidades. O catálogo do veículo contém um conjunto de dados de referência com um VIN para o mapeamento do modelo.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig2-vehicle-telematics-simulator.png)

*Figura 2 – Simulador de Telemática do Veículo*

Este é um conjunto de dados formatado em JSON que contém o esquema a seguir.

Coluna | Descrição | Valores 
 ------- | ----------- | --------- 
VIN | Número de Identificação do Veículo gerado aleatoriamente | É obtido em uma lista principal de 10.000 números de identificação do veículo gerados aleatoriamente.
Temperatura externa | A temperatura externa onde o veículo está sendo conduzido | Número gerado aleatoriamente de 0 a 100
Temperatura do motor | Temperatura do motor do veículo | Número gerado aleatoriamente de 0 a 500
Velocidade | A velocidade do motor na qual o veículo está sendo conduzido | Número gerado aleatoriamente de 0 a 100
Combustível | Nível de combustível do veículo | Número gerado aleatoriamente de 0 a 100 (indica a porcentagem do nível de combustível)
Óleo do Motor | Nível de óleo do motor do veículo | Número gerado aleatoriamente de 0 a 100 (indica a porcentagem do nível de óleo do motor)
Pressão do pneu | A pressão do pneu do veículo | Número gerado aleatoriamente de 0 a 50 (indica a porcentagem do nível de pressão do pneu)
Hodômetro | Leitura do hodômetro do veículo | Número gerado aleatoriamente de 0 a 200.000
Accelerator\_pedal\_position | Posição do pedal do acelerador do veículo | Número gerado aleatoriamente de 0 a 100 (indica a porcentagem do nível do acelerador)
Parking\_brake\_status | Indica se o veículo está estacionado ou não | Verdadeiro ou Falso
Headlamp\_status | Indica se o farol está ativado ou não | Verdadeiro ou Falso
Brake\_pedal\_status | Indica se o pedal do freio está pressionado ou não | Verdadeiro ou Falso
Transmission\_gear\_position | Posição da marcha do veículo | Estados: primeira, segunda, terceira, quarta, quinta, sexta, sétima, oitava
Ignition\_status | Indica se o veículo está em movimento ou parado | Verdadeiro ou Falso
Windshield\_wiper\_status | Indica se o limpador de para-brisas está ativado ou não | Verdadeiro ou Falso
ABS | Indica se o ABS está engatado ou não | Verdadeiro ou Falso
Timestamp | Carimbo de data/hora quando o ponto de dados é criado | Data
City | Local do veículo | Há quatro cidades nesta solução: Bellevue, Redmond, Sammamish, Seattle


O conjunto de dados de referência do modelo do veículo contém o VIN para o mapeamento do modelo.

VIN | Modelo |
--------------|------------------
FHL3O1SA4IEHB4WU1 | Automóvel Sedan |
8J0U8XCPRGW4Z3NQE | Híbrido |
WORG68Z2PLTNZDBI7 | Automóvel de três volumes |
JTHMYHQTEPP4WBMRN | Automóvel Sedan |
W9FTHG27LZN1YWO0Y | Híbrido |
MHTP9N792PHK08WJM | Automóvel de três volumes |
EI4QXI2AXVQQING4I | Automóvel Sedan |
5KKR2VB4WHQH97PF8 | Híbrido |
W9NSZ423XZHAONYXB | Automóvel de três volumes |
26WJSGHX4MA5ROHNL | Conversível |
GHLUB6ONKMOSI7E77 | Van |
9C2RHVRVLMEJDBXLP | Carro compacto |
BRNHVMZOUJ6EOCP32 | SUV pequeno |
VCYVW0WUZNBTM594J | Carro esportivo |
HNVCE6YFZSA5M82NY | SUV médio |
4R30FOR7NUOBL05GJ | Van |
WYNIIY42VKV6OQS1J | SUV grande |
8Y5QKG27QET1RBK7I | SUV grande |
DF6OX2WSRA6511BVG | Cupê |
Z2EOZWZBXAEW3E60T | Automóvel Sedan |
M4TV6IEALD5QDS3IR | Híbrido |
VHRA1Y2TGTA84F00H | Automóvel de três volumes |
R0JAUHT1L1R3BIKI0 | Automóvel Sedan |
9230C202Z60XX84AU | Híbrido |
T8DNDN5UDCWL7M72H | Automóvel de três volumes |
4WPYRUZII5YV7YA42 | Automóvel Sedan |
D1ZVY26UV2BFGHZNO | Híbrido |
XUF99EW9OIQOMV7Q7 | Automóvel de três volumes
8OMCL3LGI7XNCC21U | Conversível |
……. | |


### Para gerar dados simulados
1.	Para baixar o pacote do simulador de dados, clique na seta no canto superior direito no nó Simulador de Telemática do Veículo. Salve e extraia os arquivos localmente em seu computador. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig3-vehicle-telemetry-blueprint.png) *Figura 3 – Projeto da Solução de Análise da Telemetria do Veículo*

2.	Em seu computador local, vá para a pasta onde você extraiu o pacote Simulador de Telemática do Veículo. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-simulator-folder.png) *Figura 4 – Pasta Simulator de Telemática do Veículo*

3.	Execute o aplicativo **CarEventGenerator.exe**.

### Referências

[Solução Vehicle Telematics Simulator do Visual Studio](http://go.microsoft.com/fwlink/?LinkId=717075)

[Hub de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/)

[Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)


## Ingestão
As combinações dos Hubs de Eventos do Azure, Stream Analytics e Data Factory são otimizadas para receber os sinais do veículo, os eventos de diagnóstico e a análise em tempo real e em lote. Todos esses componentes são criados e configurados como parte da implantação da solução.

### Análise em tempo real
Os eventos gerados pelo Simulator de Telemática do Veículo são publicados para o Hub de Eventos usando o SDK do Hub de Eventos. O trabalho do Stream Analytics recebe esses eventos do Hub de Eventos e processa os dados em tempo real para analisar a integridade do veículo.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-event-hub-dashboard.png)

*Figura 5 - Painel do Hub de Eventos*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-processing-data.png)

*Figura 6 - Dados de processamento do trabalho do stream analytics*

O trabalho do stream analytics;

- consome dados do Hub de Eventos
- executa uma junção com os dados de referência para mapear o veículo VIN ao modelo correspondente
- persisti-los no armazenamento de blobs do Azure para análise avançada em lote.

A consulta do stream analytics a seguir é usada para manter os dados no armazenamento de blobs do Azure.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png)

*Figura 7 - Consulta do trabalho do stream analytics para a ingestão de dados*

### Análise do lote
Também podemos gerar um volume adicional de sinais simulados do veículo e um conjunto de dados de diagnóstico para fazer uma análise de lote mais avançada. Isto é necessário para garantir um bom volume de dados representativos para o processamento em lote. Para essa finalidade, estamos usando um pipeline chamado “PrepareSampleDataPipeline” no fluxo de trabalho do Azure Data Factory para gerar o valor de um ano dos sinais simulados do veículo e do conjunto de dados de diagnóstico. Clique em [Atividade personalizada do Data Factory](http://go.microsoft.com/fwlink/?LinkId=717077) para baixar a solução da atividade DotNet personalizada do Data Factory do Visual Studio para as personalizações baseadas em suas necessidades.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-for-batch-processing.png)

*Figura 8 - Preparar os dados de exemplo para o fluxo de trabalho do processamento em lote*

O pipeline consiste em uma Atividade .Net do ADF personalizada, mostrada aqui:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline.png)

*Figura 9 - PrepareSampleDataPipeline*

Depois que o pipeline for executado com êxito e o conjunto de dados “RawCarEventsTable” estiver marcado como “Pronto”, o valor de um ano dos sinais simulados do veículo e dos dados de diagnóstico será produzido. Você verá a pasta e o arquivo a seguir criados em sua conta de armazenamento no contêiner “connectedcar”:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-prepare-sample-data-pipeline-output.png)

*Figura 10 - PrepareSampleDataPipeline Output*

### Referências

[SDK do Hub de Eventos do Azure para ingestão de fluxo](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

[Recursos de movimentação de dados do Azure Data Factory](../data-factory/data-factory-data-movement-activities.md) [Atividade DotNet do Azure Data Factory](../data-factory/data-factory-use-custom-activities.md)

[Solução do Visual Studio de atividade DotNet do Azure Data Factory para preparação de dados de exemplo](http://go.microsoft.com/fwlink/?LinkId=717077)


## Particionar o conjunto de dados

Os sinais e o conjunto de dados de diagnóstico do veículo semi-estruturados brutos são particionados na etapa de preparação de dados em um formato ANO/MÊS. Esse particionamento promove a consulta mais eficiente e dimensionável de armazenamento a longo prazo, permitindo o failover de uma conta de blob para a próxima à medida que a primeira conta é preenchida.

>[AZURE.NOTE] Esta etapa na solução é aplicável somente ao processamento em lote.

Entrada e saída de gerenciamento de dados:

- Os **dados de saída** (rotulados como *PartitionedCarEventsTable*) devem ser mantidos por um longo período de tempo como a forma básica/“mais bruta” de dados no “Data Lake” do cliente.
- Os **dados de entrada** desse pipeline normalmente seriam descartados pois os dados de saída têm total fidelidade com a entrada - são armazenados (particionados) melhor para um uso posterior.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-workflow.png)

*Figura 11 – Fluxo de trabalho dos Eventos do Carro da Partição*

Os dados brutos são particionados usando uma atividade do Hive no HDInsight em “PartitionCarEventsPipeline”. Os dados de exemplo gerados na etapa 1 para um ano são particionados por ANO/MÊS. As partições são usadas para gerar o sinais e os dados de diagnóstico do veículo para cada mês (total de 12 partições) de um ano.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partition-car-events-pipeline.png)

*Figura 12 - PartitionCarEventsPipeline*

O script do Hive a seguir, chamado “partitioncarevents.hql”, é usado para particionar e está localizado na pasta “\\demo\\src\\connectedcar\\scripts” do zip baixado.


	SET hive.exec.dynamic.partition=true;
	SET hive.exec.dynamic.partition.mode = nonstrict;
	set hive.cli.print.header=true;

	DROP TABLE IF EXISTS RawCarEvents; 
	CREATE EXTERNAL TABLE RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
	) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

	DROP TABLE IF EXISTS Stage_RawCarEvents; 
	CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string,
				YearNo 							int,
				MonthNo 						int) 
	ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

	INSERT OVERWRITE TABLE Stage_RawCarEvents
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		Year(gendate),
		Month(gendate)

	FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

	INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		YearNo,
		MonthNo
	FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

*Figura 13 - Script do Hive PartitionConnectedCarEvents*

Após a execução bem-sucedida do pipeline, você verá as seguintes partições geradas em sua conta de armazenamento sob o contêiner “connectedcar”.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-partitioned-output.png)

*Figura 14 - Saída Particionada*

Os dados agora estão otimizados, são mais gerenciáveis e estão prontos para um processamento adicional para obter ideias avançadas de lote.

## Análise de Dados

Nesta seção, você vê como combinar o Azure Stream Analytics, o Aprendizado de Máquina do Azure, o Azure Data Factory e o Azure HDInsight para fazer análises avançadas sobre a integridade do veículo e os hábitos de condução. Há três subseções aqui:

1.	**Aprendizado de Máquina**: esta subseção contém informações sobre o teste de detecção de anomalias que usamos nesta solução para prever os veículos que exigem manutenção e veículos que exigem recalls devido a problemas de segurança.
2.	**Análise em tempo real**: esta subseção contém informações sobre a análise em tempo real usando a Linguagem de Consulta do Stream Analytics e a operação do teste de aprendizado de máquina em tempo real usando um aplicativo personalizado.
3.	**Análise do Lote**: esta subseção contém informações sobre a transformação e o processamento dos dados em lote usando o Azure HDInsight e o Aprendizado de Máquina do Azure operados pelo Azure Data Factory.

### Aprendizado de Máquina

Nosso objetivo aqui é prever os veículos que exigem manutenção ou recall com base em determinadas estatísticas de integridade. Podemos fazer as seguintes suposições

- Se uma das três condições a seguir for verdadeira, os veículos exigirão **manutenção**:
	- A pressão do pneu está baixa
	- O nível de óleo do motor está baixo
	- A temperatura do motor está alta

- Se uma das seguintes condições for verdadeira, os veículos poderão ter um **problema de segurança** e exigir um **recall**:
	- A temperatura do motor é alta, mas a temperatura externa é baixa
	- A temperatura do motor é baixa, mas a temperatura externa é alta

Com base nos requisitos anteriores, criamos dois modelos separados para detectar anomalias, um para a detecção de manutenção do veículo e outro para a detecção de recall do veículo. Em ambos os modelos, o algoritmo interno Análise do Componente Principal (PCA) é usado para a detecção de anomalias.

**Modelo de detecção de manutenção**

Se um dos três indicadores - pressão do pneu, óleo do motor ou temperatura do motor - atender à sua respectiva condição, o modelo de detecção de manutenção relatará uma anomalia. Como resultado, só precisamos considerar essas três variáveis para criar o modelo. Em nosso teste no Aprendizado de Máquina do Azure, primeiro usamos um módulo **Selecionar colunas no conjunto de dados** para extrair essas três variáveis. Em seguida, usamos o módulo de detecção de anomalias com base no PCA para criar o modelo de detecção de anomalias.

A Análise do Componente Principal (PCA) é uma técnica estabelecida no aprendizado de máquina que pode ser aplicada na seleção de recursos, classificação e detecção de anomalias. O PCA converte um conjunto de casos contendo variáveis possivelmente correlacionadas em um conjunto de valores chamado de componentes principais. A ideia-chave da modelagem baseada no PCA é projetar os dados em um espaço dimensional inferior para que os recursos e as anomalias possam ser identificados com mais facilidade.
 
Para cada nova entrada no modelo de detecção, o detector de anomalias primeiro calcula sua projeção em vetores próprios e, em seguida, calcula o erro de reconstrução normalizado. Esse erro normalizado é a pontuação da anomalia. Quanto maior o erro, mais anormal é a instância.

No problema de detecção da manutenção, cada registro pode ser considerado como um ponto em um espaço tridimensional definido pelas coordenadas da pressão do pneu, óleo do motor e temperatura do motor. Para capturar essas anomalias, podemos projetar os dados originais no espaço tridimensional em um espaço bidimensional usando o PCA. Assim, definimos o parâmetro Número de componentes a usar no PCA como sendo 2. Esse parâmetro desempenha um papel importante na aplicação da detecção de anomalias com base no PCA. Depois de projetar os dados usando o PCA, podemos identificar essas anomalias mais facilmente.

**Modelo de detecção de anomalias de recall** No modelo de detecção de anomalias de recall, usamos as Colunas selecionadas no Conjunto de Dados e os módulos de detecção de anomalias com base no PCA de maneira semelhante. Especificamente, primeiro extraímos três variáveis - temperatura do motor, temperatura externa e velocidade - usando o módulo **Colunas selecionadas no Conjunto de Dados**. Também incluímos a variável de velocidade uma vez que a temperatura do motor normalmente está correlacionada à velocidade. Em seguida, usamos o módulo de detecção de anomalias com base no PCA para projetar os dados do espaço tridimensional em um espaço bidimensional. Os critérios de recall são atendidos, portanto, o veículo requer um recall quando a temperatura do motor e a temperatura externa são correlacionadas de modo alto e negativo. Usando o algoritmo de detecção de anomalias com base no PCA, podemos capturar as anomalias depois de executar o PCA.

Durante o treinamento de qualquer modelo, precisamos usar dados normais que não exijam manutenção nem recall como os dados de entrada para treinar o modelo de detecção de anomalias com base no PCA. No teste de pontuação, usamos o modelo de detecção de anomalias treinado para detectar se o veículo requer ou não manutenção ou recall.


### Análise em tempo real

A seguinte Consulta SQL do Stream Analytics é usada para obter a média de todos os parâmetros de veículo importantes, como a velocidade do veículo, o nível de combustível, a temperatura do motor, a leitura do hodômetro, a pressão do pneu, o nível de óleo do motor e outros. As médias são usadas para detectar anomalias, emitir alertas e determinar as condições de integridade geral dos veículos operados na região específica e correlacionam dados demográficos.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig15-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Figura 15 – Consulta do stream analytics para o processamento em tempo real

Todas as médias são calculadas em uma TumblingWindow de três segundos. Estamos usando uma TubmlingWindow neste caso, pois exigimos intervalos de tempo que não se sobrepõem e são contínuos.

Para saber mais sobre todas as funcionalidades de “Janelas” no Azure Stream Analytics, clique em [Janelas (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

**Previsão em tempo real**

Um aplicativo é incluído como parte da solução para operar o modelo de aprendizado de máquina em tempo real. Esse aplicativo, chamado "RealTimeDashboardApp", é criado e configurado como parte da implantação da solução. O aplicativo faz o seguinte:

1.	Escuta em uma instância de Hub de eventos onde o Stream Analytics está publicando os eventos em um padrão contínuo. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-stream-analytics-query-for-publishing.png)*Figura 16 – Consulta do Stream Analytics para publicar os dados em uma saída de instância do Hub de Eventos*

2.	Para cada evento que esse aplicativo recebe:

	- Processa os dados usando o ponto de extremidade da Pontuação de Solicitação-Resposta do Aprendizado de Máquina (RRS). O ponto de extremidade RRS é publicado automaticamente como parte da implantação.
	- A saída do RRS é publicada em um conjunto de dados do PowerBI usando as APIs de envio por push.

Esse padrão também é aplicável em situações nas quais você deseja integrar um aplicativo da Linha de Negócios (LoB) com o fluxo de análise em tempo real para cenários como alertas, notificações e mensagens.

Clique em [Baixar RealtimeDashboardApp](http://go.microsoft.com/fwlink/?LinkId=717078) para baixar a solução RealtimeDashboardApp do Visual Studio para as personalizações.

**Para executar o Aplicativo do Painel em Tempo Real**

1.	Clique no nó do PowerBI na exibição de diagrama e clique no link Baixar Aplicativo do Painel em Tempo Real no painel de propriedades. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17-vehicle-telematics-powerbi-dashboard-setup.png) *Figura 17 – Instruções de instalação do painel do PowerBI*
2.	Extrair e salvar localmente ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-realtimedashboardapp-folder.png) *Figura 18 – Pasta RealtimeDashboardApp*
3.	Executar o aplicativo RealtimeDashboardApp.exe
4.	Fornecer credenciais válidas do PowerBI, entrar e clicar em Aceitar ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png)

*Figura 19 – RealtimeDashboardApp: entrar no PowerBI*

>[AZURE.NOTE] Se você deseja liberar o conjunto de dados do Power BI, execute o RealtimeDashboardApp com o parâmetro “flushdata”:

	RealtimeDashboardApp.exe -flushdata

### Análise do lote

O objetivo aqui é demonstrar como a Contoso Motors utiliza os recursos de computação do Azure para aproveitar o Big Data para obter informações avançadas sobre o padrão de condução, comportamento de uso e integridade do veículo. Isso possibilita:

- Melhorar a experiência do cliente e torná-la mais barata, fornecendo informações sobre os hábitos de condução e os comportamentos de condução eficientes do combustível
- Aprender proativamente sobre os clientes e os padrões de condução para controlar as decisões de negócios e fornecer os melhores produtos e serviços

Nesta solução, pretendemos as métricas a seguir:

1.	**Comportamento de condução agressiva**: identifica a tendência de modelos, locais, condições de condução e época do ano para aprofundar-se em padrões de condução agressiva. A Contoso Motors pode usar essas informações para campanhas de marketing, orientando novos recursos personalizados e seguro baseado em uso.
2.	**Comportamento de condução com economia de combustível**: identifica a tendência de modelos, locais, condições de condução e época do ano para aprofundar-se em padrões de condução para a eficiência de combustível. A Contoso Motors pode usar essas informações para campanhas de marketing orientando novos recursos e relatórios proativos para que os motoristas adquiram hábitos de condução amigáveis ao ambiente e econômicas.
3.	**Modelos para recall**: identifica os modelos que exigem recalls operando o teste de aprendizado de máquina para a detecção de anomalias

Examinemos os detalhes de cada uma dessas métricas,


**Padrão de condução agressiva**

Os sinais particionados do veículo e os dados de diagnóstico são processados no pipeline chamado “AggresiveDrivingPatternPipeline” usando o Hive para determinar modelos, local, veículo, condições de condução e outros parâmetros que exibam um padrão de condução agressiva.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-aggressive-driving-pattern.png) *Figura 20 – Fluxo de trabalho do padrão de condução agressiva*

O script do Hive chamado “aggresivedriving.hql”, usado para analisar o padrão da condição de condução agressiva, está localizado na pasta “\\demo\\src\\connectedcar\\scripts” do zip baixado.

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS CarEventsAggresive; 
	CREATE EXTERNAL TABLE CarEventsAggresive
	(
               	vin 						string, 
				model						string,
                timestamp					string,
				city						string,
				speed 			 			string,
				transmission_gear_position	string,
				brake_pedal_status			string,
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



	INSERT OVERWRITE TABLE CarEventsAggresive
	select
	vin,
	model,
	timestamp,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'

*Figura 21 – Consulta do Hive do padrão de condução agressiva*

Ele usa a combinação da posição da marcha do veículo, do status do pedal de freio e da velocidade para detectar o comportamento de condução negligente/agressiva com base no padrão de frenagem em alta velocidade.

Após a execução bem-sucedida do pipeline, você verá as seguintes partições geradas em sua conta de armazenamento sob o contêiner “connectedcar”.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aggressive-driving-pattern-output.png)

*Figura 22 – Saída do AggressiveDrivingPatternPipeline*


**Padrão de condução para a eficiência do combustível**

Os sinais de veículo particionados e os dados de diagnóstico são processados no pipeline chamado "FuelEfficientDrivingPatternPipeline". O Hive é usado para determinar modelos, local, veículo, condições de condução e outras propriedades que apresentam um padrão de condução para a eficiência de combustível.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-fuel-efficient-driving-pattern.png)

*Figura 23 – Fluxo de trabalho do padrão de condução para a eficiência do combustível*

O script do Hive chamado “fuelefficientdriving.hql”, usado para analisar o padrão da condição de condução agressiva, está localizado na pasta “\\demo\\src\\connectedcar\\scripts” do zip baixado.

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS FuelEfficientDriving; 
	CREATE EXTERNAL TABLE FuelEfficientDriving
	(
               	vin 						string, 
				model						string,
               	city						string,
				speed 			 			string,
				transmission_gear_position	string,                
				brake_pedal_status			string,            
				accelerator_pedal_position	string,                             
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



	INSERT OVERWRITE TABLE FuelEfficientDriving
	select
	vin,
	model,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	accelerator_pedal_position,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


*Figura 24 – Consulta do Hive do padrão de condução para a eficiência do combustível*

Ele usa a combinação da posição da marcha do veículo, do status do pedal de freio, da velocidade e da posição do pedal do acelerador para detectar o comportamento de condução voltado para a eficiência de combustível com base nos padrões de aceleração, frenagem e velocidade.

Após a execução bem-sucedida do pipeline, você verá as seguintes partições geradas em sua conta de armazenamento sob o contêiner “connectedcar”.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-fuel-efficient-driving-pattern-output.png)

*Figura 25 – Saída do FuelEfficientDrivingPatternPipeline*


**Previsões do Recall**

O teste do aprendizado de máquina é fornecido e publicado como um serviço da Web como parte da implantação da solução. O ponto de extremidade da pontuação em lote é utilizado neste fluxo de trabalho, registrado como um serviço vinculado do data factory e operado usando a atividade de pontuação em lote do data factory.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-machine-learning-endpoint.png)

*Figura 26 – Ponto de extremidade do aprendizado de máquina registrado como um serviço vinculado no data factory*

O serviço vinculado registrado é usado no DetectAnomalyPipeline para pontuar os dados usando o modelo de detecção de anomalias.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-aml-batch-scoring.png)

*Figura 27 – Atividade de Pontuação em Lote do Aprendizado de Máquina do Azure no data factory*

Há algumas etapas executadas nesse pipeline para a preparação dos dados para que ele possa ser operado com o serviço Web de pontuação em lote.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-pipeline-predicting-recalls.png)

*Figura 28 – DetectAnomalyPipeline para prever os veículos que exigem recalls*

Quando a pontuação for concluída, uma atividade do HDInsight será usada para processar e agregar os dados que são categorizados como anomalias pelo modelo com uma pontuação de probabilidade de 0,60 ou superior.

	DROP TABLE IF EXISTS CarEventsAnomaly; 
	CREATE EXTERNAL TABLE CarEventsAnomaly 
	(
            	vin							string,
				model						string,
				gendate						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
				fuel						string,
				engineoil					string,
				tirepressure				string,
				odometer					string,
				city						string,
				accelerator_pedal_position	string,
				parking_brake_status		string,
				headlamp_status				string,
				brake_pedal_status			string,
				transmission_gear_position	string,
				ignition_status				string,
				windshield_wiper_status		string,
				abs  						string,
				maintenanceLabel			string,
				maintenanceProbability		string,
				RecallLabel					string,
				RecallProbability			string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

	DROP TABLE IF EXISTS RecallModel; 
	CREATE EXTERNAL TABLE RecallModel 
	(

				vin							string,
				model						string,
				city						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
            	Year						string,
				Month						string				
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

	INSERT OVERWRITE TABLE RecallModel
	select
	vin,
	model,
	city,
	outsidetemperature,
	enginetemperature,
	speed,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from CarEventsAnomaly
	where RecallLabel = '1' AND RecallProbability >= '0.60'


Após a execução bem-sucedida do pipeline, você verá as seguintes partições geradas em sua conta de armazenamento sob o contêiner “connectedcar”.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-detect-anamoly-pipeline-output.png)

*Figura 30 – Figure 30 – Saída do DetectAnomalyPipeline*


## Publicar

### Análise em tempo real

Uma das consultas no trabalho do stream analytics publica os eventos em uma instância do Hub de Eventos da saída.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig31-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*Figura 31 – O trabalho do stream analytics publica em uma instância do Hub de Eventos da saída*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig32-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*Figura 32 – A consulta do stream analytics para publicar na instância do Hub de Eventos da saída*

Esse fluxo de eventos é consumido pelo RealTimeDashboardApp incluído na solução. Este aplicativo usa o serviço Web de Solicitação-Resposta do Aprendizado de Máquina para a pontuação em tempo real e publica os dados resultantes em um conjunto de dados do PowerBI para o consumo.

### Análise do lote

Os resultados do lote e do processamento em tempo real são publicados para as tabelas do Banco de Dados SQL do Azure para o consumo. O SQL Server do Azure, o Banco de Dados e as tabelas são criados automaticamente como parte do script de instalação.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig33-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*Figura 33 – Copiar os resultados do processamento em batch para o fluxo de trabalho do data mart*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig34-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*Figura 34 – O trabalho do stream analytics publica no data mart*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig35-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*Figura 35 – Configuração do data mart no trabalho do stream analytics*


## Consumir

O PowerBI fornece essa solução a um painel avançado para os dados em tempo real e as visualizações da análise preditiva.

Clique aqui para obter instruções detalhadas sobre como configurar os relatórios do PowerBI e o painel. O painel final tem esta aparência:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig36-vehicle-telematics-powerbi-dashboard.png)

*Figura 36 - Painel do PowerBI*

## Resumo

Este documento contém um detalhamento da Solução de Análise da Telemetria do Veículo. Isto apresenta um padrão de arquitetura lambda para a análise em tempo real e em lote com previsões e ações. Esse padrão se aplica a uma ampla variedade de casos de uso que exigem as análises de afunilamento (em tempo real) e de ampliação (em lote).

<!---HONumber=AcomDC_0914_2016-->