<properties 
	pageTitle="Fábrica de dados do Azure - terminologia" 
	description="Este artigo apresenta a terminologia usada na criação de fábricas de dados usando o serviço de fábrica de dados do Azure" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

#Fábrica de dados do Azure - terminologia

## Fábrica de dados
Um **fábrica de dados do Azure** tem um ou mais pipelines que processam dados em armazenamentos de dados vinculado (armazenamento do Azure, banco de dados do SQL Azure, local do SQL Server etc...) usando os serviços de computação vinculado como Azure HDInsight. Uma fábrica de dados do Azure em si não contém dados. Em vez disso, os dados são armazenados nos repositórios de dados mencionados acima.

## Serviço vinculado
Um **vinculado serviço** é um serviço que está vinculado a uma fábrica de dados do Azure. Um serviço vinculado pode ser uma das seguintes opções:

- Um **armazenamento de dados** service como o armazenamento do Azure, banco de dados SQL ou banco de dados do SQL Server local. Um repositório de dados é um contêiner de conjuntos de dados de entrada/saída.    
- Um **de computação** serviço como Azure HDInsight, aprendizado de máquina do Azure e lote do Azure. Um serviço de computação processa os dados de entrada e produz os dados de saída.  

## Conjunto de dados
Um **conjunto de dados** é um modo de exibição nomeado de dados. Os dados que estão sendo descritos podem variar de simples bytes, dados semiestruturados como arquivos CSV até mesmo modelos ou tabelas relacionais. Uma fábrica de dados **tabela** é um conjunto de dados e que tem um esquema retangular. Depois de criar um serviço vinculado em um repositório de dados que se refere a um repositório de dados, você define conjuntos de dados que representam dados de entrada/saída que são armazenados no repositório de dados.


##Pipeline
Um **pipeline** do Azure fábrica de dados processa dados nos serviços de armazenamento vinculada usando os serviços de computação vinculado. Ele contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Por exemplo, um **cópia atividade** copia dados de um armazenamento de origem para o armazenamento de destino e **HDInsight atividade** usar um cluster Azure HDInsight para processar dados usando consultas ou scripts do Pig. Uma fábrica de dados pode ter um ou mais pipelines.

As etapas comuns para criar uma instância de Fábrica de Dados do Azure são:

1. Criar um **fábrica dados**.
2. Criar um **vinculado serviço** para cada dados armazenar ou serviço de computação.
3. Criar a entrada e saída **conjuntos de dados**.
4. Criar um **pipeline**. 

##Atividade
Uma etapa um processamento de dados em um pipeline que usa um ou mais conjuntos de dados de entrada e produz um ou mais conjuntos de dados de saída. Atividades são executadas em um ambiente de execução (por exemplo: cluster Azure HDInsight) e dados de leitura/gravação para um armazenamento de dados/vinculada associados com a fábrica de dados do Azure.

O serviço de Fábrica de Dados do Azure dá suporte às seguintes atividades em um pipeline:

- **Cópia atividade** copia os dados de um armazenamento de dados para outro armazenamento de dados. Consulte [copiar dados com o Azure dados Factory][copy-data-with-adf] para obter detalhes sobre os dados que armazena a atividade de cópia oferece suporte. 
- **HDInsight atividade** processa dados por meio de scripts de Hive/Pig ou programas MapReduce em um cluster HDInsight. Consulte [usar o Pig e Hive com dados Factory][use-pig-hive] e [chamar programas MapReduce da fábrica de dados][run-map-reduce] para obter detalhes. 
- **As atividades de pontuação do azure máquina aprendizado lote** invoca o lote de aprendizado de máquina do Azure API de pontuação. Consulte [Criar previsão Pipelines usando a fábrica de dados do Azure e aprendizado de máquina do Azure][azure-ml-adf] para obter detalhes. 
- **Atividade de procedimento armazenado** chama um procedimento armazenado em um banco de dados do SQL Azure. Consulte o [atividade de procedimento armazenado][msdn-stored-procedure-activity] na biblioteca MSDN para obter detalhes.   

