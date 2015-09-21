<properties 
	pageTitle="Manipular tarefas de codificação ao personalizar predefinições de tarefa" 
	description="O Codificador de Serviços de Mídia do Azure permite que você passe os arquivos de predefinição personalizada para Codificador de Mídia do Azure. Este tópico mostra como personalizar arquivos de predefinição para atingir as seguintes tarefas: sobrepor imagem em um vídeo existente, controlar os nomes de arquivo de saída que o codificador produz, unir vídeos." 
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
	ms.date="09/07/2015" 
	ms.author="juliako"/>

#Manipular tarefas de codificação ao personalizar predefinições de tarefa 

O Codificador de Serviços de Mídia do Azure permite que você passe os arquivos de predefinição personalizada para Codificador de Mídia do Azure. Este tópico mostra como personalizar arquivos predefinidos para atingir as seguintes tarefas:

- sobrepor e uma imagem em um vídeo existente, 
- controlar os nomes de arquivo de saída que o codificador produz, 
- unir vídeos,
- codificar apresentações em que a maior parte é fala.

##Controlando nomes de arquivos de saída do Codificador de Mídia do Azure 

Por padrão, o Codificador de Mídia do Azure cria nomes de arquivo de saída ao combinar diversos atributos do ativo de entrada e o processo de codificação. Cada atributo é identificado usando uma macro, conforme discutido abaixo.

Segue uma lista completa das macros disponíveis para a nomeação de arquivo de saída: Taxa de Bits de Áudio – a taxa de bits usada durante a codificação de áudio, especificada em kbps

- Codec de Áudio – o codec usado para codificação de áudio, os valores válidos são: AAC, WMA e DDP
- Contagem de Canais – o número de canais de áudio codificados, os valores válidos são: 1, 2 ou 6
- Extensão padrão – a extensão de arquivo padrão 
- Idioma – o código de idioma BCP-47 representando a linguagem usada no áudio. O padrão atual é "und". 
- Nome de arquivo original – o nome do arquivo carregado no Armazenamento do Azure
- StreamId – a ID do fluxo, conforme definido pelo atributo streamID do elemento <StreamInfo> no arquivo de predefinição 
- Codec de vídeo – o codec usado para codificar, os valores válidos são: H264 e VC1
- Taxa de Bits de Vídeo – a taxa de bits usada durante a codificação do vídeo, especificada em kbps

Essas macros podem ser combinadas em qualquer permutação para controlar o nome dos arquivos gerados pelo Codificador de Serviços de Mídia. Por exemplo, a convenção de nomenclatura padrão é:

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

