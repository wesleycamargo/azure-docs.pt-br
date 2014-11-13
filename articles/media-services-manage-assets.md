<properties urlDisplayName="Manage Assets in Media Services" pageTitle="Como gerenciar ativos nos Servi&ccedil;os de M&iacute;dia - Azure" metaKeywords="" description="Saiba como gerenciar ativos em Servi&ccedil;os de M&iacute;dia. Voc&ecirc; tamb&eacute;m pode gerenciar trabalhos, tarefas, pol&iacute;ticas de acesso, localizadores e muito mais. Os exemplos de c&oacute;digo s&atilde;o escritos em C# e usam a SDK dos Servi&ccedil;os de M&iacute;dia para .NET." metaCanonical="" services="media-services" documentationCenter="" title="Como: Gerenciar ativos no armazenamento" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# Como: Gerenciar ativos no armazenamento

Este artigo faz parte de uma série de introdução à programação dos Serviços de Mídia do Azure. O tópico anterior era [Como: Proteger um ativo][Como: Proteger um ativo].

Depois que você cria ativos de mídia e os carrega nos Serviços de Mídia, você pode acessar e gerenciar os ativos no servidor. Você também pode gerenciar outros objetos no servidor que fazem parte dos Serviços de Mídia, incluindo trabalhos, tarefas, políticas de acesso, localizadores e muito mais.

O exemplo a seguir mostra como consultar um ativo por assetId.

    static IAsset GetAsset(string assetId){ // Use a LINQ Select query to get an asset. var assetInstance = from a in _context.Assets where a.Id == assetId select a; // Reference the asset as an IAsset. IAsset asset = assetInstance.FirstOrDefault();
    return asset;

<p>
}
</code>

</pre>
</p>
Para listar todos os recursos disponíveis no servidor, você pode usar o método a seguir que itera pela coleção de ativos e exibe detalhes sobre cada ativo.

    static void ListAssets(){ string waitMessage = "Building the list. This may take a few " + "seconds to a few minutes depending on how many assets " + "you have." + Environment.NewLine + Environment.NewLine + "Please wait..." + Environment.NewLine; Console.Write(waitMessage);
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

<p>
}
</code>

</pre>
O trecho de código a seguir exclui todos os ativos da conta de Serviços de Mídia.

    foreach (IAsset asset in _context.Assets){ asset.Delete();}

</p>
Para obter mais informações sobre como gerenciar ativos, consulte:

-   [Gerenciar ativos com o SDK dos Serviços de Mídia para .NET][Gerenciar ativos com o SDK dos Serviços de Mídia para .NET]
-   [Gerenciar ativos com a API REST dos Serviços de Mídia][Gerenciar ativos com a API REST dos Serviços de Mídia]

</p>
## Próximas etapas

Agora que você sabe como gerenciar ativos, visite o tópico [Como entregar um ativo por download][Como entregar um ativo por download].

  [Como: Proteger um ativo]: ../media-services-protect-asset/
  [Gerenciar ativos com o SDK dos Serviços de Mídia para .NET]: http://msdn.microsoft.com/pt-br/library/jj129589.aspx
  [Gerenciar ativos com a API REST dos Serviços de Mídia]: http://msdn.microsoft.com/pt-br/library/jj129583.aspx
  [Como entregar um ativo por download]: ../media-services-deliver-asset-download/
