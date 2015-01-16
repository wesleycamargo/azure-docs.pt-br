<properties urlDisplayName="Manage Assets in Media Services" pageTitle="Como gerenciar ativos nos Serviços de Mídia - Azure" metaKeywords="" description="Saiba como gerenciar ativos em Serviços de Mídia. Você também pode gerenciar trabalhos, tarefas, políticas de acesso, localizadores e muito mais. Os exemplos de código são escritos em C# e usam a SDK dos Serviços de Mídia para .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Manage Assets in storage" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />




<h1>Como: Gerenciar ativos no armazenamento</h1>

Este artigo faz parte de uma série de introdução à programação dos Serviços de Mídia do Azure. O tópico anterior era [Como: Proteger um ativo](../media-services-protect-asset/).

Depois que você cria ativos de mídia e os carrega nos Serviços de Mídia, pode acessar e gerenciar os ativos no servidor. Você também pode gerenciar outros objetos no servidor que fazem parte dos Serviços de Mídia, incluindo trabalhos, tarefas, políticas de acesso, localizadores e muito mais.

O exemplo a seguir mostra como consultar um ativo por assetId. 
<pre><code>
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
</code></pre> 

Para listar todos os recursos disponíveis no servidor, você pode usar o método a seguir que itera pela coleção de ativos e exibe detalhes sobre cada ativo.
<pre><code> 
static void ListAssets()
{
    string waitMessage = "Building the list. This may take a few "
        + "seconds to a few minutes depending on how many assets "
        + "you have."
        + Environment.NewLine + Environment.NewLine
        + "Please wait..."
        + Environment.NewLine;
    Console.Write(waitMessage);

    // Create a Stringbuilder to store the list that we build. 
    StringBuilder builder = new StringBuilder();

    foreach (IAsset asset in _context.Assets)
    {
        // Display the collection of assets.
        builder.AppendLine("");
        builder.AppendLine("******ASSET******");
        builder.AppendLine("Asset ID: " + asset.Id);
        builder.AppendLine("Name: " + asset.Name);
        builder.AppendLine("==============");
        builder.AppendLine("******ASSET FILES******");

        // Display the files associated with each asset. 
        foreach (IAssetFile fileItem in asset.AssetFiles)
        {
            builder.AppendLine("Name: " + fileItem.Name);
            builder.AppendLine("Size: " + fileItem.ContentFileSize);
            builder.AppendLine("==============");
        }
    }

    // Display output in console.
    Console.Write(builder.ToString());
}
</code></pre>
O trecho de código a seguir exclui todos os ativos da conta de Serviços de Mídia.
<pre><code>
foreach (IAsset asset in _context.Assets)
{
    asset.Delete();
}
</code></pre>

Para obter mais informações sobre como gerenciar ativos, consulte:
<ul>
<li><a href="http://msdn.microsoft.com/pt-br/library/jj129589.aspx">Gerenciar ativos com o SDK dos Serviços de Mídia para .NET</a></li>
<li><a href="http://msdn.microsoft.com/pt-br/library/jj129583.aspx">Gerenciar ativos com a API REST dos Serviços de Mídia</a></li></ul>


<h2>Próximas etapas</h2>
Agora que você sabe como gerenciar ativos, visite o tópico[Como entregar um ativo por download](../media-services-deliver-asset-download/) .

<!--HONumber=35.1-->
