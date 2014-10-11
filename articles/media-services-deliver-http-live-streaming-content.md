<properties linkid="develop-media-services-how-to-guides-deliver-apple-live-streaming" urlDisplayName="Deliver Apple HTTP Live Streaming (HLS)" pageTitle="How to Deliver Apple HTTP Live Streaming (HLS) - Azure" metaKeywords="" description="Learn how to create a locator to Apple HTTP Live Stream (HLS) content on Media Services origin server. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver Apple HLS streaming content" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# Como: Entregar conteúdo de streaming HLS da Apple

Este artigo faz parte de uma série de introdução à programação dos Serviços de Mídia do Azure. O tópico anterior era [Como: Entregar conteúdo de streaming da Apple][].

Este tópico mostra como criar um localizador para conteúdo HTTP Live Streaming (HLS) da Apple em um servidor de origem dos Serviços de Mídia. Com essa abordagem, é possível criar uma URL para conteúdo de HLS da Apple e fornecê-la aos dispositivos do Apple iOS para reprodução. A abordagem básica para a criação da URL do localizador URL é a mesma. Crie um localizador para o caminho do ativo de streaming HLS da Apple em um servidor de origem e, em seguida, crie uma URL completa que se vincule com o manifesto para o conteúdo de streaming.

O exemplo de código a seguir pressupõe que você já obteve uma referência para um ativo de streaming HLS e que a variável chamada **assetToStream** está referenciada no código. Depois de executar esse código para gerar um localizador de origem no ativo, você pode usar a URL resultante para reproduzir o conteúdo de streaming em um dispositivo iOS, como um iPad ou um iPhone.

Para criar um localizador para conteúdo de streaming HLS da Apple:

1.  Obtenha uma referência para o arquivo de manifesto no ativo
2.  Defina uma diretiva de acesso
3.  Crie o localizador de origem chamando CreateLocator
4.  Crie uma URL para o arquivo de manifesto

O código a seguir mostra como implementar as etapas:

    static ILocator GetStreamingHLSOriginLocator( string targetAssetID)
    {
        // Get a reference to the asset you want to stream.
        IAsset assetToStream = GetAsset(targetAssetID);

        // Get a reference to the HLS streaming manifest file from the  
        // collection of files in the asset. 
        var theManifest =
                            from f in assetToStream.AssetFiles
                            where f.Name.EndsWith(".ism")
                            select f;

        // Cast the reference to a true IAssetFile type. 
        IAssetFile manifestFile = theManifest.First();
        IAccessPolicy policy = null;
        ILocator originLocator = null;

        // Create a 30-day readonly access policy. 
        policy = _context.AccessPolicies.Create("Streaming HLS Policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
                    policy,
                    DateTime.UtcNow.AddMinutes(-5));

        // Create a URL to the HLS streaming manifest file. Use this for playback
        // in Apple iOS streaming clients.
        string urlForClientStreaming = originLocator.Path
            + manifestFile.Name + "/manifest(format=m3u8-aapl)";
        Console.WriteLine("URL to manifest for client streaming: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine();

        // Return the locator. 
        return originLocator;
    }

Para obter mais informações sobre entrega de ativos, consulte:

-   [Fornecer ativos com os Serviços de Mídia para .NET][]
-   [Fornecer ativos com a API REST dos Serviços de Mídia][]

</p>
## Próximas etapas

Este tópico conclui os tópicos Usando os Serviços de Mídia do Azure. Abordamos a configuração de seu computador para desenvolvimento de Serviços de Mídia e a execução de tarefas comuns de programação. Para obter mais informações sobre como programar os Serviços de Mídia, consulte os seguintes recursos:

-   [Documentação dos Serviços de Mídia do Azure][]
-   [Introdução ao SDK dos Serviços de Mídia para .NET][]
-   [Criando aplicativos com o SDK dos Serviços de Mídia para .NET][]
-   [Criando aplicativos com a API REST dos Serviços de Mídia do Azure][]
-   [Fórum dos Serviços de Mídia][]
-   [Como monitorar uma conta de Serviços de Mídia][]
-   [Como gerenciar conteúdo nos Serviços de Mídia][]

  [Como: Entregar conteúdo de streaming da Apple]: http://go.microsoft.com/fwlink/?LinkID=301942&clcid=0x409
  [Fornecer ativos com os Serviços de Mídia para .NET]: http://msdn.microsoft.com/en-us/library/jj129575.aspx
  [Fornecer ativos com a API REST dos Serviços de Mídia]: http://msdn.microsoft.com/en-us/library/jj129578.aspx
  [Documentação dos Serviços de Mídia do Azure]: http://go.microsoft.com/fwlink/?linkid=245437
  [Introdução ao SDK dos Serviços de Mídia para .NET]: http://go.microsoft.com/fwlink/?linkid=252966
  [Criando aplicativos com o SDK dos Serviços de Mídia para .NET]: http://go.microsoft.com/fwlink/?linkid=247821
  [Criando aplicativos com a API REST dos Serviços de Mídia do Azure]: http://go.microsoft.com/fwlink/?linkid=252967
  [Fórum dos Serviços de Mídia]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/threads
  [Como monitorar uma conta de Serviços de Mídia]: http://www.windowsazure.com/en-us/manage/services/media-services/how-to-monitor-a-media-services-account/
  [Como gerenciar conteúdo nos Serviços de Mídia]: http://www.windowsazure.com/en-us/manage/services/media-services/how-to-manage-content-in-media-services/
