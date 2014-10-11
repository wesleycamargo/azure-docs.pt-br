<properties linkid="develop-media-services-tutorials-get-started" urlDisplayName="Get Started with Media Services" pageTitle="Get Started with Media Services - Azure" metaKeywords="Azure media services" description="An introduction to using Media Services with Azure." metaCanonical="" services="media-services" documentationCenter="" title="Get started with Media Services" authors="" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# <a name="getting-started"></a>Introdução aos Serviços de Mídia

Este tutorial mostra como começar a desenvolver com os Serviços de Mídia do Azure. Ele apresenta o fluxo de trabalho básico dos Serviços de Mídia e os objetos e as tarefas de programação mais comuns necessárias para o desenvolvimento dos Serviços de Mídia do Windows Azure. No final do tutorial, você poderá reproduzir um arquivo de mídia de exemplo que você carrega, codifica e baixa. Ou você pode navegar para o ativo codificado e executá-lo novamente no servidor.

Um projeto C# do Visual Studio que contém o código para este tutorial está disponível aqui: [Baixe o][].

Este tutorial apresenta e explica as seguintes etapas básicas:

-   [Configurando o seu projeto][]
-   [Obtendo o contexto do servidor dos Serviços de Mídia][]
-   [Criando um ativo e carregando arquivos que estão associados ao ativo nos Serviços de Mídia][]
-   [Codificando um ativo e baixando um ativo de saída][]

## Pré-requisitos

Os seguintes pré-requisitos são necessários para a explicação passo a passo e o desenvolvimento baseado no SDK dos Serviços de Mídia do Azure.

-   Uma conta dos Serviços de Mídia em uma assinatura nova ou existente do Azure. Para obter detalhes, consulte [Como criar uma conta de Serviços de Mídia (a página pode estar em inglês)][].
-   Sistemas operacionais: Windows 7, Windows 2008 R2 ou Windows 8.
-   .NET Framework 4.5 ou .NET Framework 4.
-   Visual Studio 2012 ou Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express).
-   Instale o **SDK do Azure para .NET.**, o **SDK dos Serviços de Mídia do Azure para .NET** e o **WCF Data Services 5.0 para bibliotecas OData V3** e adicione referências a seu projeto usando o pacote [Nuget windowsazure.mediaservices][]. A seção a seguir demonstra como instalar e adicionar essas referências.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

## <span id="Step1"></span></a>Configurando o seu projeto

1.  Crie um novo Aplicativo de Console C# no Visual Studio 2012 ou no Visual Studio 2010 SP1. Digite o **nome**, o **local** e o **Nome da solução** e OK.

2.  Adicione uma referência ao assembly System.Configuration.

    Para adicionar referências usando a caixa de diálogo **Gerenciar Referências**, proceda da seguinte maneira. Clique com o botão direito do mouse no nó **Referências** no **Gerenciador de Soluções** e selecione **Adicionar Referência**. Na caixa de diálogo **Gerenciar Referências**, selecione os conjuntos apropriados (neste caso System.Configuration.)

3.  Se ainda não tiver adicionado, adicione referências ao **SDK do Azure para .NET**.(Microsoft.WindowsAzure.StorageClient.dll), ao **SDK dos Serviços de Mídia do Azure para .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll) e ao **WCF Data Services 5.0 para bibliotecas OData V3** (Microsoft.Data.OData.dll) usando o pacote [NuGet windowsazure.mediaservices][Nuget windowsazure.mediaservices].

    Para adicionar referências usando o NuGet, proceda da seguinte maneira: No Menu Principal do Visual Studio, selecione FERRAMENTAS - \>Gerenciador de Pacotes da Biblioteca -\> Console do Gerenciador de Pacotes. Na janela do console, digite *Install-Package [nome do pacote]* e pressione Enter (neste caso, use o seguinte comando: *Install-Package windowsazure.mediaservices*).

