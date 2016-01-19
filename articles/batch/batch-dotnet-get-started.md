<properties
	pageTitle="Tutorial - Introdução à Biblioteca .NET do Lote do Azure | Microsoft Azure"
	description="Saiba mais sobre os conceitos básicos do Lote do Azure e sobre como desenvolver para o serviço Lote com um cenário simples"
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="01/08/2016"
	ms.author="marsma"/>

# Introdução à biblioteca do Lote do Azure para .NET  

Conheça os fundamentos do [Lote do Azure][azure_batch] e da biblioteca do [.NET do Lote][net_api] à medida que abordamos um passo a passo do aplicativo de exemplo C# para vermos como ele aproveita o serviço Lote para processar uma carga de trabalho paralela na nuvem e como ele interage com o [Armazenamento do Azure](./../storage/storage-introduction.md) para preparação e recuperação de arquivos. Aprenda técnicas de fluxo de trabalho comuns do aplicativo Lote e obtenha uma compreensão básica dos principais componentes do Lote, como trabalhos, tarefas, pools e nós de computação.

![Fluxo de trabalho da solução do Lote (mínimo)][11]<br/>

## Pré-requisitos

Este artigo pressupõe que você tenha um conhecimento prático do C# e do Visual Studio, e que seja capaz de satisfazer os requisitos de criação de conta especificados abaixo para o Azure e os serviços de Armazenamento e de Lote.

### Contas

- **Conta do azure** -se você ainda não tiver uma assinatura do Azure, poderá criar uma conta de avaliação gratuita em minutos em [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).
- **Conta do Lote** - quando você tiver uma assinatura do Azure, poderá [Criar e gerenciar uma conta do Lote do Azure](batch-account-create-portal.md).
- **Conta de armazenamento** - veja a seção *Criar uma conta de armazenamento* em [Sobre as contas de armazenamento do Azure](../storage-create-storage-account.md).

### Visual Studio

Você deve ter o **Visual Studio 2013 ou posterior** para compilar o projeto de exemplo. Você pode encontrar versões gratuitas e de avaliação do Visual Studio na [Visão Geral dos Produtos Visual Studio 2015][visual_studio].

### Exemplo de código *DotNetTutorial*

O exemplo [DotNetTutorial][github_dotnettutorial] é um dos vários exemplos de código encontrados no repositório [azure-batch-samples][github_samples] no GitHub. Você pode baixar o exemplo clicando no botão "Baixar ZIP" na home page do repositório ou clicando no link de download direto de [azure-batch-samples-master.zip][github_samples_zip]. Depois de extrair o conteúdo do arquivo ZIP, você encontrará a solução na seguinte pasta:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### Gerenciador do Lote do Azure (opcional)

O [Gerenciador do Lote do Azure][github_batchexplorer] é um utilitário gratuito incluído no repositório [azure-batch-samples][github_samples] no GitHub. Embora não seja necessário para concluir este tutorial, seu uso é altamente recomendado na depuração e na administração de entidades na conta do Lote. Você pode ler sobre uma versão anterior do Gerenciador do Lote na postagem de blog [Passo a Passo do Gerenciador do Lote do Azure][batch_explorer_blog].

## Visão geral do projeto de exemplo DotNetTutorial

O exemplo de código *DotNetTutorial* é uma solução do Visual Studio 2013 que consiste em dois projetos: **DotNetTutorial** e **TaskApplication**.

- O **DotNetTutorial** é o aplicativo cliente que interage com os serviços Lote e Armazenamento para executar uma carga de trabalho paralela em nós de computação (máquinas virtuais). O DotNetTutorial é executado em sua estação de trabalho local.

- O **TaskApplication** é o executável que é executado em nós de computação no Azure para executar o trabalho real. No exemplo, o `TaskApplication.exe` analisa o texto em um arquivo baixado do Armazenamento do Azure (o arquivo de entrada) e produz um arquivo de texto (o arquivo de saída) que contém uma lista das três palavras principais que aparecem no arquivo de entrada. Depois de criar o arquivo de saída, o TaskApplication carrega o arquivo no Armazenamento do Azure, tornando-o disponível para o aplicativo cliente para download. O TaskApplication é executado em paralelo em vários nós de computação no serviço Lote.

