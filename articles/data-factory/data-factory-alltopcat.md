<properties
    pageTitle="Todos os tópicos do serviço de Data Factory | Microsoft Azure"
    description="Tabela de todos os tópicos para o serviço do Azure denominados Data Factory que existem em http://azure.microsoft.com/documentation/articles/, Título e descrição."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="data-factory"
    ms.workload="data-factory"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="spelluru"/>



# <a name="all-topics-for-azure-data-factory-service"></a>Todos os tópicos do serviço Azure Data Factory

Este tópico lista cada tópico que se aplica diretamente ao serviço **Data Factory** do Azure. Você pode pesquisar palavras-chave nesta página da Web usando **Ctrl + F**para encontrar os tópicos de seu interesse atual.




## <a name="new"></a>Novo

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 1 | [Mover dados do Amazon Redshift usando o Azure Data Factory](data-factory-amazon-redshift-connector.md) | Saiba mais sobre como mover dados do Amazon Redshift usando o Azure Data Factory. |
| 2 | [Mover dados do Amazon Simple Storage Service usando o Azure Data Factory](data-factory-amazon-simple-storage-service-connector.md) | Saiba mais sobre como mover dados do Amazon S3 (Simple Storage Service) usando o Azure Data Factory. |
| 3 | [Azure Data Factory – Assistente de Cópia](data-factory-azure-copy-wizard.md) | Saiba mais sobre como usar o Assistente de Cópia do Azure Data Factory para copiar dados de fontes de dados com suporte para coletores. |
| 4 | [Tutorial: Criar seu primeiro Azure Data Factory usando a API REST do Data Factory](data-factory-build-your-first-pipeline-using-rest-api.md) | Neste tutorial, você cria um pipeline de exemplo do Azure Data Factory usando a API REST do Data Factory. |
| 5 | [Tutorial: Criar um pipeline com Atividade de Cópia usando a API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md) | Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando a REST .NET. |
| 6 | [Tutorial: criar um pipeline com Atividade de Cópia usando a API REST](data-factory-copy-activity-tutorial-using-rest-api.md) | Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando a REST API. |
| 7 | [Assistente de Cópia do Data Factory](data-factory-copy-wizard.md) | Saiba mais sobre como usar o Assistente de Cópia do Data Factory para copiar dados de fontes de dados com suporte para coletores. |
| 8 | [Gateway de gerenciamento de dados](data-factory-data-management-gateway.md) | Configure um gateway de dados para mover dados entre o local e a nuvem. Use o Gateway de Gerenciamento de Dados no Azure Data Factory para mover os dados. |
| 9 | [Mover dados de um banco de dados Cassandra local usando o Azure Data Factory](data-factory-onprem-cassandra-connector.md) | Saiba mais sobre como mover dados de um banco de dados Cassandra local usando o Azure Data Factory. |
| 10 | [Mover dados do MongoDB usando o Azure Data Factory](data-factory-on-premises-mongodb-connector.md) | Saiba mais sobre como mover dados do banco de dados MongoDB usando o Azure Data Factory. |
| 11 | [Mover dados do Salesforce usando o Azure Data Factory](data-factory-salesforce-connector.md) | Saiba mais sobre como mover os dados do Salesforce usando o Azure Data Factory. |


## <a name="updated-articles,-data-factory"></a>Artigos atualizados, Data Factory

Esta seção lista os artigos que foram atualizados recentemente nos quais a atualização foi grande ou significativa. Para cada artigo atualizado, um trecho aproximado do texto markdown adicionado é exibido. Os artigos foram atualizados dentro do intervalo de datas de **22/08/2016** a **05/10/2016**.

