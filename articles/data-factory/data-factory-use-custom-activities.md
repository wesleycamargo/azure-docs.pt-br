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
	ms.date="01/05/2016"
	ms.author="spelluru"/>

# Usar atividades personalizadas em um pipeline do Data Factory do Azure
O Azure Data Factory dá suporte a atividades internas como **Atividade de Cópia** e **Atividade do HDInsight** a ser usadas em pipelines para mover e processar dados. Você também pode criar uma atividade personalizada do .NET com sua própria lógica de processamento/transformação e usar a atividade em um pipeline. Você pode configurar a atividade para que seja executada usando um cluster **Azure HDInsight** ou um serviço **Azure Batch**.

Este artigo descreve como criar uma atividade personalizada e usá-la em um pipeline do Data Factory do Azure. Ele também fornece uma explicação detalhada com instruções passo a passo para criar e usar uma atividade personalizada. O passo a passo usa o serviço vinculado HDInsight. Para em vez disso usar o serviço vinculado Azure Batch, você cria um serviço vinculado do tipo **AzureBatchLinkedService** e utiliza-o na seção de atividade do pipeline JSON (**linkedServiceName**). Consulte a seção [Serviço vinculado Azure Batch](#AzureBatch) para obter detalhes sobre como usar o Azure Batch com a atividade personalizada.


## <a name="walkthrough" /> Guia passo a passo
Este passo a passo fornece instruções passo a passo para criar uma atividade personalizada e usá-la em um pipeline do Azure Data Factory. Este passo a passo estende o tutorial de [Introdução ao Azure Data Factory][adfgetstarted]. Se você quiser ver a atividade personalizada trabalhando, precisará percorrer o tutorial de Introdução primeiro e, em seguida, seguir este passo a passo.

### Pré-requisitos


- Tutorial de [Introdução ao Azure Data Factory][adfgetstarted]. Você deve concluir o tutorial deste artigo antes de seguir este passo a passo.
- Visual Studio 2012 ou 2013
- Baixar e instalar o [SDK .NET do Azure][azure-developer-center]
- Baixe o mais recente [Pacote NuGet para o Azure Data Factory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/) e instale-o. As instruções estão no passo a passo.
- Baixe e instale o pacote NuGet para armazenamento do Azure. As instruções estão no passo a passo, portanto você pode ignorar esta etapa.

### Etapas de alto nível 
1.	**Crie uma atividade personalizada** para usar na solução Data Factory. A atividade personalizada contém a lógica de processamento de dados. 
	1.	No Visual Studio (ou editor de código de preferência), crie um projeto de biblioteca de classes do .NET, adicione o código para processar dados de entrada e compile o projeto.	
	2.	Compacte todos os arquivos binários e o arquivo PDB (opcional) na pasta de saída.	
	3.	Carregue o arquivo zip no armazenamento de blobs do Azure. Etapas detalhadas na seção de atividade personalizada Criar. 
2. **Crie um Azure Data Factory que usa a atividade personalizada**:
	1. Criar uma data factorydo Azure.
	2. Criar serviços vinculados.
		1. StorageLinkedService: fornece credenciais de armazenamento para acessar blobs.
		2. HDInsightLinkedService: especifica o Azure HDInsight como computação.
	3. Crie conjuntos de dados.
		1. InputDataset: especifica contêiner de armazenamento e pasta para os blobs de entrada.
		1. OuputDataset: especifica contêiner de armazenamento e pasta para os blobs de saída.
	2. Crie um pipeline que usa atividade personalizada.
	3. Execute e teste o pipeline.
	4. Depure o pipeline.

## Criar a atividade personalizada
Para criar uma atividade personalizada do .NET que possa ser usada em um pipeline do Azure Data Factory, você precisa criar um projeto **Biblioteca de classes do .NET** com uma classe que implemente a interface **IDotNetActivity**. Essa interface tem apenas um método: Execute. Veja a assinatura desse método:

	public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices, 
            IEnumerable<Dataset> datasets, 
            Activity activity, 
            IActivityLogger logger)
        
O método tem alguns componentes principais que você precisa entender.

