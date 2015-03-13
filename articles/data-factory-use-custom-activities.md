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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Usar atividades personalizadas em um pipeline do Data Factory do Azure
O Data Factory do Azure dá suporte a atividades internas como **Atividade de Cópia** e **Atividade do HDInsight** a ser usadas em pipelines para mover e processar dados. Você também pode criar uma atividade personalizada com sua própria lógica de processamento/transformação e usar a atividade em um pipeline. A atividade personalizada é executada como um trabalho somente de mapa em um cluster HDInsight, por isso será necessário vincular um cluster HDInsight para a atividade personalizada no pipeline.
 
Este artigo descreve como criar uma atividade personalizada e usá-la em um pipeline do Data Factory do Azure. Ele também fornece uma explicação detalhada com instruções passo a passo para criar e usar uma atividade personalizada.

## Criando uma atividade personalizada

Para criar uma atividade personalizada:
 
1.	Crie um projeto de **Biblioteca de Classes** no Visual Studio 2013.
2.	Baixe o pacote NuGet [Pacote NuGet para o Data Factory do Azure][] e instale-o. Instruções de instalação estão no passo a passo.
3. Adicione o seguinte usando instruções na parte superior do arquivo de origem na biblioteca de classes.
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. Atualize a classe para implementar a interface **ICustomActivity**.
	<ol type='a'>
		<li>
			Derive a classe de <b>ICustomActivity</b>.
			<br/>
			Exemplo: <br/>
			classe pública <b>MyCustomActivity : ICustomActivity</b>
		</li>

		<li>
			Implemente o método <b>Execute</b> da interface <b>ICustomActivity</b> 
		</li>

	</ol>
5. Compile o projeto.


## Usar a atividade personalizada em um pipeline
Para usar a atividade personalizada em um pipeline:

1.	**Compacte** todos os arquivos binários das pastas de saída **bin\debug** ou **bin\release** do projeto. 
2.	**Carregue o** arquivo zip como um blob para o **Armazenamento de blob do Azure**. 
3.	Atualize o arquivo **pipeline JSON** para referir-se ao arquivo zip, à DLL de atividade personalizada, à classe de atividade e ao blob que contém o arquivo zip no pipeline JSON. No arquivo JSON:
	<ol type ="a">
		<li><b>Tipo de atividade</b> deve ser definido como <b>CustomActivity</b>.</li>
		<li><b>AssemblyName</b> é o nome da DLL saída do projeto do Visual Studio.</li>
		<li><b>EntryPoint</b> especifica o <b>namespace</b> e o <b>nome</b> da <b>classe</b> que implementa a interface <b>ICustomActivity</b>.</li>
		<li><b>PackageLinkedService</b> é o serviço vinculado que faz referência ao blob que contém o arquivo zip. </li>
		<li><b>PackageFile</b> especifica o local e o nome do arquivo zip que foi carregado para o armazenamento de blob do Azure.</li>
		<li><b>LinkedServiceName</b> é o nome do serviço vinculado que vincula um cluster HDInsight (sob demanda ou seu próprio) a um data factory. A atividade personalizada é executada como um trabalho somente de mapa no cluster HDInsight especificado.</li>
	</ol>

	

	**Exemplo JSON parcial**

		"Name": "MyCustomActivity",
    	"Type": "CustomActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyCustomActivity.dll",
    	    "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyCustomActivity.zip",

## Para atualizar a atividade personalizada
Se você atualizar o código para a atividade personalizada, compilá-lo e carregar o arquivo zip que contém os novos binários para o armazenamento de blob, você precisará substituir o pipeline existente que usa a atividade personalizada executando **New-AzureDataFactoryPipeline**. Quando você cria um pipeline que usa a atividade personalizada, o arquivo zip é copiado do armazenamento de blob especificado para um contêiner do Data Factory do Azure no armazenamento de blob anexado ao cluster HDInsight. Isso é feito apenas na criação do pipeline. Portanto, é necessário recriar o pipeline, quando você atualizar a atividade personalizada. 

A explicação a seguir fornece instruções passo a passo para criar uma atividade personalizada e usá-la em um pipeline do Data Factory do Azure. Este passo a passo estende o tutorial de [Introdução ao Data Factory do Azure][adfgetstarted]. Se você quiser ver a atividade personalizada trabalhando, precisará percorrer o tutorial de Introdução primeiro e, em seguida, seguir este passo a passo. 

## Passo a passo
**Pré-requisitos:**


