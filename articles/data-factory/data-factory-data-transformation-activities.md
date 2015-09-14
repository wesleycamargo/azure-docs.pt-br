<properties 
	pageTitle="Atividades de Transformação de Dados | Microsoft Azure"
	description="Saiba como você pode usar o serviço Azure Data Factory para transformar e analisar dados."
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
	ms.date="07/26/2015"
	ms.author="spelluru"/>

# Transformar e analisar usando o Azure Data Factory

## Visão geral
As atividades de transformação no Azure Data Factory transformam e processam os dados brutos em previsões e em informações. A atividade de transformação é executada em um ambiente de cálculo, como um cluster do Azure HDInsight ou um Lote do Azure. O Azure Data Factory dá suporte às seguintes atividades de transformação, que podem ser adicionadas aos [pipelines](data-factory-create-pipelines.md) individualmente ou encadeadas a outra atividade.


Atividade de transformação | Ambiente de computação 
----------------------- | --------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Pig](data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Streaming do Hadoop](https://msdn.microsoft.com/library/mt185698.aspx) | HDInsight [Hadoop]
[Pontuação de Lote de Aprendizado de Máquina](data-factory-create-predictive-pipelines.md) | VM do Azure 
[Procedimento armazenado](data-factory-stored-proc-activity.md) | SQL Azure | 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] ou Lote do Azure    

É necessário criar um serviço vinculado para o ambiente de computação e, em seguida, usar o serviço vinculado ao definir uma atividade de transformação. Há dois tipos de ambientes de computação com suporte do Data Factory.

1. **Sob demanda**: nesse caso, o ambiente de computação é totalmente gerenciado pelo Data Factory. Ele é automaticamente criado pelo serviço Data Factory antes de um trabalho ser enviado a fim de processar os dados e é removido após a conclusão do trabalho. Os usuários podem configurar e controlar as configurações granulares do ambiente de computação sob demanda para execução do trabalho, gerenciamento de cluster e ações de inicialização. 
2. **Traga seu próprio**: nesse caso, você pode registrar no Data Factory seu próprio ambiente de computação (por exemplo, o cluster HDInsight) como um serviço vinculado. O ambiente de computação é gerenciado por você e o serviço Data Factory o utiliza para executar as atividades. 

Consulte o artigo [Serviços vinculados de computação](data-factory-compute-linked-services.md) para saber mais sobre os serviços vinculados de computação com suporte do Data Factory.

## Enviar comentários
Apreciamos muito seus comentários sobre este artigo. Reserve alguns minutos para enviar seus comentários por meio de [email](mailto:adfdocfeedback@microsoft.com?subject=data-factory-data-transformation-activities.md).

<!---HONumber=September15_HO1-->