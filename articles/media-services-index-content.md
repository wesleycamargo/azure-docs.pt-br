<properties 
	pageTitle="Indexando arquivos de mídia com o Indexador de Mídia do Azure" 
	description="O Indexador de Mídia do Azure permite que você torne o conteúdo de seus arquivos de mídia pesquisável e gere uma transcrição de texto completo para legendas codificadas e palavras-chave. Este tópico mostra como usar o indexador de mídia." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="juliako"/>


# Indexando arquivos de mídia com o Indexador de Mídia do Azure

Este artigo faz parte das séries do [vídeo de serviços de mídia no fluxo de trabalho sob demanda](media-services-video-on-demand-workflow.md) . 

O Indexador de Mídia do Azure permite que você torne o conteúdo de seus arquivos de mídia pesquisável e gere uma transcrição de texto completo para legendas codificadas e palavras-chave. É possível processar um arquivo de mídia ou vários arquivos de mídia em um lote.  

>[AZURE.NOTE] Quanto a indexação de conteúdo, certifique-se de usar os arquivos de mídia com fala muito clara (sem música em segundo plano, ruído, efeitos ou assovio no microfone). Alguns exemplos de conteúdo apropriado são: reuniões, palestras e apresentações registradas. O seguinte conteúdo pode não ser adequado para indexação: filmes, programas de TV, tudo com áudio misto e efeitos de som, com conteúdo mal gravado com ruídos de fundo (assovio).


Um trabalho de indexação gera quatro saídas para todo arquivo de indexação:

- Arquivo de legenda codificada no formato SAMI.
- Arquivo de legenda codificada no formato de linguagem de marcação de texto temporizada (TTML).

	O SAMI e TTML incluem uma marca chamada Recognizability, que classifica um trabalho de indexação com base em o quanto a fala é reconhecível no vídeo de origem.  Você pode usar o valor de Recognizability para arquivos de saída de tela para facilidade de uso. Uma baixa pontuação significaria resultados de indexação fraca devido a qualidade do áudio.
- Arquivo de palavra-chave (XML).
- Áudio de indexação de arquivo de blob (AIB) para uso com o SQL Server.
	
	Para obter mais informações, consulte [Usando arquivos de AIB com o indexador de mídia do Azure e SQL Server](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


Este tópico mostra como criar trabalhos de indexação **indexar um ativo** e **indexar vários arquivos**.

Para as atualizações mais recentes do indexador de mídia do Azure, consulte [blogs dos serviços de mídia](http://azure.microsoft.com/blog/topics/media-services/).

##Usando arquivos de configuração e de manifesto para tarefas de indexação

Você pode especificar mais detalhes para as tarefas de indexação usando uma configuração de tarefa. Por exemplo, você pode especificar quais metadados usar para o arquivo de mídia. Esses metadados são usados pelo mecanismo de idioma para expandir seu vocabulário e melhora significativamente a precisão do reconhecimento de fala.

Você também pode processar vários arquivos de mídia ao mesmo tempo usando um arquivo de manifesto.

Para obter mais informações, consulte [Predefinição de tarefa para o indexador de mídia do Azure](https://msdn.microsoft.com/library/azure/dn783454.aspx).

##Indexe um ativo

O método a seguir carrega um arquivo de mídia como um ativo e cria um trabalho para indexar o ativo.

Observe que, se nenhum arquivo de configuração for especificado, o arquivo de mídia será indexado com todas as configurações padrão.
	
	static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
	{
	    // Criar um ativo e carregar o arquivo de mídia de entrada no armazenamento.
	    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
	        "My Indexing Input Asset",
	        AssetCreationOptions.None);
	
	    // Declarar um novo trabalho.
	    IJob job = _context.Jobs.Create("My Indexing Job");
	
	    // Obter uma referência para o indexador de mídia do Azure.
	    string MediaProcessorName = "Azure Media Indexer",
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Ler a configuração do arquivo, se especificada.
	    string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);
	
	    // Criar uma tarefa com os detalhes de codificação, usando uma predefinição de cadeia de caracteres.
	    ITask task = job.Tasks.AddNew("My Indexing Task",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Especificar o ativo de entrada a ser indexado.
	    task.InputAssets.Add(asset);
	
	    // Adicionar um ativo de saída para conter o resultado do trabalho. 
	    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
	
	    // Usar o manipulador de eventos a seguir para verificar o andamento do trabalho.  
	    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	    // Iniciar o trabalho.
	    job.Submit();
	
	    // Verificar a execução do trabalho e aguardar até que o trabalho seja concluído. 
	    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	    progressJobTask.Wait();
	
	    // Se o estado do trabalho for Erro, o método de manipulação de eventos 
	    // para o andamento do trabalho deverá registrar erros.  Aqui, verificamos 
	    // o estado de erro e saímos, se necessário.
	    if (job.State == JobState.Error)
	    {
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Baixar as saídas de trabalho.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}
	
	static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = _context.Assets.Create(assetName, options);
	
	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	    assetFile.Upload(filePath);
	
	    return asset;
	}
	        
	static void DownloadAsset(IAsset asset, string outputDirectory)
	{
	    foreach (IAssetFile file in asset.AssetFiles)
	    {
	        file.Download(Path.Combine(outputDirectory, file.Name));
	    }
	}
	
	static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	    var processor = _context.MediaProcessors
	    .Where(p => p.Name == mediaProcessorName)
	    .ToList()
	    .OrderBy(p => new Version(p.Version))
	    .LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor",
	                                                   mediaProcessorName));
	
	    return processor;
	} 
	
