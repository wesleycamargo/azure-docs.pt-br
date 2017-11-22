---
title: "Aprofundamento sobre como prever a integridade do veículo e os hábitos de condução – Azure | Microsoft Docs"
description: "Use os recursos do Cortana Intelligence para obter informações preditivas em tempo real sobre a integridade do veículo e hábitos de condução."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: cdde0c8dc2fd1189970c0782769a609ca8142372
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Guia estratégico da Solução de Análise de Telemetria do Veículo: aprofundamento na solução
Este menu fornece links para as seções deste guia estratégico: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Este documento faz uma busca detalhada em cada um dos estágios descritos na arquitetura da solução. Instruções e ponteiros para personalização são incluídos. 

## <a name="data-sources"></a>Fontes de dados
A solução usa duas fontes de dados diferentes:

* Sinais de veículo simulados e conjunto de dados de diagnóstico
* Catálogo de veículos

Um simulador de telemática do veículo é incluído como parte desta solução, conforme mostrado na captura de tela a seguir. Ele emite informações de diagnóstico e sinais que correspondem ao estado do veículo e ao padrão de condução em determinado ponto no tempo. Para baixar a Solução de Simulador de Telemática do Veículo do Visual Studio para fazer personalizações de acordo com suas necessidades, acesse a página da Web [Simulador de telemática do veículo](http://go.microsoft.com/fwlink/?LinkId=717075). O catálogo de veículos contém um conjunto de dados de referência que mapeia os VINs (números de identificação do veículo) para os modelos.

![Simulador de telemática do veículo](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


Este conjunto de dados formatado em JSON contém o esquema a seguir.

| Coluna | Descrição | Valores |
| --- | --- | --- |
| VIN |VIN gerado aleatoriamente |Obtido de uma lista mestre de 10.000 VINs gerados aleatoriamente |
| Temperatura externa |A temperatura externa onde o veículo está sendo conduzido |Número gerado aleatoriamente de 0 a 100 |
| Temperatura do motor |Temperatura do motor do veículo |Número gerado aleatoriamente de 0 a 500 |
| Velocidade |A velocidade do motor na qual o veículo está sendo conduzido |Número gerado aleatoriamente de 0 a 100 |
| Combustível |Nível de combustível do veículo |Número gerado aleatoriamente de 0 a 100 (indica o percentual do nível de combustível) |
| Óleo do Motor |Nível de óleo do motor do veículo |Número gerado aleatoriamente de 0 a 100 (indica o percentual do nível de óleo do motor) |
| Pressão do pneu |A pressão do pneu do veículo |Número gerado aleatoriamente de 0 a 50 (indica o percentual do nível de pressão do pneu) |
| Hodômetro |Leitura do hodômetro do veículo |Número gerado aleatoriamente de 0 a 200.000 |
| Accelerator_pedal_position |Posição do pedal do acelerador do veículo |Número gerado aleatoriamente de 0 a 100 (indica o percentual do nível do acelerador) |
| Parking_brake_status |Indica se o veículo está estacionado ou não |Verdadeiro ou Falso |
| Headlamp_status |Indica se o farol dianteiro está ligado ou não |Verdadeiro ou Falso |
| Brake_pedal_status |Indica se o pedal do freio está pressionado ou não |Verdadeiro ou Falso |
| Transmission_gear_position |Posição da marcha do veículo |Estados: primeira, segunda, terceira, quarta, quinta, sexta, sétima, oitava |
| Ignition_status |Indica se o veículo está em movimento ou parado |Verdadeiro ou Falso |
| Windshield_wiper_status |Indica se o limpador de para-brisas está ligado ou não |Verdadeiro ou Falso |
| ABS |Indica se o ABS está engatado ou não |Verdadeiro ou Falso |
| Timestamp |Carimbo de data/hora quando o ponto de dados é criado |Data |
| City |Local do veículo |Há quatro cidades nesta solução: Bellevue, Redmond, Sammamish e Seattle |

O conjunto de dados de referência de modelos de veículo mapeia VINs para os modelos. 

| VIN | Modelo |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |Automóvel Sedan |
| 8J0U8XCPRGW4Z3NQE |Híbrido |
| WORG68Z2PLTNZDBI7 |Automóvel de três volumes |
| JTHMYHQTEPP4WBMRN |Automóvel Sedan |
| W9FTHG27LZN1YWO0Y |Híbrido |
| MHTP9N792PHK08WJM |Automóvel de três volumes |
| EI4QXI2AXVQQING4I |Automóvel Sedan |
| 5KKR2VB4WHQH97PF8 |Híbrido |
| W9NSZ423XZHAONYXB |Automóvel de três volumes |
| 26WJSGHX4MA5ROHNL |Conversível |
| GHLUB6ONKMOSI7E77 |Van |
| 9C2RHVRVLMEJDBXLP |Carro compacto |
| BRNHVMZOUJ6EOCP32 |SUV pequeno |
| VCYVW0WUZNBTM594J |Carro esportivo |
| HNVCE6YFZSA5M82NY |SUV médio |
| 4R30FOR7NUOBL05GJ |Van |
| WYNIIY42VKV6OQS1J |SUV grande |
| 8Y5QKG27QET1RBK7I |SUV grande |
| DF6OX2WSRA6511BVG |Cupê |
| Z2EOZWZBXAEW3E60T |Automóvel Sedan |
| M4TV6IEALD5QDS3IR |Híbrido |
| VHRA1Y2TGTA84F00H |Automóvel de três volumes |
| R0JAUHT1L1R3BIKI0 |Automóvel Sedan |
| 9230C202Z60XX84AU |Híbrido |
| T8DNDN5UDCWL7M72H |Automóvel de três volumes |
| 4WPYRUZII5YV7YA42 |Automóvel Sedan |
| D1ZVY26UV2BFGHZNO |Híbrido |
| XUF99EW9OIQOMV7Q7 |Automóvel de três volumes |
| 8OMCL3LGI7XNCC21U |Conversível |
| ……. | |

## <a name="ingestion"></a>Ingestão
As combinações dos Hubs de Eventos do Azure, do Azure Stream Analytics e do Azure Data Factory são usadas para ingerir os sinais de veículo, os eventos de diagnóstico e a análise em tempo real e em lote. Todos esses componentes são criados e configurados como parte da implantação da solução. 

### <a name="real-time-analysis"></a>Análise em tempo real
Os eventos gerados pelo simulador de telemática do veículo são publicados no hub de eventos usando o SDK do hub de eventos.  

![Painel do Hub de Eventos](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

O trabalho do Stream Analytics ingere esses eventos do hub de eventos e processa os dados em tempo real para analisar a integridade do veículo.

![Dados de processamento do trabalho do Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


O trabalho do Stream Analytics:

* Ingere dados do hub de eventos.
* Executa uma junção com os dados de referência para mapear o VIN do veículo para o modelo correspondente. 
* Persiste-os no armazenamento de Blobs do Azure para uma análise em lote avançada. 

A seguinte consulta do Stream Analytics é usada para persistir os dados no armazenamento de Blobs: 

![Consulta do trabalho do Stream Analytics para a ingestão de dados](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>Análise do lote
Também é gerado um volume adicional de sinais de veículo simulados e um conjunto de dados de diagnóstico para uma análise em lote mais avançada. Esse volume adicional é necessário para garantir um bom volume de dados representativo para o processamento em lotes. Para essa finalidade, PrepareSampleDataPipeline é usado no fluxo de trabalho do Data Factory para gerar sinais de veículo simulados e um conjunto de dados de diagnóstico de um ano. Para baixar a solução da atividade .NET personalizada do Data Factory do Visual Studio para fazer personalizações de acordo com suas necessidades, acesse a página da Web [Atividade personalizada do Data Factory](http://go.microsoft.com/fwlink/?LinkId=717077). 

Este fluxo de trabalho mostra dados de exemplo preparados o processamento em lotes.

![Dados de exemplo preparados para o fluxo de trabalho do processamento em lotes](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


O pipeline consiste em uma atividade .NET personalizada do Data Factory.

![PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

Após a execução bem-sucedida do pipeline e o conjunto de dados RawCarEventsTable estiver marcado como “Pronto”, sinais de veículo simulados e dados de diagnóstico de um ano serão produzidos. Você verá a seguinte pasta e arquivo criados em sua conta de armazenamento no contêiner connectedcar:

![Saída de PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>Particionar o conjunto de dados
Na etapa de preparação de dados, os sinais de veículo semi-estruturados brutos e o conjunto de dados de diagnóstico são particionados em um formato ANO/MÊS. Esse particionamento promove uma consulta mais eficiente e um armazenamento de longo prazo escalonável permitindo o failover. Por exemplo, conforme a primeira conta de blob é preenchida, ela faz failover para a conta seguinte. 

>[!NOTE] 
>Esta etapa da solução aplica-se somente ao processamento em lotes.

Gerenciamento de dados de entrada e saída:

* Os **dados de saída** (rotulados como PartitionedCarEventsTable) são mantidos por um longo período como a forma básica/“mais bruta” de dados no data lake do cliente. 
* Os **dados de entrada** desse pipeline são normalmente descartados porque os dados de saída têm total fidelidade à entrada. Eles são armazenados (particionados) de uma forma melhor para uso posterior.

O fluxo de trabalho de eventos de partição de carros.

![Fluxo de trabalho de eventos de partição de carros](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


Os dados brutos são particionados usando uma atividade do Hive do Azure HDInsight em PartitionCarEventsPipeline, conforme mostrado na captura de tela a seguir. Os dados de exemplo gerados para um ano na etapa de preparação de dados são particionados por ANO/MÊS. As partições são usadas para gerar sinais de veículo e dados de diagnóstico de um ano para cada mês (total de 12 partições). 

![Atividade de PartitionCarEventsPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


**Script do Hive de PartitionConnectedCarEvents**

O script do Hive partitioncarevents.hql é usado para o particionamento. Ele está localizado na pasta \demo\src\connectedcar\scripts do arquivo zip baixado. 
    
    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string,
                YearNo                             int,
                MonthNo                         int) 
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

Após a execução bem-sucedida do pipeline, você verá as seguintes partições geradas em sua conta de armazenamento no contêiner connectedcar:

![Saída particionada](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

Os dados agora estão otimizados, são mais gerenciáveis e estão prontos para um processamento adicional, a fim de obter insights avançados em lote. 

## <a name="data-analysis"></a>Análise de dados
Nesta seção, você vê como combinar o Stream Analytics, o Azure Machine Learning, o Data Factory e o HDInsight para fazer análises avançadas sobre a integridade do veículo e os hábitos de condução.

### <a name="machine-learning"></a>Aprendizado de máquina
A meta aqui é prever os veículos que exigem manutenção ou recall com base em determinadas estatísticas de integridade, de acordo com as seguintes suposições:

* Se uma das seguintes três condições for verdadeira, os veículos exigirão manutenção:
  
  * A pressão do pneu está baixa.
  * O nível de óleo do motor está baixo.
  * A temperatura do motor está alta.

* Se uma das seguintes condições for verdadeira, os veículos poderão ter um problema de segurança e exigir um recall:
  
  * A temperatura do motor é alta, mas a temperatura externa é baixa.
  * A temperatura do motor é baixa, mas a temperatura externa é alta.

De acordo com os requisitos anteriores, dois modelos separados detectam anomalias. Um modelo refere-se à detecção de manutenção do veículo e outro refere-se à detecção de recall do veículo. Em ambos os modelos, o algoritmo interno de PCA (análise do componente principal) é usado para a detecção de anomalias. 

#### <a name="maintenance-detection-model"></a>**Modelo de detecção de manutenção**

Se um dos três indicadores – pressão do pneu, óleo do motor ou temperatura do motor – atender à sua respectiva condição, o modelo de detecção de manutenção relatará uma anomalia. Como resultado, apenas essas três variáveis precisam ser consideradas na criação do modelo. No experimento do aprendizado de máquina, o módulo **Selecionar Colunas no Conjunto de Dados** é usado para extrair essas três variáveis. Em seguida, o módulo de detecção de anomalias baseado em PCA é usado para criar o modelo de detecção de anomalias. 

O PCA é uma técnica estabelecida no aprendizado de máquina que pode ser aplicado na seleção de recursos, classificação e detecção de anomalias. O PCA converte um conjunto de casos que contém variáveis possivelmente correlacionadas em um conjunto de valores chamado de componentes principais. A ideia-chave da modelagem baseada em PCA é projetar os dados em um espaço dimensional inferior para identificar os recursos e as anomalias com mais facilidade.

Para cada nova entrada no modelo de detecção, o detector de anomalias primeiro calcula sua projeção em vetores próprios. Em seguida, ele calcula o erro de reconstrução normalizado. Esse erro normalizado é a pontuação de anomalias: quanto maior o erro, mais anômala é a instância. 

No problema de detecção de manutenção, cada registro é considerado como um ponto em um espaço tridimensional definido pelas coordenadas da pressão do pneu, óleo do motor e temperatura do motor. Para capturar essas anomalias, o PCA é usado para projetar os dados originais do espaço tridimensional em um espaço bidimensional. Assim, o número de parâmetro de componentes a ser usado no PCA é definido como dois. Esse parâmetro desempenha um papel importante na aplicação da detecção de anomalias com base no PCA. Depois de usar o PCA para projetar dados, essas anomalias são identificadas com mais facilidade.

#### <a name="recall-anomaly-detection-model"></a>**Modelo de detecção de anomalias de recall**

No modelo de detecção de anomalias de recall, os módulos **Selecionar Colunas no Conjunto de Dados** e de detecção de anomalias baseado em PCA são usados de maneira semelhante. Especificamente, três variáveis – temperatura do motor, temperatura externa e velocidade – são extraídas primeiro usando o módulo **Selecionar Colunas no Conjunto de Dados**. A variável de velocidade também é incluída, porque a temperatura do motor geralmente se correlaciona com a velocidade. Em seguida, o módulo de detecção de anomalias baseado em PCA é usado para projetar os dados do espaço tridimensional em um espaço bidimensional. Os critérios de recall são atendidos. O veículo exige um recall quando a temperatura do motor e a temperatura externa são correlacionadas de modo alto e negativo. Após a execução do PCA, o algoritmo de detecção de anomalias baseado em PCA é usado para capturar as anomalias. 

Durante o treinamento de um desses modelos, dados normais são usados como os dados de entrada para treinar o modelo de detecção de anomalias baseado em PCA. (Dados normais não exigem manutenção nem recall.) No teste de pontuação, o modelo de detecção de anomalias treinado é usado para detectar se o veículo exige manutenção ou recall. 

### <a name="real-time-analysis"></a>Análise em tempo real
A consulta SQL do Stream Analytics a seguir é usada para obter a média de todos os parâmetros de veículo importantes. Esses parâmetros incluem a velocidade do veículo, o nível de combustível, a temperatura do motor, a leitura do hodômetro, a pressão do pneu, o nível de óleo do motor e outros. As médias são usadas para detectar anomalias, emitir alertas e determinar as condições de integridade geral dos veículos operados em uma região específica. As médias são então correlacionadas com os dados demográficos. 

![Consulta do Stream Analytics para o processamento em tempo real](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Todas as médias são calculadas em uma janela em cascata de três segundos. Uma janela em cascata é usada porque são necessários intervalos de tempo não sobrepostos e contíguos. 

Para saber mais sobre as funcionalidades de janelas do Stream Analytics, consulte [Janelas (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

#### <a name="real-time-prediction"></a>**Previsão em tempo real**

Um aplicativo é incluído como parte da solução para operar o modelo de aprendizado de máquina em tempo real. O aplicativo RealTimeDashboardApp é criado e configurado como parte da implantação da solução. O aplicativo:

* Escuta em uma instância do hub de eventos na qual o Stream Analytics publica os eventos em um padrão continuamente.

    ![Consulta do Stream Analytics para publicação dos dados](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* Recebe eventos. Para cada evento que esse aplicativo recebe: 
   
   * Os dados são processados com um ponto de extremidade de RRS (pontuação de solicitação-resposta) do aprendizado de máquina. O ponto de extremidade RRS é publicado automaticamente como parte da implantação.
   * A saída do RRS é publicada em um conjunto de dados do Power BI usando as APIs de push.

Esse padrão também é aplicável a cenários em que você deseja integrar um aplicativo de linha de negócios ao fluxo de análise em tempo real. Esses cenários incluem alertas, notificações e mensagens.

Para baixar a solução RealtimeDashboardApp do Visual Studio para fazer personalizações, consulte a página da Web [Download do RealtimeDashboardApp](http://go.microsoft.com/fwlink/?LinkId=717078). 

#### <a name="execute-the-real-time-dashboard-application"></a>**Executar o aplicativo de painel em tempo real**
1. Extraia o RealtimeDashboardApp e salve-o localmente.

    ![Pasta RealTimeDashboardApp](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. Execute o aplicativo RealtimeDashboardApp.exe.

3. Insira suas credenciais válidas do Power BI e selecione **Entrar**.  

    ![Janela de entrada do aplicativo de painel em tempo real](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. Selecione **Aceitar**.

    ![Janela de entrada final do aplicativo de painel em tempo real](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>Caso deseje liberar o conjunto de dados do Power BI, execute o RealtimeDashboardApp com o parâmetro “flushdata”. 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Análise do lote
A meta aqui é mostrar como a Contoso Motors utiliza as funcionalidades de computação do Azure para aproveitar Big Data. Esses dados revelam insights avançados sobre os padrões de condução, o comportamento de uso e a integridade do veículo. Essas informações possibilitam:

* Melhorar a experiência do cliente e torná-la mais barata, fornecendo insights sobre os hábitos de condução e os comportamentos de condução com economia de combustível.
* Aprender de maneira proativa sobre os clientes e seus padrões de condução para controlar as decisões de negócios e fornecer os melhores produtos e serviços.

Nesta solução, pretendemos alcançar as seguintes métricas:

* **Comportamento de condução agressiva**: identifica a tendência de modelos, locais, condições de condução e época do ano para aprofundar-se nos padrões de condução agressiva. A Contoso Motors pode usar esses insights em campanhas de marketing, a fim de apresentar novos recursos personalizados e seguro baseado em uso.
* **Comportamento de condução com economia de combustível**: identifica a tendência de modelos, locais, condições de condução e época do ano para aprofundar-se em padrões de condução com economia de combustível. A Contoso Motors pode usar esses insights em campanhas de marketing, a fim de apresentar novos recursos e relatórios proativos para que os motoristas adquiram hábitos de condução econômica e ecológica.
* **Modelos para recall**: identifica os modelos que exigem recalls operacionalizando o experimento do aprendizado de máquina para a detecção de anomalias.

Vamos examinar os detalhes de cada uma dessas métricas.

#### <a name="aggressive-driving-behavior-patterns"></a>**Padrões de comportamento de condução agressiva**

Os sinais de veículo e os dados de diagnóstico particionados são processados em AggresiveDrivingPatternPipeline, conforme mostrado no fluxo de trabalho a seguir. O Hive é usado para determinar os modelos, o local, o veículo, as condições de condução e outros parâmetros que apresentam padrões de condução agressiva.

![Fluxo de trabalho do padrão de condução agressiva](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***Consulta do Hive do padrão de condução agressiva***

O script do Hive aggresivedriving.hql é usado para analisar padrões de condição de uma condução agressiva. Ele está localizado na pasta \demo\src\connectedcar\scripts do arquivo zip baixado. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                   vin                         string, 
                model                        string,
                timestamp                    string,
                city                        string,
                speed                          string,
                transmission_gear_position    string,
                brake_pedal_status            string,
                Year                        string,
                Month                        string

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


O script usa a combinação da posição da marcha de transmissão de um veículo, do status do pedal de freio e da velocidade para detectar o comportamento de condução negligente/agressiva com base nos padrões de frenagem em alta velocidade. 

Após a execução bem-sucedida do pipeline, você verá as seguintes partições geradas em sua conta de armazenamento no contêiner connectedcar:

![Saída do AggressiveDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**Padrões de comportamento de condução com economia de combustível**

Os sinais de veículo e os dados de diagnóstico particionados são processados em FuelEfficientDrivingPatternPipeline, conforme mostrado no fluxo de trabalho a seguir. O Hive é usado para determinar os modelos, o local, o veículo, as condições de condução e outras propriedades que apresentam padrões de condução com economia de combustível.

![Padrões de condução com economia de combustível](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***Consulta do Hive sobre o padrão de condução com economia de combustível***

O script do Hive fuelefficientdriving.hql é usado para analisar padrões de condição de uma condução com economia de combustível. Ele está localizado na pasta \demo\src\connectedcar\scripts do arquivo zip baixado. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                   vin                         string, 
                model                        string,
                   city                        string,
                speed                          string,
                transmission_gear_position    string,                
                brake_pedal_status            string,            
                accelerator_pedal_position    string,                             
                Year                        string,
                Month                        string

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


O script usa a combinação da posição da marcha de transmissão de um veículo, do status do pedal de freio, da velocidade e da posição do pedal do acelerador para detectar o comportamento de condução com economia de combustível, com base nos padrões de aceleração, frenagem e velocidade. 

Após a execução bem-sucedida do pipeline, você verá as seguintes partições geradas em sua conta de armazenamento no contêiner connectedcar:

![Saída do FuelEfficientDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**Previsões de modelo para recall**

O teste do aprendizado de máquina é fornecido e publicado como um serviço da Web como parte da implantação da solução. O ponto de extremidade da pontuação em lote é usado neste fluxo de trabalho. Ele é registrado como um serviço vinculado ao data factory e operacionalizado usando a atividade de pontuação em lote do data factory.

![Ponto de extremidade do aprendizado de máquina](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

O serviço vinculado registrado é usado em DetectAnomalyPipeline para pontuar os dados usando o modelo de detecção de anomalias. 

![Atividade de pontuação em lote do aprendizado de máquina no data factory](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

Algumas etapas são executadas nesse pipeline para a preparação de dados, de modo que ele possa ser operacionalizado com o serviço Web de pontuação em lote. 

![DetectAnomalyPipeline para a previsão de recall](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***Consulta do Hive para detecção de anomalias***

Depois que a pontuação é concluída, uma atividade do HDInsight processa e agrega os dados que o modelo categorizou como anomalias. O modelo usa uma pontuação de probabilidade de 0,60 ou superior.

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                            string,
                model                        string,
                gendate                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                fuel                        string,
                engineoil                    string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position    string,
                parking_brake_status        string,
                headlamp_status                string,
                brake_pedal_status            string,
                transmission_gear_position    string,
                ignition_status                string,
                windshield_wiper_status        string,
                abs                          string,
                maintenanceLabel            string,
                maintenanceProbability        string,
                RecallLabel                    string,
                RecallProbability            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                            string,
                model                        string,
                city                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                Year                        string,
                Month                        string                

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


Após a execução bem-sucedida do pipeline, você verá as seguintes partições geradas em sua conta de armazenamento no contêiner connectedcar:

![Saída de DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>Publicar

### <a name="real-time-analysis"></a>Análise em tempo real
Uma das consultas no trabalho do Stream Analytics publica os eventos em uma instância de saída do hub de eventos. 

![Trabalho do Stream Analytics publicado em uma instância de saída do hub de eventos](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

A seguinte consulta do Stream Analytics é usada para publicar na instância de saída do hub de eventos:

![Consulta do Stream Analytics a ser publicada na instância de saída do hub de eventos](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

Esse fluxo de eventos é consumido pelo RealTimeDashboardApp incluído na solução. Esse aplicativo usa o serviço Web de solicitação-resposta do aprendizado de máquina para a pontuação em tempo real. Ele publica os dados resultantes em um conjunto de dados do Power BI para consumo. 

### <a name="batch-analysis"></a>Análise do lote
Os resultados do processamento em lotes e em tempo real são publicados em tabelas do Banco de Dados SQL do Azure para consumo. O SQL Server, o banco de dados e as tabelas são criados automaticamente como parte do script de instalação. 

Os resultados do processamento em lotes são copiados para o fluxo de trabalho do data mart.

![Resultados do processamento em lotes copiados para o fluxo de trabalho do data mart](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

O trabalho do Stream Analytics é publicado no data mart.

![Trabalho do Stream Analytics publicado no data mart](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

A configuração do data mart está no trabalho do Stream Analytics.

![Configuração do data mart no trabalho do Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>Consumir
O PowerBI fornece essa solução a um painel avançado para os dados em tempo real e as visualizações da análise preditiva. 

O painel final é parecido com este exemplo:

![Painel do Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>Resumo
Este documento contém um detalhamento da Solução de Análise da Telemetria do Veículo. O padrão de arquitetura lambda é usado para a análise em tempo real e em lote com previsões e ações. Esse padrão se aplica a uma ampla variedade de casos de uso que exigem as análises de afunilamento (em tempo real) e de ampliação (em lote). 

### <a name="references"></a>Referências

* [Solução Vehicle Telematics Simulator do Visual Studio](http://go.microsoft.com/fwlink/?LinkId=717075) 
* [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/)
* [Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)
* [SDK dos Hubs de Eventos do Azure para ingestão de fluxo](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Funcionalidades de movimentação de dados do Azure Data Factory](../../data-factory/v1/data-factory-data-movement-activities.md)
* [Atividade .NET do Azure Data Factory](../../data-factory/v1/data-factory-use-custom-activities.md)
* [Solução de atividade .NET do Azure Data Factory do Visual Studio usada para preparar os dados de exemplo](http://go.microsoft.com/fwlink/?LinkId=717077) 