##Fatia
Uma tabela em uma fábrica de dados do Azure é composta de fatias. A largura de uma fatia é determinada pelo agendamento – por hora/diariamente. Quando o agendamento é "por hora", uma fatia é produzida por hora com a hora de início e de término de um pipeline. Por exemplo, se a data e hora de iniciar o pipeline é 03/03/2015 06:00:00 (6 horas) e a data e hora de término é 03-03/2015 09:00:00 (9 AM no mesmo dia), três dados fatias são produzidas, uma fatia de cada intervalo de 1 hora: 6-7 AM, 7-8 horas e 8-9: 00.

Fatias fornecem a capacidade de trabalhar com um subconjunto dos dados gerais para uma janela de tempo específico (por exemplo: a fatia que é gerada para a duração (horas): 1:00 PM às 2:00).

## Execução de atividade para uma fatia
O **executar** ou uma atividade execute é uma unidade de processamento de uma fatia. Pode haver uma ou mais execuções de uma fatia no caso de repetições ou se você executar novamente a fatia em caso de falha. Uma fatia é identificada pela sua hora de início.

##Gateway de gerenciamento de dados
Microsoft **Data Management Gateway** é um software que se conecta a fontes de dados locais para a nuvem de serviços para consumo. É necessário ter pelo menos um gateway instalado no seu ambiente corporativo e registrá-lo com o portal da Fábrica de Dados do Azure antes de adicionar fontes de dados locais como serviços vinculados.
 
##Hub de dados
Um **Data Hub** é um contêiner lógico para serviços de computação e armazenamento de dados. Por exemplo, um cluster de Hadoop com HDFS como armazenamento e Hive/Pig como computação (processamento) é um hub de dados. Da mesma forma, um EDW(data warehouse corporativo) pode ser estabelecido como um hub de dados (banco de dados como armazenamento, procedimentos armazenados e/ou ferramenta ETL, como serviços de computação). Os pipelines utilizam armazenamentos de dados e executam em recursos de computação em um hub de dados. Apenas o hub de HDInsight tem suporte neste momento.

O Hub de dados permite que uma fábrica de dados seja dividida em agrupamentos específicos de domínio ou lógica, como o "Hub de Azure do oeste dos EUA" que gerencia todos os serviços vinculados (armazenamentos de dados e computação) e pipelines voltados para o data center do oeste dos EUA ou para as "Vendas de hub de EDW" que gerencia todos os serviços vinculados e pipelines interessados com dados de preenchimento e processamento para o data warehouse da empresa de vendas.

Uma característica importante do Hub é que um pipeline é executado em um único hub. Isso significa que, ao definir um pipeline, todos os serviços vinculados referenciados por tabelas ou atividades dentro do pipeline devem ter o mesmo nome do hub que o pipeline em si. Se a propriedade HubName não for especificada para um serviço vinculado, o serviço vinculado é colocado no hub "Padrão".

## Consulte também

1. [Introdução aos dados do Azure fábrica][adf-intro]. Este artigo fornece uma visão geral do serviço do Azure Data Factory, o valor que ele oferece e os cenários que com suporte.
2. [Introdução aos dados fábrica][datafactory-getstarted]. Este artigo fornece um tutorial de ponta a ponta que mostra como criar uma fábrica de dados do Azure que copia dados de um blob do Azure para um banco de dados SQL do Azure.


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-intro]: data-factory-introduction.md
[copy-data-with-adf]: data-factory-copy-activity.md
[use-pig-hive]: data-factory-pig-hive-activities.md
[run-map-reduce]: data-factory-map-reduce.md
[azure-ml-adf]: data-factory-create-predictive-pipelines.md
[adf-common-scenarios]: data-factory-common-scenarios.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[data-factory-editor]: data-factory-editor.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx


[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]: ./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]: ./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]: ./media/data-factory-introduction/DataFactoryDataFlow.png

<!---HONumber=GIT-SubDir--> 