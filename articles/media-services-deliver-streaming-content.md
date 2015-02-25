<properties pageTitle="Como fornecer conteúdo de streaming dos Serviços de Mídia do Azure" description="Saiba como fornecer conteúdo de streaming dos Serviços de Mídia usando uma URL direta. Os exemplos de código são escritos em C# e usam a SDK dos Serviços de Mídia para .NET." authors="juliako" manager="dwrede" editor="" services="media-services" documentationCenter=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako"/>


# Como: Fornecer conteúdo de streaming

Este artigo faz parte de uma série de introdução à programação dos Serviços de Mídia do Azure. O tópico anterior era [Como: Fornecer um ativo por download](../media-services-deliver-asset-download/).

Além de fazer o download de conteúdo de mídia nos Serviços de Mídia, você pode usar streaming de taxa de bits adaptável para fornecer conteúdo. Por exemplo, você pode criar uma URL direta, chamada de localizador, para conteúdo de streaming em um servidor de origem dos Serviços de Mídia. Aplicativos clientes, como o Microsoft Silverlight, podem reproduzir o conteúdo de streaming diretamente no localizador.

O exemplo a seguir mostra como criar um localizador de origem para um ativo de saída produzido por um trabalho. O exemplo pressupõe que você já tem uma referência a um ativo que contém arquivos de smooth streaming, e que a variável chamada **assetToStream** é referenciada no código. 

Para criar um localizador de origem para conteúdo de streaming:

   1. Obtenha uma referência ao arquivo de manifesto de streaming (.ism) no ativo 
   2. Defina uma política de acesso
   3. Crie o localizador de origem chamando o método CreateLocator 
   4. Crie uma URL para o arquivo de manifesto 

O código a seguir mostra como implementar as etapas:
<pre><code>
private static ILocator GetStreamingOriginLocator( string targetAssetID)
{
    // Get a reference to the asset you want to stream.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Get a reference to the streaming manifest file from the  
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
    policy = _context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    // Create an OnDemandOrigin locator to the asset. 
    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
        policy,
        DateTime.UtcNow.AddMinutes(-5));
            
    // Display some useful values based on the locator.
    Console.WriteLine("Streaming asset base path on origin: ");
    Console.WriteLine(originLocator.Path);
    Console.WriteLine();
    
    // Create a full URL to the manifest file. Use this for playback
    // in streaming media clients. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();
    
    // Display the ID of the origin locator, the access policy, and the asset.
    Console.WriteLine("Origin locator Id: " + originLocator.Id);
    Console.WriteLine("Access policy Id: " + policy.Id);
    Console.WriteLine("Streaming asset Id: " + assetToStream.Id);

    // Return the locator. 
    return originLocator;
}
</code></pre>

Para obter mais informações sobre como fornecer ativos, consulte:
<ul>
<li><a href="http://msdn.microsoft.com/pt-br/library/jj129575.aspx">Fornecer ativos com os Serviços de Mídia para .NET</a></li>
<li><a href="http://msdn.microsoft.com/pt-br/library/jj129578.aspx">Fornecer ativos com a API REST dos Serviços de Mídia</a></li>
</ul>

<h2>Próximas etapas</h2>
Até agora, explicamos como fornecer mídia baixando do Armazenamento do Azure e usando o Smooth Streaming. O próximo tópico [Como fornecer conteúdo HLS](../media-services-deliver-http-live-streaming-content/) explica como fornecer conteúdo de streaming usando HTTP Live Streaming (HLS) da Apple.


<!--HONumber=42-->