A convenção de nomenclatura de arquivo é especificada usando o atributo DefaultMediaOutputFileName do elemento [Preset](https://msdn.microsoft.com/library/azure/dn554334.aspx). Por exemplo:

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

O codificador inserirá sublinhados entre cada macro, por exemplo, a configuração acima resultará em um nome de arquivo como: MyVideo\_H264\_4500kpbs\_AAC\_und\_ch2\_128kbps.mp4.


##Criando sobreposições

O Codificador de Serviços de Mídia do Azure permite sobrepor uma imagem (jpg, bmp, gif, tif), um vídeo ou uma faixa de áudio (*wma, *.mp3, *.wav) em um vídeo existente. Essa funcionalidade é semelhante à do Expression Encoder 4 (Service Pack 2).

###Sobreposições com o Codificador de Serviços de Mídia

É possível especificar quando a sobreposição será apresentada, a duração que ela será apresentada e para sobreposições de imagem/vídeo onde a sobreposição aparece na tela. Também é possível ter sobreposições que aparecem ou desaparecem. Os arquivos de áudio/vídeo para sobreposição podem estar contidos em um único ou vários ativos. As sobreposições são controladas pelo XML predefinido que é transmitido ao codificador. Para obter uma descrição completa do esquema predefinido, consulte esquemas de codificador de mídia do Azure. As sobreposições são especificadas no elemento <MediaFile>, conforme mostrado no seguinte trecho predefinido:

	<MediaFile
	    ...
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="WholeSequence"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">
	    ...
	</MediaFile>

###Predefinições para sobreposições de vídeo ou imagem

As sobreposições podem ser de um ou vários ativos. Ao criar sobreposições de vídeo usando vários ativos, o nome de arquivo de sobreposição é especificado no atributo OverlayFileName usando a sintaxe %n% onde n é o índice baseado em zero dos ativos de entrada para a tarefa de codificação. Ao criar sobreposições de vídeo com um único ativo, o nome do arquivo de sobreposição é especificado diretamente no atributo OverlayFileName, conforme os seguintes trechos de código de predefinição:

Exemplo 1:

	<!-- Multiple Assets -->
	<MediaFile
		...
		OverlayFileName="%1%"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

Exemplo 2:

	<!-- Single Asset -->
	<MediaFile
		...
		OverlayFileName="videoOverlay.mp4"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

O local e o tamanho da sobreposição de vídeo são controlados pelo atributo OverlayRect. O conteúdo que a ser sobreposto será ajustado para caber nesse retângulo. A opacidade é controlada pelo atributo OverlayOpacity. Os valores válidos variam entre 0,0 e 1,0, em que 1,0 é 100% opaco. A sobreposição será exibida no momento especificado pelo atributo OverlayStartTime e terminará no momento especificado pelo atributo OverlayEndTime. OverlayStartTime e OverlayEndTime devem estar dentro da linha do tempo do vídeo de origem. Para obter mais informações sobre cada atributo de específico de sobreposição, consulte os Esquemas de Codificador de Mídia do Azure.

###Predefinições para sobreposições de áudio

As sobreposições de áudio podem de qualquer formato de arquivo de áudio com suporte, por exemplo. Para obter uma lista completa de formatos com suporte de arquivos de áudio, consulte Formatos com suporte pelo codificador dos Serviços de Mídia. As sobreposições de áudio também são especificadas no <MediaFile> elemento, conforme mostrado no seguinte trecho de predefinição:

	<MediaFile
		...
		AudioOverlayFileName="%1%" <!-- or AudioOverlayFileName=”audioOverlay.mp3” for overlays from a single asset -->
		AudioOverlayLoop="True"
		AudioOverlayLoopingGap="00:00:00"
		AudioOverlayLayoutMode="Custom"
		AudioOverlayStartTime="00:05:00"
		AudioOverlayEndTime="00:10:00"
		AudioOverlayGainLevel="2.2"
		AudioOverlayFadeInDuration="00:00:00"
		AudioOverlayFadeOutDuration="00:00:00">

Para sobreposições de áudio armazenadas em vários ativos, o nome de arquivo da sobreposição de áudio é especificado no atributo AudioOverlayFileName usando a sintaxe %n%, em que n é o índice baseado em zero da coleção de ativos de entrada para a tarefa de codificação. Para sobreposições de áudio armazenadas em um único ativo, o nome de arquivo da sobreposição é especificado diretamente no atributo AudioOverlayFileName. O AudioOverlayLayoutMode determina quando a sobreposição de áudio será apresentada. Quando definido como "WholeSequence", a faixa de áudio será apresentada durante toda a duração do vídeo. Quando definido como "Custom" os atributos AudioOverlayStartTime e AudioOverlayEndTime determinam quando a sobreposição de áudio começa e termina. OverlayStartTime e OverlayEndTime devem estar dentro da linha do tempo do vídeo de origem. Para obter mais informações sobre todos os atributos de sobreposição de áudio, consulte os esquemas de codificador de mídia do Azure. As sobreposições de áudio podem ser combinadas com as sobreposições de vídeo, conforme mostrado no seguinte trecho de predefinição:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch"
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="Custom"
	    AudioOverlayStartTime="00:05:00"
	    AudioOverlayEndTime="00:10:00"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">


###Envio de tarefas com sobreposições

Depois de criar um arquivo de predefinição, você deve fazer o seguinte:

- Carregar seu(s) ativo(s)
- Carregar a configuração predefinida (observação: o código a seguir pressupõe a predefinição acima.
- Criar um trabalho
- Obter uma referência para o Codificador de Serviços de Mídia
- Criar uma tarefa especificando a coleção de ativos de entrada, a configuração predefinida, o codificador de mídia e o ativo de saída
- Enviar o trabalho

O trecho de código a seguir mostra como realizar estas etapas:

	static public void CreateOverlayJob()
	{
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);


		// Upload assets to overlay
		IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4); // this is the input mezzanine
		IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);// this will be used as a video overlay
		IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv); // this will be used as an audio overlay
		
		// Load the preset configuration
		string presetFileName = "OverlayPreset.xml";
		string configuration = File.ReadAllText(presetFileName);
		
		// Create a job
		IJob job = _context.Jobs.Create("A AME overlay job, using " + presetFileName);
		         
		// Get a reference to the media services encoder   
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		    
		// Create a task    
		ITask task = job.Tasks.AddNew("Encode Task for overlay, using " + presetFileName, processor, configuration, TaskOptions.None);
		
		// Add the input assets
		task.InputAssets.Add(inputAsset1);
		task.InputAssets.Add(inputAsset2);
		task.InputAssets.Add(inputAsset3);
		
		// Add the output asset
		task.OutputAssets.AddNew("Result of an overlay job, using " + presetFileName, AssetCreationOptions.None);
		
		// Submit the job
		job.Submit();
	}