- Tutorial de [Introdução ao Data Factory do Azure][adfgetstarted]. Você deve concluir o tutorial deste artigo antes de prosseguir com este passo a passo.
- Visual Studio 2012 ou 2013
- Baixe e instale o [SDK do .NET do Microsoft Azure][azure-developer-center]
- Baixe os [Pacotes NuGet para o Data Factory do Azure][nuget-package].
- Baixe e instale o pacote NuGet para armazenamento do Azure. As instruções estão no passo a passo, portanto você pode ignorar esta etapa.

### Etapa 1: Criar uma atividade personalizada

1.	Crie um projeto de biblioteca de classes do .NET.
	<ol type="a">
		<li>Inicie o <b>Visual Studio 2012</b> ou <b>Visual Studio 2013</b>.</li>
		<li>Clique em <b>Arquivo</b>, indique <b>Novo</b> e, em seguida, clique em <b>Projeto</b>.</li> 
		<li>Expanda <b>Modelos</b> e selecione <b>Visual C#</b>. Neste passo a passo, você pode usar C#, mas você pode usar qualquer linguagem .NET para desenvolver a atividade personalizada.</li> 
		<li>Selecione <b>Biblioteca de Classes</b> na lista de tipos de projetos à direita.</li>
		<li>Insira <b>MyCustomActivity</b> para o <b>Nome</b>.</li> 
		<li>Selecione <b>C:\ADFGetStarted</b> para o <b>Local</b>.</li>
		<li>Clique em <b>OK</b> para criar o projeto.</li>
	</ol>
2.  Clique em <b>Ferramentas</b>, aponte para <b>Gerenciador de Pacotes NuGet</b> e clique em <b>Console do Gerenciador de Pacotes</b>.
3.	No <b>Console do Gerenciador de Pacotes</b>, execute o seguinte comando para importar <b>Microsoft.Azure.Management.DataFactories</b> baixado anteriormente. Substitua a pasta pelo local que contém o pacote NuGet do Data Factory baixado.

		Install-Package Microsoft.Azure.Management.DataFactories -Source d:\packages -Pre

3.	No <b>Console do Gerenciador de Pacotes</b>, execute o seguinte comando para importar <b>Microsoft.DataFactories.Runtime</b> baixado anteriormente. Substitua a pasta pelo local que contém o pacote NuGet do Data Factory baixado.

		Install-Package Microsoft.DataFactories.Runtime -Source d:\packages -Pre

4. Importe o pacote NuGet do Armazenamento do Microsoft Azure para o projeto.

		Install-Package WindowsAzure.Storage

5. Adicione o seguinte **usando** instruções ao arquivo de origem no projeto.

		using System.IO;
		using System.Globalization;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. Altere o nome do **namespace** para **MyCustomActivityNS**.

		namespace MyCustomActivityNS

7. Altere o nome da classe para **MyCustomActivity** e derive-a da interface **ICustomActivity** conforme mostrado abaixo.

		public class MyCustomActivity : ICustomActivity

8. Implemente (Adicione) o método **Execute** da interface **ICustomActivity** à classe **MyCustomActivity** e copie o seguinte código de exemplo para o método. 

	Os parâmetros **inputTables** e **outputTables** representam tabelas de entrada e saída da atividade, como os nomes sugerem. Você pode ver mensagens registradas usando o objeto **logger** no arquivo de log que você pode baixar no portal do Azure ou usando cmdlets. O dicionário **extendedProperties** contém a lista de propriedades estendidas que você especifica no arquivo JSON para a atividade e seus valores. 

	O código de exemplo a seguir conta o número de linhas no blob de entrada e produz o seguinte conteúdo no blob de saída: caminho para o blob, o número de linhas no blob, o computador em que a atividade foi executada, data e hora atuais.

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
										"{0},{1},{2},{3}\n", 
										folderPath, 
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

9. Adicione os seguintes métodos auxiliares. O método **Execute** invoca esses métodos auxiliares. O método **GetConnectionString** recupera a cadeia de conexão do armazenamento do Azure e o método **GetFolderPath** recupera o local do blob. 


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
   


10. Compile o projeto. Clique em **Compilar** no menu e clique em **Compilar Solução**.
11. Inicie o **Windows Explorer** e navegue até a pasta **bin\debug** ou **bin\release** dependendo do tipo da compilação.
12. Crie um arquivo zip **MyCustomActivity.zip** que contêm todos os binários na pasta <project folder>\bin\Debug.
	![zip output binaries][image-data-factory-zip-output-binaries]
