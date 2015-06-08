<properties 
	pageTitle="Usar atividades personalizadas em um pipeline do Data Factory do Azure" 
	description="Saiba como criar atividades personalizadas e usá-las em um pipeline do Azure Data Factory." 
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
	ms.date="06/02/2015" 
	ms.author="spelluru"/>

# Usar atividades personalizadas em um pipeline do Data Factory do Azure
Fábrica de dados do Azure oferece suporte a atividades internas como **cópia atividade** e **HDInsight atividade** a ser usado em pipelines de mover e processar dados. Você também pode criar uma atividade personalizada do .NET com sua própria lógica de transformação/processamento e usar a atividade em um pipeline. Você pode configurar a atividade seja executada usando um **Azure HDInsight** cluster ou um **Azure lote** service.

Este artigo descreve como criar uma atividade personalizada e usá-la em um pipeline do Data Factory do Azure. Ele também fornece uma explicação detalhada com instruções passo a passo para criar e usar uma atividade personalizada. Passo a passo usa o serviço HDInsight vinculado. Para usar o lote Azure vinculado serviço em vez disso, você cria um serviço vinculado do tipo **AzureBatchLinkedService** e usá-lo na seção de atividade do pipeline JSON (* * linkedServiceName * *). Consulte o [Service vinculado do Azure lote](#AzureBatch) seção para obter detalhes sobre como usar o Azure lote com a atividade personalizada.

## Pré-requisitos
Baixe o versão mais recente [pacote do NuGet para Azure Data Factory][nuget-package] e instalá-lo. As instruções estão no [passo a passo](#SupportedSourcesAndSinks) neste artigo.

## Criando uma atividade personalizada

Para criar uma atividade personalizada:
 
1.	Criar um **biblioteca de classes** projeto no Visual Studio 2013.
3. Adicione o seguinte usando instruções na parte superior do arquivo de origem na biblioteca de classes.
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. Atualize a classe para implementar o **IDotNetActivity** interface.
	<ol type='a'>
	<li>
		Derive a classe de <b>IDotNetActivity</b>.
		<br/>
		Exemplo: <br/>
		classe pública <b>MyDotNetActivity: IDotNetActivity</b>
	</li>

	<li>
		Implemente o <b>Execute</b> método <b>IDotNetActivity</b> interface
	</li>

</ol>
5. Compile o projeto.


## Usando a atividade personalizada em um pipeline
Para usar a atividade personalizada em um pipeline:

1.	**Compactar** todos os arquivos binários do **bin\debug** ou **bin\release** pastas para o projeto de saída. 
2.	**Carregar o CEP** arquivo como um blob de sua **armazenamento de BLOBs do Azure**. 
3.	Atualização de **pipeline JSON** arquivo consultem o arquivo zip, DLL de atividade personalizada, a classe de atividade e o blob que contém o arquivo zip no pipeline de JSON. No arquivo JSON:
	<ol type ="a">
	<li><b>Tipo de atividade</b> deve ser definido como <b>DotNetActivity</b>.</li>
	<li><b>AssemblyName</b> é o nome da DLL saída do projeto do Visual Studio.</li>
	<li><b>EntryPoint</b> Especifica o <b>namespace</b> e <b>nome</b> do <b>classe</b> que implementa o <b>IDotNetActivity</b> interface.</li>
	<li><b>PackageLinkedService</b> é o serviço vinculado que faz referência ao blob que contém o arquivo zip. </li>
	<li><b>PackageFile</b> Especifica o local e o nome do arquivo zip que foi carregado para o armazenamento de BLOBs do Azure.</li>
	<li><b>LinkedServiceName</b> é o nome do serviço vinculado que vincula a um cluster HDInsight (sob demanda ou seu próprio) para uma fábrica de dados. A atividade personalizada é executada como um trabalho somente de mapa no cluster HDInsight especificado.</li>
</ol>**Exemplo JSON parcial**

		"Name": "MyDotNetActivity",
    	"Type": "DotNetActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyDotNetActivity.dll",
    	    "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",

## Atualizando uma atividade personalizada
Se você atualizar o código para a atividade personalizada, compilá-lo e carregar o arquivo zip que contém os novos binários para o armazenamento de blob.

## <a name="walkthrough" /> Instruções passo a passo
Este passo a passo fornece instruções passo a passo para criar uma atividade personalizada e usar a atividade em um pipeline de fábrica de dados do Azure. Este passo a passo estende o tutorial a [Introdução ao Azure Data Factory][adfgetstarted]. Se você quiser ver a atividade personalizada trabalhando, precisará percorrer o tutorial de Introdução primeiro e, em seguida, seguir este passo a passo.

**Pré-requisitos:**


- Tutorial de [Introdução ao Azure Data Factory][adfgetstarted]. Você deve concluir o tutorial deste artigo antes de executar este passo a passo.
- Visual Studio 2012 ou 2013
- Baixe e instale o [Azure .NET SDK][azure-developer-center]
- Baixe o versão mais recente [pacote do NuGet para Azure Data Factory][nuget-package] e instalá-lo. As instruções estão no passo a passo.
- Baixe e instale o pacote NuGet para armazenamento do Azure. As instruções estão no passo a passo, portanto você pode ignorar esta etapa.

## Etapa 1: Criar uma atividade personalizada

1.	Crie um projeto de biblioteca de classes do .NET.
	<ol type="a">
	<li>Iniciar <b>Visual Studio 2012</b> ou <b>Visual Studio 2013</b>.</li>
	<li>Clique em <b>arquivo</b>, aponte para <b>novo</b>, e clique em <b>projeto</b>.</li> 
	<li>Expanda <b>modelos</b>, e selecione <b>Visual C#</b>. Neste passo a passo, você pode usar C#, mas você pode usar qualquer linguagem .NET para desenvolver a atividade personalizada.</li> 
	<li>Selecione <b>biblioteca de classes</b> da lista de tipos de projeto à direita.</li>
	<li>Digite <b>MyDotNetActivity</b> para o <b>nome</b>.</li> 
	<li>Selecione <b>C:\ADFGetStarted</b> para o <b>local</b>.</li>
	<li>Clique em <b>OK</b> para criar o projeto.</li>
</ol>
2.  Clique em <b>ferramentas</b>, aponte para <b>NuGet Package Manager</b>, e clique em <b>Package Manager Console</b>.
3.	No <b>Package Manager Console</b>, execute o seguinte comando para importar <b>Microsoft.Azure.Management.DataFactories</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre

3.	No <b>Package Manager Console</b>, execute o seguinte comando para importar <b>Microsoft.DataFactories.Runtime</b>. Substitua a pasta pelo local que contém o pacote NuGet do Data Factory baixado.

		Install-Package Microsoft.DataFactories.Runtime –Pre

4. Importe o pacote do NuGet do armazenamento do Azure para o projeto.

		Install-Package Azure.Storage

5. Adicione o seguinte **usando** instruções para o arquivo de origem no projeto.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. Alterar o nome do **namespace** para **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7. Alterar o nome da classe para **MyDotNetActivity** e derivar a partir de **IDotNetActivity** interface conforme mostrado abaixo.

		public class MyDotNetActivity : IDotNetActivity

8. Implementar (Adicionar) o **Execute** método o **IDotNetActivity** interface para o **MyDotNetActivity** classe e copie o seguinte código de exemplo para o método.

	O **inputTables** e **outputTables** parâmetros representam tabelas de entrada e saídas da atividade, como os nomes sugerem. Você pode ver mensagens de log usando o **agente** objeto no arquivo de log que você pode baixar do portal do Azure ou usando cmdlets. O **extendedProperties** dicionário contém a lista de propriedades estendidas que você especificar no arquivo JSON para a atividade e seus valores.

	O código de exemplo a seguir conta o número de linhas no blob de entrada e produz o seguinte conteúdo no blob de saída: caminho do blob, o número de linhas no blob, a máquina em que a atividade foi executada, data e hora atuais.

        public IDictionary<string, string> Execute(
                    IEnumerable<ResolvedTable> inputTables, 
                    IEnumerable<ResolvedTable> outputTables, 
                    IDictionary<string, string> extendedProperties, 
                    IActivityLogger logger)
        {
            string output = string.Empty;

            logger.Write(TraceEventType.Information, "Before anything...");

            logger.Write(TraceEventType.Information, "Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write(TraceEventType.Information, "<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            foreach (ResolvedTable inputTable in inputTables)
            {
                string connectionString = GetConnectionString(inputTable.LinkedService);
                string folderPath = GetFolderPath(inputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Reading blob from: {0}", folderPath);

                CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
                CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

                BlobContinuationToken continuationToken = null;

                do
                {
                    BlobResultSegment result = inputClient.ListBlobsSegmented(folderPath, 
												true, 
												BlobListingDetails.Metadata, 
												null, 
												continuationToken, 
												null, 
												null);
                    foreach (IListBlobItem listBlobItem in result.Results)
                    {
                        CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                        int count = 0;
                        if (inputBlob != null)
                        {
                            using (StreamReader sr = new StreamReader(inputBlob.OpenRead()))
                            {
                                while (!sr.EndOfStream)
                                {
                                    string line = sr.ReadLine();
                                    if (count == 0)
                                    {
                                        logger.Write(TraceEventType.Information, "First line: [{0}]", line);
                                    }
                                    count++;
                                }

                            }

                        }
                        output += string.Format(CultureInfo.InvariantCulture,
                                        "{0},{1},{2},{3},{4}\n",
                                        folderPath,
                                        inputBlob.Name,
                                        count,
                                        Environment.MachineName,
                                        DateTime.UtcNow);

                    }
                    continuationToken = result.ContinuationToken;

                } while (continuationToken != null);
            }

            foreach (ResolvedTable outputTable in outputTables)
            {
                string connectionString = GetConnectionString(outputTable.LinkedService);
                string folderPath = GetFolderPath(outputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Writing blob to: {0}", folderPath);

                CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
                Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

                CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
                outputBlob.UploadText(output);

            }
            return new Dictionary<string, string>();

        }

9. Adicione os seguintes métodos auxiliares. O **Execute** método chama esses métodos auxiliares. O **GetConnectionString** método recupera a cadeia de caracteres de conexão do armazenamento do Azure e o **GetFolderPath** método recupera o local do blob.


        private static string GetConnectionString(LinkedService asset)
        {
            AzureStorageLinkedService storageAsset;
            if (asset == null)
            {
                return null;
            }

            storageAsset = asset.Properties as AzureStorageLinkedService;
            if (storageAsset == null)
            {
                return null;
            }

            return storageAsset.ConnectionString;
        }

        private static string GetFolderPath(Table dataArtifact)
        {
            AzureBlobLocation blobLocation;
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            blobLocation = dataArtifact.Properties.Location as AzureBlobLocation;
            if (blobLocation == null)
            {
                return null;
            }

            return blobLocation.FolderPath;
        }
   


10. Compile o projeto. Clique em **criar** no menu e clique em **Build Solution**.
11. Iniciar **Windows Explorer**, e navegue até **bin\debug** ou **bin\release** dependendo do tipo da compilação.
12. Criar um arquivo zip **MyDotNetActivity.zip** que contêm todos os binários no <project folder>pasta \bin\Debug.
13. Carregar **MyDotNetActivity.zip** como um blob para o contêiner de blob: **customactvitycontainer** no Azure armazenamento de blob que o **MyBlobStore** vinculado de serviço no **ADFTutorialDataFactory** usa. Criar o contêiner de blob **blobcustomactivitycontainer** se ele ainda não existir. 


## Etapa 2: Usar a atividade personalizada em um pipeline
Aqui estão as etapas que você realizará nesta etapa:

1. Crie um serviço vinculado para o cluster HDInsight em que a atividade personalizada será executado como um trabalho somente de mapa. 
2. Crie uma tabela de saída que produzirá o pipeline neste exemplo.
3. Criar e executar um pipeline que usa a atividade personalizado que você criou na etapa 1. 
 
### Criar um serviço vinculado para o cluster HDInsight que será usada para executar a atividade personalizada
O serviço Data Factory do Azure dá suporte à criação de um cluster sob demanda e o usa para processar entrada a fim de gerar dados de saída. Você também pode usar seu próprio cluster para fazer isso. Quando você usa o cluster HDInsight sob demanda, um cluster é criado para cada fatia. Ao passo que, se você usar seu próprio cluster HDInsight, o cluster estará pronto para processar a fatia imediatamente. Portanto, quando você usar cluster sob demanda, não verá os dados de saída mais rapidamente que quando usa seu próprio cluster.

> [AZURE.NOTE]Em tempo de execução, uma instância de uma atividade do .NET é executada somente em um nó de trabalho no cluster HDInsight. ele não pode ser dimensionado para ser executado em vários nós. Podem executar várias instâncias de atividade do .NET em paralelo em diferentes nós do cluster do HDInsight.

Se você tiver estendido o [Introdução ao Azure Data Factory][adfgetstarted] tutorial com a explicação passo a passo de [usar Pig e Hive com o Azure dados Factory][hivewalkthrough], você pode pular a criação desse serviço vinculado e usar o serviço vinculado tiver o ADFTutorialDataFactory.


#### Para usar um cluster HDInsight sob demanda

1. No **Portal Azure**, clique em **autor e implantar** na home page do alocador de dados.
2. No Editor de fábrica de dados, clique em **nova computação** da barra de comandos e selecione **cluster HDInsight sob demanda** no menu.
2. Faça o seguinte no script JSON: 
	1. Para o **clusterSize** propriedade, especifique o tamanho do cluster do HDInsight.
	2. Para o **jobsContainer** propriedade, especifique o nome do recipiente padrão onde os logs de cluster serão armazenados. Para fins deste tutorial, especifique **adfjobscontainer**.
	3. Para o **timeToLive** propriedade, especifique quanto tempo o cliente pode ficar ocioso antes de serem excluído. 
	4. Para o **versão** propriedade, especifique a versão do HDInsight que você deseja usar. Se você excluir essa propriedade, a versão mais recente é usada.  
	5. Para o **linkedServiceName**, especifique **StorageLinkedService** que você tivesse criado em Get tutorial de Introdução. 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. Clique em **Deploy** na barra de comandos para implantar o serviço vinculado.
   
#### Para usar seu próprio cluster HDInsight: 

1. No **Portal Azure**, clique em **autor e implantar** na home page do alocador de dados.
2. No **dados fábrica de Editor**, clique em **nova computação** da barra de comandos e selecione **cluster HDInsight** no menu.
2. Faça o seguinte no script JSON: 
	1. Para o **clusterUri** propriedade, digite a URL para o HDInsight. Por exemplo: https://<clustername>.azurehdinsight.net/     
	2. Para o **nome de usuário** propriedade, digite o nome de usuário que tenha acesso ao cluster HDInsight.
	3. Para o **senha** propriedade, digite a senha do usuário. 
	4. Para o **LinkedServiceName** propriedade, digite **StorageLinkedService**. Este é o serviço vinculado que você tivesse criado no tutorial do guia de Introdução. 

2. Clique em **Deploy** na barra de comandos para implantar o serviço vinculado.

### Criar uma tabela de saída

1. No **editor de fábrica dados**, clique em **novo conjunto de dados**, e, em seguida, clique em **armazenamento de BLOBs do Azure** na barra de comandos.
2. Substitua o script JSON no painel à direita com o script JSON a seguir:

		{
    		"name": "OutputTableForCustom",
    		"properties":
    		{
        		"location": 
        		{
					"type": "AzureBlobLocation",
					"folderPath": "adftutorial/customactivityoutput/{Slice}",
					"partitionedBy": [ { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }],

					"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1
        		}   
    		}
		}


 	Local de saída é **customactivityoutput/adftutorial/AAAAMMDDHH/** onde AAAAMMDDHH é o ano, mês, data e hora da fatia sendo produzida. Consulte [referência do desenvolvedor][adf-developer-reference] para obter detalhes.

2. Clique em **Deploy** na barra de comando para implantar a tabela.


### Criar e executar um pipeline que usa a atividade personalizada
   
1. No Editor de fábrica de dados, clique em **novo pipeline** na barra de comandos. Se você não vir o comando, clique em **... (Reticências)** Para vê-lo. 
2. Substitua o seguinte script JSON JSON no painel à direita. Se você quiser usar seu próprio cluster e seguido as etapas para criar o **HDInsightLinkedService** vinculado de serviço, substitua **HDInsightOnDemandLinkedService** com **HDInsightLinkedService** em JSON a seguir. 

		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyDotNetActivity",
                     	"Type": "DotNetActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "HDInsightLinkedService",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyDotNetActivity.dll",
                            "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
                            "ExtendedProperties":
							{
								"SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
							}
                      	},
                        "Policy":
                        {
                        	"Concurrency": 1,
                            "ExecutionPriorityOrder": "OldestFirst",
                            "Retry": 3,
                            "Timeout": "00:30:00",
                            "Delay": "00:00:00"		
						}
					}
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
			}
		}

	> [AZURE.NOTE]Substitua **StartDateTime** valor com três dias antes do dia atual e **EndDateTime** valor com o dia atual. StartDateTime e EndDateTime devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A tabela de saída está agendada para ser produzida diariamente, então haverá três fatias produzidas.

	Observe o seguinte:

	- Há uma atividade na seção de atividades e é do tipo: **DotNetActivity**.
	- Use a mesma tabela de entrada **EmpTableFromBlob** que você usou em Get tutorial de Introdução.
	- Use uma nova tabela de saída **OutputTableForCustom** que você criará na próxima etapa.
	- **AssemblyName** é definido como o nome da DLL: **MyActivities.dll**.
	- **EntryPoint** é definido como **MyDotNetActivityNS.MyDotNetActivity**.
	- **PackageLinkedService** é definido como **MyBlobStore** que foi criado como parte do tutorial do [Introdução ao Azure Data Factory][adfgetstarted]. Esse armazenamento de blob contém o arquivo zip da atividade personalizada.
	- **PackageFile** é definido como **customactivitycontainer/MyDotNetActivity.zip**.
     
4. Clique em **Deploy** na barra de comando para implantar o pipeline.
8. Verifique se os arquivos de saída são gerados no armazenamento de blob no **adftutorial** contêiner.

	![saída de atividade personalizada][image-data-factory-ouput-from-custom-activity]

9. Se você abrir o arquivo de saída, verá uma saída semelhante ao seguinte:
	
	adftutorial/,Emp.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(local de blob), (nome do blob) (número de linhas no blob) (nó em que a atividade foi executada), (carimbo de data)

10.	Use o [Portal Azure][azure-preview-portal] ou cmdlets do PowerShell do Azure para monitorar sua fábrica de dados, pipelines e conjuntos de dados. Você pode ver mensagens de **ActivityLogger** no código para a atividade personalizada nos logs, você pode baixar do portal ou usando os cmdlets.

	![baixar logs de atividade personalizada][image-data-factory-download-logs-from-custom-activity]
   
Consulte [Introdução ao Azure Data Factory][adfgetstarted] para obter etapas detalhadas para monitoramento de conjuntos de dados e pipelines.
    
## <a name="AzureBatch"></a> Usar o Azure lote vinculadas a serviço 
> [AZURE.NOTE]Consulte [Visão geral técnica do Azure lote][batch-technical-overview] para obter uma visão geral do Azure lote de serviço e consulte [Noções básicas sobre a biblioteca de lote do Azure para .NET][batch-get-started] para iniciar rapidamente com o serviço de lote do Azure.

Aqui estão as etapas de alto nível para usar o serviço de vinculado de lote do Azure no passo a passo descrita na seção anterior:

1. Crie uma conta de lote do Azure usando o Portal de gerenciamento do Azure. Consulte [Visão geral técnica do Azure lote][batch-create-account] artigo para obter instruções. Anote a chave de nome e uma conta da conta do Azure lote. 

	Você também pode usar [novo AzureBatchAccount][new-azure-batch-account] cmdlet para criar uma conta de lote do Azure. Consulte [usando o Azure PowerShell para gerenciar conta do Azure lote][azure-batch-blog] para obter instruções detalhadas sobre como usar este cmdlet. 
2. Crie um pool de lote do Azure. Você pode baixar e usar o [ferramenta Gerenciador de lote do Azure][batch-explorer] (ou) usar [biblioteca de lote do Azure para .NET][batch-net-library] para criar um pool de lote do Azure. Consulte [Azure lote Explorer exemplo passo a passo][batch-explorer-walkthrough] para obter instruções passo a passo para usar o Gerenciador de lote do Azure.
	
	Você também pode usar [novo AzureBatchPool][new-azure-batch-pool] cmdlet para criar um pool de lote do Azure.

2. Crie um serviço de vinculado em lotes do Azure usando o seguinte modelo JSON. O Editor de fábrica de dados exibe um modelo semelhante para você começar com. Especifique o nome da conta de lote do Azure, o pool e nome da conta no trecho de JSON.

		{
		    "name": "AzureBatchLinkedService",
		    "properties": {
		        "type": "AzureBatchLinkedService",
		        "accountName": "<Azure Batch account name>",
		        "accessKey": "<Azure Batch account key>",
		        "poolName": "<Azure Batch pool name>",
		        "linkedServiceName": "<Specify associated storage linked service reference here>"
		  }
		}

	Consulte [tópico do MSDN vinculado de serviço do Azure lote](https://msdn.microsoft.com/library/mt163609.aspx) para obter descrições dessas propriedades.

2.  No Editor de fábrica de dados, abra a definição de JSON para o pipeline criado no passo a passo e substituir **HDInsightLinkedService** com **AzureBatchLinkedService**.
3.  Você talvez queira alterar as horas de início e fim do pipeline, para que você possa testar o cenário com o serviço de lote do Azure. 
4.  Você pode ver as tarefas em lote do Azure associadas ao processamento as fatias no Gerenciador de lote do Azure, conforme mostrado no diagrama a seguir.

	![Tarefas em lote do Azure][image-data-factory-azure-batch-tasks]

## Consulte também

[Atualizações de fábrica de dados do azure: atividades de executar o ADF personalizada .NET usando o Azure lote](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

[batch-net-library]: ./batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ./batch/batch-technical-overview.md/#batch-concepts
[batch-technical-overview]: ./batch/batch-technical-overview.md
[batch-get-started]: ./batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-get-started.md
[hivewalkthrough]: data-factory-pig-hive-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png

<!---HONumber=GIT-SubDir-->