- O método utiliza quatro parâmetros:
	- **linkedServices**. É uma lista enumerável de serviços vinculados que vinculam fontes de dados de entrada/saída (por exemplo: armazenamento de blobs do Azure) no data factory. Neste exemplo, há apenas um serviço vinculado do tipo armazenamento do Azure usado para entrada e saída. 
	- **conjuntos de dados**. É uma lista enumerável de conjuntos de dados. Você pode usar esse parâmetro para obter os locais e esquemas definidos por conjuntos de dados de entrada e saída.
	- **activity**. Esse parâmetro representa a entidade de computação atual - nesse caso, um Azure HDInsight.
	- **logger**. O logger permite que você escreva comentários de depuração que serão exibidos como o log do “usuário" no pipeline. 

- O método retorna um dicionário que pode ser usado para concatenar atividades personalizadas. Não usaremos esse recurso nesta solução de exemplo.

### Procedimento: 
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

		/// <summary>
        /// Execute method is the only method of IDotNetActivity interface you must implement. 
        /// In this sample, the method invokes the Calculate method to perform the core logic.  
		/// </summary>

        public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
        {
			// to get extended properties (for example: SliceStart)
			DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
            string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

			// to log all extended properties			
			IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
			logger.Write("Logging extended properties if any...");
			foreach (KeyValuePair<string, string> entry in extendedProperties)
			{
				logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
			}
		

            // declare types for input and output data stores
            AzureStorageLinkedService inputLinkedService;

            // declare dataset types
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

            foreach (LinkedService ls in linkedServices)
                logger.Write("linkedService.Name {0}", ls.Name);

            // using First method instead of Single since we are using the same 
            // Azure Storage linked service for input and output. 
            inputLinkedService = linkedServices.First(
                linkedService =>
                linkedService.Name ==
                inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
                as AzureStorageLinkedService;

            string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
            string folderPath = GetFolderPath(inputDataset);
            string output = string.Empty; // for use later.

            // create storage client for input. Pass the connection string.
            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            // initialize the continuation token before using it in the do-while loop.
            BlobContinuationToken continuationToken = null;
            do
            {   // get the list of input blobs from the input storage client object.
                BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                         true,
                                         BlobListingDetails.Metadata,
                                         null,
                                         continuationToken,
                                         null,
                                         null);
                
                // Calculate method returns the number of occurrences of 
                // the search term (“Microsoft”) in each blob associated
       			// with the data slice. 
        		// 
        	    // definition of the method is shown in the next step.
 
                output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

            } while (continuationToken != null);

            // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
            // convert to blob location object.
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to the folder: {0}", folderPath);

            // create a storage object for the output blob.
            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            // write the name of the file. 
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

            logger.Write("output blob URI: {0}", outputBlobUri.ToString());
            // create a new blob and upload the output text.
            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            logger.Write("Writing {0} to the output blob", output);
            outputBlob.UploadText(output);

            // return a new Dictionary object (unused in this code).
            return new Dictionary<string, string>();
        }

