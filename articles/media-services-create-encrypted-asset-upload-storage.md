<properties linkid="develop-media-services-how-to-guides-create-assets" urlDisplayName="Create Encrypted Asset and Upload to Storage" pageTitle="Create Encrypted Asset and Upload to Storage Azure" metaKeywords="" description="Learn how to get media content into Media Services by creating and uploading an encrypted asset." metaCanonical="" services="media-services" documentationCenter="" title="How to: Create an encrypted Asset and upload to storage" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# <a name="create-asset"> </a><span class="short header">Como: Como criar um ativo criptografado e carregar no armazenamento</span>

Este artigo faz parte de uma série de introdução à programação dos Serviços de Mídia do Azure. O tópico anterior foi [Configurando seu computador para os Serviços de Mídia][]

Para obter conteúdo de mídia nos Serviços de Mídia, primeiro crie um ativo e adicione arquivos a ele e, em seguida, carregue o ativo. Esse processo é chamado de conteúdo de absorção.

Quando cria ativos, você pode especificar três opções diferentes para criptografia.

-   **AssetCreationOptions.None**: sem criptografia. Se desejar criar um ativo não criptografado, você deve definir essa opção.
-   **AssetCreationOptions.CommonEncryptionProtected**: para arquivos CENC (protegidos de criptografia comum). Um exemplo é um conjunto de arquivos que já estão criptografados por PlayReady.
-   **AssetCreationOptions.StorageEncrypted**: criptografia para armazenamento. Criptografa um arquivo de entrada limpo antes de ele ser carregado no Armazenamento do Azure.

**OBSERVAÇÃO**: Observe que os Serviços de Mídia fornecem criptografia de armazenamento em disco, e não pela conexão, como o DRM (gerenciamento de direitos digitais).

O código de exemplo a seguir faz o seguinte:

-   Cria um ativo vazio.
-   Cria uma instância de AssetFile que desejamos associar ao ativo.
-   Cria uma instância de AccessPolicy que define as permissões e a duração do acesso ao ativo.
-   Cria uma instância de localizador que fornece acesso ao ativo.
-   Carrega um único arquivo de mídia nos Serviços de Mídia.

<!-- -->

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
        Console.WriteLine("Upload {0}", assetFile.Name);

        assetFile.Upload(singleFilePath);
        Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

        return asset;
    }

O código a seguir mostra como criar um ativo e carregar vários arquivos.

    static public IAsset CreateAssetAndUploadMultipleFiles( AssetCreationOptions assetCreationOptions, string folderPath)
    {
        var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

        var asset = CreateEmptyAsset(assetName, assetCreationOptions);

        var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                            AccessPermissions.Write | AccessPermissions.List);
        var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

        var blobTransferClient = new BlobTransferClient();
        blobTransferClient.NumberOfConcurrentTransfers = 20;
        blobTransferClient.ParallelTransferThreadCount = 20;

        blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

        var filePaths = Directory.EnumerateFiles(folderPath);

        Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

        if (!filePaths.Any())
        {
            throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
        }

        var uploadTasks = new List<Task>();
        foreach (var filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            Console.WriteLine("Created assetFile {0}", assetFile.Name);
                    
            // It is recommended to validate AccestFiles before upload. 
            Console.WriteLine("Start uploading of {0}", assetFile.Name);
            uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
        }

        Task.WaitAll(uploadTasks.ToArray());
        Console.WriteLine("Done uploading the files");

        blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

        locator.Delete();
        accessPolicy.Delete();

        return asset;
    }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }

## Próximas etapas

Agora que você carregou um ativo nos Serviços de Mídia, vá para o tópico [Como obter um processador de mídia][].

  [Configurando seu computador para os Serviços de Mídia]: http://go.microsoft.com/fwlink/?LinkID=301751&clcid=0x409
  [Como obter um processador de mídia]: http://go.microsoft.com/fwlink/?LinkID=301732&clcid=0x409