O diagrama a seguir ilustra as principais operações executadas pelo aplicativo cliente, *DotNetTutorial* e o aplicativo é executado pelas tarefas *TaskApplication*. Esse fluxo de trabalho básico é típico de muitas soluções de computação criadas com o Lote e, embora não demonstre todos os recursos disponíveis no serviço Lote, praticamente todos os cenários do Lote incluirão processos semelhantes.

![Fluxo de trabalho de exemplo do Lote][8]<br/>

**1.** Crie **contêineres** no Armazenamento de Blobs do Azure<br/> **2.** Carregue o aplicativo de tarefas e os arquivos de entrada para contêineres<br/> **3.** Crie o **pool**<br/> do Lote &nbsp;&nbsp;&nbsp;&nbsp;**3a.** O pool **StartTask** baixa o binário da tarefa (TaskApplication) para os nós ao ingressarem no pool<br/> **4.** Crie o **trabalho**<br/> do Lote **5.** Adicione **tarefas** ao trabalho<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5a.** As tarefas serão agendadas para a execução em nós<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Cada tarefa baixa seus dados de entrada do Armazenamento do Azure e, em seguida, inicia a execução<br/> **6.** Monitore as tarefas<br/> &nbsp;&nbsp;&nbsp;&nbsp;**6a.** À medida que as tarefas são concluídas, elas carregam os dados de saída no Armazenamento do Azure<br/> **7.** Baixar a saída da tarefa do Armazenamento

Como mencionado, nem todas as soluções do Lote executarão essas etapas exatas e podem incluir muitas outras, mas o aplicativo de exemplo *DotNetTutorial* demonstra os processos comuns encontrados em uma solução do Lote.

## Compilar o projeto de exemplo *DotNetTutorial*

Antes de poder executar o exemplo com êxito, você deverá especificar as credenciais de conta do Lote e do Armazenamento no arquivo `Program.cs` do projeto *DotNetTutorial*. Se você ainda não tiver feito isso, abra a solução no Visual Studio clicando duas vezes no arquivo de soluçã `DotNetTutorial.sln` ou no Visual Studio usando o menu **Arquivo > Abrir > Projeto/Solução**.

Abra `Program.cs` no projeto *DotNetTutorial* e adicione suas credenciais como especificado na parte superior do arquivo:

```
// Update the Batch and Storage account credential strings below with the values unique to your accounts.
// These are used when constructing connection strings for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

Você pode encontrar suas credenciais de conta do Lote e do Armazenamento na folha de conta de cada serviço no [Portal do Azure][azure_portal]\:

![Credenciais do Lote no Portal][9] ![Credenciais do Armazenamento no Portal][10]<br/>

Agora que você atualizou o projeto com suas credenciais, clique com botão direito do mouse na solução no *Gerenciador de Soluções* e clique em **Compilar Solução**. Confirme a restauração de qualquer pacote NuGet, se solicitado.

> [AZURE.TIP]Se os pacotes NuGet não forem automaticamente restaurados ou se você encontrar erros em relação a uma falha ao restaurar os pacotes, verifique se você tem o [Gerenciador de Pacotes NuGet][nuget_packagemgr] instalado e habilite o download de pacotes ausentes. Confira [Habilitando a Restauração de Pacotes Durante a Compilação][nuget_restore] para habilitar o download de pacotes.

Nas seções a seguir, separamos o aplicativo de exemplo nas etapas executadas para processar uma carga de trabalho no serviço Lote e discutimos essas etapas detalhadamente. Você é incentivado a fazer referência à solução aberta no Visual Studio enquanto estiver trabalhando até o restante deste artigo já que nem todas as linhas de código do exemplo serão discutidas.

Navegue até a parte superior do método `MainAsync` no `Program.cs` do projeto *DotNetTutorial* para começar pela Etapa 1. Cada etapa abaixo segue aproximadamente a progressão de chamadas de método em `MainAsync`.

## Etapa 1: Criar contêineres do Armazenamento

![Criar contêineres no Armazenamento do Azure][1] <br/>

O Lote inclui suporte interno para a interação com o Armazenamento do Azure e os contêineres em sua conta do Armazenamento fornecerão tarefas executadas em sua conta do Lote com os arquivos necessários para a execução, além de um local para armazenar os dados de saída que foram produzidos. A primeira coisa que o aplicativo cliente *DotNetTutorial* faz é criar três contêineres no [Armazenamento de Blobs do Azure](./../storage/storage-introduction.md):

- **application** - esse contêiner hospedará o aplicativo que será executado pelas tarefas, além de qualquer uma de suas dependências, como DLLs.
- **input** - as tarefas baixarão os arquivos de dados que devem processar a partir do contêiner *input*.
- **output** - quando as tarefas concluírem o processamento dos arquivos de entrada, carregarão os resultados no contêiner *output*.

Para interagir com uma conta do Armazenamento e criar contêineres, a [Biblioteca de Cliente do Armazenamento do Azure para .NET][net_api_storage] é usada para criar uma referência à conta com [CloudStorageAccount][net_cloudstorageaccount] e, a partir dela, um [CloudBlobClient][net_cloudblobclient] é obtido:

```
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                                                StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Usamos a referência `blobClient` em todo o aplicativo, passando-a como um parâmetro para uma série de métodos. Um exemplo disso é o bloco de código logo após o trecho mostrado acima, onde chamamos `CreateContainerIfNotExistAsync` para realmente criarmos os contêineres.