9. Adicione os seguintes métodos auxiliares. O método **Execute** invoca esses métodos auxiliares. O método **GetConnectionString** recupera a cadeia de conexão do armazenamento do Azure e o método **GetFolderPath** recupera o local do blob. Mais importante, o método **Calculate** isola o código que itera através de cada blob.

        /// <summary>
        /// Gets the folderPath value from the input/output dataset.   
		/// </summary>

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

        /// <summary>
        /// Gets the fileName value from the input/output dataset.   
        /// </summary>

        private static string GetFileName(Dataset dataArtifact)
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

            return blobDataset.FileName;
        }

        /// <summary>
        /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file, 
        /// and prepares the output text that will be written to the output blob. 
        /// </summary>

        public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
        {
            string output = string.Empty;
            logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
            foreach (IListBlobItem listBlobItem in Bresult.Results)
            {
                CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
                {
                    string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                    logger.Write("input blob text: {0}", blobText);
                    string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                    var matchQuery = from word in source
                                     where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                     select word;
                    int wordCount = matchQuery.Count();
                    output += string.Format("{0} occurrences(s) of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
                }
            }
            return output;
        }

	O método GetFolderPath retorna o caminho para a pasta indicada pelo conjunto de dados e o método GetFileName retorna o nome do blob/arquivo para o qual o conjunto de dados aponta.
	
		    "name": "InputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "file.txt",
		            "folderPath": "mycontainer/inputfolder/",
	
	O método Calculate calcula o número de instâncias da palavra-chave Microsoft nos arquivos de entrada (blobs na pasta). O termo de pesquisa ("Microsoft") é embutido no código.

10. Compile o projeto. Clique em **Compilar** no menu e clique em **Compilar Solução**.
11. Inicie o **Windows Explorer** e navegue até a pasta **bin\\debug** ou **bin\\release** dependendo do tipo da compilação.
12. Crie um arquivo zip **MyDotNetActivity.zip** que contém todos os binários na pasta <project folder>\\bin\\Debug. Inclua o arquivo **MyDotNetActivity.pdb** para obter detalhes adicionais, como número de linha no código-fonte que causou o problema, no caso de uma falha.

	![Arquivos de saída binários](./media/data-factory-use-custom-activities/Binaries.png)
13. Carregue **MyDotNetActivity.zip** como um blob para o contêiner de blobs: **customactvitycontainer** no armazenamento de blobs do Azure que o serviço vinculado **StorageLinkedService** em **ADFTutorialDataFactory** utiliza. Crie o contêiner de blob **customactivitycontainer** se ele ainda não existir.

> [AZURE.NOTE] Se você adicionar esse projeto de atividade do .NET para uma solução no Visual Studio que contenha um projeto de Data Factory, não será necessário executar as duas últimas etapas de criação do arquivo zip e carregá-lo manualmente para o armazenamento de blobs do Azure. Quando você publica entidades de Data Factory usando o Visual Studio, essas etapas são executadas automaticamente pelo processo de publicação. Consulte os artigos [Compilar seu primeiro pipeline usando o Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) e [Copiar dados de blob do Azure no SQL do Azure](data-factory-get-started-using-vs.md) para saber mais sobre como criar e publicar entidades de Data Factory usando o Visual Studio.

### Método Execute

Esta seção fornece mais detalhes e observações sobre o código no método **Execute**.
 
1. Os membros para iteração pela coleção de entrada são encontrados no namespace [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx). A iteração através da coleção de blobs requer o uso da classe **BlobContinuationToken**. Em essência, você deve usar um loop do-while com o token, como o mecanismo para saída do loop. Para obter mais informações, consulte o artigo sobre [Como usar o armazenamento de blobs do .NET](../storage/storage-dotnet-how-to-use-blobs.md). Um loop básico é mostrado aqui:

		// Initialize the continuation token.
		BlobContinuationToken continuationToken = null;
		do
		{   
			// Get the list of input blobs from the input storage client object.
			BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    					              true,
    	                              BlobListingDetails.Metadata,
    	                              null,
    	                              continuationToken,
    	                              null,
    	                              null);
			// Return a string derived from parsing each blob.
    		output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
		} while (continuationToken != null);

	Consulte a documentação do método [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) para obter detalhes.