4.  Adicione uma seção *appSettings* ao arquivo **app.config** e defina os valores do nome e da chave de sua conta dos Serviços de Mídia do Azure. Você obteve o nome e a chave da conta dos Serviços de Mídia durante o processo de configuração da conta. Adicione esses valores ao atributo de valor de cada configuração do arquivo app.config no projeto do Visual Studio.

    > [WACOM.NOTE]
    > No Visual Studio 2012, o arquivo App.config é adicionado por padrão. No Visual Studio 2010, você precisa adicionar manualmente o arquivo de Configuração do Aplicativo.

        <configuration>
        . . . 
        <appSettings>
            <add key="accountName" value="Add-Media-Services-Account-Name" />
            <add key="accountKey" value="Add-Media-Services-Account-Key" />
        </appSettings>
        </configuration>

5.  Crie uma nova pasta em sua máquina local e nomeie-a como supportFiles (neste exemplo, supportFiles está localizada no diretório do projeto MediaServicesGettingStarted.) O [Projeto][Baixe o] que acompanha esta explicação passo a passo contém o diretório supportFiles. Você pode copiar o conteúdo desse diretório em sua pasta supportFiles.

6.  Substitua a instruções using existentes no início do arquivo Program.cs pelo código a seguir.

        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Collections.Generic;
        using Microsoft.WindowsAzure;
        using Microsoft.WindowsAzure.MediaServices.Client;

7.  Adicione as seguintes variáveis de caminho de nível de classe. O caminho \*\*\_supportFiles\*\* deve apontar para a pasta que você criou na etapa anterior.

        // Base support files path.  Update this field to point to the base path  
        // for the local support files folder that you create. 
        private static readonly string _supportFiles =
                    Path.GetFullPath(@"../..\supportFiles");

        // Paths to support files (within the above base path). You can use 
        // the provided sample media files from the "supportFiles" folder, or 
        // provide paths to your own media files below to run these samples.
        private static readonly string _singleInputFilePath =
            Path.GetFullPath(_supportFiles + @"\multifile\interview2.wmv");
        private static readonly string _outputFilesFolder =
            Path.GetFullPath(_supportFiles + @"\outputfiles");

8.  Adicione as seguintes variáveis de nível de classe para recuperar as configurações de autenticação e conexão. Essas configurações são extraídas do arquivo App.Config e são necessárias para que você se conecte aos Serviços de Mídia, autentique-se e obtenha um token para acessar o contexto do servidor. O código no projeto faz referência a essas variáveis para criar uma instância do contexto do servidor.

        private static readonly string _accountKey = ConfigurationManager.AppSettings["accountKey"];
        private static readonly string _accountName = ConfigurationManager.AppSettings["accountName"];

9.  Adicione a seguinte variável de nível de classe que é usada como uma referência estática ao contexto do servidor.

        // Field for service context.
        private static CloudMediaContext _context = null;

## <span id="Step2"></span></a>Obtendo o contexto dos Serviços de Mídia

O objeto de contexto dos Serviços de Mídia contém todos os objetos e coleções fundamentais a serem acessados para a programação dos Serviços de Mídia. O contexto inclui referências a importantes coleções, incluindo trabalhos, ativos, arquivos, políticas de acesso, localizadores e outros objetos. Você deve obter o contexto do servidor para a maioria das tarefas de programação dos Serviços de Mídia.

No arquivo Program.cs, adicione o seguinte código como o primeiro item em seu método **Main**. Esse código usa o nome da conta dos Serviços de Mídia e os valores da chave da conta do arquivo app.config para criar uma instância de contexto do servidor. A instância é atribuída à variável **_context** criada no nível de classe.

    // Get the service context.
    _context = new CloudMediaContext(_accountName, _accountKey);

## <span id="Step3"></span></a>Criando um ativo e carregando um arquivo

O código desta seção faz o seguinte:

1.  Cria um ativo vazio

    Quando você cria ativos, você pode especificar três opções diferentes para criptografá-los.

    -   **AssetCreationOptions.None**: sem criptografia. Se desejar criar um ativo não criptografado, você deve definir essa opção.
    -   **AssetCreationOptions.CommonEncryptionProtected**: para arquivos CENC (protegidos de criptografia comum). Um exemplo é um conjunto de arquivos que já estão criptografados por PlayReady.
    -   **AssetCreationOptions.StorageEncrypted**: criptografia para armazenamento. Criptografa um arquivo de entrada limpo antes de ele ser carregado no Armazenamento do Azure.

        <div class="dev-callout"> 
