<properties linkid="develop-media-services-how-to-guides-create-media-processor" urlDisplayName="Create a Media Processor" pageTitle="How to Create a Media Processor - Azure" metaKeywords="" description="Learn how to create a media processor component to encode, convert format, encrypt, or decrypt media content for Azure Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# Como: Obter uma instância do processador de mídia

Este artigo faz parte de uma série de introdução à programação dos Serviços de Mídia do Azure. O tópico anterior era [Como: Criar um ativo criptografado e carregá-lo no armazenamento][].

Nos Serviços de Mídia, um processador de mídia é um componente que manipula uma tarefa de processamento específica, como codificação, conversão de formato, criptografia ou descriptografia de conteúdo de mídia. Normalmente, você cria um processador de mídia quando está criando uma tarefa para codificar, criptografar ou converter o formato do conteúdo de mídia.

A tabela a seguir fornece o nome e a descrição de cada processador de mídia disponível.

| Nome do processador de mídia     | Descrição                                                                                                                                                                              | Mais informações                                                                      |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| Codificador de Mídia do Azure    | Permite que você execute tarefas de codificação usando o Codificador de Mídia.                                                                                                         | [Cadeias de caracteres de predefinição de tarefa do Codificador de Mídia do Azure][]  |
| Gerenciador de mídia do Azure    | Permite converter ativos de mídia de .mp4 para o formato de smooth streaming. Além disso, permite converter ativos de smooth streaming para o formato Apple HTTP Live Streaming (HLS). | [Cadeias de caracteres de predefinição de tarefa do Empacotador de Mídia do Azure][]  |
| Criptografador de Mídia do Azure | Permite criptografar ativos de mídia usando a Proteção do PlayReady.                                                                                                                   | [Cadeias de caracteres de predefinição de tarefa do Empacotador de Mídia do Azure][1] |
| Descriptografia do armazenamento | Permite descriptografar ativos de mídia que foram criptografados usando a criptografia de armazenamento.                                                                               | N/D                                                                                   |

O método a seguir mostra como obter uma instância do processador de mídia. O exemplo de código pressupõe o uso de uma variável em nível de módulo chamada **_context** para fazer referência ao contexto do servidor, conforme descrito na seção [Como: conectar-se aos Serviços de Mídia de modo programático][].

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

  [Como: Criar um ativo criptografado e carregá-lo no armazenamento]: http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409
  [Cadeias de caracteres de predefinição de tarefa do Codificador de Mídia do Azure]: http://msdn.microsoft.com/en-us/library/jj129582.aspx
  [Cadeias de caracteres de predefinição de tarefa do Empacotador de Mídia do Azure]: http://msdn.microsoft.com/en-us/library/hh973635.aspx
  [1]: http://msdn.microsoft.com/en-us/library/hh973610.aspx
  [Como: conectar-se aos Serviços de Mídia de modo programático]: http://www.windowsazure.com/en-us/develop/media-services/how-to-guides/set-up-computer-for-media-services
  [Como codificar um ativo]: http://go.microsoft.com/fwlink/?LinkId=301753
