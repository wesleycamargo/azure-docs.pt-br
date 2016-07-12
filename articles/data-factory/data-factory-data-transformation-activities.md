<properties 
	pageTitle="Transformação de dados: Processo e dados de transformação | Microsoft Azure" 
	description="Aprenda sobre a transformação de dados no Azure Data Factory. Transformar e processar dados no cluster HDInsight do Azure ou um lote do Azure." 
	keywords="transformação de dados, dados de processo, transformar dados, atividade de transformação"
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
	ms.date="06/27/2016" 
	ms.author="spelluru"/>

# Saiba mais sobre transformação e análise de dados no Azure Data Factory
Este artigo explica as atividades de transformação de dados no Azure Data Factory para que você saiba como o ADF transforma e processa os dados brutos em previsões e ideias. A atividade de transformação é executada em um ambiente de cálculo, como um cluster do Azure HDInsight ou um Lote do Azure. Links para artigos que mostram como usar atividades de transformação específica são incluídas também.
 
O Azure Data Factory dá suporte às seguintes atividades de transformação, que podem ser adicionadas aos [pipelines](data-factory-create-pipelines.md) individualmente ou encadeadas a outra atividade.

Atividades de transformação de dados | Ambiente de computação 
:----------------------- | :--------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Pig](data-factory-pig-activity.md) | HDInsight [Hadoop] 
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop] 
[Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Atividades de aprendizado de máquina: recurso de execução em lote e de atualização](data-factory-azure-ml-batch-execution-activity.md) | VM do Azure 
[Procedimento armazenado](data-factory-stored-proc-activity.md) | SQL Azure, Azure SQL Data Warehouse ou SQL Server |
[U-SQL da Análise Data Lake](data-factory-usql-activity.md) | Análise Azure Data Lake 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] ou Lote do Azure
   

 > [AZURE.NOTE] Você pode usar a atividade MapReduce para executar programas Spark no cluster HDInsight Spark. Veja [Invocar programas Spark pelo Azure Data Factory](data-factory-spark.md) para obter detalhes. Você pode criar uma atividade personalizada para executar scripts R em seu cluster HDInsight com R instalado. Veja [Executar scripts R usando o Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample).

É necessário criar um serviço vinculado para o ambiente de computação e, em seguida, usar o serviço vinculado ao definir uma atividade de transformação. Há dois tipos de ambientes de computação com suporte do Data Factory.

1. **Sob demanda**: nesse caso, o ambiente de computação é totalmente gerenciado pelo Data Factory. Ele é automaticamente criado pelo serviço Data Factory antes de um trabalho ser enviado a fim de processar os dados e é removido após a conclusão do trabalho. Os usuários podem configurar e controlar as configurações granulares do ambiente de computação sob demanda para execução do trabalho, gerenciamento de cluster e ações de inicialização.
2. **Traga seu próprio**: nesse caso, você pode registrar no Data Factory seu próprio ambiente de computação (por exemplo, o cluster HDInsight) como um serviço vinculado. O ambiente de computação é gerenciado por você e o serviço Data Factory o utiliza para executar as atividades.

Veja o artigo [Serviços vinculados de computação](data-factory-compute-linked-services.md) para saber mais sobre os serviços vinculados de computação com suporte do Data Factory.

<!---HONumber=AcomDC_0629_2016-->