###<a id="output_files"></a>Arquivos de saída

O trabalho de indexação gera os seguintes arquivos de saída. Os arquivos serão armazenados no primeiro ativo de saída.


<table border="1">
<tr><th>Nome do arquivo</th><th>Descrição</th></tr>
<tr><td>InputFileName.aib </td>
<td>Arquivo de blob de indexação de áudio.<br/><br/>
O arquivo de blob de indexação de áudio (AIB) é um arquivo binário que pode ser pesquisado no Microsoft SQL Server usando a pesquisa de texto completa.  O arquivo AIB é mais eficiente do que os arquivos de legenda simples, porque ele contém alternativas para cada palavra, permitindo uma experiência de pesquisa muito mais sofisticada.
<br/>
<br/>
Ele requer a instalação do complemento do indexador SQL em uma máquina executando Microsoft SQL Server 2008 ou posterior. Pesquisando o AIB usando a pesquisa de texto completa do Microsoft SQL Server fornece resultados da pesquisa mais precisos que pesquisando os arquivos de legenda codificada gerados pelo WAMI. Isso ocorre porque o AIB contém palavras alternativas que parecem familiares enquanto os arquivos de legenda codificados contêm a palavra com a confiança mais alta para cada segmento do áudio. Se estiver procurando palavras faladas de máxima importância, é recomendável usar o AIB em conjunto com o Microsoft SQL Server.
<br/><br/>
Para baixar o complemento, clique em <a href="http://aka.ms/indexersql">Complemento de SQL do Indexador de Mídia do Azure</a>.
<br/><br/>
Também é possível utilizar outros mecanismos de pesquisa, como o Apache Lucene/Solr para indexar apenas vídeo com base na legenda codificada e arquivos XML de palavra-chave, mas isso resulta em resultados de pesquisa menos precisos.</td></tr>
<tr><td>InputFileName.smi<br/>InputFileName.ttml</td>
<td>Arquivos de legenda (CC) nos formatos SAMI e TTML.
<br/><br/>
Eles podem ser usados para tornar os arquivos de áudio e vídeo acessíveis para pessoas com deficiência auditiva.
<br/><br/>
O SAMI e TTML incluem uma marca chamada <b>Recognizability</b>, que classifica um trabalho de indexação com base em o quanto a fala é reconhecível no vídeo de origem.  Você pode usar o valor de <b>Recognizability</b> para arquivos de saída de tela para facilidade de uso. Uma baixa pontuação significaria resultados de indexação fraca devido a qualidade do áudio.</td></tr>
<tr><td>InputFileName.kw.xml</td>
<td>Arquivo de palavra-chave.
<br/><br/>
O arquivo de palavra-chave é um arquivo XML que contém as palavras-chave extraídas do conteúdo de fala, com frequência e informações de deslocamento.
<br/><br/>
O arquivo pode ser usado para várias finalidades, como para executar análise de fala ou expor aos mecanismos de pesquisa como Bing, Google ou Microsoft SharePoint para tornar os arquivos de mídia mais detectáveis ou usado para entregar anúncios mais relevantes.</td></tr>
</table>

