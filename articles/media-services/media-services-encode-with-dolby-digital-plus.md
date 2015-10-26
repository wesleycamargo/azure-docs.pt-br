<properties 
	pageTitle="Codificando sua mídia com Dolby Digital Plus" 
	description="Este tópico descreve como codificar suas mídias com Dolby Digital Plus." 
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

#Codificando sua mídia com Dolby Digital Plus

O Codificador de Mídia do Azure oferece suporte à codificação **Dolby® Digital Plus**. Dolby® Digital Plus ou AC-3 melhorado (E-AC-3) é um codec de áudio de sistema surround avançado desenvolvido especificamente para mídia em evolução. De home theaters e PCs a celulares e transmissões online, o Dolby Digital Plus define áudio de alta fidelidade. Você obterá a experiência de cinema Dolby renomada de todas as suas opções de entretenimento. Dolby Digital Plus baseia-se nos principais tecnologias Dolby Digital, o padrão estabelecido para cinema, transmissão e sistema surround de home-theater. À medida que os dispositivos móveis proliferam, Dolby Digital Plus também está surgindo como o padrão para entretenimento móvel. Estas novas tecnologias avançadas para aprimorar áudio oferecem qualidade de som ainda melhor e economia de largura de banda adicional. Você obtém conteúdo excelente com menos interrupções, mesmo quando a largura de banda é limitada.


##Configurar o Azure Media Encoder para codificar com Dolby Digital Plus

###Obtenha o processador do Azure Media Encoder 

O Azure Media Encoder dá suporte ao Dolby Digital Plus. Para obter uma referência ao **Codificador de Mídia do Azure**, consulte o tópico [Obter processadores de mídia](media-services-get-media-processor.md).

###<a id="configure_preset"></a>Definir configurações do Codificador de Mídia do Azure

Ao configurar as definições de codificação para uso com o Azure Media Encoder, havia diversas predefinições representadas por cadeias de caracteres fáceis de lembrar. O codificador do Dolby Digital Plus fornece um vasto conjunto de controles, consulte [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx) para obter mais informações. Portanto, não há predefinições de cadeia pré-criadas que utilizem esse codec. Você deve especificar as configurações de codificação desejadas em um arquivo XML e enviar esses dados com a tarefa, conforme mostrado no seguinte exemplo de código:
	
	string configuration = File.ReadAllText(pathToXMLConfigFile));

    ITask task = job.Tasks.AddNew("My Dolby Digital Plus Encode Task",
        processor,
        configuration,
        _clearConfig);

Este tópico descreve diversas predefinições XML de exemplo que configuram as definições do codificador. O elemento usado para configurar a codificação Dolby Digital Plus é [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx) que aparece como um nó filho do elemento <AudioProfile> em uma predefinição XML do Codificador de Mídia do Azure. Este elemento XML contém um número de atributos que controlam a vários elementos da codificação.

##Codificação Dolby Digital Plus 5.1 multicanal

Para codificar para Dolby Digital Plus 5.1 multicanal, defina os atributos Codec e EncoderMode como "DolbyDigitalPlus". O número de canais codificados é especificado usando o atributo AudioCodingMode do elemento <DolbyDigitalPlusAudioProfile>. Para uma codificação multicanal 5.1, defina o AudioCodingMode como "Mode32".

A predefinição XML a seguir contém uma predefinição completa do Azure Media Encoder XML que produz um arquivo MP4 com banda larga H264, vídeo 1080p e áudio multicanal 5.1 Dolby Digital Plus. Essa predefinição também especifica para codificar um canal de baixa frequência LFE (efeitos), que é especificada definindo o atributo LFEOn como true. Quaisquer atributos não especificados terão seus valores padrão.