```
// Use the blob client to create the containers in Azure Storage if they don't yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```
private static async Task CreateContainerIfNotExistAsync(CloudBlobClient blobClient, string containerName)
{
		CloudBlobContainer container = blobClient.GetContainerReference(containerName);

		if (await container.CreateIfNotExistsAsync())
		{
				Console.WriteLine("Container [{0}] created.", containerName);
		}
		else
		{
				Console.WriteLine("Container [{0}] exists, skipping creation.", containerName);
		}
}
```

Depois que os contêineres tiverem sido criados, o aplicativo poderá carregar os arquivos que serão usados pelas tarefas.

> [AZURE.TIP] [How to use Blob storage from .NET](./.. / storage/storage-dotnet-how-to-use-blobs.md) fornece uma boa visão geral de como trabalhar com blobs e contêineres do Armazenamento do Azure e deverá estar na parte superior da sua lista de leitura quando você começar a trabalhar com o Lote.

## Etapa 2: carregar aplicativos e de tarefa e arquivos de dados

![Carregar arquivos de aplicativo e de entrada (dados) da tarefa nos contêineres][2] <br/>

Na operação de carregamento do arquivo, *DotNetTutorial* primeiro define as coleções de caminhos de arquivo **application** e **input** como eles existem no computador local e, em seguida, carrega esses arquivos nos contêineres criados na etapa anterior.

```
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication, allowing us to
    // determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the application that will