<strong>Observa&ccedil;&atilde;o</strong> 
<p>Os Servi&ccedil;os de M&iacute;dia fornecem criptografia de armazenamento em disco, n&atilde;o pela conex&atilde;o, como o DRM (gerenciamento de direitos digitais).</p> 
</div>

2.  Cria uma instância de AssetFile que desejamos associar ao ativo.
3.  Cria uma instância de AccessPolicy que define as permissões e a duração do acesso ao ativo.
4.  Cria uma instância de localizador que fornece acesso ao ativo.
5.  Carrega um único arquivo de mídia nos Serviços de Mídia. O processo de criação e carregamento também é chamado de absorção de ativos.

Adicione os seguintes métodos à classe.

    static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)
    {
        var asset = _context.Assets.Create(assetName, assetCreationOptions);

        Console.WriteLine("Asset name: " + asset.Name);
        Console.WriteLine("Time created: " + asset.Created.Date.ToString());

        return asset;
    }

    static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
    {
        var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
        var asset = CreateEmptyAsset(assetName, assetCreationOptions);

        var fileName = Path.GetFileName(singleFilePath);

        var assetFile = asset.AssetFiles.Create(fileName);

        Console.WriteLine("Created assetFile {0}", assetFile.Name);

        var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(3),
                                                            AccessPermissions.Write | AccessPermissions.List);

        var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

        Console.WriteLine("Upload {0}", assetFile.Name);

        assetFile.Upload(singleFilePath);
        Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

        locator.Delete();
        accessPolicy.Delete();

        return asset;
    }

Adicione uma chamada para o método após a linha \*\*\_context = new CloudMediaContext(\_accountName, \_accountKey);\*\* em seu método Main.

    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, _singleInputFilePath)

## <span id="Step4"></span></a>Codificando o ativo no servidor e baixando um ativo de saída

Nos Serviços de Mídia, você pode criar trabalhos que processam conteúdo de mídia de várias maneiras: codificação, criptografia, fazendo conversões de formato e assim por diante. Um trabalho dos Serviços de Mídia sempre contém uma ou mais tarefas que especificam os detalhes do trabalho de processamento. Nesta seção, você criará uma tarefa básica de codificação e executará um trabalho que a realizará usando o Codificador de Mídia do Azure. A tarefa usa uma sequência de caracteres predefinida para especificar o tipo de codificação a ser executada. Para ver os valores de codificação predefinidos disponíveis, consulte [Cadeias de caracteres predefinidas de tarefas do Codificador de Mídia do Azure][]. Os Serviços de Mídia oferecem suporte aos mesmos formatos de entrada e de saída de arquivos de mídia que o Microsoft Expression Encoder. Para obter uma lista dos formatos com suporte, consulte [Tipos de arquivos com suporte para Serviços de Mídia (a página pode estar em inglês)][]