Esta predefinição XML deve ser passada ao **Codificador de Mídia do Azure** para criar um trabalho de codificação, conforme descrito [neste](media-services-dotnet-encode-asset.md) tópico (em vez de uma cadeia de caracteres de predefinição predefinida, você passará a predefinição XML inteira, como descrito [aqui](#configure_preset)).


	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode32"
	              LFEOn="True"
	              SamplesPerSecond="48000"
	              BandwidthLimitingLowpassFilter="True"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="512"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="3"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Balanced"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="64"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	                <StreamInfo
	                  Size="1920, 1080">
	                  <Bitrate>
	                    <ConstantBitrate
	                      Bitrate="6000"
	                      IsTwoPass="False"
	                      BufferWindow="00:00:05" />
	                  </Bitrate>
	                </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##Codificação Dolby Digital Plus estéreo

Para codificar para Dolby Digital Plus estéreo, defina os atributos Codec e EncoderMode como "DolbyDigitalPlus". O número de canais codificados é especificado usando o atributo AudioCodingMode. Para uma codificação estéreo, defina o AudioCodingMode como "Mode20". O exemplo de predefinição XML a seguir mostra que o <DolbyDigitalPlusAudioProfile> é usado para codificar como áudio 5.1. Quaisquer atributos não especificados terão seus valores padrão.

Esta predefinição XML deve ser passada ao **Codificador de Mídia do Azure** para criar um trabalho de codificação, conforme descrito [neste](media-services-dotnet-encode-asset.md) tópico (em vez de uma cadeia de caracteres de predefinição predefinida, você passará a predefinição XML inteira, como descrito [aqui](#configure_preset)).

	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode20"
	              LFEOn="False"
	              SamplesPerSecond="48000"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="128"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="1"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Speed"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="32"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	              <StreamInfo
	                Size="852, 480">
	                <Bitrate>
	                  <ConstantBitrate
	                    Bitrate="2200"
	                    IsTwoPass="False"
	                    BufferWindow="00:00:05" />
	                </Bitrate>
	              </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##Codificação para múltiplos arquivos MP4 

É possível codificar como MP4s múltiplas dentro de uma única predefinição XML. Para cada MP4 que você deseja gerar, adicione um elemento <Preset> à configuração. Cada elemento <Preset> pode conter informações de configuração de vídeo, áudio ou ambos.

###Configuração

A configuração a seguir gerará as seguintes saídas:

- 8 arquivos MP4 de apenas vídeo
	- Vídeo 1080p @ 6000 kbps
	- Vídeo de 1080p a 4700 kbps
	- Vídeo 720p @ 3400 kbps
	- Vídeo 960 x 540 @ 2250 kbps
	- Vídeo de 960 x 540 a 1500 kbps
	- Vídeo de 640 x 380 a 1000 kbps
	- Vídeo de 640 x 380 a 650 kbps
	- Vídeo de 320 x 180 a 400 kbps

- 5 arquivos MP4 de apenas áudio
	- Áudio estéreo AAC @ 128 kbps
	- Áudio 5.1 AAC @ 512 kbps
	- Dolby Digital Plus estéreo @ 128 kbps
	- Dolby Digital Plus 5.1 multicanal @ 512 kbps
	- Estéreo AAC @ 56 kbps
- Um manifesto .ism
- Um arquivo XML que lista as propriedades dos arquivos MP4 gerados.
		
		<?xml version="1.0" encoding="utf-16"?>
		<!--Created for Azure Media Encoder, May 16 2013 -->
		<Presets>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"   
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <VideoProfile Condition="SourceContainsVideo">
		            <HighH264VideoProfile
		              BFrameCount="3"
		              EntropyMode="Cabac"
		              RDOptimizationMode="Speed"
		              HadamardTransform="False"
		              SubBlockMotionSearchMode="Speed"
		              MultiReferenceMotionSearchMode="Balanced"
		              ReferenceBFrames="False"
		              AdaptiveBFrames="True"
		              SceneChangeDetector="True"
		              FastIntraDecisions="False"
		              FastInterDecisions="False"
		              SubPixelMode="Quarter"
		              SliceCount="0"
		              KeyFrameDistance="00:00:05"
		              InLoopFilter="True"
		              MEPartitionLevel="EightByEight"
		              ReferenceFrames="4"
		              SearchRange="64"
		              AutoFit="True"
		              Force16Pixels="False"
		              FrameRate="0"
		              SeparateFilesPerStream="True"
		              SmoothStreaming="False"
		              NumberOfEncoderThreads="0">
		              <Streams
		                AutoSize="False"
		                FreezeSort="False">
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="6000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="4700"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1280, 720">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="3400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="2250"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1500"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="650"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="320, 180">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		              </Streams>
		            </HighH264VideoProfile>
		          </VideoProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="PropagateSourceTimeStamps">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="6"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="2"
		              AudioCodingMode="Mode20"
		              LFEOn="False"
		              NinetyDegreePhaseShiftSurrounds="False"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="False"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile Condition="SourceContainsAudio">
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="6"
		              AudioCodingMode="Mode32"
		              LFEOn="True"
		              NinetyDegreePhaseShiftSurrounds="True"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="True"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="56"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		</Presets>

##Criando serviços de codificação comercial

Alguns clientes talvez queiram compilar um serviço de codificação comercial nos Serviços de Mídia do Azure. Se você estiver criando um 'compilação de serviço é importante que todos os parâmetros de codificação Dolby Digital Plus estejam disponíveis. Certifique-se de que todos os parâmetros dentro da marca <DolbyDigitalPlusAudioProfile> sejam expostos e configuráveis pelo usuário final. Entre em contato com prolicensingsupport@dolby.com para obter orientação sobre como disponibilizar esses parâmetros.

##Usando suporte a Dolby Professional Loudness Metering (DPLM)

O Azure Media Encoder pode usar o SDK do DPLM para medir a intensidade da caixa de diálogo no áudio de entrada e definir o valor correto de DialogNormalization. Esse recurso é habilitado somente se o áudio estiver sendo codificado como Dolby Digital Plus. O DPLM é configurado em um arquivo de predefinição de configuração usando o elemento <LoudnessMetering>, que é um filho do elemento <DolbyDigitalPlusAudioProfile>. A predefinição do exemplo a seguir mostra como configurar o DPLM:
	
	<?xml version="1.0" encoding="utf-16"?>
	<Preset
	  Version="5.0">
	  <Job />
	  <MediaFile>
	    <OutputFormat>
	      <MP4OutputFormat
	        StreamCompatibility="Standard">
	    <AudioProfile>
	             <DolbyDigitalPlusAudioProfile
	               Codec="DolbyDigitalPlus"
	               EncoderMode="DolbyDigitalPlus"
	               DolbySurroundMode="NotIndicated"
	               StereoDownmixPreference="NotIndicated"
	               SamplesPerSecond="48000"
	               AudioCodingMode="Mode20"
	               Channels="2"
	               BitsPerSample="24">
	               <LoudnessMetering
	                 Mode= "ITU_R_BS_1770_2_DI"
	                 SpeechThreshold="20"
	                 TruePeakEmphasis="false"
	                 TruePeakDCBlock="false" />
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="320"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	     </DolbyDigitalPlusAudioProfile>
	    </AudioProfile>
	      </MP4OutputFormat>
	    </OutputFormat>
	  </MediaFile>
	</Preset>

O elemento <LoudnessMetering> só pode ser especificado em um elemento <DolbyDigitalPlusAudioProfile>. Se o elemento <LoudnessMetering> é usado, o atributo DialogNormalization não deve ser usado. O codificador gera um erro se o elemento <LoudnessMetering> e o atributo DialogNormalization forem usados. Todos os atributos de LoudnessMetering são opcionais e o codificador usará como padrão os valores recomendados pela Dolby Laboratories, Inc.

Cada atributo é descrito nas seções a seguir.

###Atributo Mode

Este atributo determina o modo de medição de intensidade. Valores permitidos são:

 
**ITU\_R\_BS\_1770\_2\_DI** (padrão) — indica ITU-R BS.1770-2 mais Dialogue Intelligence

**ITU\_R\_BS\_1770\_1\_DI** — indica ITU-R BS.1770-1 mais Dialogue Intelligence

**ITU\_R\_BS\_1770\_2** — indica ITU-R BS.1770-2

**LEQA\_DI** — indica Leq(A) mais Dialogue Intelligence

**Observação**

O modo **EBU R128** pode ser alcançado com **ITU\_R\_BS\_1770\_2\_DI**

O **Leq(A)** é incluído exclusivamente por motivos de herança e só deve ser usado em fluxos de trabalho herdados específicos

O **ITU** recentemente lançou uma atualização chamada BS.1770-3, que é equivalente ao BS.1770-2 com TruePeakDCBlock e TruePeakEmphasis definidos como false

###Atributo SpeechThreshold

Especifica o limite de fala usado pelo DPLM para produzir um resultado de intensidade integrado (por exemplo, selecionando entre restrição de fala, restrição de nível e nenhuma restrição). O intervalo de configuração de limite de fala é de 0% a 100%, em incrementos de 1%. Esse parâmetro tem efeito somente quando o DPLM é configurado de modo que utilize Dialogue Intelligence, que significa que só pode ser especificado se o modo é definido como ITU\_R\_BS\_1770\_2\_DI ou ITU\_R\_BS\_1770\_1\_DI. O valor padrão, quando Mode for ITU\_R\_BS\_1770\_2\_DI ou ITU\_R\_BS\_1770\_1\_DI, é 20%. Valores para este atributo devem ser definidos no intervalo entre 0, 1 – 100.

###Atributo TruePeakDCBlock

Este parâmetro de entrada especifica se o bloco de controlador de domínio na medição de true‐peak está habilitado (true) ou desabilitado (false). Para obter mais detalhes do bloco de controlador de domínio, consulte ITU‐R BS.1770‐2. Seu valor padrão é false.

###Atributo TruePeakEmphasis

Especifica se o filtro de ênfase na medição de true‐peak está habilitado (true) ou desabilitado (false). Para obter mais detalhes sobre o filtro de ênfase, consulte ITU‐R BS.1770‐2. Seu valor padrão é false.


###Resultados do DPLM

Quando uma tarefa de codificação especifica o uso do DPLM, os resultados da medição de intensidade serão incluídos no XML de metadados na saída do ativo. A seguir está um exemplo.
	
	<LoudnessMeteringResultParameters 
	     DPLMVersionInformation="Dolby Professional Loudness Metering Development Kit Version 1.0"
	     DialogNormalization="-15" 
	     IntegratedLoudness="-14.8487606" 
	     IntegratedLoudnessGatingMethod="2" 
	     IntegratedLoudnessSpeechPercentage="11.673481" 
	     SamplePeak="-0.7028221" 
	     TruePeak="0.705999851" />

Cada atributo é descrito abaixo.

**DPLMVersionInformation**: uma cadeia de caracteres que representa a versão do SDK do DPLM usado.

**DialogNormalization**: o valor de DialNorm, em decibéis, medido a partir do áudio de entrada, que será inserido no fluxo de saída DD +, no intervalo {-31, -30,..., -1} dB.

**IntegratedLoudness**: a intensidade integrada conforme medida pelo DPLM, no intervalo de -70 a + 10 LKFS/dBFS (onde dBFS é usado somente quando o modo é definido como LEQA\_DI).

**IntegratedLoudnessGatingMethod**: os valores válidos são: 0 – None/Ungated; 1 – Speech Gated; 2 – Level Gated.

**IntegratedLoudnessSpeechPercentage**: esse resultado contém a porcentagem da linha do tempo da mídia de entrada onde a fala é detectada. Os valores variam de 0% a 100%.

**SamplePeak**: esse resultado contém o maior valor de amostra absoluto em qualquer canal desde que a medição foi redefinida, e varia de -70 a + 10 dBFS.

**TruePeak**: esse resultado contém o maior valor absoluto true‐peak em qualquer canal desde que a medição foi redefinida. Para obter uma descrição de true-peak, consulte ITU‐R BS.1770‐2. Os valores podem variar de -70 a 12.04 dBTP.
 

##Roteiros de aprendizagem dos Serviços de Mídia

Você pode exibir os roteiros de aprendizagem do AMS aqui:

- [Fluxo de trabalho do streaming ao vivo do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Fluxo de trabalho do streaming sob demanda do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

<!---HONumber=Oct15_HO3-->