Se nem todos os arquivos de mídia de entrada são indexados com êxito, o trabalho de indexação falhará com o código de erro 4000. Para obter mais informações, consulte [códigos de erro](#error_codes).

##Vários arquivos de índice

O método a seguir carrega vários arquivos de mídia como um ativo e cria um trabalho para indexar todos esses arquivos em um lote.

Um arquivo de manifesto com a extensão .lst é criado e carregado para o ativo. O arquivo de manifesto contém a lista de todos os arquivos de ativo. Para obter mais informações, consulte [Predefinição de tarefa para o indexador de mídia do Azure](https://msdn.microsoft.com/library/azure/dn783454.aspx).
	
	static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
	{
	    // Criar um ativo e carregar no armazenamento.
	    IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
	        "My Indexing Input Asset - Batch Mode",
	        AssetCreationOptions.None);
	
	    // Criar um arquivo de manifesto que contém todos os nomes de arquivos ativos e carregar no armazenamento.
	    string manifestFile = "input.lst";            
	    File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
	    assetFile.Upload(manifestFile);
	
	    // Declarar um novo trabalho.
	    IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");
	
	    // Obter uma referência para o indexador de mídia do Azure.
	    string MediaProcessorName = "Azure Media Indexer";
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration.
	    string configuration = File.ReadAllText("batch.config");
	
	    // Criar uma tarefa com os detalhes de codificação, usando uma predefinição de cadeia de caracteres.
	    ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Especificar o ativo de entrada a ser indexado.
	    task.InputAssets.Add(asset);
	
	    // Adicionar um ativo de saída para conter o resultado do trabalho.
	    task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);
	
	    // Usar o manipulador de eventos a seguir para verificar o andamento do trabalho.  
	    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	    // Iniciar o trabalho.
	    job.Submit();
	
	    // Verificar a execução do trabalho e aguardar até que o trabalho seja concluído. 
	    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	    progressJobTask.Wait();
	
	    // Se o estado do trabalho for Erro, o método de manipulação de eventos 
	    // para o andamento do trabalho deverá registrar erros.  Aqui, verificamos 
	    // o estado de erro e saímos, se necessário.
	    if (job.State == JobState.Error)
	    {
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Baixar as saídas de trabalho.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}
	
	private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = _context.Assets.Create(assetName, options);
	
	    foreach (string filePath in filePaths)
	    {
	        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	        assetFile.Upload(filePath);
	    }
	
	    return asset;
	}


###Arquivos de saída

Quando houver mais de um arquivo de mídia de entrada, o WAMI gerará um arquivo de manifesto para as saídas de trabalho, chamado 'JobResult.txt'. Para cada mídia de entrada de arquivo, o AIB, SAMI, TTML e arquivos de palavra-chave resultantes são numerados em sequência, como listado abaixo.

