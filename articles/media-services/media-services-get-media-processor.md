<properties 
	pageTitle="Como criar um processador de mídia - Azure" 
	description="Saiba como criar um componente de processador de mídia para codificar, converter o formato, criptografar ou descriptografar conteúdo de mídia dos Serviços de Mídia do Azure. Os exemplos de código são escritos em C# e usam a SDK dos Serviços de Mídia para .NET." 
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
	ms.date="05/13/2015" 
	ms.author="juliako"/>


#Como obter uma instância do processador de mídia

Este artigo faz parte da série do [Fluxo de trabalho do Vídeo sob demanda dos Serviços de Mídia](media-services-video-on-demand-workflow.md).

##Visão geral

Nos Serviços de Mídia, um processador de mídia é um componente que manipula uma tarefa de processamento específica, como codificação, conversão de formato, criptografia ou descriptografia de conteúdo de mídia. Normalmente, você cria um processador de mídia quando está criando uma tarefa para codificar, criptografar ou converter o formato do conteúdo de mídia.

A tabela a seguir fornece o nome e a descrição de cada processador de mídia disponível.

Nome do processador de mídia|Descrição|Mais informações
---|---|---
Codificador de Mídia do Azure|Permite que você execute tarefas de codificação usando o Codificador de Mídia do Azure.| [Cadeias de caracteres de predefinição de tarefa do Codificador de Mídia do Azure](http://msdn.microsoft.com/library/jj129582.aspx)
Fluxo de trabalho do Media Encoder Premium|Permite que você execute tarefas de codificação usando o fluxo de trabalho do Media Encoder Premium.|[Codificar com o fluxo de trabalho do Media Encoder Premium.](media-services-encode-with-premium-workflow.md)
Indexador de Mídia do Azure|Permite tornar arquivos e conteúdo de mídia pesquisáveis, bem como gerar faixas de legenda e palavras-chave.|[Indexando arquivos de mídia com o Indexador de Mídia do Azure](media-services-index-content.md).
Empacotador de mídia do Microsoft Azure|Permite converter ativos de mídia de .mp4 para o formato de smooth streaming. Além disso, permite converter ativos de smooth streaming para o formato Apple HTTP Live Streaming (HLS).|[Cadeias de caracteres de predefinição de tarefa do Empacotador de Mídia do Azure](http://msdn.microsoft.com/library/hh973635.aspx)
Criptografador de Mídia do Microsoft Azure|Permite criptografar ativos de mídia usando a Proteção do PlayReady.|[Cadeias de caracteres de predefinição de tarefa do Empacotador de Mídia do Azure](http://msdn.microsoft.com/library/hh973610.aspx)
Azure Media Hyperlapse (visualização)|Permite suavizar "impactos" no vídeo com estabilização do vídeo. Também permite que você a acelere o seu conteúdo em um clipe de consumo.|[Azure Media Hyperlapse](http://go.microsoft.com/fwlink/?LinkId=613274)
Descriptografia do armazenamento|Permite descriptografar ativos de mídia que foram criptografados usando a criptografia de armazenamento.|N/D

##Obter MediaProcessor

O método a seguir mostra como obter uma instância do processador de mídia. O exemplo de código pressupõe o uso de uma variável em nível de módulo chamada **\_context** para fazer referência ao contexto do servidor, conforme descrito na seção [Como: conectar-se aos Serviços de Mídia de forma programática].

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	    return processor;
	}

##Próximas etapas
Agora que você sabe como obter uma instância do processador de mídia, vá para o tópico [Como codificar um ativo][] que mostra como usar o Codificador de Mídia do Azure para codificar um ativo.

[Como codificar um ativo]: media-services-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[Como: conectar-se aos Serviços de Mídia de forma programática]: ../media-services-set-up-computer/

<!---HONumber=August15_HO6-->