1.  Adicione a seguinte definição do método **CreateEncodingJob** a sua classe. Esse método demonstra como realizar várias tarefas necessárias para um trabalho de codificação:
    -   Declarar um novo trabalho.
    -   Declarar um processador de mídia para tratar o trabalho. Um processador de mídia é um componente que manipula a codificação, a criptografia, a conversão de formato e outras tarefas de processamento relacionadas. Há vários tipos de processadores de mídia disponíveis (você pode iterar por todos eles usando \_context.MediaProcessors.) O método GetLatestMediaProcessorByName, exibido mais adiante neste tutorial, retorna o processador do Codificador de Mídia do Azure.
    -   Declarar uma nova tarefa. Todo trabalho tem uma ou mais tarefas. Observe que com a tarefa, você passa para ele um nome amigável, uma instância do processador de mídia, uma cadeia de caracteres de configuração de tarefa e opções de criação de tarefa. A cadeia de caracteres de configuração especifica as configurações de codificação. Este exemplo usa a configuração **H264 Broadband 720p**. Essa predefinição produz um único arquivo MP4. Para obter mais informações sobre esse e outros valores predefinidos, consulte [Cadeias de caracteres predefinidas de tarefas para o Codificador de Mídia do Azure][].
    -   Adicionar um ativo de entrada à tarefa. Neste exemplo, o ativo de entrada é o que você criou na seção anterior.
    -   Adicionar um ativo de saída à tarefa. Para um ativo de saída, especifique um nome amigável, um valor booliano para indicar se você deseja salvar a saída no servidor após a conclusão do trabalho e um valor de **AssetCreationOptions.None** para indicar que a saída não é criptografada para armazenamento e transporte.
    -   Enviar o trabalho.
         O envio de um trabalho é a última etapa que é necessária para fazer um trabalho de codificação.

    O método também demonstra como executar outras tarefas úteis (mas opcionais), como acompanhar o andamento do trabalho e acessar o ativo que seu trabalho de codificação cria.

        static IJob CreateEncodingJob(IAsset asset, string inputMediaFilePath, string outputFolder)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My encoding job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "H264 Broadband 720p",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);
            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Optionally log job details. This displays basic job details
            // to the console and saves them to a JobDetails-{JobId}.txt file 
            // in your output folder.
            LogJobDetails(job.Id);

            // Check job execution and wait for job to finish. 
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // **********
            // Optional code.  Code after this point is not required for 
            // an encoding job, but shows how to access the assets that 
            // are the output of a job, either by creating URLs to the 
            // asset on the server, or by downloading. 
            // **********

            // Get an updated job reference.
            job = GetJob(job.Id);

            // If job state is Error the event handling 
            // method for job progress should log errors.  Here we check 
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                Console.WriteLine("\nExiting method due to job error.");
                return job;
            }

            // Get a reference to the output asset from the job.
            IAsset outputAsset = job.OutputMediaAssets[0];
            IAccessPolicy policy = null;
            ILocator locator = null;

            // Declare an access policy for permissions on the asset. 
            // You can call an async or sync create method. 
            policy =
                _context.AccessPolicies.Create("My 30 days readonly policy",
                    TimeSpan.FromDays(30),
                    AccessPermissions.Read);

            // Create a SAS locator to enable direct access to the asset 
            // in blob storage. You can call a sync or async create method.  
            // You can set the optional startTime param as 5 minutes 
            // earlier than Now to compensate for differences in time  
            // between the client and server clocks. 

            locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset,
                policy,
                DateTime.UtcNow.AddMinutes(-5));

            // Build a list of SAS URLs to each file in the asset. 
            List<String> sasUrlList = GetAssetSasUrlList(outputAsset, locator);

            // Write the URL list to a local file. You can use the saved 
            // SAS URLs to browse directly to the files in the asset.
            if (sasUrlList != null)
            {
                string outFilePath = Path.GetFullPath(outputFolder + @"\" + "FileSasUrlList.txt");
                StringBuilder fileList = new StringBuilder();
                foreach (string url in sasUrlList)
                {
                    fileList.AppendLine(url);
                    fileList.AppendLine();
                }
                WriteToFile(outFilePath, fileList.ToString());

                // Optionally download the output to the local machine.
                DownloadAssetToLocal(job.Id, outputFolder);
            }

            
            return job;
        }

2.  Adicione uma chamada ao método **CreateEncodingJob** em seu método **Main** após as linhas que você adicionou anteriormente.

        CreateEncodingJob(asset, _singleInputFilePath, _outputFilesFolder);