// process the data files, and will be executed by each of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(blobClient, appContainerName, applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of the tasks that are
// executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(blobClient, inputContainerName, inputFilePaths);
```

Há dois métodos no `Program.cs` que estão envolvidos no processo de carregamento:

- `UploadFilesToContainerAsync` - esse método retorna uma coleção de objetos [ResourceFile][net_resourcefile] (discutidos abaixo) e internamente chama `UploadFileToContainerAsync` para carregar cada arquivo passado no parâmetro *filePaths*.
- `UploadFileToContainerAsync` - esse é o método que realmente executa o carregamento do arquivo e cria os objetos [ResourceFile][net_resourcefile]. Depois de carregar o arquivo, ele obtém uma Assinatura de Acesso Compartilhado (SAS) para o arquivo e retorna um objeto ResourceFile que a representa. As assinaturas de acesso compartilhado também são discutidas abaixo.

```
private static async Task<ResourceFile> UploadFileToContainerAsync(CloudBlobClient blobClient, string containerName, string filePath)
{
		Console.WriteLine("Uploading file {0} to container [{1}]...", filePath, containerName);

		string blobName = Path.GetFileName(filePath);

		CloudBlobContainer container = blobClient.GetContainerReference(containerName);
		CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
		await blobData.UploadFromFileAsync(filePath, FileMode.Open);

		// Set the expiry time and permissions for the blob shared access signature. In this case, no start time is specified,
		// so the shared access signature becomes valid immediately
		SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
		{
				SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
				Permissions = SharedAccessBlobPermissions.Read
		};

		// Construct the SAS URL for blob
		string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
		string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

		return new ResourceFile(blobSasUri, blobName);
}
```

### ResourceFiles

O [ResourceFile][net_resourcefile] fornece tarefas no Lote com a URL para um arquivo no Armazenamento do Azure que será baixado para um nó de computação antes da execução da tarefa. A propriedade [ResourceFile.BlobSource][net_resourcefile_blobsource] especifica a URL completa do arquivo conforme ela existe no Armazenamento do Azure, o que também pode incluir uma assinatura de acesso compartilhado (SAS) que fornece acesso seguro ao arquivo. A maioria dos tipos de tarefas no .NET do Lote inclui uma propriedade *ResourceFiles*, incluindo:

- [CloudTask][net_task]
- [StartTask][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

O aplicativo de exemplo DotNetTutorial não usa a JobPreparationTask ou a JobReleaseTask, mas você pode ler mais sobre isso em [Executar tarefas de preparação e de conclusão de trabalhos em nós de computação do Lote do Azure](batch-job-prep-release.md).

### SAS (Assinaturas de Acesso Compartilhado)

As assinaturas de acesso compartilhado são cadeias de caracteres que, quando incluídas como parte de uma URL, oferecem acesso seguro a contêineres e a blobs no Armazenamento do Azure. O aplicativo DotNetTutorial usa URLs SAS de blob e de contêiner e demonstra como obter essas cadeias de caracteres SAS a partir do serviço de Armazenamento.

- **SAS do Blob** - a StartTask do pool em DotNetTutorial usa assinaturas de acesso compartilhado de blob ao baixar os binários do aplicativo e os dados de entrada de arquivos de Armazenamento (confira a Etapa 3 abaixo). O método `UploadFileToContainerAsync` no `Program.cs` do DotNetTutorial contém o código que obtém a SAS de cada blob e faz isso chamando [CloudblobData.GetSharedAccessSignature][net_sas_blob].

- **SAS do contêiner** - como cada tarefa conclui seu trabalho em nós de computação, ele carrega o arquivo de saída para o contêiner *output* no Armazenamento do Azure. Para fazer isso, o TaskApplication usa um SAS de contêiner que fornece acesso de gravação ao contêiner como parte do caminho ao carregar o arquivo. A obtenção da SAS do contêiner é feita de maneira semelhante, como ao obter a SAS do blob e, no DotNetTutorial, você verá que o método auxiliar `GetContainerSasUrl` chama [CloudBlobContainer.GetSharedAccessSignature][net_sas_container] para fazer isso. Leia mais sobre como o TaskApplication usa a SAS do contêiner na Etapa 6 abaixo, "Monitorar Tarefas".

> [AZURE.TIP]Confira a série de duas partes sobre as assinaturas de acesso compartilhado, [Parte 1: Noções Básicas sobre o Modelo SAS](./../storage/storage-dotnet-shared-access-signature-part-1.md) e [Parte 2: Criar e Usar uma SAS com o Serviço Blob](./../storage/storage-dotnet-shared-access-signature-part-2.md), para saber mais sobre como fornecer acesso seguro aos dados em sua conta de Armazenamento.

## Etapa 3: Criar pool do Lote

![Criar pool do Lote][3] <br/>

Depois de carregar os arquivos de aplicativo e de dados para a conta de Armazenamento, o *DotNetTutorial* inicia sua interação com o serviço Lote usando a biblioteca .NET do Lote. Para fazer isso, um [BatchClient][net_batchclient] é criado primeiro:

```
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);
using (BatchClient batchClient = BatchClient.Open(cred))
{
	...
```

Em seguida, um pool de nós de computação é criado na conta do Lote com uma chamada para `CreatePoolAsync`. `CreatePoolAsync` usa o método [BatchClient.PoolOperations.CreatePool][net_pool_create] para realmente criar o pool no serviço Lote.

```
private static async Task CreatePoolAsync(BatchClient batchClient, string poolId, IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(), no pool is actually created in the
    // Batch service. This CloudPool instance is therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
				poolId: poolId,
				targetDedicated: 3,           // 3 compute nodes
				virtualMachineSize: "small",  // single-core, 1.75 GB memory, 225 GB disk
				osFamily: "4");               // Windows Server 2012 R2

    // Create and assign the StartTask that will be executed when compute nodes join the pool.
    // In this case, we copy the StartTask's resource files (that will be automatically downloaded
    // to the node by the StartTask) into the shared directory that all tasks will have access to.
    pool.StartTask = new StartTask
    {
        // Specify a command line for the StartTask that copies the task application files to the
        // node's shared directory. Every compute node in a Batch pool is configured with a number
        // of pre-defined environment variables that can be referenced by commands or applications
        // run by tasks.

        // Since a successful execution of robocopy can return a non-zero exit code (e.g. 1 when one or
        // more files were successfully copied) we need to manually exit with a 0 for Batch to recognize
        // StartTask execution success.
        CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
        ResourceFiles = resourceFiles,
        WaitForSuccess = true
    };

    await pool.CommitAsync();
}
```

Ao criar um pool com [CreatePool][net_pool_create], você especificará um número de parâmetros como o número de nós de computação, o [tamanho dos nós](./../cloud-services/cloud-services-sizes-specs.md) e o [sistema operacional](./../cloud-services/cloud-services-guestos-update-matrix.md) dos nós.

> [AZURE.IMPORTANT]Você é cobrado pelos recursos de computação no Lote. Para minimizar o custo, você poderá diminuir `targetDedicated` para 1 antes de executar o exemplo.

Junto com essas propriedades de nó físico, você também poderá especificar uma [StartTask][net_pool_starttask] para o pool. A StartTask será executada em cada nó quando o nó ingressar no pool e sempre que um nó for reiniciado. A StartTask é especialmente útil para instalar aplicativos em nós de computação antes da execução de tarefas. Por exemplo, se suas tarefas processassem dados usando scripts Python, você poderia usar uma StartTask para instalar o Python nos nós de computação.

Neste aplicativo de exemplo, a StartTask copia os arquivos baixados do Armazenamento (especificados usando a propriedade *ResourceFiles* da StartTask) a partir do diretório de trabalho StartTask para o diretório compartilhado que *todas* as tarefas em execução no nó podem acessar.

Também podemos notar no trecho de código acima o uso de duas variáveis de ambiente na propriedade *CommandLine* da StartTask: `%AZ_BATCH_TASK_WORKING_DIR%` e `%AZ_BATCH_NODE_SHARED_DIR%`. Cada nó de computação em um pool do Lote é configurado automaticamente com um número de variáveis de ambiente específicas do Lote e todos os processos executados por uma tarefa terão acesso a essas variáveis de ambiente.

> [AZURE.TIP]Para saber mais sobre as variáveis de ambiente disponíveis em nós de computação em um pool do Lote, além de informações sobre os diretórios de trabalho da tarefa, confira as seções **Configurações de ambiente para tarefas** e **Arquivos e diretórios** na [Visão geral dos recursos do Lote do Azure](batch-api-basics.md).

## Etapa 4: Criar o trabalho do Lote

![Criar trabalho do Lote][4]<br/>

Um trabalho do Lote é essencialmente uma coleção de tarefas associadas a um pool de nós de computação e é usado não apenas para organizar e acompanhar tarefas relacionadas de cargas de trabalho, mas também pode impor certas restrições, como o tempo máximo de execução do trabalho (e, por extensão, de suas tarefas), além da prioridade do trabalho em relação a outros trabalhos na conta do Lote. Neste exemplo, no entanto, o trabalho só estará associado ao pool criado na etapa 3 e nenhuma propriedade adicional será configurada.

Todos os trabalhos do Lotes estão associados a um pool específico. Isso indica em quais nós as tarefas do trabalho serão executadas e é feito usando a propriedade [CloudJob.PoolInformation][net_job_poolinfo] como mostrado no trecho de código abaixo.

```
private static async Task CreateJobAsync(BatchClient batchClient, string jobId, string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Agora que um trabalho foi criado, as tarefas serão adicionadas para a execução do trabalho.

## Etapa 5: Adicionar tarefas ao trabalho

![Adicionar tarefas ao trabalho][5]<br/> *(1) As tarefas são adicionadas ao trabalho, (2) as tarefas são agendadas para execução em nós e (3) as tarefas baixam os arquivos de dados para processamento*

Para realmente executar o trabalho, as tarefas devem ser adicionadas a um trabalho. Cada [CloudTask][net_task] é configurada com uma linha de comando e, assim como acontece com a StartTask do pool, a propriedade [ResourceFiles][net_task_resourcefiles] que a tarefa baixa para o nó antes da linha de comando é executada automaticamente. No projeto de exemplo *DotNetTutorial*, cada tarefa processa apenas um arquivo e, portando, sua coleção ResourceFiles contém um único elemento.

```
private static async Task<List<CloudTask>> AddTasksAsync(BatchClient batchClient, string jobId, List<ResourceFile> inputFiles, string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on whichever node each task will run.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format("cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 "{1}"", inputFile.FilePath, outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection opposed to a separate AddTask call for each. Bulk task submission
    // helps to ensure efficient underlying API calls to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [AZURE.IMPORTANT]Ao acessar variáveis de ambiente, como `%AZ_BATCH_NODE_SHARED_DIR%` ou ao executar um aplicativo não encontrado no `PATH` do nó, as linhas de comando da tarefa devem ser prefixadas com `cmd /c` para executar explicitamente o interpretador de comandos e para instruí-lo a terminar após a execução do comando. Esse requisito será desnecessário se suas tarefas executarem um aplicativo no CAMINHO do nó (como *robocopy.exe* ou *powershell.exe*) e nenhuma variável de ambiente será usada.

No loop `foreach` no trecho de código acima, você verá que a linha de comando da tarefa é construída de forma que três argumentos de linha de comando sejam passados para *TaskApplication.exe*:

1. O **primeiro argumento** é o caminho do arquivo a ser processado. Esse é o caminho local para o arquivo conforme ele existe no nó. Ao criar pela primeira vez o objeto ResourceFile em `UploadFileToContainerAsync` acima, o nome do arquivo foi usado para essa propriedade (como um parâmetro para o construtor ResourceFile), indicando assim que o arquivo pode ser encontrado no mesmo diretório que *TaskApplication.exe*.

2. O **segundo argumento** especifica que as *N* palavras principais devem ser gravadas no arquivo de saída. No exemplo, isso é codificado para que as 3 palavras principais sejam gravadas no arquivo de saída.

3. O **terceiro argumento** é a assinatura de acesso compartilhado (SAS) que fornece acesso de gravação ao contêiner **output** no Armazenamento do Azure. *TaskApplication.exe* usa essa URL da SAS ao carregar o arquivo de saída para o Armazenamento do Azure e o código para isso pode ser encontrado no método `UploadFileToContainer` do arquivo `Program.cs` do projeto TaskApplication:

```
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
		string blobName = Path.GetFileName(filePath);

		// Obtain a reference to the container using the SAS URI.
		CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

		// Upload the file (as a new blob) to the container
		try
		{
				CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
				blob.UploadFromFile(filePath, FileMode.Open);

				Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
				Console.WriteLine();
		}
		catch (StorageException e)
		{

				Console.WriteLine("Write operation failed for SAS URL " + containerSas);
				Console.WriteLine("Additional error information: " + e.Message);
				Console.WriteLine();

				// Indicate that a failure has occurred so that when the Batch service sets the
				// CloudTask.ExecutionInformation.ExitCode for the task that executed this application,
				// it properly indicates that there was a problem with the task.
				Environment.ExitCode = -1;
		}
}
```

## Etapa 6: Monitorar tarefas

![Monitorar tarefas][6]<br/> *O aplicativo cliente (1) monitora as tarefas para a conclusão e o status de êxito e (2) os dados resultantes do carregamento de tarefas para o Armazenamento do Azure*

Quando as tarefas são adicionadas a um trabalho, são automaticamente enfileiradas e agendadas para execução em nós de computação no pool associado ao trabalho. Com base nas configurações especificadas, o Lote manipula o enfileiramento, o agendamento, a repetição de todas as tarefas e outras obrigações de administração de tarefas para você. Há muitas abordagens para a execução da tarefa de monitoramento - DotNetTutorial mostra um exemplo simples que relata apenas a conclusão e a falha de tarefas ou os estados de êxito.

No método `MonitorTasks` no `Program.cs` do DotNetTutorial, há três conceitos do .NET do Lote que garantem a discussão, listados em ordem de aparição:

1. **ODATADetailLevel** - a especificação de um [ODATADetailLevel][net_odatadetaillevel] na lista de operações (como a obtenção de uma lista de tarefas do trabalho) é essencial para garantir o desempenho do aplicativo do Lote. Adicione [Consultar o serviço Lote do Azure com eficiência](batch-efficient-list-queries.md) à sua lista de leitura se você planejar fazer qualquer tipo de monitoramento de status em seus aplicativos do Lote.

2. **TaskStateMonitor** - o [TaskStateMonitor][net_taskstatemonitor] fornece aplicativos .NET do Lote com utilitários auxiliares para monitorar estados da tarefa. Em `MonitorTasks`, *DotNetTutorial* aguarda que todas as tarefas atinjam [TaskState.Completed][net_taskstate] dentro de um limite de tempo e então encerra o trabalho.

3. **TerminateJobAsync** - o encerramento de um trabalho com [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] (ou o bloqueio JobOperations.TerminateJob) marcará esse trabalho como concluído. Fazer isso será essencial caso sua solução do Lote use uma [JobReleaseTask][net_jobreltask], um tipo especial de tarefa detalhada em [Tarefas de preparação e de conclusão de trabalhos](batch-job-prep-release).

O método `MonitorTasks` no `Program.cs` do *DotNetTutorial* aparece abaixo:

```
private static async Task<bool> MonitorTasks(BatchClient batchClient, string jobId, TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use a detail level to
    // specify that only the "id" property of each task should be populated. Using a detail level for
    // all list operations helps to lower response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks = await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...", timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we will wait for all tasks to
    // reach the Completed state.
    TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
    bool timedOut = await taskStateMonitor.WaitAllAsync(tasks, TaskState.Completed, timeout);

    if (timedOut)
    {
        allTasksSuccessful = false;

        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);

        Console.WriteLine(failureMessage);
    }
    else
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

        // All tasks have reached the "Completed" state, however, this does not guarantee all tasks completed successfully.
        // Here we further check each task's ExecutionInfo property to ensure that it did not encounter a scheduling error
        // or return a non-zero exit code.

        // Update the detail level to populate only the task id and executionInfo properties.
        // We refresh the tasks below, and need only this information for each task.
        detail.SelectClause = "id, executionInfo";

        foreach (CloudTask task in tasks)
        {
            // Populate the task's properties with the latest info from the Batch service
            await task.RefreshAsync(detail);

            if (task.ExecutionInformation.SchedulingError != null)
            {
                // A scheduling error indicates a problem starting the task on the node. It is important to note that
                // the task's state can be "Completed," yet still have encountered a scheduling error.

                allTasksSuccessful = false;

                Console.WriteLine("WARNING: Task [{0}] encountered a scheduling error: {1}", task.Id, task.ExecutionInformation.SchedulingError.Message);
            }
            else if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by the task encountered an error
                // during execution. As not every application returns non-zero on failure by default (e.g. robocopy),
                // your implementation of error checking may differ from this example.

                allTasksSuccessful = false;

                Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
            }
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## Etapa 7: Baixar a saída da tarefa

