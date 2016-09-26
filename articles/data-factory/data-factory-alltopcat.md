<properties
	pageTitle="Todos os tópicos do serviço de Data Factory | Microsoft Azure"
	description="Tabela de todos os tópicos para o serviço do Azure denominados Data Factory que existem em http://azure.microsoft.com/documentation/articles/, Título e descrição."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="data-factory"
	ms.workload="data-factory"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="spelluru"/>


# Todos os tópicos do serviço Azure Data Factory

Este tópico lista cada tópico que se aplica diretamente ao serviço **Data Factory** do Azure. Você pode pesquisar palavras-chave nesta página da Web usando **Ctrl + F** para encontrar os tópicos de seu interesse atual.




## Novo

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 1 | [Criar seu primeiro data factory do Azure usando a API REST do Data Factory](data-factory-build-your-first-pipeline-using-rest-api.md) | Neste tutorial, você cria um pipeline de exemplo do Azure Data Factory usando a API REST do Data Factory. |
| 2 | [Tutorial: criar um pipeline com Atividade de Cópia usando a API REST](data-factory-copy-activity-tutorial-using-rest-api.md) | Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando a REST API. |
| 3 | [Assistente de Cópia do Data Factory](data-factory-copy-wizard.md) | Saiba mais sobre como usar o Assistente de Cópia do Data Factory para copiar dados de fontes de dados com suporte para coletores. |
| 4 | [Gateway de gerenciamento de dados](data-factory-data-management-gateway.md) | Configure um gateway de dados para mover dados entre o local e a nuvem. Use o Gateway de Gerenciamento de Dados no Azure Data Factory para mover os dados. |
| 5 | [Mover dados de um banco de dados Cassandra local usando o Azure Data Factory](data-factory-onprem-cassandra-connector.md) | Saiba mais sobre como mover dados de um banco de dados Cassandra local usando o Azure Data Factory. |
| 6 | [Mover dados do MongoDB usando o Azure Data Factory](data-factory-on-premises-mongodb-connector.md) | Saiba mais sobre como mover dados do banco de dados MongoDB usando o Azure Data Factory. |
| 7 | [Mover dados do Salesforce usando o Azure Data Factory](data-factory-salesforce-connector.md) | Saiba mais sobre como mover os dados do Salesforce usando o Azure Data Factory. |


## Artigos atualizados

Esta seção lista os artigos que foram atualizados recentemente nos quais a atualização foi grande ou significativa. Para cada artigo atualizado, um trecho aproximado do texto markdown adicionado é exibido. Os artigos foram atualizados dentro do intervalo de datas de **26/07/2016** a **21/08/2016**.

| &nbsp; | Artigo | Texto atualizado, trecho |
| --: | :-- | :-- |
| 8 | [Criar, monitorar e gerenciar data factories do Azure usando o SDK do .NET da Data Factory](data-factory-create-data-factories-programmatically.md) | **Logon sem caixa de diálogo pop-up** O código acima inicia uma caixa de diálogo para inserção das credenciais do Azure. Se você precisar entrar programaticamente sem usar uma caixa de diálogo, consulte Autenticação de uma entidade de serviço com o Azure Resource Manager (resource-group-authenticate-service-principal.md authenticate-service-principal-with-certificate---powershell). **Exemple** Crie o método GetAuthorizationHeaderNoPopup conforme mostrado abaixo: public static string GetAuthorizationHeaderNoPopup() { var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings "ActiveDirectoryTenantId" ); var context = new AuthenticationContext(authority.AbsoluteUri); var credential = new ClientCredential(ConfigurationManager.AppSettings "AdfClientId" , ConfigurationManager.AppSettings "AdfClientSecret" ); AuthenticationResult result = context.AcquireTokenAsync(ConfigurationManager.AppSettings "WindowsManagementUri" , credential).Result; |
| 9 | [Mover dados usando Atividade de Cópia](data-factory-data-movement-activities.md) | **Formatos de arquivo com suporte** A Atividade de Cópia pode copiar os arquivos como estão entre dois armazenamentos de dados como Blob do Azure, o Sistema de Arquivos e o Sistema de Arquivos Distribuído Hadoop (HDFS). Para fazer isso, você pode ignorar a seção de formatação (data-factory-create-datasets.md) nas definições de conjunto de dados de entrada e de saída e os dados serão copiados com eficiência sem qualquer serialização/desserialização. A Atividade de Cópia também lê e grava em arquivos em formatos especificados: texto, Avro, ORC e JSON. Aqui estão alguns exemplos de atividades de cópia que você pode obter: / Copiar dados em formato de texto (CSV) do Blob do Azure e gravá-los no Azure SQL / Copiar arquivos no formato de texto (CSV) do Sistema de Arquivos local e gravá-los no Blob do Azure no formato Avro / Copiar os dados no Banco de Dados SQL do Azure e gravá-los no HDFS local no formato ORC** .a name="global"../a.Movimentação de dados disponíveis globalmente** O serviço alimentando a atividade de cópia está disponível globalmente nas seguintes áreas e regiões geográficas, mesmo que o Azure Data Factory esteja disponível somente nas regiões Oeste dos EUA, Leste dos EUA e Europa Setentrional.|
| 10 | [Mover dados De uma origem de OData usando o Azure Data Factory](data-factory-odata-connector.md) | **Usando a autenticação do Windows para acessar uma fonte OData local** { "name": "inputLinkedService", "properties": { "type": "OData", "typeProperties": { "url": ".endpoint of on-premises OData source e.g. Dynamics CRM.", "authenticationType": "Windows", "username": "domain\\user", "password": "password", "gatewayName": "mygateway" } } } |