13. Carregue **MyCustomActivity.zip** como um blob para o contêiner de blob: **customactvitycontainer** no armazenamento de Blob do Azure que o serviço vinculado **MyBlobStore** em **ADFTutorialDataFactory** usa.  Crie o contêiner de blob **blobcustomactivitycontainer** se ele ainda não existir. 
    ![upload zip to blob][image-data-factory-upload-zip-to-blob]

### Criar um serviço vinculado para o  cluster HDInsight que será usado para executar a atividade personalizada
O serviço Data Factory do Azure dá suporte à criação de um cluster sob demanda e o usa para processar entrada a fim de gerar dados de saída. Você também pode usar seu próprio cluster para fazer isso. Quando você usa o cluster HDInsight sob demanda, um cluster é criado para cada fatia. Ao passo que, se você usar seu próprio cluster HDInsight, o cluster estará pronto para processar a fatia imediatamente. Portanto, quando você usar cluster sob demanda, não verá os dados de saída mais rapidamente que quando usa seu próprio cluster. Para fins de exemplo, vamos usar um cluster sob demanda. 

> [WACOM.NOTE] Se você tiver estendido o tutorial [Introdução ao Data Factory do Azure][adfgetstarted] com o passo a passo de [Usar o Pig e Hive com o Data Factory do Azure][hivewalkthrough], poderá pular a criação desse serviço vinculado e usar o serviço vinculado que você já tem em ADFTutorialDataFactory. 

#### Para usar um cluster HDInsight sob demanda

1. Crie um arquivo JSON chamado **HDInsightOnDemandCluster.json** com o seguinte conteúdo e salve-o na pasta **C:\ADFGetStarted\Custom**.


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": 4,
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. Inicie o **PowerShell do Azure** e execute o seguinte comando para alternar para o modo **AzureResourceManager**. Os cmdlets do Data Factory do Azure estão disponíveis no modo **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Alterne para a pasta **C:\ADFGetstarted\Custom**.
4. Execute o seguinte comando para criar o serviço vinculado para o cluster HDInsight sob demanda.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
   
#### Para usar seu próprio cluster HDInsight: 

1. Crie um arquivo JSON chamado **MyHDInsightCluster.json** com o seguinte conteúdo e salve-o na pasta **C:\ADFGetStarted\Custom**. Substitua clustername, nome de usuário e senha por valores adequados antes de salvar o arquivo JSON.  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. Inicie o **PowerShell do Azure** e execute o seguinte comando para alternar para o modo **AzureResourceManager**. Os cmdlets do Data Factory do Azure estão disponíveis no modo **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Alterne para a pasta **C:\ADFGetstarted\Custom**.
4. Execute o seguinte comando para criar o serviço vinculado para o cluster HDInsight sob demanda.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json



### Etapa 2: Usar a atividade personalizada em um pipeline
Vamos estender o tutorial de [Introdução ao Data Factory do Azure][adfgetstarted] para criar outro pipeline para testar essa atividade personalizada.

#### Criar um serviço vinculado para o cluster HDInsight a ser usado para executar a atividade personalizada


1.	Crie um arquivo a JSON para o pipeline conforme mostrado no exemplo a seguir e salve-o como **ADFTutorialPipelineCustom.json** na pasta **C:\ADFGetStarted\Custom**. Altere o nome de **LinkedServiceName** para o nome do cluster HDInsight (**HDInsightOnDemandCluster** ou **MyHDInsightCluster**)


		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyCustomActivity",
                     	"Type": "CustomActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "MyHDInsightCluster",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyCustomActivity.dll",
                            "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyCustomActivity.zip",
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
				]
			}
		}

	Observe o seguinte: 

	- Há uma atividade na seção de atividades, sendo do tipo: **CustomActivity**.
	- Use a mesma tabela de entrada **EmpTableFromBlob** usada no tutorial Introdução.
	- Use uma nova tabela de saída **OutputTableForCustom** que será criada na próxima etapa.
	- **AssemblyName** é definido como o nome da DLL: **MyActivities.dll**.
	- **EntryPoint** é definido como **MyCustomActivityNS.MyCustomActivity**.
	- **PackageLinkedService** é definido como **MyBlobStore** que foi criado como parte do tutorial de [Introdução ao Data Factory do Azure][adfgetstarted]. Esse armazenamento de blob contém o arquivo zip da atividade personalizada.
	- **PackageFile** é definido como **customactivitycontainer/MyCustomActivity.zip**.
     

