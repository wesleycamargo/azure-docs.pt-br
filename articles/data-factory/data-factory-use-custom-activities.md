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
	ms.date="10/06/2015"
	ms.author="spelluru"/>

# Usar atividades personalizadas em um pipeline do Data Factory do Azure
O Azure Data Factory dá suporte a atividades internas como **Atividade de Cópia** e **Atividade do HDInsight** a ser usadas em pipelines para mover e processar dados. Você também pode criar uma atividade personalizada do .NET com sua própria lógica de processamento/transformação e usar a atividade em um pipeline. Você pode configurar a atividade para que seja executada usando um cluster **Azure HDInsight** ou um serviço **Azure Batch**.

Este artigo descreve como criar uma atividade personalizada e usá-la em um pipeline do Data Factory do Azure. Ele também fornece uma explicação detalhada com instruções passo a passo para criar e usar uma atividade personalizada. O passo a passo usa o serviço vinculado HDInsight. Para em vez disso usar o serviço vinculado Azure Batch, você cria um serviço vinculado do tipo **AzureBatchLinkedService** e utiliza-o na seção de atividade do pipeline JSON (**linkedServiceName**). Consulte a seção [Serviço vinculado Azure Batch](#AzureBatch) para obter detalhes sobre como usar o Azure Batch com a atividade personalizada.


## <a name="walkthrough" /> Guia passo a passo
Este passo a passo fornece instruções passo a passo para criar uma atividade personalizada e usá-la em um pipeline do Azure Data Factory. Este passo a passo estende o tutorial de [Introdução ao Azure Data Factory][adfgetstarted]. Se você quiser ver a atividade personalizada trabalhando, precisará percorrer o tutorial de Introdução primeiro e, em seguida, seguir este passo a passo.

**Pré-requisitos:**


- Tutorial de [Introdução ao Azure Data Factory][adfgetstarted]. Você deve concluir o tutorial deste artigo antes de seguir este passo a passo.
- Visual Studio 2012 ou 2013
- Baixar e instalar o [SDK .NET do Azure][azure-developer-center]
- Baixe o mais recente [Pacote NuGet para o Azure Data Factory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/) e instale-o. As instruções estão no passo a passo.
- Baixe e instale o pacote NuGet para armazenamento do Azure. As instruções estão no passo a passo, portanto você pode ignorar esta etapa.

## Etapa 1: Criar uma atividade personalizada

1.	Crie um projeto de biblioteca de classes do .NET.
	<ol type="a">
	<li>Inicie o <b>Visual Studio 2012</b> ou <b>Visual Studio 2013</b>.</li>
	<li>Clique em <b>Arquivo</b>, indique <b>Novo</b> e, em seguida, clique em <b>Projeto</b>.</li>
	<li>Expanda <b>Modelos</b> e selecione <b>Visual C#</b>. Neste passo a passo, você pode usar C#, mas você pode usar qualquer linguagem .NET para desenvolver a atividade personalizada.</li>
	<li>Selecione <b>Biblioteca de Classes</b> na lista de tipos de projeto à direita.</li>
	<li>Insira <b>MyDotNetActivity</b> como o <b>Nome</b>.</li>
	<li>Selecione <b>C:\ADFGetStarted</b> como o <b>Local</b>.</li>
	<li>Clique em <b>OK</b> para criar o projeto.</li>
</ol>
2.  Clique em <b>Ferramentas</b>, aponte para <b>Gerenciador de Pacotes NuGet</b> e clique em <b>Console do Gerenciador de Pacotes</b>.
3.	No <b>Console do Gerenciador de Pacotes</b>, execute o comando a seguir para importar <b>Microsoft.Azure.Management.DataFactories</b>.

		Install-Package Microsoft.Azure.Management.DataFactories

4. Importe o pacote NuGet do Armazenamento do Azure para o projeto.

		Install-Package Azure.Storage

5. Adicione as instruções **using** a seguir ao arquivo de origem no projeto.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
		using System.Linq;

		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Runtime;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

6. Altere o nome do **namespace** para **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7. Altere o nome da classe para **MyDotNetActivity** e derive-a da interface **IDotNetActivity**, conforme mostrado abaixo.

		public class MyDotNetActivity : IDotNetActivity

8. Implemente (Adicione) o método **Execute** da interface **IDotNetActivity** à classe **MyDotNetActivity** e copie o seguinte código de exemplo para o método.

	O código de exemplo a seguir conta o número de linhas no blob de entrada e produz o conteúdo a seguir no blob de saída: caminho para o blob, número de linhas no blob, computador em que a atividade foi executada, data e hora atuais.

		public IDictionary<string, string> Execute(IEnumerable<LinkedService> linkedServices, IEnumerable<Dataset> datasets, Activity activity, IActivityLogger logger)
        {
            IDictionary<string, string> extendedProperties = ((DotNetActivity)activity.TypeProperties).ExtendedProperties;

            AzureStorageLinkedService inputLinkedService, outputLinkedService;
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

            // using First method instead of Single since we are using the same
            // Azure Storage linked service for input and output.
            inputLinkedService = linkedServices.First(linkedService => linkedService.Name == inputDataset.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;

            string output = string.Empty;

            logger.Write("Before anything...");

            logger.Write("Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            string connectionString = GetConnectionString(inputLinkedService);
            string folderPath = GetFolderPath(inputDataset);

            logger.Write("Reading blob from: {0}", folderPath);

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
                                    logger.Write("First line: [{0}]", line);
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

            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;
            outputLinkedService = linkedServices.First(linkedService => linkedService.Name == outputDataset.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;

            connectionString = GetConnectionString(outputLinkedService);
            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to: {0}", folderPath);

            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            outputBlob.UploadText(output);

            return new Dictionary<string, string>();

        }

9. Adicione os seguintes métodos auxiliares. O método **Execute** invoca esses métodos auxiliares. O método **GetConnectionString** recupera a cadeia de conexão do armazenamento do Azure e o método **GetFolderPath** recupera o local do blob.


        private static string GetConnectionString(AzureStorageLinkedService asset)
        {

            if (asset == null)
            {
                return null;
            }

            return asset.ConnectionString;
        }

        private static string GetFolderPath(Dataset dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FolderPath;
        }


10. Compile o projeto. Clique em **Compilar** no menu e clique em **Compilar Solução**.
11. Inicie o **Windows Explorer** e navegue até a pasta **bin\\debug** ou **bin\\release** dependendo do tipo da compilação.
12. Crie um arquivo zip **MyDotNetActivity.zip** que contém todos os binários na pasta <project folder>\\bin\\Debug. Convém incluir o arquivo MyDotNetActivity.pdb para que você obtenha detalhes adicionais, como por exemplo, em caso de falha, o número de linha no código-fonte que causou o problema.
13. Carregue **MyDotNetActivity.zip** como um blob para o contêiner de blob: **customactvitycontainer** no armazenamento de Blob do Azure que o serviço vinculado **StorageLinkedService** em **ADFTutorialDataFactory** utiliza. Crie o contêiner de blob **customactivitycontainer** se ele ainda não existir.

> [AZURE.NOTE]Se você adicionar esse projeto de atividade do .NET para uma solução no Visual Studio que contenha um projeto de Data Factory, não será necessário executar as duas últimas etapas de criação do arquivo zip e carregá-lo manualmente para o armazenamento de blobs do Azure. Quando você publica entidades de Data Factory usando o Visual Studio, essas etapas são executadas automaticamente pelo processo de publicação. Consulte os artigos [Criar seu primeiro pipeline usando o Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) e [Copiar dados do Blob do Azure para o Azure SQL](data-factory-get-started-using-vs.md) para saber mais sobre a criação e a publicação de entidades de Data Factory usando o Visual Studio.


## Etapa 2: Usar a atividade personalizada em um pipeline
Aqui estão os passos que você realizará nesta etapa:

1. Crie um serviço vinculado para o cluster HDInsight em que a atividade personalizada será executada como um trabalho somente de mapa.
2. Crie uma tabela de saída que será produzida pelo pipeline neste exemplo.
3. Criar e executar um pipeline que usa a atividade personalizada que você criou na etapa 1.

### Criar um serviço vinculado para o cluster HDInsight que será usado para executar a atividade personalizada
O serviço Data Factory do Azure dá suporte à criação de um cluster sob demanda e o usa para processar entrada a fim de gerar dados de saída. Você também pode usar seu próprio cluster para fazer isso. Quando você usa o cluster HDInsight sob demanda, um cluster é criado para cada fatia. Ao passo que, se você usar seu próprio cluster HDInsight, o cluster estará pronto para processar a fatia imediatamente. Portanto, quando você usar cluster sob demanda, não verá os dados de saída mais rapidamente que quando usa seu próprio cluster.

> [AZURE.NOTE]Em tempo de execução, uma instância de uma atividade do .NET é executada em somente um nó de trabalho no cluster HDInsight. Ela não pode ser dimensionada para ser executada em vários nós. Múltiplas instâncias de atividade do .NET podem ser executadas em paralelo em diferentes nós do cluster do HDInsight.

Se você tiver estendido o tutorial [Introdução ao Azure Data Factory][adfgetstarted] com o passo a passo de [Usar o Pig e Hive com o Azure Data Factory][hivewalkthrough], poderá pular a criação desse serviço vinculado e usar o serviço vinculado que você já tem em ADFTutorialDataFactory.


#### Para usar um cluster HDInsight sob demanda

1. No **Portal do Azure**, clique em **Criar e implantar** na página inicial do Data Factory.
2. No Editor Data Factory, Clique em **Nova computação** na barra de comandos e selecione **Cluster HDInsight sob demanda** no menu.
2. No script JSON, faça o seguinte:
	1. Para a propriedade **clusterSize**, especifique o tamanho do cluster do HDInsight.
	3. Para a propriedade **timeToLive**, especifique quanto tempo o cliente pode ficar ocioso antes de ser excluído.
	4. Para a propriedade **version**, especifique a versão do HDInsight que você deseja usar. Se você excluir essa propriedade, a versão mais recente será usada.  
	5. Para o **linkedServiceName**, especifique **StorageLinkedService** que você criou no tutorial do guia de Introdução.

			{
			  "name": "HDInsightOnDemandLinkedService",
			  "properties": {
			    "type": "HDInsightOnDemand",
			    "typeProperties": {
			      "clusterSize": "1",
			      "timeToLive": "00:05:00",
			      "version": "3.1",
			      "linkedServiceName": "StorageLinkedService"
			    }
			  }
			}

2. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

#### Para usar seu próprio cluster HDInsight:

1. No **Portal do Azure**, clique em **Criar e implantar** na página inicial do Data Factory.
2. No **Editor Data Factory**, clique em **Nova computação** na barra de comandos e selecione **Cluster HDInsight** no menu.
2. No script JSON, faça o seguinte:
	1. Para a propriedade **clusterUri**, insira a URL para seu HDInsight. Por exemplo: https://<clustername>.azurehdinsight.net/     
	2. Para a propriedade **UserName**, digite o nome de usuário que tem acesso ao cluster HDInsight.
	3. Para a propriedade **Password**, especifique a senha para o usuário.
	4. Para a propriedade **LinkedServiceName**, digite **StorageLinkedService**. Este é o serviço vinculado que você criou no tutorial do guia de Introdução.

2. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

### Criar uma tabela de saída

1. No **Editor Data Factory**, clique em **Novo conjunto de dados** e, em seguida, clique em **Armazenamento de Blobs do Azure** na barra de comandos.
2. Substitua o script JSON no painel direito pelo script JSON a seguir:

		{
		  "name": "OutputTableForCustom",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/customactivityoutput/{Slice}",
		      "partitionedBy": [
		        {
		          "name": "Slice",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMddHH"
		          }
		        }
		      ]
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

 	O local de saída é **adftutorial/customactivityoutput/AAAAMMDDHH/**, em que AAAAMMDDHH é o ano, mês, data e hora da fatia sendo produzida. Consulte a [Referência do Desenvolvedor][adf-developer-reference] para obter detalhes.

2. Clique em **Implantar** na barra de comandos para implantar a tabela.


### Criar e executar um pipeline que usa a atividade personalizada

1. No Editor Data Factory, clique em **Novo pipeline** na barra de comandos. Se você não vir o comando, clique em **... (Reticências)** para vê-lo.
2. Substitua o JSON no painel direito pelo script JSON a seguir. Se você quiser utilizar seu próprio cluster e tiver seguido as etapas para criar o serviço vinculado **HDInsightLinkedService**, substitua **HDInsightOnDemandLinkedService** por **HDInsightLinkedService** no JSON a seguir.

		{
		  "name": "ADFTutorialPipelineCustom",
		  "properties": {
		    "description": "Use custom activity",
		    "activities": [
		      {
		        "Name": "MyDotNetActivity",
		        "Type": "DotNetActivity",
		        "Inputs": [
		          {
		            "Name": "EmpTableFromBlob"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputTableForCustom"
		          }
		        ],
		        "LinkedServiceName": "HDInsightOnDemandLinkedService",
		        "typeProperties": {
		          "AssemblyName": "MyDotNetActivity.dll",
		          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
		          "PackageLinkedService": "StorageLinkedService",
		          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
		          "extendedProperties": {
		            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
		          }
		        },
		        "Policy": {
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

	Substitua o valor **StartDateTime** pelos três dias anteriores ao dia atual e valor **EndDateTime** pelo dia atual. StartDateTime e EndDateTime precisam ambos estar em [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A tabela de saída está agendada para ser produzida diariamente, então haverá três fatias produzidas.

	Observe o seguinte:

	- Há uma atividade na seção de atividades, que é do tipo **DotNetActivity**.
	- Use a mesma tabela de entrada **EmpTableFromBlob** que você usou no tutorial Introdução.
	- Use uma nova tabela de saída **OutputTableForCustom** que será criada na próxima etapa.
	- **AssemblyName** é definido como o nome da DLL **MyActivities.dll**.
	- **EntryPoint** é definido como **MyDotNetActivityNS.MyDotNetActivity**.
	- **PackageLinkedService** é definido como **MyBlobStore**, que foi criado como parte do tutorial de [Introdução ao Azure Data Factory][adfgetstarted]. Esse armazenamento de blob contém o arquivo zip da atividade personalizada.
	- **PackageFile** é definido como **customactivitycontainer/MyDotNetActivity.zip**.

4. Clique em **Implantar** na barra de comandos para implantar o pipeline.
8. Verifique se os arquivos de saída são gerados no armazenamento de blob no contêiner **adftutorial**.

	![saída de atividade personalizada][image-data-factory-ouput-from-custom-activity]

9. Se você abrir o arquivo de saída, verá uma saída semelhante ao seguinte:

	adftutorial/,emp.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(local do blob), (nome do blob), (número de linhas no blob), (nó em que a atividade foi executada), (carimbo de data/hora)

10.	Use o [Portal do Azure][azure-preview-portal] ou cmdlets do PowerShell do Azure para monitorar sua data factory, pipelines e conjuntos de dados. Você pode ver as mensagens de **ActivityLogger** no código da atividade personalizada nos logs (especificamente, user-0.log) que você pode baixar do portal ou usando os cmdlets.

	![baixar logs de atividade personalizada][image-data-factory-download-logs-from-custom-activity]


Consulte [Monitorar e Gerenciar Pipelines](data-factory-monitor-manage-pipelines.md) para obter etapas detalhadas para monitoramento de conjuntos de dados e pipelines.

## Atualizando uma atividade personalizada
Se você atualizar o código para a atividade personalizada, compile-o e carregue o arquivo zip que contém os novos binários para o armazenamento de blob.

## <a name="AzureBatch"></a> Usando o serviço vinculado Azure Batch
> [AZURE.NOTE]Consulte [Noções básicas do Lote do Azure][batch-technical-overview] para obter uma visão geral do serviço do Lote do Azure e consulte [Introdução à Biblioteca do Lote do Azure para .NET][batch-get-started] para começar rapidamente a usar o serviço do Lote do Azure.

Você pode executar suas atividades personalizadas do .NET usando o Azure Batch como um recurso de computação. Você precisará criar seus próprios pools do Azure Batch e especificar o número de VMs junto com outras configurações. Pools do Azure Batch fornecem os seguintes recursos para os clientes:

1. Criar pools que contenham um único núcleo até milhares de núcleos.
2. Dimensionamento automático da contagem de VM com base em uma fórmula
3. Suporte a VMs de qualquer tamanho
4. Número configurável de tarefas por VM
5. Número ilimitado de tarefas em fila.


Aqui estão os passos de alto nível para usar o serviço vinculado Azure Batch no passo a passo descrito na seção anterior:

1. Crie uma conta de lote do Azure usando o Portal de Gerenciamento do Azure. Consulte o artigo [Criar e gerenciar uma conta do Lote do Azure][batch-create-account] para ver as instruções. Anote a chave e o nome da conta do Azure Batch.

	Você também pode usar o cmdlet [New-AzureBatchAccount][new-azure-batch-account] para criar uma conta do Azure Batch. Consulte [usando o Azure PowerShell para gerenciar conta do Azure lote][azure-batch-blog] para obter instruções detalhadas sobre como usar este cmdlet.
2. Crie um pool do Azure Batch. Você pode baixar o código-fonte da [ferramenta Gerenciador do Lote do Azure][batch-explorer], compilar e usá-lo (ou) usar a [Biblioteca do Lote do Azure para .NET][batch-net-library] para criar um pool do Lote do Azure. Consulte [Passo a passo de exemplo do gerenciador do Azure Batch][batch-explorer-walkthrough] para obter instruções passo a passo de como usar o Gerenciador do Azure Batch.

	Você também pode usar o cmdlet [New-AzureBatchPool][new-azure-batch-pool] para criar um pool do Azure Batch.

2. Crie um serviço vinculado Azure Batch usando o modelo JSON a seguir. O Editor Data Factory exibe um modelo semelhante com o qual você pode começar. Especifique o nome da conta do Azure Batch, o pool e a chave de conta no trecho de código JSON.

		{
		  "name": "AzureBatchLinkedService",
		  "properties": {
		    "type": "AzureBatch",
		    "typeProperties": {
		      "accountName": "<Azure Batch account name>",
			  "batchUri": "https://<region>.batch.azure.com",
		      "accessKey": "<Azure Batch account key>",
		      "poolName": "<Azure Batch pool name>",
		      "linkedServiceName": "<Specify associated storage linked service reference here>"
		    }
		  }
		}

	> [AZURE.IMPORTANT]A **URL** da **folha de conta do Lote do Azure** está no seguinte formato: nomedaconta.região.lote.azure.com. Para a propriedade **batchUri** no JSON, você precisará **remover "accountname."** da URL e usar o **accountname** para a propriedade JSON **accountName**.

	Para a propriedade **poolName**, você também pode especificar a ID do pool em vez do nome do pool.

	Consulte [tópico do MSDN do serviço vinculado Azure Batch](https://msdn.microsoft.com/library/mt163609.aspx) para obter descrições dessas propriedades.

2.  No Editor Data Factory, abra a definição do JSON para o pipeline criado no passo a passo e substitua **HDInsightLinkedService** por **AzureBatchLinkedService**.
3.  Você talvez queira alterar as horas de início e término do pipeline, para que você possa testar o cenário com o serviço Azure Batch.
4.  Você pode ver as tarefas do Azure Batch associadas ao processamento das fatias no Gerenciador do Azure Batch, conforme mostrado no diagrama a seguir.

	![tarefas do Azure Batch][image-data-factory-azure-batch-tasks]

> [AZURE.NOTE]O serviço de Data Factory não suporta uma opção sob demanda para o Azure Batch como o faz para o HDInsight. Você só pode usar seu próprio pool do Azure Batch em um Azure Data Factory.

## Consulte também

[Atualizações do Azure Data Factory: Execute atividades ADF personalizadas do .NET usando o Azure Batch](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-account-create-portal.md
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
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
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png

<!---HONumber=AcomDC_1125_2015-->