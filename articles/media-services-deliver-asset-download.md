<properties linkid="develop-media-services-how-to-guides-deliver-media-assets" urlDisplayName="Delivering Media Assets" pageTitle="How to Deliver Media Assets - Azure" metaKeywords="" description="Learn about options for delivering media assets that have been uploaded to Media Services in Azure. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver an Asset by Download" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# Como: Fornecer um ativo por download

Este artigo faz parte de uma série de introdução à programação dos Serviços de Mídia do Azure. O tópico anterior era [Como: Gerenciar ativos][].

Este tópico descreve as opções para a entrega de ativos de mídia carregados nos Serviços de Mídia. Você pode entregar conteúdo dos Serviços de Mídia em vários cenários de aplicativos. Você pode baixar ativos de mídia ou acessá-los usando um localizador. Você pode enviar conteúdo de mídia para outro aplicativo ou para outro provedor de conteúdo. Para obter melhor desempenho e escalabilidade, você também pode entregar conteúdo usando uma CDN (Rede de Distribuição de Conteúdo), como a CDN do Azure.

Este exemplo mostra como baixar ativos de mídia dos Serviços de Mídia. O código consulta os trabalhos associados à conta dos Serviços de Mídia por ID do trabalho e acessa sua coleção **OutputMediaAssets** (que é o conjunto de um ou mais ativos de mídia de saída que resulta da execução de um trabalho). Este exemplo mostra como baixar os ativos de mídia da saída de um trabalho, mas você pode aplicar a mesma abordagem para baixar outros ativos.

     
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
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

Para obter mais informações sobre entrega de ativos, consulte:

-   [Fornecer ativos com os Serviços de Mídia para .NET][]
-   [Fornecer ativos com a API REST dos Serviços de Mídia][]

</p>
## Próximas etapas

Este tópico explicou como baixar um ativo do Armazenamento do Azure. Para obter informações sobre outras maneiras de entregar ativos, vá para o tópico [Como entregar conteúdo de streaming][].

  [Como: Gerenciar ativos]: http://go.microsoft.com/fwlink/?LinkID=301815&clcid=0x409
  [Fornecer ativos com os Serviços de Mídia para .NET]: http://msdn.microsoft.com/en-us/library/jj129575.aspx
  [Fornecer ativos com a API REST dos Serviços de Mídia]: http://msdn.microsoft.com/en-us/library/jj129578.aspx
  [Como entregar conteúdo de streaming]: http://go.microsoft.com/fwlink/?LinkID=301942
