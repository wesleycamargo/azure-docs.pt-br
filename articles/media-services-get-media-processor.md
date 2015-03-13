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
	ms.date="02/10/2015" 
	ms.author="juliako"/>


# Como: Obter uma instância do processador de mídia

Este artigo faz parte da série do [vídeo de serviços de mídia no fluxo de trabalho de demanda](../media-services-video-on-demand-workflow). 

## Visão geral

Nos Serviços de Mídia, um processador de mídia é um componente que manipula uma tarefa de processamento específica, como codificação, conversão de formato, criptografia ou descriptografia de conteúdo de mídia. Normalmente, você cria um processador de mídia quando está criando uma tarefa para codificar, criptografar ou converter o formato do conteúdo de mídia.

A tabela a seguir fornece o nome e a descrição de cada processador de mídia disponível.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Nome do processador de mídia</th>
       <th>Descrição</th>
	<th>Mais informações</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Codificador de Mídia do Azure</td>
       <td>Permite que você execute tarefas de codificação usando o Codificador de Mídia.</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx"> Cadeias de caracteres de predefinição de tarefa para o Codificador de Mídia do Azure</a></td>
    </tr>
    <tr>
        <td>Empacotador de mídia do Microsoft Azure</td>
        <td>Permite converter ativos de mídia de .mp4 para o formato de smooth streaming. Além disso, permite converter ativos de smooth streaming para o formato Apple HTTP Live Streaming (HLS).</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Cadeias de caracteres de predefinição de tarefa do Empacotador de Mídia do Azure</a></td>
    </tr>
    <tr>
        <td>Criptografador de Mídia do Microsoft Azure</td>
        <td>Permite criptografar ativos de mídia usando a Proteção do PlayReady.</td>
        <td><a href="http://msdn.microsoft.com/library/hh973610.aspx">Cadeias de caracteres de predefinição de tarefa do Empacotador de Mídia do Azure</a></td>
    </tr>
    <tr>
        <td>Indexador de Mídia do Azure</td>
        <td>Permite tornar arquivos e conteúdo de mídia pesquisáveis, bem como gerar faixas de legenda e palavras-chave.</td>
		<td>N/D</td>
    </tr>
    <tr>
        <td>Descriptografia do armazenamento</td>
        <td>Permite descriptografar ativos de mídia que foram criptografados usando a criptografia de armazenamento.</td>
		<td>N/D</td>
    </tr>  </tbody>
</table>

<br />

## Obter MediaProcessor

O método a seguir mostra como obter uma instância do processador de mídia. O exemplo de código pressupõe o uso de uma variável em nível de módulo chamada **_context** para fazer referência ao contexto do servidor, conforme descrito na seção [Como: conectar-se aos Serviços de Mídia de modo programático].

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	    return processor;
	}

## Próximas etapas
Agora que você sabe como obter uma instância do processador de mídia, vá para o tópico [Como codificar um ativo][] que mostra como usar o Codificador de Mídia do Azure para codificar um ativo.

[Como codificar um ativo]: ../media-services-encode-asset/
[Cadeias de caracteres de predefinição de tarefa para o Codificador de Mídia do Azure]: http://msdn.microsoft.com/library/jj129582.aspx
[Como: conectar-se aos Serviços de Mídia de modo programático]: ../media-services-set-up-computer/
<!--HONumber=45--> 