Para obter descrições dos arquivos de saída, consulte [arquivos de saída](#output_files). 


<table border="1">
<tr><th>Nome do arquivo</th><th>Descrição</th></tr>
<tr><td>JobResult.txt</td>
<td>Manifesto de saída
<br/><br/>Abaixo está o formato do arquivo de manifesto de saída (JobResult.txt).
<br/><br/>

<table border="1">
<tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Erro</th></tr>
<tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr>
<tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr>
<tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr>
</table><br/>
Cada linha representa um arquivo de mídia de entrada:
<br/><br/>
InputFile: nome de arquivo do ativo ou URL do arquivo de mídia de entrada.
<br/><br/>
Alias: nome do arquivo de saída correspondente.
<br/><br/>
MediaLength: tamanho do arquivo de mídia de entrada, em segundos. Pode ser 0 se o erro ocorreu para esta entrada.
<br/><br/>
Error: indica se este arquivo de mídia é indexado com êxito. 0 para êxito, caso contrário, falha. Please refer to <a href="#error_codes">Error Codes</a> for concrete errors.
</td></tr>
<tr><td>Media_1.aib </td>
<td>Arquivo #0 - indexação de áudio de arquivo de blob.</td></tr>
<tr><td>Media_1.smi<br/>Media_1.ttml</td>
<td>Arquivo #0 - Legenda (CC) de arquivos nos formatos SAMI e TTML.</td></tr>
<tr><td>Media_1.kw.xml</td>
<td>Arquivo #0 - arquivo de palavra-chave.</td></tr>
<tr><td>Media_2.aib </td>
<td>Arquivo #1 - indexação de áudio de arquivo de blob.</td></tr>
</table>

Se nem todos os arquivos de mídia de entrada são indexados com êxito, o trabalho de indexação falhará com o código de erro 4000. Para obter mais informações, consulte [códigos de erro](#error_codes).

###Trabalho parcialmente bem-sucedido

Se nem todos os arquivos de mídia de entrada são indexados com êxito, o trabalho de indexação falhará com o código de erro 4000. Para obter mais informações, consulte [códigos de erro](#error_codes).


As mesmas saídas (como trabalhos com êxito) são geradas. Você pode consultar o arquivo de manifesto de saída para descobrir quais arquivos de entrada estão com falha, de acordo com os valores da coluna de erro. Para arquivos de entrada com falha, o AIB, SAMI, TTML e arquivos de palavra-chave resultantes não serão gerados.


### <a id="error_codes"></a>Códigos do Erro


<table border="1">
<tr><th>Código</th><th>Nome</th><th>Possíveis razões</th></tr>
<tr><td>2000</td><td>Configuração inválida</td><td>Configuração inválida</td></tr>
<tr><td>2001</td><td>Ativos de entrada inválidos</td><td>Faltando ativos de entrada ou um ativo vazio.</td></tr>
<tr><td>2002</td><td>Manifesto inválido</td><td>O manifesto está vazio ou o manifesto contém itens inválidos.</td></tr>
<tr><td>2003</td><td>Falha ao baixar o arquivo de mídia</td><td>URL inválida no arquivo de manifesto.</td></tr>
<tr><td>2004</td><td>Protocolo não suportado</td><td>Não há suporte para o protocolo de URL de mídia.</td></tr>
<tr><td>2005</td><td>Tipo de arquivo sem suporte</td><td>Não há suporte para o tipo de arquivo de mídia de entrada.</td></tr>
<tr><td>2006</td><td>Muitos arquivos de entrada</td><td>Há mais de 10 arquivos no manifesto de entrada. </td></tr>
<tr><td>3000</td><td>Falha ao decodificar o arquivo de mídia</td>
<td>Codec de mídia sem suporte.
<br/>ou<br/>
Arquivo de mídia corrompido.
<br/>ou<br/>
Nenhum fluxo de áudio na mídia de entrada.</td></tr>
<tr><td>4000</td><td>Indexação de lotes parcialmente bem-sucedida</td><td>Alguns dos arquivos de mídia de entrada estão com falha para serem indexados. Para obter mais informações, consulte <a href="output_files">Arquivos de saída</a>.</td></tr>
<tr><td>outros</td><td>Erros internos</td><td>Entre em contato com a equipe de suporte.</td></tr>
</table>


##<a id="supported_languages"></a>Idiomas com suporte

Atualmente, há suporte para apenas o idioma inglês.

##Links relacionados

[Usando arquivos AIB com o indexador de mídia do Azure e SQL Server](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->


<!--HONumber=52-->