4. Crie um arquivo JSON para a tabela de saída (**OutputTableForCustom** referido pelo pipeline JSON) e salve-o como C:\ADFGetStarted\Custom\OutputTableForCustom.json.

		

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

 	O local de saída é **adftutorial/customactivityoutput/AAAAMMDDHH/**, em que AAAAMMDDHH é o ano, mês, data e hora da fatia sendo produzida. Consulte a [Referência do Desenvolvedor][adf-developer-reference] para obter detalhes. 

5. Execute o seguinte comando para **criar a tabela de saída** em **ADFTutorialDataFactory**.
		
		

		New-AzureDataFactoryTable  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\OutputTableForCustom.json -ResourceGroupName ADFTutorialResourceGroup


6. Agora, execute o seguinte comando para **criar o pipeline**. Você criou o arquivo de pipeline JSON em uma etapa anterior.

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\ADFTutorialPipelineCustom.json -ResourceGroupName ADFTutorialResourceGroup



7. Execute o seguinte comando do PowerShell para **definir o período ativo** no pipeline criado.

		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineCustom

	> [WACOM.NOTE] Substitua o valor **StartDateTime** pelo dia atual e o valor **EndDateTime** pelo dia seguinte. StartDateTime e EndDateTime são horas UTC (Tempo Universal Coordenado) e devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O **EndDateTime** é opcional, mas o usaremos neste tutorial. 
	> Se você não especificar **EndDateTime**, ele será calculado como "**StartDateTime + 48 horas**". Para executar o pipeline indefinidamente, especifique **9/9/9999** como o **EndDateTime**.



8. Verifique se os arquivos de saída são gerados no armazenamento de blob no contêiner **adftutorial**.

	![output from custom activity][image-data-factory-ouput-from-custom-activity]

9. Se você abrir o arquivo de saída, verá uma saída semelhante ao seguinte:
	
	adftutorial/input,2,WORKERNODE0,10/07/2014 18:34:33 

	(local do blob), (número de linhas no blob), (nó em que a atividade foi executada), (carimbo de data/hora)

10.	Use o [Portal do Azure][azure-preview-portal] ou cmdlets do PowerShell do Azure para monitorar seu data factory, pipelines e conjuntos de dados. Você pode ver as mensagens de **ActivityLogger** no código da atividade personalizada nos logs que você pode baixar do portal ou usando os cmdlets.

	![download logs from custom activity][image-data-factory-download-logs-from-custom-activity]
   
Consulte [Introdução ao Data Factory do Azure][adfgetstarted] para obter etapas detalhadas para monitorar conjuntos de dados e pipelines.      
    
## Consulte também

Artigo | Descrição
------ | ---------------
[Habilitar seus pipelines para trabalhar com dados locais][use-onpremises-datasources] | Este artigo tem um passo a passo que mostra como copiar dados de um banco de dados SQL Server local para um blob do Azure.
[Usar o Pig e Hive com o Data Factory][use-pig-and-hive-with-data-factory] | Este artigo possui um passo a passo que mostra como usar a atividade de HDInsight para executar um script do hive/pig para processar dados de entrada para gerar dados de saída. 
[Tutorial: Mover e arquivos de log do processo usando o Data Factory][adf-tutorial] | Este artigo fornece um passo a passo que mostra como implementar um cenário próximo do real usando o Data Factory do Azure para transformar dados de arquivos de log em informações.
[Usar atividades personalizadas em um Data Factory][use-custom-activities] | Este artigo fornece um passo a passo com instruções para criar uma atividade personalizada e usá-la em um pipeline. 
[Monitorar e gerenciar a Data Factory do Azure usando o PowerShell][monitor-manage-using-powershell] | Este artigo descreve como monitorar um Data Factory do Azure usando cmdlets do PowerShell do Azure. Você pode testar os exemplos neste artigo sobre ADFTutorialDataFactory.
[Solucionar problemas da fábrica dados][troubleshoot] | Este artigo descreve como solucionar problemas do Data Factory do Azure. Você pode testar o passo a passo neste artigo no ADFTutorialDataFactory introduzindo um erro (excluindo a tabela no Banco de Dados SQL do Azure). 
[Referência do Desenvolvedor do Data Factory do Azure][developer-reference] | A Referência do Desenvolvedor tem o conteúdo de referência abrangente para cmdlets, script JSON, funções etc... 


[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456




[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: ../data-factory-get-started
[hivewalkthrough]: ../data-factory-pig-hive-activities

[image-data-factory-zip-output-binaries]: ./media/data-factory-use-custom-activities/ZipOuputBinaries.png

[image-data-factory-upload-zip-to-blob]: ./media/data-factory-use-custom-activities/UploadZipToBlob.png

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

<!--HONumber=46--> 
