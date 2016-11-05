O Azure Data Factory dá suporte às seguintes atividades de transformação, que podem ser adicionadas aos pipelines individualmente ou encadeadas a outra atividade.

| Atividades de transformação de dados | Ambiente de computação |
|:--- |:--- |
| [Hive](../articles/data-factory/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](../articles/data-factory/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Atividades do Machine Learning: recurso de execução em lote e de atualização](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) |VM do Azure |
| [Procedimento armazenado](../articles/data-factory/data-factory-stored-proc-activity.md) |SQL Azure, Azure SQL Data Warehouse ou SQL Server |
| [U-SQL da Análise Data Lake](../articles/data-factory/data-factory-usql-activity.md) |Análise Azure Data Lake |
| [DotNet](../articles/data-factory/data-factory-use-custom-activities.md) |HDInsight [Hadoop] ou Lote do Azure |

> [!NOTE]
> Você pode usar a atividade MapReduce para executar programas Spark no cluster HDInsight Spark. Veja [Invocar programas Spark pelo Azure Data Factory](../articles/data-factory/data-factory-spark.md) para obter detalhes. 
> Você pode criar uma atividade personalizada para executar scripts R em seu cluster HDInsight com R instalado. Veja [Executar scripts R usando o Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample).
> 
> 

<!---HONumber=AcomDC_0928_2016-->