3.  Adicione os seguintes métodos auxiliares à classe. Eles são exigidos para dar suporte ao método **CreateEncodingJob**. O seguinte é um resumo dos métodos auxiliares.
    -   O método **GetLatestMediaProcessorByName** retorna um processador de mídia apropriado para manipular uma codificação, criptografia ou outra tarefa de processamento relacionada. Você cria um processador de mídia usando o nome da cadeia de caracteres apropriada do processador que você deseja criar. As cadeias de caracteres possíveis que podem ser passadas para o método do parâmetro mediaProcessor são: **Codificador de Mídia do Azure**, **Empacotador de Mídia do Azure**, **Codificador de Mídia do Azure**, **Descriptografia do Armazenamento**.

            private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                // The possible strings that can be passed into the 
                // method for the mediaProcessor parameter:
                //   Azure Media Encoder
                //   Azure Media Packager
                //   Azure Media Encryptor
                //   Storage Decryption

                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                return processor;
            }

    -   Quando você executa trabalhos, você normalmente precisa de uma maneira de acompanhar o andamento do trabalho. O exemplo de código a seguir define o manipulador de eventos StateChanged: Esse manipulador de eventos acompanha o andamento do trabalho e fornece o status atualizado, dependendo do estado. O código também define o método LogJobStop. Esse método auxiliar registra os detalhes de erros.

            private static void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);

                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("********************");
                        Console.WriteLine("Job is finished.");
                        Console.WriteLine("Please wait while local tasks or downloads complete...");
                        Console.WriteLine("********************");
                        Console.WriteLine();
                        Console.WriteLine();
                        break;
                    case JobState.Canceling:
                    case JobState.Queued:
                    case JobState.Scheduled:
                    case JobState.Processing:
                        Console.WriteLine("Please wait...\n");
                        break;
                    case JobState.Canceled:
                    case JobState.Error:
                        // Cast sender as a job.
                        IJob job = (IJob)sender;
                        // Display or log error details as needed.
                        LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }

            private static void LogJobStop(string jobId)
            {
                StringBuilder builder = new StringBuilder();
                IJob job = GetJob(jobId);

                builder.AppendLine("\nThe job stopped due to cancellation or an error.");
                builder.AppendLine("***************************");
                builder.AppendLine("Job ID: " + job.Id);
                builder.AppendLine("Job Name: " + job.Name);
                builder.AppendLine("Job State: " + job.State.ToString());
                builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
                builder.AppendLine("Media Services account name: " + _accountName);
                // Log job errors if they exist.  
                if (job.State == JobState.Error)
                {
                    builder.Append("Error Details: \n");
                    foreach (ITask task in job.Tasks)
                    {
                        foreach (ErrorDetail detail in task.ErrorDetails)
                        {
                            builder.AppendLine("  Task Id: " + task.Id);
                            builder.AppendLine("    Error Code: " + detail.Code);
                            builder.AppendLine("    Error Message: " + detail.Message + "\n");
                        }
                    }
                }
                builder.AppendLine("***************************\n");
                // Write the output to a local file and to the console. The template 
                // for an error output file is:  JobStop-{JobId}.txt
                string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
                WriteToFile(outputFile, builder.ToString());
                Console.Write(builder.ToString());
            }

            private static void LogJobDetails(string jobId)
            {
                StringBuilder builder = new StringBuilder();
                IJob job = GetJob(jobId);

                builder.AppendLine("\nJob ID: " + job.Id);
                builder.AppendLine("Job Name: " + job.Name);
                builder.AppendLine("Job submitted (client UTC time): " + DateTime.UtcNow.ToString());
                builder.AppendLine("Media Services account name: " + _accountName);

                // Write the output to a local file and to the console. The template 
                // for an error output file is:  JobDetails-{JobId}.txt
                string outputFile = _outputFilesFolder + @"\JobDetails-" + JobIdAsFileName(job.Id) + ".txt";
                WriteToFile(outputFile, builder.ToString());
                Console.Write(builder.ToString());
            }
                    
            private static string JobIdAsFileName(string jobID)
            {
                return jobID.Replace(":", "_");
            }

    -   O método WriteToFile grava um arquivo na pasta de saída especificada.

            static void WriteToFile(string outFilePath, string fileContent)
            {
                StreamWriter sr = File.CreateText(outFilePath);
                sr.Write(fileContent);
                sr.Close();
            }

    -   Depois de codificar ativos nos Serviços de Mídia, você pode acessar os ativos de saída que resultam de um trabalho de codificação. Esta explicação passo a passo mostra duas maneiras de acessar a saída de um trabalho de codificação:

        -   Criando uma URL de SAS para um ativo no servidor.
        -   Baixando o ativo de saída no servidor.

        O método GetAssetSasUrlList cria uma lista de URLs de SAS para todos os arquivos em um ativo.

            static List<String> GetAssetSasUrlList(IAsset asset, ILocator locator)
            {
                // Declare a list to contain all the SAS URLs.
                List<String> fileSasUrlList = new List<String>();

                // If the asset has files, build a list of URLs to 
                // each file in the asset and return. 
                foreach (IAssetFile file in asset.AssetFiles)
                {
                    string sasUrl = BuildFileSasUrl(file, locator);
                    fileSasUrlList.Add(sasUrl);
                }

                // Return the list of SAS URLs.
                return fileSasUrlList;
            }

            // Create and return a SAS URL to a single file in an asset. 
            static string BuildFileSasUrl(IAssetFile file, ILocator locator)
            {
                // Take the locator path, add the file name, and build 
                // a full SAS URL to access this file. This is the only 
                // code required to build the full URL.
                var uriBuilder = new UriBuilder(locator.Path);
                uriBuilder.Path += "/" + file.Name;

                // Optional:  print the locator.Path to the asset, and 
                // the full SAS URL to the file
                Console.WriteLine("Locator path: ");
                Console.WriteLine(locator.Path);
                Console.WriteLine();
                Console.WriteLine("Full URL to file: ");
                Console.WriteLine(uriBuilder.Uri.AbsoluteUri);
                Console.WriteLine();


                //Return the SAS URL.
                return uriBuilder.Uri.AbsoluteUri;
            }

    -   O método **DownloadAssetToLocal** baixa cada arquivo do ativo em uma pasta local. Neste exemplo, como o ativo foi criado com um arquivo de mídia de entrada, a coleção de arquivos do ativo de saída contém dois arquivos: o arquivo de mídia codificado (um arquivo .mp4) e um arquivo .xml com metadados sobre o ativo. O método baixa os dois arquivos.

            static IAsset DownloadAssetToLocal(string jobId, string outputFolder)
            {
                // This method illustrates how to download a single asset. 
                // However, you can iterate through the OutputAssets
                // collection, and download all assets if there are many. 

                // Get a reference to the job. 
                IJob job = GetJob(jobId);
                // Get a reference to the first output asset. If there were multiple 
                // output media assets you could iterate and handle each one.
                IAsset outputAsset = job.OutputMediaAssets[0];

                IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
                ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);
                BlobTransferClient blobTransfer = new BlobTransferClient
                {
                    NumberOfConcurrentTransfers = 10,
                    ParallelTransferThreadCount = 10
                };

                var downloadTasks = new List<Task>();
                foreach (IAssetFile outputFile in outputAsset.AssetFiles)
                {
                    // Use the following event handler to check download progress.
                    outputFile.DownloadProgressChanged += DownloadProgress;

                    string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

                    Console.WriteLine("File download path:  " + localDownloadPath);

                    downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

                    outputFile.DownloadProgressChanged -= DownloadProgress;
                }

                Task.WaitAll(downloadTasks.ToArray());

                return outputAsset;
            }

            static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
            {
                Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
            }

    -   Os métodos auxiliares GetJob e GetAsset consultam e retornam uma referência a um objeto de trabalho e a um objeto de ativo com as IDs fornecidas. Você pode usar um tipo semelhante de consulta LINQ para retornar referências a outros objetos dos Serviços de Mídia no servidor.

            static IJob GetJob(string jobId)
            {
                // Use a Linq select query to get an updated 
                // reference by Id. 
                var jobInstance =
                    from j in _context.Jobs
                    where j.Id == jobId
                    select j;
                // Return the job reference as an Ijob. 
                IJob job = jobInstance.FirstOrDefault();

                return job;
            }
            static IAsset GetAsset(string assetId)
            {
                // Use a LINQ Select query to get an asset.
                var assetInstance =
                    from a in _context.Assets
                    where a.Id == assetId
                    select a;
                // Reference the asset as an IAsset.
                IAsset asset = assetInstance.FirstOrDefault();

                return asset;
            }