>[AZURE.NOTE]Para facilitar, este trecho carrega cada ativo de maneira sequencial. Em ambiente de produção, os ativos deveriam ser carregados em massa. Para saber mais sobre como carregar vários ativos em massa, consulte [Ingestão de ativos em massa com o SDK dos Serviços de Mídia para .NET](media-services-dotnet-upload-files.md#ingest_in_bulk).

Para o exemplo de código completo, consulte [Criação de sobreposições com o Codificador de Serviços de Mídia](https://code.msdn.microsoft.com/Creating-Audio-and-Video-c2942c47).

###Condições de erro

Ao editar a cadeia de caracteres de predefinição, você deve garantir o seguinte:

- Para sobreposições de imagem/vídeo, o retângulo da sobreposição deve caber completamente dentro das dimensões do vídeo de origem
- Os horários de início e término das sobreposições devem estar dentro da linha do tempo do vídeo de origem
- Se o XML predefinido contiver uma referência a ?OverlayFileName=”%n%”, a coleção de ativos de entrada para as tarefas deve conter no mínimo n+1 ativos



##Unindo segmentos de vídeo

O codificador de serviços de mídia oferece suporte para juntar um conjunto de vídeos. Os vídeos podem ser unidos de ponta a ponta, ou você pode especificar partes de um ou ambos os vídeos a serem juntadas. O resultado da junção é um único ativo de saída que contém o vídeo especificado a partir de ativos de entrada. Os arquivos de vídeo podem estar contidos em um único ou vários ativos. A união é controlada pelo XML predefinido transmitido ao codificador. Para obter uma descrição completa do esquema predefinido, consulte [Esquemas de Codificador de Mídia do Azure](https://msdn.microsoft.com/library/azure/dn584702.aspx).

###União com Codificador de Serviços de Mídia

A união é controlada dentro do elemento <MediaFile>, conforme mostrado na predefinição a seguir:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Para cada vídeo a ser unido, um elemento de <Source> é adicionado para o elemento de <Sources>. Cada elemento de <Source> contém um elemento de <Clips>. Cada elemento de <Clips> contém um ou mais elementos de <Clip> que especificam a quantidade do vídeo a ser unida ao ativo de saída, especificando um horário de início e de término. O elemento de <Source> faz referência ao ativo no qual atua. O formato da referência depende se os vídeos a serem unidos estão em ativos separados ou em um único ativo. Se você deseja unir um vídeo inteiro, basta omitir o elemento de <Clips>.

###União de vídeos a partir de vários ativos

Ao unir vídeos a partir de vários ativos, um índice baseado em zero é usado para o atributo MediaFile do elemento de <Source> para identificar qual ativo ao qual o elemento de <Source> corresponde. O índice zero não é especificado, o elemento de <Source> que não especifica um atributo MediaFile referencia o primeiro ativo de entrada. Todos os outros elementos <Source> devem especificar o índice baseado em zero do ativo de entrada ao qual ele se refere usando sintaxe %n%, em que n é o índice baseado em zero do ativo de entrada. No exemplo anterior, o primeiro elemento de <Source> especifica o primeiro ativo de entrada, o segundo elemento de <Source> especifica o segundo ativo, e assim por diante. Não há nenhuma exigência de que os ativos de entrada sejam referenciados em ordem, por exemplo:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip EndTime="00:00:05" 
	                StartTime="00:00:00" />
	        </Clips>
	                  
	        </Source>
	      <Source
	       AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Este exemplo une porções do segundo, do primeiro e do terceiro ativos de entrada. Observe que, como cada vídeo é referenciado por um índice baseado em zero, é possível unir dois vídeos que têm o mesmo nome. Depois de criar um arquivo de predefinição, você deve fazer o seguinte:
 
- Carregar seus ativos
- Carregar a configuração de predefinição
- Criar um trabalho
- Obter uma referência para o Codificador de Serviços de Mídia
- Criar uma tarefa especificando os ativos de entrada, a configuração predefinida, o codificador de mídia e o ativo de saída
- Enviar o trabalho

O trecho de código a seguir mostra como realizar estas etapas:
	
	static public void StitchWithMultipleAssets()
	{
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);
		
		// Upload assets to stitch
		IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4);
		IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);
		IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv);
		
		// Load the preset configuration
		string presetFileName = "StitchingWithMultipleAssets.xml";
		string configuration = File.ReadAllText(presetFileName);
		
		// Create a job
		IJob job = _context.Jobs.Create("A AME stitching job, using " + presetFileName);
		         
		// Get a reference to the media services encoder   
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		    
		// Create a task    
		ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
		
		// Add the input assets
		task.InputAssets.Add(inputAsset1);
		task.InputAssets.Add(inputAsset2);
		task.InputAssets.Add(inputAsset3);
		
		// Add the output asset
		task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);
		
		// Submit the job
		job.Submit();
		} 