![Baixar a saída da tarefa do Armazenamento][7]<br/>

Agora que o trabalho foi concluído, a saída das tarefas pode ser baixada do Armazenamento do Azure. Isso é feito com uma chamada a `DownloadBlobsFromContainerAsync` no `Program.cs` do *DotNetTutorial*:

```
private static async Task DownloadBlobsFromContainerAsync(CloudBlobClient blobClient, string containerName, string directoryPath)
{
		Console.WriteLine("Downloading all files from container [{0}]...", containerName);

		// Retrieve a reference to a previously created container
		CloudBlobContainer container = blobClient.GetContainerReference(containerName);

		// Get a flat listing of all the block blobs in the specified container
		foreach (IListBlobItem item in container.ListBlobs(prefix: null, useFlatBlobListing: true))
		{
				// Retrieve reference to the current blob
				CloudBlob blob = (CloudBlob)item;

				// Save blob contents to a file in the specified folder
				string localOutputFile = Path.Combine(directoryPath, blob.Name);
				await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
		}

		Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [AZURE.NOTE]A chamada a `DownloadBlobsFromContainerAsync` no aplicativo *DotNetTutorial* especifica que os arquivos devem ser baixados para a pasta `%TEMP%`. Fique à vontade para modificar esse local de saída.

## Etapa 8: Excluir contêineres

Como você é cobrado pelos dados que residem no Armazenamento do Azure, sempre será uma boa ideia remover todos os blobs que não sejam mais necessários para seus trabalhos do Lotes. No `Program.cs` do DotNetTutorial, isso é feito com três chamadas para o método auxiliar `DeleteContainerAsync`:

```
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