## Testando o código

Execute o programa (pressione F5). O console exibe uma saída semelhante à seguinte:

    Asset name: UploadSingleFile_11/14/2012 10:09:11 PM
    Time created: 11/14/2012 12:00:00 AM
    Created assetFile interview2.wmv
    Upload interview2.wmv
    Done uploading of interview2.wmv using Upload()

    Job ID: nb:jid:UUID:ea8d5a66-86b8-9b4d-84bc-6d406259acb8
    Job Name: My encoding job
    Job submitted (client UTC time): 11/14/2012 10:09:39 PM
    Media Services account name: Add-Media-Services-Account-Name
    Media Services account location: Add-Media-Services-account-location-name

    Job(My encoding job) state: Queued.
    Please wait...

    Job(My encoding job) state: Processing.
    Please wait...

    ********************
    Job(My encoding job) is finished.
    Please wait while local tasks or downloads complete...
    ********************

    Locator path:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
    si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
    xylq3oESc%3D

    Full URL to file:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62/interview2.mp4?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3
    A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2F
    BxERnav8Jb6hL7fxylq3oESc%3D

    Locator path:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
    si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
    xylq3oESc%3D

    Full URL to file:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62/interview2_metadata.xml?st=2012-11-14T22%3A07%3A01Z&se=2012-1
    1-14T23%3A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8F
    od3NsV%2FBxERnav8Jb6hL7fxylq3oESc%3D

    Downloads are in progress, please wait.

    File download path:  C:\supportFiles\outputfiles\interview2.mp4
    1.70952185308162 % download progress.
    3.68508804454907 % download progress.
    6.48870388360293 % download progress.
    6.83808741232649 % download progress.
    . . . 
    99.0763740574049 % download progress.
    99.1522674787341 % download progress.
    100 % download progress.
    File download path:  C:\supportFiles\outputfiles\interview2_metadata.xml
    100 % download progress.

