<properties 
	pageTitle="Como criar um processador de mídia | Microsoft Azure" 
	description="Saiba como criar um componente de processador de mídia para codificar, converter o formato, criptografar ou descriptografar conteúdo de mídia dos Serviços de Mídia do Azure. Os exemplos de código são escritos em C# e usam a SDK dos Serviços de Mídia para .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2016" 
	ms.author="juliako"/>


#Como obter uma instância do processador de mídia

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)


##Visão geral

Nos Serviços de Mídia, um processador de mídia é um componente que manipula uma tarefa de processamento específica, como codificação, conversão de formato, criptografia ou descriptografia de conteúdo de mídia. Normalmente, você cria um processador de mídia quando está criando uma tarefa para codificar, criptografar ou converter o formato do conteúdo de mídia.

A tabela a seguir fornece o nome e a descrição de cada processador de mídia disponível.

Nome do processador de mídia|Descrição|Mais informações
---|---|---
Media Encoder Standard|Fornece funcionalidades padrão para codificação sob demanda. |[Visão Geral e Comparação de Codificadores de Mídia sob Demanda do Azure](media-services-encode-asset.md)
Fluxo de trabalho do Media Encoder Premium|Permite que você execute tarefas de codificação usando o fluxo de trabalho do Media Encoder Premium.|[Visão Geral e Comparação de Codificadores de Mídia sob Demanda do Azure](media-services-encode-asset.md)
Indexador de Mídia do Azure| Permite tornar arquivos e conteúdo de mídia pesquisáveis, bem como gerar faixas de legenda e palavras-chave.|[Indexador de Mídia do Azure](media-services-index-content.md)
Azure Media Hyperlapse (visualização)|Permite suavizar "impactos" no vídeo com estabilização do vídeo. Também permite que você a acelere o seu conteúdo em um clipe de consumo.|[Azure Media Hyperlapse](media-services-hyperlapse-content.md)
Codificador de Mídia do Azure|Substituído
Descriptografia do armazenamento| Substituído|
Gerenciador de mídia do Azure|Substituído|
Criptografador de Mídia do Azure|Substituído|

##Obter processador de mídia

O método a seguir mostra como obter uma instância do processador de mídia. O exemplo de código pressupõe o uso de uma variável em nível de módulo chamada **\_context** para fazer referência ao contexto do servidor, conforme é descrito na seção [Como: conectar-se aos Serviços de Mídia de forma programática](media-services-dotnet-connect-programmatically.md).

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
		var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		if (processor == null)
		throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		return processor;
	}


##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Próximas etapas

Agora que você já sabe como obter uma instância do processador de mídia, vá para o tópico [Como codificar um ativo](media-services-dotnet-encode-with-media-encoder-standard.md), que mostrará como usar o Codificador de Mídia Padrão para codificar um ativo.

<!---HONumber=AcomDC_0629_2016-->