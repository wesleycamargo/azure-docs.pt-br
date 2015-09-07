<properties 
	pageTitle="Azure Data Factory - Terminologia"
	description="Este artigo apresenta a terminologia usada na criação de data factories usando o serviço Azure Data Factory"
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
	ms.date="08/25/2015"
	ms.author="spelluru"/>

#Azure Data Factory - Terminologia

## Fábrica de dados
Uma **Azure Data Factory** tem um ou mais pipelines que processam dados em repositórios de dados vinculados (Armazenamento do Azure, Banco de Dados SQL do Azure, SQL Server local, etc.) usando serviços de computação vinculados, como o Azure HDInsight. Uma fábrica de dados do Azure em si não contém dados. Em vez disso, os dados são armazenados nos repositórios de dados mencionados acima.

## Serviço vinculado
Um **serviço vinculado** é um serviço que está vinculado a uma Azure Data Factory. Um serviço vinculado pode ser uma das seguintes opções:

- Um serviço de **armazenamento de dados**, como o Armazenamento do Azure, o Banco de Dados SQL do Azure ou um banco de dados do SQL Server local. Um repositório de dados é um contêiner de conjuntos de dados de entrada/saída.    
- Um serviço de **computação** como o Azure HDInsight, o Aprendizado de Máquina do Azure e o Azure Batch. Um serviço de computação processa os dados de entrada e produz os dados de saída.  

## Conjunto de dados
Um **conjunto de dados** é um modo de exibição de dados nomeado. Os dados que estão sendo descritos podem variar de simples bytes, dados semiestruturados como arquivos CSV até mesmo modelos ou tabelas relacionais. Uma **tabela** de Data Factory é um conjunto de dados que tem um esquema e é retangular. Depois de criar um serviço vinculado em um repositório de dados que se refere a um repositório de dados, você define conjuntos de dados que representam dados de entrada/saída que são armazenados no repositório de dados.


##Pipeline
Um **pipeline** em uma Azure Data Factory processa dados nos serviços de armazenamento vinculados utilizando serviços de computação vinculados. Ele contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Por exemplo, uma **Atividade de Cópia** copia dados de um armazenamento de origem para um armazenamento de destino e a **Atividade de HDInsight** utiliza um cluster do Azure HDInsight para processar os dados utilizando consultas Hive ou scripts Pig. Uma fábrica de dados pode ter um ou mais pipelines.

As etapas comuns para criar uma instância de Fábrica de Dados do Azure são:

1. Criar uma **data factory**.
2. Criar um **serviço vinculado** para cada armazenamento de dados ou serviço de computação.
3. Criar **conjuntos de dados** de entrada e saída.
4. Criar um **pipeline**. 

##Atividade
Uma etapa um processamento de dados em um pipeline que usa um ou mais conjuntos de dados de entrada e produz um ou mais conjuntos de dados de saída. As atividades são executadas em um ambiente de execução (por exemplo: cluster HDInsight do Azure) e dados de leitura/gravação para um armazenamento de dados associado/vinculado à Azure Data Factory.

O serviço de Fábrica de Dados do Azure dá suporte às seguintes atividades em um pipeline:

- **Atividade de Cópia** copia os dados de um repositório de dados para outro.  
- A **Atividade de HDInsight** processa dados executando scripts Hive/Pig ou programas MapReduce em um cluster HDInsight.  
- A **Atividade de contagem em lotes do Aprendizado de Máquina do Azure** invoca a API de pontuação em lotes do Aprendizado de Máquina do Azure. Consulte [Criar pipelines de previsão usando a Azure Data Factory e o Aprendizado de Máquina do Azure][azure-ml-adf] para obter detalhes. 
- A **Atividade de Procedimento Armazenado** invoca um procedimento armazenado em um Banco de Dados SQL do Azure. Consulte a [Atividade de Procedimento Armazenado][msdn-stored-procedure-activity] na biblioteca MSDN para obter detalhes.   

##Fatia
Uma tabela em uma fábrica de dados do Azure é composta de fatias. A largura de uma fatia é determinada pelo agendamento – por hora/diariamente. Quando o agendamento é "por hora", uma fatia é produzida por hora com a hora de início e de término de um pipeline. Por exemplo, se a data a e hora de início do pipeline forem 03/03/2015 06:00:00 (6h) e a data e a hora de término forem 03-03/2015 09:00:00 (9h no mesmo dia), três fatias de dados serão produzidas, uma fatia para cada intervalo de uma hora: 6h-7h, 7h-8h e 8h-9h.

As fatias fornecem a capacidade de trabalhar com um subconjunto dos dados gerais para uma janela de tempo específico (por exemplo: a fatia é gerada para a duração (hora): 13h às 14h).

## Execução de atividade para uma fatia
A **execução** ou uma execução de atividade é uma unidade de processamento de uma fatia. Pode haver uma ou mais execuções de uma fatia no caso de repetições ou se você executar novamente a fatia em caso de falha. Uma fatia é identificada pela sua hora de início.

##Gateway de gerenciamento de dados
O **Gateway de Gerenciamento de Dados** da Microsoft é um software que se conecta a fontes de dados locais para serviços de nuvem para consumo. É necessário ter pelo menos um gateway instalado no seu ambiente corporativo e registrá-lo com o portal da Fábrica de Dados do Azure antes de adicionar fontes de dados locais como serviços vinculados.
 
##Hub de dados
Um **Hub de Dados** é um contêiner lógico para serviços de computação e armazenamento de dados. Por exemplo, um cluster de Hadoop com HDFS como armazenamento e Hive/Pig como computação (processamento) é um hub de dados. Da mesma forma, um EDW(data warehouse corporativo) pode ser estabelecido como um hub de dados (banco de dados como armazenamento, procedimentos armazenados e/ou ferramenta ETL, como serviços de computação). Os pipelines utilizam armazenamentos de dados e executam em recursos de computação em um hub de dados. Apenas o hub de HDInsight tem suporte neste momento.

O Hub de dados permite que uma fábrica de dados seja dividida em agrupamentos específicos de domínio ou lógica, como o "Hub de Azure do oeste dos EUA" que gerencia todos os serviços vinculados (armazenamentos de dados e computação) e pipelines voltados para o data center do oeste dos EUA ou para as "Vendas de hub de EDW" que gerencia todos os serviços vinculados e pipelines interessados com dados de preenchimento e processamento para o data warehouse da empresa de vendas.

Uma característica importante do Hub é que um pipeline é executado em um único hub. Isso significa que, ao definir um pipeline, todos os serviços vinculados referenciados por tabelas ou atividades dentro do pipeline devem ter o mesmo nome do hub que o pipeline em si. Se a propriedade HubName não for especificada para um serviço vinculado, o serviço vinculado é colocado no hub "Padrão".

## Consulte também

1. [Introdução à Azure Data Factory][adf-intro]. Este artigo fornece uma visão geral do serviço do Azure Data Factory, o valor que ele oferece e os cenários que com suporte.
2. [Introdução à Data Factory.][datafactory-getstarted] Este artigo fornece um tutorial de ponta a ponta que mostra como criar uma fábrica de dados do Azure que copia dados de um blob do Azure para um banco de dados SQL do Azure.


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-intro]: data-factory-introduction.md
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



 

<!---HONumber=August15_HO9-->