1.  Como resultado da execução deste aplicativo ocorre o seguinte:

2.  Um arquivo .wmv é carregado nos Serviços de Mídia.

3.  Em seguida, o arquivo é codificado usando a predefinição **H264 Broadband 720p** do **Codificador de Mídia do Azure**.

4.  O arquivo FileSasUrlList.txt é criado na pasta \\supportFiles\\outputFiles. O arquivo contém a URL para o ativo codificado.

    Para reproduzir o arquivo de mídia, copie a URL para o ativo no arquivo de texto e cole a URL em um navegador.

5.  O arquivo de mídia .mp4 e o arquivo \_metadata.xml são baixados na pasta outputFiles.

<div class="dev-callout"> 
<strong>Observa&ccedil;&atilde;o</strong> 
<p>No modelo de objeto dos Servi&ccedil;os de M&iacute;dia, um ativo &eacute; um objeto de cole&ccedil;&atilde;o de conte&uacute;do dos Servi&ccedil;os de M&iacute;dia que representa um ou muitos arquivos. O caminho do localizador fornece uma URL de Blob do Azure que &eacute; o caminho base para esse ativo no Armazenamento do Azure. Para acessar arquivos espec&iacute;ficos dentro do ativo, adicione um nome de arquivo ao caminho do localizador base.</p> 
</div>

## Próximas etapas

Esta explicação passo a passo demonstrou uma sequência de tarefas de programação para criar um aplicativo simples dos Serviços de Mídia. Você aprendeu as tarefas de programação principais dos Serviços de Mídia incluindo como obter o contexto do servidor, criar ativos, codificar ativos e baixar ou acessar ativos no servidor. Para obter as próximas etapas e as tarefas de desenvolvimento mais avançadas, consulte o seguinte:

-   [Como usar os Serviços de Mídia][]
-   [Criando aplicativos com a API REST dos Serviços de Mídia][]

<!-- Anchors. -->

  [Baixe o]: http://go.microsoft.com/fwlink/?linkid=253275
  [Configurando o seu projeto]: #Step1
  [Obtendo o contexto do servidor dos Serviços de Mídia]: #Step2
  [Criando um ativo e carregando arquivos que estão associados ao ativo nos Serviços de Mídia]: #Step3
  [Codificando um ativo e baixando um ativo de saída]: #Step4
  [Como criar uma conta de Serviços de Mídia (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/?LinkId=256662
  [Nuget windowsazure.mediaservices]: http://nuget.org/packages/windowsazure.mediaservices
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A8A8397B5
  [Cadeias de caracteres predefinidas de tarefas do Codificador de Mídia do Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/jj129582.aspx
  [Tipos de arquivos com suporte para Serviços de Mídia (a página pode estar em inglês)]: http://msdn.microsoft.com/en-us/library/windowsazure/hh973634.aspx
  [Cadeias de caracteres predefinidas de tarefas para o Codificador de Mídia do Azure]: http://msdn.microsoft.com/library/windowsazure/jj129582.aspx
  [Como usar os Serviços de Mídia]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/media-services/
  [Criando aplicativos com a API REST dos Serviços de Mídia]: http://msdn.microsoft.com/en-us/library/windowsazure/hh973618.aspx
