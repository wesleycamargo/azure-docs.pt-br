<properties 
	pageTitle="Como gerenciar ativos nos Serviços de Mídia" 
	description="Saiba como gerenciar ativos em Serviços de Mídia. Você também pode gerenciar trabalhos, tarefas, políticas de acesso, localizadores e muito mais. Os exemplos de código são escritos em C# e usam a SDK dos Serviços de Mídia para .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>


#Como gerenciar ativos no armazenamento

Este artigo faz parte das séries de [Vídeo de serviços de mídia no fluxo de trabalho sob demanda](media-services-video-on-demand-workflow.md) e [Fluxo de trabalho da transmissão ao vivo dos serviços de mídia](media-services-live-streaming-workflow.md).


Depois de criar ativos de mídia, você pode acessar e gerenciar os ativos no servidor. Você também pode gerenciar outros objetos no servidor que fazem parte dos Serviços de Mídia, incluindo trabalhos, tarefas, políticas de acesso, localizadores e muito mais.

O exemplo a seguir mostra como consultar um ativo por assetId.

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

Para listar todos os recursos disponíveis no servidor, você pode usar o método a seguir que itera pela coleção de ativos e exibe detalhes sobre cada ativo.
	
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

O trecho de código a seguir exclui todos os ativos da conta de Serviços de Mídia. Observe que, se um ativo é associado a um programa, você primeiro deve excluir o programa.

	foreach (IAsset asset in _context.Assets)
	{
	    asset.Delete();
	}

 

<!---HONumber=July15_HO4-->