2.	O código para trabalhar com o conjunto de blobs logicamente fica dentro do loop do-while. No método **Execute**, o loop do-while passa a lista de blobs para um método chamado **Calculate**. O método retorna uma variável de cadeia de caracteres chamada **output**, que é o resultado da iteração nos blobs do segmento.

	Ele retorna o número de ocorrências do termo de pesquisa (**Microsoft**) no blob passado para o método **Calculate**.

			output += string.Format("{0} occurrences of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);

3.	Quando o método **Calculate** tiver feito o trabalho, ele deverá ser gravado em um novo blob. Para cada conjunto de blobs processado, um novo blob pode ser gravado nos resultados. Para gravar um novo blob, primeiro localize o conjunto de dados de saída.

			// Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
			Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

			// Convert to blob location object.
			outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

4.	O código também chama um método auxiliar: **GetFolderPath** para recuperar o caminho da pasta (o nome do contêiner de armazenamento).
 
			folderPath = GetFolderPath(outputDataset);

	O **GetFolderPath** converte o objeto DataSet em um AzureBlobDataSet, que tem uma propriedade chamada FolderPath.
			
			AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
			
			return blobDataset.FolderPath;

5.	O código chama o método **GetFileName** para recuperar o nome do arquivo (nome do blob). O código é semelhante ao código acima para obter o caminho da pasta.

			AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

			return blobDataset.FileName;

6.	O nome do arquivo é gravado, ao criar um novo objeto URI. O construtor de URI usa a propriedade **BlobEndpoint** propriedade para retornar o nome do contêiner. O nome do arquivo e caminho da pasta são adicionados para construir o URI do blob de saída.

			// Write the name of the file. 
			Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

7.	O nome do arquivo foi escrito e agora você pode gravar a cadeia de caracteres de saída do método Calculate em um novo blob:

			// Create a new blob and upload the output text.
			CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
			logger.Write("Writing {0} to the output blob", output);
			outputBlob.UploadText(output);

## Criar o data factory

Na seção **Criar atividade personalizada**, você criou uma atividade personalizada e carregou o arquivo zip com binários e o arquivo PDB em um contêiner de blob do Azure. Nesta seção, você criará um Azure **data factory** com um **pipeline** que usa **atividade personalizada**.
 
O conjunto de dados de entrada da atividade personalizada representa os blobs (arquivos) na pasta de entrada (mycontainer\\inputfolder) no armazenamento de blobs. O conjunto de dados de saída da atividade representa os blobs de saída na pasta de saída (mycontainer\\inputfolder) no armazenamento de blobs.

Crie um arquivo chamado file.txt com o seguinte conteúdo e carregue-o em mycontainer\\inputfolder (mycontainer é o nome do contêiner de blob do Azure e inputfolder é o nome da pasta no contêiner.)

	test custom activity Microsoft test custom activity Microsoft

A pasta de entrada corresponde a uma fatia no Azure Data Factory, mesmo que a pasta tenha 2 ou mais arquivos. Quando cada fatia é processada pelo pipeline, a atividade personalizada itera em todos os blobs na pasta de entrada dessa fatia.

Você verá um arquivo de saída com na pasta mycontainer\\output com 1 ou mais linhas (mesmo número de blobs na pasta dev entrada):
 
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.


Aqui estão as etapas que você realizará nesta seção:

1. Criar uma **data factory**.
2. Crie **serviços vinculados** para o cluster HDInsight em que a atividade personalizada seja executada como um trabalho só de mapa e o armazenamento do Azure que contém os blobs de entrada/saída. 
2. Crie **conjuntos de dados** de entrada e saída que representem a entrada e saída da atividade personalizada. 
3. Crie execute um **pipeline** que usa a atividade personalizada.

### Etapa 1: Criar o data factory

1.	Depois de fazer logon no Portal do Azure, faça o seguinte:
	1.	Clique em **NOVO** no menu à esquerda.
	2.	Clique em **Dados + Análise** na folha **Novo**.
	3.	Clique em **Data Factory** na folha **Análise de dados**.
2.	Na folha **Novo data factory**, insira **CustomActivityFactory** para Nome. O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome de data factory “CustomActivityFactory” não está disponível**, altere o nome (por exemplo, **yournameCustomActivityFactory**) e tente criá-lo novamente.
3.	Clique em **NOME DO GRUPO DE RECURSOS** para selecionar um grupo de recursos existente ou criar um novo. 
4.	Depois de selecionar o grupo de recursos, verifique se que você está usando a **assinatura** e **região** na qual deseja que o data factory seja criado. 
5.	Clique em **Criar** na folha **Novo data factory**.
6.	Você verá o data factory sendo criado no **Painel** do Portal do Azure.
7.	Após a data factory ter sido criado com êxito, você verá a folha Data Factory, com a exibição do conteúdo.

### Etapa 2: Criar serviços vinculados

Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Nesta etapa, você vinculará o cluster da conta de armazenamento do Azure e o Azure HDInsight ao data factory.

#### Criar o serviço vinculado do armazenamento do Azure

1.	Clique no bloco **Criar e implantar** na folha **DATA FACTORY** para **CustomActivityFactory**. Isso inicia o Data Factory Editor.
2.	Clique em **Novo armazenamento de dados** na barra de comando e escolha **Armazenamento do Azure**. Você deve ver o script JSON para criar um serviço de armazenamento vinculado do Azure no editor.
3.	Substitua **nome da conta** pelo nome da conta de armazenamento do Azure e **chave de conta** pela chave de acesso da sua conta de armazenamento do Azure. Para saber como obter sua chave de acesso de armazenamento, confira [Exibir, copiar e regenerar chaves de acesso de armazenamento](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
4.	Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.


#### Criar o serviço vinculado do Azure HDInsight 
O serviço Data Factory do Azure dá suporte à criação de um cluster sob demanda e o usa para processar entrada a fim de gerar dados de saída. Você também pode usar seu próprio cluster para fazer isso. Quando você usa o cluster HDInsight sob demanda, um cluster é criado para cada fatia. Ao passo que, se você usar seu próprio cluster HDInsight, o cluster estará pronto para processar a fatia imediatamente. Portanto, quando você usar cluster sob demanda, não verá os dados de saída mais rapidamente que quando usa seu próprio cluster.

> [AZURE.NOTE] Em tempo de execução, uma instância de uma atividade do .NET é executada em somente um nó de trabalho no cluster HDInsight. Ela não pode ser dimensionada para ser executada em vários nós. Múltiplas instâncias de atividade do .NET podem ser executadas em paralelo em diferentes nós do cluster do HDInsight.

Se você tiver estendido o tutorial [Introdução ao Azure Data Factory][adfgetstarted] com o passo a passo de [Usar o Pig e Hive com o Azure Data Factory][hivewalkthrough], poderá pular a criação desse serviço vinculado e usar o serviço vinculado que você já tem em ADFTutorialDataFactory.


##### Para usar um cluster HDInsight sob demanda

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
			      "version": "3.2",
			      "linkedServiceName": "StorageLinkedService"
			    }
			  }
			}

2. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

##### Para usar seu próprio cluster HDInsight:

1. No **Portal do Azure**, clique em **Criar e implantar** na página inicial do Data Factory.
2. No **Editor Data Factory**, clique em **Nova computação** na barra de comandos e selecione **Cluster HDInsight** no menu.
2. No script JSON, faça o seguinte:
	1. Para a propriedade **clusterUri**, insira a URL para seu HDInsight. Por exemplo: https://<clustername>.azurehdinsight.net/     
	2. Para a propriedade **UserName**, digite o nome de usuário que tem acesso ao cluster HDInsight.
	3. Para a propriedade **Password**, especifique a senha para o usuário.
	4. Para a propriedade **LinkedServiceName**, digite **StorageLinkedService**. Este é o serviço vinculado que você criou no tutorial do guia de Introdução.

2. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

### Etapa 3: Criar conjuntos de dados
Nesta etapa, você criará conjuntos de dados para representar a entrada e saída de dados.

#### Criar conjunto de dados de entrada
1.	No **Editor** para Data Factory, clique no botão **Novo conjunto de dados** na barra de ferramentas e clique em **Armazenamento de Blob do Azure** no menu suspenso.
2.	Substitua o JSON no painel direito pelo trecho de código JSON a seguir:

			{
			    "name": "InputDataset",
			    "properties": {
			        "type": "AzureBlob",
			        "linkedServiceName": "StorageLinkedService",
			        "typeProperties": {
			            "folderPath": "adftutorial/customactivityinput/",
			            "format": {
			                "type": "TextFormat"
			            }
			        },
			        "availability": {
			            "frequency": "Hour",
			            "interval": 1
			        },
			        "external": true,
			        "policy": {}
			    }
			}

	Você criará um pipeline posteriormente neste passo a passo com hora de início: 2015-11-16T00:00:00Z e final: 2015-11-16T05:00:00Z. Ele é agendado para gerar dados de hora em hora, então haverá 5 fatias de entrada/saída (entre **00**: 00:00 -> **05**: 00:00).

	A **frequência** e o **intervalo** do conjunto de dados de entrada é definido como **Hora** e **1**, o que significa que a fatia de entrada está disponível por hora. Neste exemplo, é o mesmo arquivo (file.txt) na intputfolder.

	Aqui estão as horas de início de cada fatia, representado pela variável de sistema SliceStart no trecho de código JSON acima.

	
3.	Clique em **Implantar** na barra de ferramentas para implantar o **InputDataset**. Confirme que você vê a mensagem **TABELA CRIADA COM ÊXITO** na barra de título do Editor.


#### Criar uma tabela de saída