| &nbsp; | Artigo | Texto atualizado, trecho | Data da atualização |
| --: | :-- | :-- | :-- |
| 12 | [Azure Data Factory - Log de alterações da API .NET](data-factory-api-change-log.md) | Este artigo fornece informações sobre alterações no SDK do Azure Data Factory em uma versão específica. Você pode encontrar o pacote mais recente do NuGet para o Azure Data Factory aqui (https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) ** Versão 4.11.0** Adições de recursos: / Os seguintes tipos de serviço vinculados foram adicionados: - OnPremisesMongoDbLinkedService (https://msdn.microsoft.com/library/mt765129.aspx) - AmazonRedshiftLinkedService (https://msdn.microsoft.com/library/mt765121.aspx) - AwsAccessKeyLinkedService (https://msdn.microsoft.com/library/mt765144.aspx) / Os seguintes tipos de conjunto de dados foram adicionados: - MongoDbCollectionDataset (https://msdn.microsoft.com/library/mt765145.aspx) - AmazonS3Dataset (https://msdn.microsoft.com/library/mt765112.aspx) / Os seguintes tipos de origem de cópia foram adicionados:-MongoDbSource (https://msdn.microsoft.com/en-US/library/mt765123.aspx) ** Versão 4.10.0** / As propriedades opcionais a seguir foram adicionadas ao TextFormat:    -  Ski | 2016-09-22 |
| 13 | [Mover dados para e do blob do Azure usando o Azure Data Factory](data-factory-azure-blob-connector.md) |  /  copyBehavior  /  Define o comportamento de cópia quando a origem é BlobSource ou FileSystem.  /  **PreserveHierarchy:** preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino..br/..br/.**FlattenHierarchy:** todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm o nome gerado automaticamente. .br/..br/.**MergeFiles: (padrão)** mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/blob for especificado, o nome do arquivo mesclado será o nome especificado; caso contrário, será o nome de arquivo gerado automaticamente.  /  Não  / O **BlobSource** também dá suporte a essas duas propriedades para compatibilidade com versões anteriores. / **treatEmptyAsNull**: especifica se deve-se tratar a cadeia de caracteres nula ou vazia como valor nulo. / **skipHeaderLineCount** – Especifica quantas linhas precisam ser ignoradas. É aplicável somente quando o conjunto de dados de entrada usa TextFormat. Da mesma forma, **BlobSink** dá suporte a | 2016-09-28 |
| 14 | [Criar pipelines preditivos usando as atividades do Aprendizado de Máquina do Azure](data-factory-azure-ml-batch-execution-activity.md) | ** O serviço Web requer diversas entradas** Se o serviço Web receber várias entradas, use a propriedade **webServiceInputs** em vez de usar **webServiceInput**. Os conjuntos de dados referenciados por **webServiceInputs** também devem ser incluídos nas **entradas** da Atividade. Em seu experimento de Azure ML, as portas de entrada e saída do serviço Web e os parâmetros globais têm nomes padrão ("input1", "input2") os quais você pode personalizar. Os nomes que você usa para as configurações webServiceInputs, webserviceoutputs e globalParameters devem corresponder exatamente aos nomes nos testes. Você pode exibir a carga de solicitação de exemplo na página de Ajuda de Execução do Lote no ponto de extremidade de Azure ML para verificar o mapeamento esperado.    {       "name": "PredictivePipeline",       "properties": {             "description": "use AzureML model",             "activities":  {                "name": "MLActivity",               "type": "AzureMLBatchExecution",                "description": "prediction analysis on batch input",                "inputs":  {                    "name": "inputDataset1"                 }, {                    "name": "inputDatase | 2016-09-13 |
| 15 | [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) | 1. **Estabelecer uma linha de base**. Durante a fase de desenvolvimento, teste seu pipeline com a Atividade de Cópia em relação a um exemplo de dados representativo. Você pode usar o modelo de divisão do Data Factory (data-factory-scheduling-and-execution.md time-series-datasets-and-data-slices) para limitar a quantidade de dados com que você trabalha.  Colete o tempo de execução e as características do desempenho usando o **Monitoramento e Gerenciamento de Aplicativos**. Escolha **Monitorar e Gerenciar** na página inicial do Data Factory. Na exibição em árvore, escolha o **conjunto de dados de saída**. Na lista **Janelas de Atividade** escolha a execução Atividade de Cópia. **Janelas de Atividade** listam a duração da Atividade de Cópia e o tamanho dos dados copiados. A taxa de transferência é listada no **Gerenciador de Janelas de Atividades**. Para saber mais sobre o aplicativo, consulte Monitorar e gerenciar os pipelines do Azure Data Factory usando o Monitoramento e Gerenciamento de Aplicativos (data-factory-monitor-manage-app.md).  ! Detalhes de execução de atividade (./media/data-factory-copy-activity-performance/mmapp-activity-run-details.pn | 2016-09-27 |
| 16 | [Tutorial: Criar um pipeline com a Atividade de Cópia usando o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) |   Observe os seguintes pontos: – o **tipo** do conjunto de dados está definido como **AzureBlob**.     - **linkedServiceName** está definido como **AzureStorageLinkedService**. Você criou esse serviço vinculado na Etapa 2.     - **folderPath** é definido como o contêiner **adftutorial**. Você também pode especificar o nome de um blob dentro da pasta usando a propriedade **fileName** . Como você não está especificando o nome do blob, dados de todos os blobs no contêiner são considerados como um entrada de dados.    – o formato de **tipo** é definido para **TextFormat** – Há dois campos no arquivo de texto ΓÇô **FirstName** e **LastName** ΓÇô separados por uma vírgula (**columnDelimiter**)     – A **disponibilidade** é definida como **por hora** (**frequência** é definida como **hora** e **intervalo** é definido como **1**). Portanto, o Data Factory procurará dados de entrada a cada hora na pasta raiz do contêiner de blob (**adftutorial**) especificado.  Se você não especificar um **fileName** para um conjunto de dados de **entrada**, todos os arquivos/blobs da pasta de entrada (**folderPath**) serão considerados | 2016-09-29 |
| 17 | [Criar, monitorar e gerenciar data factories do Azure usando o SDK do .NET da Data Factory](data-factory-create-data-factories-programmatically.md) | Anote a ID do aplicativo e a senha (segredo do cliente), e use-a no passo a passo. ** Obter a assinatura do Azure e IDs de locatário** Siga as instruções do artigo Como instalar e configurar o Azure PowerShell para instalar a última versão do Azure PowerShell (../powershell-install-configure.md) no computador, caso ela ainda não esteja instalada. 1. Inicie o Azure PowerShell e execute o seguinte comando 2. Execute o comando a seguir e insira o nome de usuário e a senha que você usa para entrar no portal do Azure.         Login-AzureRmAccount   Se você tiver apenas uma assinatura do Azure associada a essa conta, não será necessário executar as duas próximas etapas. 3. Execute o comando a seguir para exibir todas as assinaturas dessa conta.       Get-AzureRmSubscription 4. Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **NameOfAzureSubscription** pelo nome da sua assinatura do Azure.       Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription  /  Set-AzureRmCo | 2016-09-14 |
| 18 | [Pipelines e atividades no Azure Data Factory](data-factory-create-pipelines.md) |       ,       "start": "2016-07-12T00:00:00Z",    "end": "2016-07-13T00:00:00Z"       }     } Observe os seguintes pontos: / Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **Cópia**. / A entrada da atividade é definida como **InputDataset** e a saída da atividade é definida como **OutputDataset**. / Na seção **typeProperties**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor. Para obter uma explicação completa da criação desse pipeline, confira Tutorial: copiar dados de Armazenamento de Blobs para o Banco de Dados SQL (data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). ** Pipeline de transformação de exemplo** No pipeline de exemplo a seguir, há uma atividade do tipo **HDInsightHive** na seção de **atividades**. Neste exemplo, a atividade de Hive do HDInsight (data-factory-hive-activity.md) transforma os dados de um Armazenamento de Blobs do Azure executando um arquivo de script do Hive em um cluster Hadoop do HDInsight do Azure.  {     "name": "TransformPipeline",    "p | 2016-09-27 |
| 19 | [Transformar dados no Azure Data Factory](data-factory-data-transformation-activities.md) | O Data Factory dá suporte às seguintes atividades de transformação de dados, que podem ser adicionadas aos pipelines (data-factory-create-pipelines.md) individualmente ou encadeadas a outra atividade. .  AZURE.NOTE  Para uma explicação com instruções passo a passo, confira o artigo Criar um pipeline com a transformação do Hive (data-factory-build-your-first-pipeline.md). ** HDInsight Hive activity** A atividade de Hive do HDInsight em um pipeline de Data Factory executa consultas de Hive em seu próprio cluster ou no cluster sob demanda do HDInsight baseado em Windows/Linux. Confira o artigo Atividade de Hive (data-factory-hive-activity.md) para obter detalhes sobre essa atividade. ** Atividade de Pig do HDInsight** A atividade de Pig do HDInsight em um pipeline de Data Factory executa consultas de Pig em seu próprio cluster ou no cluster sob demanda do HDInsight baseado em Windows/Linux. Confira o artigo Atividade de Pig (data-factory-pig-activity.md) para obter detalhes sobre essa atividade. ** Atividade de MapReduce do HDInsight** A atividade de MapReduce do HDInsight em um pipeline do Data Factory executa programas MapReduce no | 2016-09-26 |
| 20 | [Agendamento e execução com o Data Factory](data-factory-scheduling-and-execution.md) | CopyActivity2 seria executado somente se CopyActivity1 tivesse sido executado com êxito e Dataset2 estivesse disponível. Este é o exemplo de pipeline JSON:  {       "name": "ChainActivities",    "properties": {           "description": "Run activities in sequence",      "activities":       {       "type": "Copy",     "typeProperties": {     "source": {     "type": "BlobSource"    },      "sink": {       "type": "BlobSink",     "copyBehavior": "PreserveHierarchy",    "writeBatchSize": 0,    "writeBatchTimeout": "00:00:00"     }       },      "inputs":       {       "name": "Dataset1"      }       ,       "outputs":      {       "name": "Dataset2"      }       ,       "policy": {     "timeout": "01:00:00"       },      "scheduler": {      "frequency": "Hour",    "interval": 1       },      "name": "CopyFromBlob1ToBlob2",     "description": "Copy data from a blob to another"       },      {       "type": "Copy",     "typeProperties": {     "source": {     "type": "BlobSource"    },      "sink": {       "type": "BlobSink",     "writeBatchSize": 0,    "writeBatchTimeout": "00:00:00"     }       },      "in | 2016-09-28 |





## <a name="tutorials"></a>Tutoriais

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 21 | [Tutorial: Criar seu primeiro pipeline para processar dados usando cluster Hadoop](data-factory-build-your-first-pipeline.md) | Este tutorial do Azure Data Factory mostra como criar e agendar um data factory que processa os dados usando o script Hive em um cluster Hadoop. |
| 22 | [Tutorial: compilar sua primeira Azure Data Factory usando o modelo do Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) | Neste tutorial, você criará um pipeline de exemplo do Azure Data Factory usando um modelo do Azure Resource Manager. |
| 23 | [Tutorial: Criar seu primeiro Azure Data Factory usando o portal do Azure](data-factory-build-your-first-pipeline-using-editor.md) | Neste tutorial, você cria um pipeline de exemplo do Azure Data Factory usando o Data Factory Editor no portal do Azure. |
| 24 | [Tutorial: Criar seu primeiro Azure Data Factory usando o Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) | Neste tutorial, você cria um pipeline de exemplo do Azure Data Factory usando o Azure PowerShell. |
| 25 | [Tutorial: Criar seu primeiro Azure Data Factory usando o Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) | Neste tutorial, você cria um pipeline de exemplo do Azure Data Factory usando o Visual Studio. |
| 26 | [Tutorial: criar um pipeline com a Atividade de Cópia usando o portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) | Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Data Factory Editor no portal do Azure. |
| 27 | [Tutorial: Criar um pipeline com a Atividade de Cópia usando o Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) | Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Azure PowerShell. |
| 28 | [Tutorial: Criar um pipeline com a Atividade de Cópia usando o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) | Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Visual Studio. |
| 29 | [Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL usando o Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | Este tutorial mostra como usar a Atividade de Cópia em um pipeline do Azure Data Factory para copiar dados do Armazenamento de Blobs para um banco de dados SQL. |
| 30 | [Tutorial: Criar um pipeline com a Atividade de Cópia usando o Assistente de Cópia do Data Factory](data-factory-copy-data-wizard-tutorial.md) | Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Assistente de Cópia com suporte do Data Factory |



## <a name="data-movement"></a>Movimentação de dados

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 31 | [Mover dados para e do blob do Azure usando o Azure Data Factory](data-factory-azure-blob-connector.md) | Saiba como copiar dados de blob no Azure Data Factory. O(s) exemplo(s) a seguir mostra(m) como copiar dados de e para o Armazenamento de Blobs do Azure e o Banco de Dados SQL do Azure. |
| 32 | [Mover dados para e do Repositório Data Lake do Azure usando o Azure Data Factory](data-factory-azure-datalake-connector.md) | Saiba como mover dados para/do Repositório Data Lake do Azure usando o Azure Data Factory |
| 33 | [Mover dados para e do Banco de Dados de Documentos usando o Azure Data Factory](data-factory-azure-documentdb-connector.md) | Saiba como mover dados para/da coleção Banco de Dados de Documentos do Azure usando o Azure Data Factory |
| 34 | [Mover dados de e para o Banco de Dados SQL do Azure usando o Azure Data Factory](data-factory-azure-sql-connector.md) | Saiba como mover dados para/do Banco de Dados SQL do Azure usando o Azure Data Factory |
| 35 | [Mover dados para e do SQL Data Warehouse do Azure usando o Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) | Saiba como mover dados para/do SQL Data Warehouse do Azure usando o Azure Data Factory |
| 36 | [Mover dados para e da Tabela do Azure | Azure Data Factory](data-factory-azure-table-connector.md) | Saiba como mover dados para/do Armazenamento de Tabela do Azure usando o Azure Data Factory |
| 37 | [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) | Saiba mais sobre os principais fatores que afetam o desempenho da movimentação de dados no Azure Data Factory quando você usa a Atividade de Cópia. |
| 38 | [Mover dados usando a Atividade de Cópia](data-factory-data-movement-activities.md) | Saiba mais sobre a movimentação de dados em pipelines do Data Factory: migração de dados entre repositórios na nuvem e entre um repositório local e um repositório na nuvem. Use a Atividade de Cópia. |
| 11,8 | [Notas de versão para o Gateway de Gerenciamento de Dados](data-factory-gateway-release-notes.md) | Notas de versão do Gateway de Gerenciamento de Dados |
| 40 | [Mover dados do HDFS local usando o Azure Data Factory](data-factory-hdfs-connector.md) | Saiba mais sobre como mover dados do HDFS local usando o Azure Data Factory |
| 41 | [Monitorar e gerenciar os pipelines do Azure Data Factory usando novo Aplicativo de Monitoramento e Gerenciamento](data-factory-monitor-manage-app.md) | Saiba como usar o Aplicativo de Monitoramento e Gerenciamento para monitorar e gerenciar data factories e pipelines do Azure. |
| 42 | [Mover dados entre fontes locais e a nuvem com o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) | Configure um gateway de dados para mover dados entre o local e a nuvem. Use o Gateway de Gerenciamento de Dados no Azure Data Factory para mover os dados. |
| 43 | [Mover dados De uma origem de OData usando o Azure Data Factory](data-factory-odata-connector.md) | Saiba mais sobre como mover dados de fontes OData usando o Azure Data Factory. |
| 44 | [Mover dados de armazenamentos de dados ODBC usando o Azure Data Factory](data-factory-odbc-connector.md) | Saiba mais sobre como mover dados de armazenamentos de dados ODBC usando o Azure Data Factory. |
| 45 | [Mover dados do DB2 usando o Azure Data Factory](data-factory-onprem-db2-connector.md) | Saiba mais sobre como mover dados do banco de dados DB2 usando o Azure Data Factory |
| 46 | [Mover dados de e para o sistema de arquivos local usando o Azure Data Factory](data-factory-onprem-file-system-connector.md) | Aprenda como mover dados de um sistema de arquivos local e para ele usando o Azure Data Factory. |
| 47 | [Mover dados do MySQL usando o Azure Data Factory](data-factory-onprem-mysql-connector.md) | Saiba mais sobre como mover dados do banco de dados MySQL usando o Azure Data Factory |
| 48 | [Mover dados para dentro e fora do Oracle local usando o Azure Data Factory](data-factory-onprem-oracle-connector.md) | Aprenda a mover dados de/para o banco de dados da Oracle que está no local usando o Azure Data Factory. |
| 49 | [Mover dados do PostgreSQL usando o Azure Data Factory](data-factory-onprem-postgresql-connector.md) | Saiba mais sobre como mover dados do banco de dados PostgreSQL usando o Azure Data Factory |
| 50 | [Mover dados do Sybase usando o Azure Data Factory](data-factory-onprem-sybase-connector.md) | Saiba mais sobre como mover dados do banco de dados Sybase usando o Azure Data Factory. |
| 51 | [Mover dados do Teradata usando o Azure Data Factory](data-factory-onprem-teradata-connector.md) | Saiba mais sobre o conector do Teradata para o serviço do Data Factory que permite mover dados do banco de dados Teradata |
| 52 | [Mover dados para e do SQL Server local ou em IaaS (VM do Azure) usando o Azure Data Factory](data-factory-sqlserver-connector.md) | Saiba mais sobre como mover dados de/para o banco de dados do SQL Server local ou em uma VM do Azure usando o Azure Data Factory. |
| 53 | [Mover dados de uma fonte de tabela da Web usando o Azure Data Factory](data-factory-web-table-connector.md) | Saiba mais sobre como mover dados de uma tabela em uma página da Web usando o Azure Data Factory |



## <a name="data-transformation"></a>Transformação de dados

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 54 | [Criar pipelines preditivos usando as atividades do Aprendizado de Máquina do Azure](data-factory-azure-ml-batch-execution-activity.md) | Descreve como criar pipelines de previsão usando o Azure Data Factory e o Aprendizado de Máquina do Azure |
| 55 | [Serviços vinculados de computação](data-factory-compute-linked-services.md) | Aprenda sobre ambientes de computação que você pode usar em pipelines do Azure Data Factory para transformar/processar dados. |
| 56 | [Processar conjuntos de dados em larga escala usando o Data Factory e o Lote](data-factory-data-processing-using-batch.md) | Descreve como processar grandes volumes de dados em um pipeline do Data Factory do Azure usando o recurso de processamento paralelo do Lote do Azure. |
| 57 | [Transformar dados no Azure Data Factory](data-factory-data-transformation-activities.md) | Aprenda a transformar ou processar dados no Azure Data Factory usando o Hadoop, o Machine Learning ou o Azure Data Lake Analytics. |
| 58 | [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md) | Saiba como usar a Atividade de Transmissão do Hadoop em um Azure Data Factory para executar programas de Transmissão do Hadoop em um cluster HDInsight sob demanda ou em seu próprio cluster HDInsight. |
| 59 | [Atividade de Hive](data-factory-hive-activity.md) | Saiba como usar a atividade de Hive em uma Azure Data Factory para executar consultas de Hive em um cluster sob demanda/próprio de HDInsight. |
| 60 | [Chamar Programas MapReduce da Data Factory](data-factory-map-reduce.md) | Saiba como processar dados executando programas MapReduce em um cluster HDInsight do Azure em uma Azure Data Factory. |
| 61 | [Atividade Pig](data-factory-pig-activity.md) | Saiba como usar a Atividade Pig em uma data factory do Azure para executar scripts Pig em um cluster sob demanda/próprio do HDInsight. |
| 62 | [Invocar Programas Spark pelo Data Factory](data-factory-spark.md) | Aprenda a invocar programas Spark de uma Azure Data Factory usando a atividade MapReduce. |
| 63 | [Atividade de procedimento armazenado do SQL Server](data-factory-stored-proc-activity.md) | Saiba como é possível usar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado em um banco de dados SQL do Azure ou SQL Data Warehouse do Azure de um pipeline de Data Factory. |
| 64 | [Usar atividades personalizadas em um pipeline do Data Factory do Azure](data-factory-use-custom-activities.md) | Saiba como criar atividades personalizadas e usá-las em um pipeline do Azure Data Factory. |
| 65 | [Execute o script U-SQL na Análise Azure Data Lake do Azure Data Factory](data-factory-usql-activity.md) | Saiba como processar dados executando scripts U-SQL no serviço de computação da Análise Azure Data Lake. |



## <a name="samples"></a>Exemplos

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 66 | [Azure Data Factory - Exemplos](data-factory-samples.md) | Fornece detalhes sobre os exemplos fornecidos com o serviço Azure Data Factory. |



## <a name="use-cases"></a>Casos de uso

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 67 | [Estudos de caso de cliente](data-factory-customer-case-studies.md) | Saiba mais sobre como alguns de nossos clientes estão usando o Azure Data Factory. |
| 68 | [Caso de uso - Criação de perfil de cliente](data-factory-customer-profiling-usecase.md) | Saiba como o Azure Data Factory é usado para criar um fluxo de trabalho orientado a dados (pipeline) para criar o perfil de clientes de jogos. |
| 69 | [Caso de uso - recomendações de produtos](data-factory-product-reco-usecase.md) | Saiba mais sobre um caso de uso implementado usando o Azure Data Factory junto com outros serviços. |



## <a name="monitor-and-manage"></a>Monitorar e gerenciar

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 70 | [Monitorar e gerenciar pipelines do Azure Data Factory](data-factory-monitor-manage-pipelines.md) | Saiba como usar o Portal do Azure e o Azure PowerShell para monitorar e gerenciar as data factories e os pipelines do Azure que você criou. |



## <a name="sdk"></a>.

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 71 | [Azure Data Factory - Log de alterações da API .NET](data-factory-api-change-log.md) | Descreve alterações significativas, novos recursos e correções de bugs etc... em uma versão específica de API do .NET para o Azure Data Factory. |
| 72 | [Criar, monitorar e gerenciar data factories do Azure usando o SDK do .NET da Data Factory](data-factory-create-data-factories-programmatically.md) | Aprenda como criar, monitorar e gerenciar as data factories do Azure programaticamente usando o SDK da Data Factory. |
| 73 | [Referência do desenvolvedor da Azure Data Factory](data-factory-sdks.md) | Aprenda maneiras diferentes de criar, monitorar e gerenciar data factories do Azure |



## <a name="miscellaneous"></a>Diversos

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 74 | [Azure Data Factory - Perguntas frequentes](data-factory-faq.md) | Perguntas frequentes sobre o Azure Data Factory. |
| 75 | [Azure Data Factory - Funções e Variáveis do Sistema](data-factory-functions-variables.md) | Fornece uma lista de funções do Azure Data Factory e variáveis do sistema |
| 76 | [Azure Data Factory - Regras de nomenclatura](data-factory-naming-rules.md) | Descreve as regras de nomenclatura para entidades de Data Factory. |
| 77 | [Solucionar problemas da Data Factory](data-factory-troubleshoot.md) | Saiba como solucionar problemas com o uso do Azure Data Factory. |




<!--HONumber=Oct16_HO2-->