## Tutoriais

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 11 | [Tutorial: Criar seu primeiro pipeline para processar dados usando cluster Hadoop](data-factory-build-your-first-pipeline.md) | Este tutorial do Azure Data Factory mostra como criar e agendar um data factory que processa os dados usando o script Hive em um cluster Hadoop. |
| 12 | [Tutorial: compilar sua primeira Azure Data Factory usando o modelo do Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) | Neste tutorial, você criará um pipeline de exemplo do Azure Data Factory usando um modelo do Gerenciador de Recursos do Azure. |
| 13 | [Criar seu primeiro data factory do Azure usando o Editor do Portal/Data Factory do Azure](data-factory-build-your-first-pipeline-using-editor.md) | Neste tutorial, você cria um pipeline de exemplo do Azure Data Factory usando o Data Factory Editor no portal do Azure. |
| 14 | [Compilar sua primeira Azure Data Factory usando o Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) | Neste tutorial, você cria um pipeline de exemplo do Azure Data Factory usando o Azure PowerShell. |
| 15 | [Compilar sua primeira data factory do Azure usando o Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) | Neste tutorial, você cria um pipeline de exemplo do Azure Data Factory usando o Visual Studio. |
| 16 | [Tutorial: Criar um pipeline com a Atividade de Cópia usando o Data Factory Editor](data-factory-copy-activity-tutorial-using-azure-portal.md) | Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Data Factory Editor no portal do Azure. |
| 17 | [Tutorial: Criar um pipeline com a Atividade de Cópia usando o Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) | Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Azure PowerShell. |
| 18 | [Tutorial: Criar um pipeline com a Atividade de Cópia usando o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) | Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Visual Studio. |
| 19 | [Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL usando o Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | Este tutorial mostra como usar a Atividade de Cópia em um pipeline do Azure Data Factory para copiar dados do Armazenamento de Blobs para um banco de dados SQL. |
| 20 | [Tutorial: Criar um pipeline com a Atividade de Cópia usando o Assistente de Cópia do Data Factory](data-factory-copy-data-wizard-tutorial.md) | Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Assistente de Cópia com suporte do Data Factory |



## Movimentação de dados

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 21 | [Mover dados para e do blob do Azure usando o Azure Data Factory](data-factory-azure-blob-connector.md) | Saiba como copiar dados de blob no Azure Data Factory. O(s) exemplo(s) a seguir mostra(m) como copiar dados de e para o Armazenamento de Blobs do Azure e o Banco de Dados SQL do Azure. |
| 22 | [Mover dados para e do Repositório Data Lake do Azure usando o Azure Data Factory](data-factory-azure-datalake-connector.md) | Saiba como mover dados para/do Repositório Data Lake do Azure usando o Azure Data Factory |
| 23 | [Mover dados para e do Banco de Dados de Documentos usando o Azure Data Factory](data-factory-azure-documentdb-connector.md) | Saiba como mover dados para/da coleção Banco de Dados de Documentos do Azure usando o Azure Data Factory |
| 24 | [Mover dados de e para o Banco de Dados SQL do Azure usando o Azure Data Factory](data-factory-azure-sql-connector.md) | Saiba como mover dados para/do Banco de Dados SQL do Azure usando o Azure Data Factory |
| 25 | [Mover dados para e do SQL Data Warehouse do Azure usando o Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) | Saiba como mover dados para/do SQL Data Warehouse do Azure usando o Azure Data Factory |
| 26 | [Mover dados para e da Tabela do Azure \| Azure Data Factory](data-factory-azure-table-connector.md) | Saiba como mover dados para/do Armazenamento de Tabela do Azure usando o Azure Data Factory |
| 27 | [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md) | Conheça os principais fatores que afetam o desempenho de movimentação de dados no Azure Data Factory por meio da atividade de cópia. |
| 28 | [Mover dados usando Atividade de Cópia](data-factory-data-movement-activities.md) | Saiba mais sobre a movimentação de dados em pipelines do Data Factory: migração de dados entre armazenamentos em nuvem, entre os locais e a nuvem. Usar a Atividade de Cópia. |
| 29 | [Notas de versão para o Gateway de Gerenciamento de Dados](data-factory-gateway-release-notes.md) | Notas de versão do Gateway de Gerenciamento de Dados |
| 30 | [Mover dados do HDFS local usando o Azure Data Factory](data-factory-hdfs-connector.md) | Saiba mais sobre como mover dados do HDFS local usando o Azure Data Factory |
| 31 | [Monitorar e gerenciar os pipelines do Azure Data Factory usando novo Aplicativo de Monitoramento e Gerenciamento](data-factory-monitor-manage-app.md) | Saiba como usar o Aplicativo de Monitoramento e Gerenciamento para monitorar e gerenciar data factories e pipelines do Azure. |
| 32 | [Mover dados entre fontes locais e a nuvem com o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) | Configure um gateway de dados para mover dados entre o local e a nuvem. Use o Gateway de Gerenciamento de Dados no Azure Data Factory para mover os dados. |
| 33 | [Mover dados De uma origem de OData usando o Azure Data Factory](data-factory-odata-connector.md) | Saiba mais sobre como mover dados de fontes OData usando o Azure Data Factory. |
| 34 | [Mover dados de armazenamentos de dados ODBC usando o Azure Data Factory](data-factory-odbc-connector.md) | Saiba mais sobre como mover dados de armazenamentos de dados ODBC usando o Azure Data Factory. |
| 35 | [Mover dados do DB2 usando o Azure Data Factory](data-factory-onprem-db2-connector.md) | Saiba mais sobre como mover dados do banco de dados DB2 usando o Azure Data Factory |
| 36 | [Mover dados para e do sistema de arquivos local usando o Azure Data Factory](data-factory-onprem-file-system-connector.md) | Aprenda como mover dados para/do sistema de arquivos local usando o Azure Data Factory |
| 37 | [Mover dados do MySQL usando o Azure Data Factory](data-factory-onprem-mysql-connector.md) | Saiba mais sobre como mover dados do banco de dados MySQL usando o Azure Data Factory |
| 38 | [Mover dados para dentro e fora do Oracle local usando o Azure Data Factory](data-factory-onprem-oracle-connector.md) | Aprenda a mover dados de/para o banco de dados da Oracle que está no local usando o Azure Data Factory. |
| 11,8 | [Mover dados do PostgreSQL usando o Azure Data Factory](data-factory-onprem-postgresql-connector.md) | Saiba mais sobre como mover dados do banco de dados PostgreSQL usando o Azure Data Factory |
| 40 | [Mover dados do Sybase usando o Azure Data Factory](data-factory-onprem-sybase-connector.md) | Saiba mais sobre como mover dados do banco de dados Sybase usando o Azure Data Factory. |
| 41 | [Mover dados do Teradata usando o Azure Data Factory](data-factory-onprem-teradata-connector.md) | Saiba mais sobre o conector do Teradata para o serviço do Data Factory que permite mover dados do banco de dados Teradata |
| 42 | [Mover dados para e do SQL Server local ou em IaaS (VM do Azure) usando o Azure Data Factory](data-factory-sqlserver-connector.md) | Saiba mais sobre como mover dados de/para o banco de dados do SQL Server local ou em uma VM do Azure usando o Azure Data Factory. |
| 43 | [Mover dados de uma fonte de tabela da Web usando o Azure Data Factory](data-factory-web-table-connector.md) | Saiba mais sobre como mover dados de uma tabela em uma página da Web usando o Azure Data Factory |



## Transformação de dados

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 44 | [Criar pipelines preditivos usando as atividades do Aprendizado de Máquina do Azure](data-factory-azure-ml-batch-execution-activity.md) | Descreve como criar pipelines de previsão usando o Azure Data Factory e o Aprendizado de Máquina do Azure |
| 45 | [Serviços vinculados de computação](data-factory-compute-linked-services.md) | Aprenda sobre ambientes de computação que você pode usar em pipelines do Azure Data Factory para transformar/processar dados. |
| 46 | [Processar conjuntos de dados em larga escala usando o Data Factory e o Lote](data-factory-data-processing-using-batch.md) | Descreve como processar grandes volumes de dados em um pipeline do Data Factory do Azure usando o recurso de processamento paralelo do Lote do Azure. |
| 47 | [Saiba mais sobre transformação e análise de dados no Azure Data Factory](data-factory-data-transformation-activities.md) | Aprenda sobre a transformação de dados no Azure Data Factory. Transformar e processar dados no cluster HDInsight do Azure ou um lote do Azure. |
| 48 | [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md) | Saiba como usar a Atividade de Transmissão do Hadoop em um Azure Data Factory para executar programas de Transmissão do Hadoop em um cluster HDInsight sob demanda ou em seu próprio cluster HDInsight. |
| 49 | [Atividade de Hive](data-factory-hive-activity.md) | Saiba como usar a atividade de Hive em uma Azure Data Factory para executar consultas de Hive em um cluster sob demanda/próprio de HDInsight. |
| 50 | [Chamar Programas MapReduce da Data Factory](data-factory-map-reduce.md) | Saiba como processar dados executando programas MapReduce em um cluster HDInsight do Azure em uma Azure Data Factory. |
| 51 | [Atividade Pig](data-factory-pig-activity.md) | Saiba como usar a Atividade Pig em uma data factory do Azure para executar scripts Pig em um cluster sob demanda/próprio do HDInsight. |
| 52 | [Invocar Programas Spark pelo Data Factory](data-factory-spark.md) | Aprenda a invocar programas Spark de uma Azure Data Factory usando a atividade MapReduce. |
| 53 | [Atividade de procedimento armazenado do SQL Server](data-factory-stored-proc-activity.md) | Saiba como é possível usar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado em um banco de dados SQL do Azure ou SQL Data Warehouse do Azure de um pipeline de Data Factory. |
| 54 | [Usar atividades personalizadas em um pipeline do Data Factory do Azure](data-factory-use-custom-activities.md) | Saiba como criar atividades personalizadas e usá-las em um pipeline do Azure Data Factory. |
| 55 | [Execute o script U-SQL na Análise Azure Data Lake do Azure Data Factory](data-factory-usql-activity.md) | Saiba como processar dados executando scripts U-SQL no serviço de computação da Análise Azure Data Lake. |



## Exemplos

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 56 | [Azure Data Factory - Exemplos](data-factory-samples.md) | Fornece detalhes sobre os exemplos fornecidos com o serviço Azure Data Factory. |



## Casos de uso

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 57 | [Estudos de caso de cliente](data-factory-customer-case-studies.md) | Saiba mais sobre como alguns de nossos clientes estão usando o Azure Data Factory. |
| 58 | [Caso de uso - Criação de perfil de cliente](data-factory-customer-profiling-usecase.md) | Saiba como o Azure Data Factory é usado para criar um fluxo de trabalho orientado a dados (pipeline) para criar o perfil de clientes de jogos. |
| 59 | [Caso de uso - recomendações de produtos](data-factory-product-reco-usecase.md) | Saiba mais sobre um caso de uso implementado usando o Azure Data Factory junto com outros serviços. |



## Monitorar e gerenciar

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 60 | [Monitorar e gerenciar pipelines do Azure Data Factory](data-factory-monitor-manage-pipelines.md) | Saiba como usar o Portal do Azure e o Azure PowerShell para monitorar e gerenciar as data factories e os pipelines do Azure que você criou. |



## .

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 61 | [Azure Data Factory - Log de alterações da API .NET](data-factory-api-change-log.md) | Descreve alterações significativas, novos recursos e correções de bugs etc... em uma versão específica de API do .NET para o Azure Data Factory. |
| 62 | [Criar, monitorar e gerenciar data factories do Azure usando o SDK do .NET da Data Factory](data-factory-create-data-factories-programmatically.md) | Aprenda como criar, monitorar e gerenciar as data factories do Azure programaticamente usando o SDK da Data Factory. |
| 63 | [Referência do desenvolvedor da Azure Data Factory](data-factory-sdks.md) | Aprenda maneiras diferentes de criar, monitorar e gerenciar data factories do Azure |



## Diversos

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 64 | [Azure Data Factory - Perguntas frequentes](data-factory-faq.md) | Perguntas frequentes sobre o Azure Data Factory. |
| 65 | [Azure Data Factory - Funções e Variáveis do Sistema](data-factory-functions-variables.md) | Fornece uma lista de funções do Azure Data Factory e variáveis do sistema |
| 66 | [Azure Data Factory - Regras de nomenclatura](data-factory-naming-rules.md) | Descreve as regras de nomenclatura para entidades de Data Factory. |
| 67 | [Solucionar problemas da Data Factory](data-factory-troubleshoot.md) | Saiba como solucionar problemas com o uso do Azure Data Factory. |

<!---HONumber=AcomDC_0914_2016-->