1. No **Editor Data Factory**, clique em **Novo conjunto de dados** e, em seguida, clique em **Armazenamento de Blobs do Azure** na barra de comandos.
2. Substitua o script JSON no painel direito pelo script JSON a seguir:

		{
		    "name": "OutputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "{slice}.txt",
		            "folderPath": "adftutorial/customactivityoutput",
		            "partitionedBy": [
		                {
		                    "name": "slice",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "yyyy-MM-dd-HH"
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

	Um blob/arquivo de saída é gerado para cada fatia de entrada. Aqui está como um arquivo de saída é chamado para cada fatia. Todos os arquivos de saída são gerados em uma pasta de saída: **adftutorial\\customactivityoutput**.

	| Fatia | Hora de início | Arquivo de saída |
	| :---- | :--------- | :---------- | 
	| 1 | 2015-11-16T00:00:00 | 2015-11-16-00.txt |
	| 2 | 2015-11-16T01:00:00 | 2015-11-16-01.txt |
	| 3 | 2015-11-16T02:00:00 | 2015-11-16-02.txt |
	| 4 | 2015-11-16T03:00:00 | 2015-11-16-03.txt |
	| 5 | 2015-11-16T04:00:00 | 2015-11-16-04.txt |

	Lembre-se de que todos os arquivos em uma pasta de entrada são parte de uma fatia com as horas de início mencionadas acima. Quando essa fatia é processada, a atividade personalizada examina cada arquivo e produz uma linha no arquivo de saída com o número de ocorrências do termo de pesquisa ("Microsoft"). Se houver três arquivos na inputfolder, haverá três linhas no arquivo de saída para cada fatia de hora: 2015-11-16-00.txt, 2015-11-16:01:00:00.txt, etc....


2. Clique em **Implantar** na barra de comando para implantar o **OutputDataset**.


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
		            "Name": "InputDataset"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputDataset"
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
    		"start": "2015-11-16T00:00:00Z",
			"end": "2015-11-16T05:00:00Z",
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
	- **PackageLinkedService** é definido como **StorageLinkedService** que aponta para o armazenamento de blobs que contém o arquivo zip da atividade personalizada. Se você estiver usando diferentes contas de armazenamento do Azure para arquivos de entrada/saída e o arquivo zip da atividade personalizada, terá de criar outro serviço vinculado do armazenamento do Azure. Este artigo pressupõe que você está usando a mesma conta de armazenamento do Azure.
	- **PackageFile** é definido como **customactivitycontainer/MyDotNetActivity.zip**. O formato é: <containerforthezip>/<nameofthezip.zip>.
	- A atividade personalizada usa **InputDataset** como entrada e **OutputDataset** como saída.
	- A propriedade linkedServiceName da atividade personalizada aponta para o **HDInsightLinkedService**, que informa ao Azure Data Factory que a atividade personalizada precisa ser executada em um cluster do Azure HDInsight.
	- A propriedade **isPaused** está definida como **falsa** por padrão. O pipeline é executado imediatamente neste exemplo porque a fatias começam no passado. Você pode definir essa propriedade como verdadeira para pausar o pipeline e defini-lo novamente como falsa para reiniciar. 
	- As horas de **início** e **fim** são distantes **5** horas e as fatias são produzidas por hora, portanto 5 fatias são produzidas pelo pipeline. 


4. Clique em **Implantar** na barra de comandos para implantar o pipeline.

### Monitorar o Pipeline
 
8. Na folha Data Factory do Portal do Azure, clique em **Diagrama**.
	
	![Bloco do diagrama](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
 
9. Na exibição Diagrama, clique em OutputDataset.
 
	![Exibição de diagrama](./media/data-factory-use-custom-activities/diagram.png)

10. Você verá que as 5 fatias de saída estarão no estado Pronto, se já tiverem sido produzidas.

	![Fatias de saída](./media/data-factory-use-custom-activities/OutputSlices.png)
	
12. Verifique se os arquivos de saída são gerados no armazenamento de blob no contêiner **adftutorial**.

	![saída de atividade personalizada][image-data-factory-ouput-from-custom-activity]

9. Se você abrir o arquivo de saída, verá uma saída semelhante ao seguinte:

	2 (s) de ocorrências do termo de pesquisa "Microsoft" foram encontradas no arquivo inputfolder/2015-11-16-00/file.txt.

10.	Use o [Portal do Azure][azure-preview-portal] ou cmdlets do PowerShell do Azure para monitorar sua data factory, pipelines e conjuntos de dados. Você pode ver as mensagens de **ActivityLogger** no código da atividade personalizada nos logs (especificamente, user-0.log) que você pode baixar do portal ou usando cmdlets.

	![baixar logs de atividade personalizada][image-data-factory-download-logs-from-custom-activity]


Consulte [Monitorar e Gerenciar Pipelines](data-factory-monitor-manage-pipelines.md) para obter etapas detalhadas do monitoramento de conjuntos de dados e pipelines.

### Depurar o pipeline
A depuração consiste em algumas técnicas básicas:

1.	Se a fatia de entrada não estiver definida como **Pronto**, confirme se a estrutura de pasta de entrada está correta e se file.txt existe nas pastas de entrada.
2.	No método **Execute** da atividade personalizada, use o objeto **IActivityLogger** para registrar informações que o ajudarão a solucionar problemas. As mensagens registradas aparecerão no arquivo user\_0.log. 

	Na folha **OutputDataset**, clique na fatia para ver a folha **FATIA DE DADOS** dessa fatia. Você verá as **execuções de atividade** para essa fatia. Você deverá ver uma execução de atividade para a fatia. Se você clicar em Executar na barra de comandos, poderá iniciar outra execução de atividade para a mesma fatia.

	Quando você clicar na execução da atividade, verá a folha **DETALHES DE EXECUÇÃO DA ATIVIDADE** com uma lista de arquivos de log. Você verá mensagens registradas no arquivo user\_0.log. Quando ocorrer um erro, você verá três execuções de atividade porque a contagem de repetições é definida como 3 no JSON do pipeline/atividade. Quando você clicar na execução da atividade, verá os arquivos de log que pode examinar para solucionar o erro.

	Na lista de arquivos de log, clique em **user-0.loo**. No painel à direita, estão os resultados do uso do método **IActivityLogger.Write**.

	Você também deve verificar **system-0.log** para quaisquer mensagens de erro e exceções do sistema.

3.	Inclua o arquivo **PDB** no arquivo zip para que os detalhes do erro tenham informações como **pilha de chamadas** quando ocorrer um erro.
4.	Todos os arquivos no arquivo zip da atividade personalizada devem estar no **nível mais elevado** sem subpastas.
5.	Verifique se **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip), e **packageLinkedService** (devem apontar para o armazenamento de blobs do Azure que contém o arquivo zip) estão definidos com os valores corretos. 
6.	Se você corrigir um erro e quiser reprocessar a fatia, clique com o botão direito do mouse na fatia na folha **OutputDataset** e clique em **Executar**. 


## Atualizar a atividade personalizada
Se você atualizar o código para a atividade personalizada, compile-o e carregue o arquivo zip que contém os novos binários para o armazenamento de blob.

## Acessar propriedades estendidas
Você pode declarar propriedades estendidas na atividade JSON, conforme mostrado abaixo:

	"typeProperties": {
	  "AssemblyName": "MyDotNetActivity.dll",
	  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
	  "PackageLinkedService": "StorageLinkedService",
	  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
	  "extendedProperties": {
	    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
		"DataFactoryName": "CustomActivityFactory"
	  }
	},

No exemplo acima, há duas propriedades estendidas: **SliceStart** e **DataFactoryName**. O valor de SliceStart baseia-se na variável de sistema SliceStart. Consulte [Variáveis de sistema](data-factory-scheduling-and-execution.md#data-factory-system-variables) para obter uma lista de variáveis de sistema com suporte. O valor de DataFactoryName é embutido no código como "CustomActivityFactory".

Para acessar essas propriedades estendidas no método **Execute**, use código semelhante ao seguinte:

	// to get extended properties (for example: SliceStart)
	DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
	string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

	// to log all extended properties                               
    IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
    logger.Write("Logging extended properties if any...");
    foreach (KeyValuePair<string, string> entry in extendedProperties)
    {
    	logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
	}


## <a name="AzureBatch"></a> Usar o serviço vinculado do Lote do Azure
> [AZURE.NOTE] Consulte [Fundamentos do Lote do Azure][batch-technical-overview] para uma visão geral do serviço do Lote do Azure e consulte [Introdução à biblioteca do Lote do Azure para .NET][batch-get-started] para iniciar rapidamente o serviço Lote do Azure.

Você pode executar suas atividades personalizadas do .NET usando o Azure Batch como um recurso de computação. Você precisará criar seus próprios pools do Azure Batch e especificar o número de VMs junto com outras configurações. Pools do Azure Batch fornecem os seguintes recursos para os clientes:

1. Criar pools que contenham um único núcleo até milhares de núcleos.
2. Dimensionamento automático da contagem de VM com base em uma fórmula
3. Suporte a VMs de qualquer tamanho
4. Número configurável de tarefas por VM
5. Número ilimitado de tarefas em fila.


Aqui estão os passos de alto nível para usar o serviço vinculado Azure Batch no passo a passo descrito na seção anterior:

1. Crie uma conta do Lote do Azure usando o [Portal do Azure](http://manage.windowsazure.com). Consulte o artigo [Criar e gerenciar uma conta do Lote do Azure][batch-create-account] para obter instruções. Anote a chave e o nome da conta do Azure Batch.

	Você também pode usar o cmdlet [New-AzureBatchAccount][new-azure-batch-account] para criar uma conta do Azure Batch. Consulte [usando o Azure PowerShell para gerenciar conta do Azure lote][azure-batch-blog] para obter instruções detalhadas sobre como usar este cmdlet.
2. Crie um pool do Azure Batch. Você pode baixar o código-fonte da [ferramenta Gerenciador do Lote do Azure][batch-explorer], compilar e usá-lo (ou) usar a [Biblioteca do Lote do Azure para .NET][batch-net-library] para criar um pool do Lote do Azure. Consulte [Passo a passo de exemplo do gerenciador do Azure Batch][batch-explorer-walkthrough] para obter instruções passo a passo de como usar o Gerenciador do Azure Batch.

	Você também pode usar o cmdlet [New-AzureRmBatchPool](https://msdn.microsoft.com/library/mt628690.aspx) para criar um pool do Lote do Azure.

	Crie o pool do Lote do Azure com pelo menos 2 nós de computação para que as fatias sejam processadas em paralelo. Se você estiver usando o Gerenciador de lotes:

	- Insira uma ID para o pool (**ID do Pool**). Observe a **ID do pool**; você precisará dela ao criar a solução Data Factory. 
	- Especifique **Windows Server 2012 R2** para a configuração da família do sistema operacional.
	- Especifique **2** como valor da configuração **Máximo de tarefas por nó de computação**.
	- Especifique **2** como valor da configuração **Número de destino dedicado**. 

	O serviço Data Factory cria um trabalho no Lote do Azure com o nome: df-<pool name>:job-xxx. Uma tarefa é criada para cada execução de atividade da fatia. Se houver 10 fatias prontas para serem processadas, 10 tarefas serão criadas neste trabalho. Pode haver mais de uma fatia em execução em paralelo, se você tiver vários nós de computação no pool. Você também pode ter mais de uma fatia em execução na mesmo computação, se o máximo de tarefas por nó de computação for definido como 1 >.
	
	![Tarefas do Gerenciador de lotes](./media/data-factory-use-custom-activities/BatchExplorerTasks.png)

	![Data Factory e lote](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

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

	> [AZURE.IMPORTANT] A **URL** da **folha de conta do Lote do Azure** está no seguinte formato: accountname.region.batch.azure.com. Para a propriedade **batchUri** no JSON, você precisará **remover "accountname."** da URL e usar o **accountname** para a propriedade JSON de **accountName**.

	Para a propriedade **poolName**, você também pode especificar a ID do pool em vez do nome do pool.

	Consulte [tópico do MSDN do serviço vinculado Azure Batch](https://msdn.microsoft.com/library/mt163609.aspx) para obter descrições dessas propriedades.

2.  No Editor Data Factory, abra a definição do JSON para o pipeline criado no passo a passo e substitua **HDInsightLinkedService** por **AzureBatchLinkedService**.
3.  Você talvez queira alterar as horas de início e término do pipeline, para que você possa testar o cenário com o serviço Azure Batch.
4.  Você pode ver as tarefas do Azure Batch associadas ao processamento das fatias no Gerenciador do Azure Batch, conforme mostrado no diagrama a seguir.

	![tarefas do Azure Batch][image-data-factory-azure-batch-tasks]

> [AZURE.NOTE] O serviço de Data Factory não suporta uma opção sob demanda para o Azure Batch como o faz para o HDInsight. Você só pode usar seu próprio pool do Azure Batch em um Azure Data Factory.

## Consulte também

[Atualizações do Azure Data Factory: Execute atividades ADF personalizadas do .NET usando o Azure Batch](https://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

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

<!---HONumber=AcomDC_0128_2016-->