O próprio método simplesmente obtém uma referência para o contêiner e, em seguida, chama [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]\:

```
private static async Task DeleteContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.", containerName);
    }
}
```

## Etapa 9: Excluir o trabalho e o pool

Na etapa final, o usuário é solicitado a excluir o trabalho e o pool criados pelo aplicativo DotNetTutorial. Embora você não seja cobrado pelos trabalhos e pelas tarefas, *será* cobrado pelos nós de computação e, portanto, é recomendável alocar os nós conforme necessário e excluir os pools não utilizados como parte de seu processo de manutenção.

As [JobOperations][net_joboperations] e [PoolOperations][net_pooloperations] do BatchClient têm métodos de exclusão correspondentes, chamados se o usuário confirmar a exclusão:

```
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [AZURE.IMPORTANT]Tenha em mente que você será cobrado pelos recursos de computação e a exclusão dos pools não utilizados reduzirá o custo. Lembre-se de que a exclusão de um pool exclui todos os nós de computação no pool e que os dados em nós não poderão ser recuperados depois que o pool for excluído.

## Executar o exemplo *DotNetTutorial*

Quando você executar o aplicativo de exemplo, a saída do console será semelhante à seguinte. Durante a execução, você terá uma pausa em `Awaiting task completion, timeout in 00:30:00...` enquanto os nós de computação do pool são iniciados. Use o [Gerenciador do Lote][github_batchexplorer] para monitorar o pool, os nós de computação, o trabalho e as tarefas durante e após a execução. Use o [Portal do Azure][azure_portal] ou um dos [gerenciadores do Armazenamento do Azure disponíveis][storage_explorers] para exibir os recursos do Armazenamento (contêineres e blobs) criados pelo aplicativo.

O tempo de execução típico é de **aproximadamente 5 minutos** ao executar o aplicativo em sua configuração padrão.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## Próximas etapas

Fique à vontade para fazer alterações em *DotNetTutorial* e em *TaskApplication* para fazer experiências com cenários de computação diferentes. Tente adicionar um atraso de execução para *TaskApplication*, como com [Thread.Sleep][net_thread_sleep], para simular tarefas demoradas e para monitorá-las com o recurso *Mapa de Calor* do Gerenciador do Lote. Tente adicionar mais tarefas ou ajustar o número de nós de computação. Adicione lógica para verificar e permitir o uso de um pool existente para acelerar o tempo de execução (*Dica*: confira `ArticleHelpers.cs` no projeto [Microsoft.Azure.Batch.Samples.Common][github_samples_common] em [azure-batch-samples][github_samples]).

Agora que você está familiarizado com o fluxo de trabalho básico de uma solução do Lote, é hora de se aprofundar nos recursos adicionais do serviço Lote.

- [Visão geral dos recursos do Lote do Azure](batch-api-basics.md) - este artigo oferece uma visão geral dos diversos recursos do Lote e é uma leitura recomendável para quem ainda não conhece o serviço.
- Comece pelos outros artigos de desenvolvimento do Lote em **Desenvolvimento detalhado** no [Roteiro de aprendizagem do Lote][batch_learning_path]
- Confira uma implementação diferente do processamento da carga de trabalho “N palavras principais” usando o Lote no exemplo [TopNWords][github_topnwords]

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_portal]: https://portal.azure.com
[batch_explorer_blog]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Criar contêineres no Armazenamento do Azure"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Carregar arquivos de aplicativo e de entrada (dados) da tarefa nos contêineres"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Criar pool do Lote"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Criar trabalho do Lote"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Adicionar tarefas ao trabalho"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Monitorar tarefas"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Baixar a saída da tarefa do Armazenamento"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Fluxo de trabalho da solução do Lote (diagrama completo)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Credenciais do Lote no Portal"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Credenciais do Armazenamento no Portal"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Fluxo de trabalho da solução do Lote (diagrama mínimo)"

<!---HONumber=AcomDC_0114_2016-->