Para facilitar, este trecho carrega cada ativo de maneira sequencial. Em ambiente de produção, os ativos deveriam ser carregados em massa. Para saber mais sobre como carregar vários ativos em massa, consulte [Ingestão de ativos em massa com o SDK dos Serviços de Mídia para .NET](media-services-dotnet-upload-files.md#ingest_in_bulk). Para o exemplo completo de código, consulte [União com o Codificador de Serviços de Mídia](https://code.msdn.microsoft.com/Stitching-with-Media-8fd5f203).

###União de vídeos com um único ativo

Ao unir vídeos dentro de um único ativo, cada vídeo deve ter um nome exclusivo. Os vídeos são especificados usando o atributo MediaFile usando o nome de arquivo como o valor do atributo. Por exemplo:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="video1.mp4">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	       AudioStreamIndex="0"
	       MediaFile="video2.wmv">
	
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="video3.wmv">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Esta predefinição une partes do video1.mp4, video2.wmv e video3.wmv ao ativo de saída. A criação do trabalho e tarefas é igual a unir vídeos a partir de vários ativos, você só precisa carregar um único ativo conforme mostrado no código a seguir:

	// Creates a stitching job that uses a single asset 
    static public void StitchWithASingleAsset()
    {
        string presetFileName = "StitchingWithASingleAsset.xml";
        string configuration = File.ReadAllText(presetFileName);

        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);

        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
        IJob job = _context.Jobs.Create("A AME stitching job, using " + presetFileName);
        IAsset asset = CreateAssetAndUploadMultipleFiles(AssetCreationOptions.None, _stitchingFiles);

        ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);

        job.Submit();
    }

##Codificação de apresentações em que a maior parte é fala.
 
Ao codificar um vídeo cuja faixa de áudio contém na maior parte fala, as predefinições do codificador padrão podem fazer com que os ruídos de fundo sejam amplificados no ativo codificado. Esse comportamento é causado pelo atributo NormalizeAudio definido como true.

###Codificação das apresentações em que a maior parte é fala

Para evitar a amplificação dos ruídos de fundo, faça o seguinte:

1. Copie o conteúdo da predefinição do codificador usada em um arquivo XML. As predefinições do codificador podem ser encontradas em: Esquemas de Codificador de Mídia do Azure
1. Exclua o atributo NormalizeAudio, ele pode ser localizado na parte superior do arquivo de predefinição, sob o elemento <MediaFile>:
	
	<MediaFile
	     DeinterlaceMode="AutoPixelAdaptive"
	     ResizeQuality="Super"
	     NormalizeAudio="True"
	     AudioGainLevel="1"
	     VideoResizeMode="Stretch">

1. Salve o arquivo de predefinições modificado no disco rígido local e usar código da seguinte maneira para codificar com a predefinição personalizada:
		
		// Upload file and create asset
		IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, @"C:\TEMP\Original.mp4");
		 
		string inputPresetFile = @"C:\TEMP\H264 Broadband 720p NoAudioNorm.xml";
		string presetName = Path.GetFileNameWithoutExtension(inputPresetFile);
		 
		IJob job = _context.Jobs.Create("Encode Job for " + asset.Name + ", encoded using " +  presetName);
		
		Console.WriteLine("Encode Job for " + asset.Name + ", encoded using " + presetName);
		
		// Get a media processor reference, and pass to it the name of the processor to use for the specific task.
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		Console.WriteLine("Got MP " + processor.Name + ", ID : " + processor.Id + ", version: " + processor.Version);
		 
		// Read the configuration data into a string. 
		string configuration = File.ReadAllText(inputPresetFile);
		 
		// Create a task with the encoding details, using a string preset.
		ITask task = job.Tasks.AddNew("Encode Task for " + asset.Name + ", encoded using " + presetName, processor, configuration,
		                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
		 
		// Specify the input asset to be encoded.
		task.InputAssets.Add(asset);
		 
		// Add an output asset to contain the results of the job.
		task.OutputAssets.AddNew("Output asset for " + asset.Name + ", encoded using " + presetName, AssetCreationOptions.None);
		 
		// Launch the job. 
		job.Submit();

##Roteiros de aprendizagem dos Serviços de Mídia

Você pode exibir os roteiros de aprendizagem do AMS aqui:

- [Fluxo de trabalho do streaming ao vivo do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Fluxo de trabalho do streaming sob demanda do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##Consulte também

[Esquema XML do Codificador de Mídia do Azure](https://msdn.microsoft.com/library/azure/dn584702.aspx)

<!---HONumber=Sept15_HO2-->