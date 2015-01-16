<properties urlDisplayName="Smooth Streaming Plugin" pageTitle="Plug-in do Smooth Streaming para o Open Source Media Framework" metaKeywords="" description="Saiba como usar o plug-in Smooth Streaming dos Serviços de Mídia do Azure para o Adobe Open Source Media Framework." metaCanonical="" services="media-services" documentationCenter="" title="How to Use the Microsoft Smooth Streaming Plugin for the Adobe Open Source Media Framework" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />



# Como usar o plug-in do Microsoft Smooth Streaming para o Adobe Open Source Media Framework #

##Visão geral ##
O plug-in do Microsoft Smooth Streaming para o Open Source Media Framework 2.0 (SS para OSMF) estende os recursos padrão do OSMF e adiciona a reprodução de conteúdo do Microsoft Smooth Streaming para players do OSMF novos e existentes. O plug-in também adiciona recursos de reprodução de Smooth Streaming para Strobe Media Playback (SMP).

O SS para OSMF inclui duas versões do plug-in:

- Plug-in do Static Smooth Streaming para OSMF (.swc)

- Plug-in do Dynamic Smooth Streaming para OSMF (.swf)

Este documento pressupõe que o leitor tenha um conhecimento geral do trabalho com o OSMF e os plug-ins do OSMF. Para obter mais informações sobre o OSMF, consulte a documentação no [site oficial do OSMF](http://osmf.org/).

###Plug-in do Smooth Streaming para OSMF 2.0

O plug-in dá suporte para o carregamento e a reprodução de conteúdo de Smooth Streaming sob demanda com os seguintes recursos:

- Reprodução de Smooth Streaming sob demanda (reproduzir, pausar, buscar, parar)
- Reprodução de Smooth Streaming ao vivo (reproduzir)
- Funções de DVR ao vivo (pausar, buscar, reprodução de DVR, Go-to-Live)
- Suporte para codecs de vídeo - H.264
- Suporte para codecs de áudio - AAC
- Comutação de vários idiomas de áudio com as APIs internas do OSMF
- Seleção da qualidade de reprodução máxima com as APIs internas do OSMF
- Legendas codificadas sidecar com o plug-in de legendas do OSMF
- Adobe&reg; Flash&reg; Player 10.2 ou superior.
- Essa versão dá suporte apenas ao OSMF 2.0.

Os recursos sem suporte são os seguintes:

- Codec VC-1 e WMA
- Proteção de conteúdo (PlayReady)
- Texto e faixas esparsas
- Trickplay (câmera lenta, avanço e retrocesso)

Os problemas conhecidos são listados a seguir:

- A reprodução de conteúdo de Smooth Streaming com faixas de áudio de 48 KHz tem problemas. O tempo de execução do Flash tem um problema conhecido para renderizar conteúdo de áudio de 48 KHz. Por causa desse problema, o conteúdo do Smooth Streaming codificado com configurações de 48 Khz pode não funcionar como o esperado. Consulte: [Usando o Flash Player](http://forums.adobe.com/message/4483498#4483498) e[Adobe Flash Player 11.3 - Bug 3210964](https://bugbase.adobe.com/index.cfm?event=bug&id=3210964) para obter mais informações.
- Várias reproduções de conteúdo de Smooth Streaming em uma única página pode provocar problemas. Esse é um problema conhecido com o OSMF.
- Reprodução de vídeo em etapas pode provocar problemas e nenhum vídeo em alguns computadores. Para solucionar esse problema, você pode desabilitar a aceleração de hardware ou o vídeo em etapas.

## Carregando o plug-in
Os plug-ins do OSMF podem ser carregados estaticamente (em tempo de compilação) ou dinamicamente (em tempo de execução). O plug-in do Smooth Streaming para download do OSMF inclui versões dinâmicas e estáticas.

- Carregamento estático: para carregar estaticamente, é necessário um arquivo de biblioteca estática (SWC). Os plug-ins estáticos são adicionados como uma referência aos projetos e são mesclados no arquivo de saída final em tempo de compilação.

- Carregamento dinâmico: para carregar dinamicamente, é necessário um arquivo pré-compilado (SWF). Os plug-ins dinâmicos são carregados em tempo de execução e não são incluídos na saída do projeto. (Saída compilada) Os plug-ins dinâmicos podem ser carregados usando os protocolos HTTP e FILE.

Para obter mais informações sobre carregamento estático e dinâmico, consulte a [página oficial do plug-in do OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

###SS para carregamento dinâmico de OSMF
O trecho de código a seguir mostra como carregar o plug-in SS para OSMF estaticamente e executa um vídeo básico usando a classe MediaFactory do OSMF. Antes de incluir o SS para código de OSMF, verifique se a referência ao projeto inclui o plug-in estático "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc".

<pre><code>
package 
{
	
	import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;
	
	import flash.display.*;
	import org.osmf.media.*;
	import org.osmf.containers.MediaContainer;
	import org.osmf.events.MediaErrorEvent;
	import org.osmf.events.MediaFactoryEvent;
	import org.osmf.events.MediaPlayerStateChangeEvent;
	import org.osmf.layout.*;
	
	
	
	[SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
	public class TestPlayer extends Sprite
	{        
		public var _container:MediaContainer;
		public var _mediaFactory:DefaultMediaFactory;
		private var _mediaPlayerSprite:MediaPlayerSprite;
		

		public function TestPlayer( )
		{
			stage.quality = StageQuality.HIGH;

			initMediaPlayer();

		}
	
		private function initMediaPlayer():void
		{
		
			// Create the container (sprite) for managing display and layout
			_mediaPlayerSprite = new MediaPlayerSprite();    
			_mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
			_mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
			_mediaPlayerSprite.scaleMode = ScaleMode.NONE;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			//Adds the container to the stage
			addChild(_mediaPlayerSprite);
			
			// Create a mediafactory instance
			_mediaFactory = new DefaultMediaFactory();
			
			// Add the listeners for PLUGIN_LOADING
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
			
			// Load the plugin class 
			loadAdaptiveStreamingPlugin( );  
			
		}
		
		private function loadAdaptiveStreamingPlugin( ):void
		{
			var pluginResource:MediaResourceBase;
			
			pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
			_mediaFactory.loadPlugin( pluginResource ); 
		}
		
		private function onPluginLoaded( event:MediaFactoryEvent ):void
		{
			// The plugin is loaded successfully.
			// Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
		loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
		
		}
		
		private function onPluginLoadFailed( event:MediaFactoryEvent ):void
		{
			// The plugin is failed to load ...
		}
		
		
		private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
		{
			var state:String;
			
			state =  event.state;
			
			switch (state)
			{
				case MediaPlayerState.LOADING: 
					
					// A new source is started to load.
					
					break;
				
				case  MediaPlayerState.READY :   
					// Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
					
					break;
				
				case MediaPlayerState.BUFFERING :
					
					break;
				
				case  MediaPlayerState.PAUSED :
					break;      
				// other states ...          
			}
		}
		
		private function onPlayerFailed(event:MediaErrorEvent) : void
		{
			// Media Player is failed .           
		}
		
		private function loadMediaSource(sourceURL : String):void 
		{
			// Take an URL of SmoothStreamingSource's manifest and add it to the page.
			
			var resource:URLResource= new URLResource( sourceURL );
			
			var element:MediaElement = _mediaFactory.createMediaElement( resource );
			_mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			
			// Add the media element
			_mediaPlayerSprite.media = element;
		}     
		
	}
}
</code></pre>


###Carregamento dinâmico de SS para OSMF

O trecho de código a seguir mostra como carregar o plug-in SS para OSMF dinamicamente e reproduzir um vídeo básico usando a classe MediaFactory do OSMF. Antes de incluir o SS para código do OSMF, copie o plug-in dinâmico da "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" na pasta do projeto se desejar carregar usando protocolo FILE ou copiar em um servidor Web para carga HTTP. Não é necessário incluir "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" nas referências ao projeto.

<pre><code>
package 
{
	
	import flash.display.*;
	import org.osmf.media.*;
	import org.osmf.containers.MediaContainer;
	import org.osmf.events.MediaErrorEvent;
	import org.osmf.events.MediaFactoryEvent;
	import org.osmf.events.MediaPlayerStateChangeEvent;
	import org.osmf.layout.*;
	import flash.events.Event;
	import flash.system.Capabilities;

	
	//Sets the size of the SWF
	
	[SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
	public class TestPlayer extends Sprite
	{        
		public var _container:MediaContainer;
		public var _mediaFactory:DefaultMediaFactory;
		private var _mediaPlayerSprite:MediaPlayerSprite;
		
		
		public function TestPlayer( )
		{
			stage.quality = StageQuality.HIGH;
			initMediaPlayer();
		}
		
		private function initMediaPlayer():void
		{
			
			// Create the container (sprite) for managing display and layout
			_mediaPlayerSprite = new MediaPlayerSprite();    
			_mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
			_mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

			//Adds the container to the stage
			addChild(_mediaPlayerSprite);
			
			// Create a mediafactory instance
			_mediaFactory = new DefaultMediaFactory();
			
			// Add the listeners for PLUGIN_LOADING
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
			
			// Load the plugin class 
			loadAdaptiveStreamingPlugin( );  
			
		}
		
		private function loadAdaptiveStreamingPlugin( ):void
		{
			var pluginResource:MediaResourceBase;
			var adaptiveStreamingPluginUrl:String;

			// Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

			adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
			pluginResource = new URLResource(adaptiveStreamingPluginUrl);
			_mediaFactory.loadPlugin( pluginResource ); 

		}
		
		private function onPluginLoaded( event:MediaFactoryEvent ):void
		{
			// The plugin is loaded successfully.

			// Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

	loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
		}
		
		private function onPluginLoadFailed( event:MediaFactoryEvent ):void
		{
			// The plugin is failed to load ...
		}
		
		
		private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
		{
			var state:String;
			
			state =  event.state;
			
			switch (state)
			{
				case MediaPlayerState.LOADING: 
					
					// A new source is started to load.
					
					break;
				
				case  MediaPlayerState.READY :   
					// Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
					
					break;
				
				case MediaPlayerState.BUFFERING :
					
					break;
				
				case  MediaPlayerState.PAUSED :
					break;      
				// other states ...          
			}
		}
		
		private function onPlayerFailed(event:MediaErrorEvent) : void
		{
			// Media Player is failed .           
		}
		
		private function loadMediaSource(sourceURL : String):void 
		{
			// Take an URL of SmoothStreamingSource's manifest and add it to the page.
			
			var resource:URLResource= new URLResource( sourceURL );
			
			var element:MediaElement = _mediaFactory.createMediaElement( resource );
			_mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			// Add the media element
			_mediaPlayerSprite.media = element;
		}     
		
	}
}
</code></pre>

##Strobe Media Playback com o plug-in dinâmico do ODMF do SS
O plug-in dinâmico do Smooth Streaming para o OSMF é compatível com o [Strobe Media Playback (SMP)](http://osmf.org/strobe_mediaplayback.html). Você pode usar o SS para o plug-in OSMF para adicionar reprodução de conteúdo de Smooth Streaming ao SMP. Para fazer isso, copie o "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" em um servidor Web para carga HTTP, usando as seguintes etapas:

1.	Procure a [página de configuração do Strobe Media Playback](http://osmf.org/dev/2.0gm/setup.html). 
2.	Defina o src para uma fonte Smooth Streaming, (por exemplo http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3.	Faça as alterações desejadas na configuração e clique em Visualizar e Atualizar.
 
	**Observação** o servidor Web de conteúdo precisa de um crossdomain.xml válido. 
4.	Copie e cole o código em uma página HTML simples usando seu editor de texto preferido, como no exemplo a seguir:



		<html>
		<body>
		<object width="920" height="640"> 
		<param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
		<param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
		<param name="allowFullScreen" value="true"></param>
		<param name="allowscriptaccess" value="always"></param>
		<param name="wmode" value="direct"></param>
		<embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
    		type="application/x-shockwave-flash" 
    		allowscriptaccess="always" 
    		allowfullscreen="true" 
    		wmode="direct" 
    		width="920" 
    		height="640" 
    		flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
		</embed>
		</object>
		</body>
		</html>



5. Adicione o plug-in do OSMF do Smooth Streaming ao código de inserção e salve.

		<html>
		<object width="920" height="640"> 
		<param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
		<param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
		<param name="allowFullScreen" value="true"></param>
		<param name="allowscriptaccess" value="always"></param>
		<param name="wmode" value="direct"></param>
		<embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
    		type="application/x-shockwave-flash" 
    		allowscriptaccess="always" 
    		allowfullscreen="true" 
    		wmode="direct" 
    		width="920" 
    		height="640" 
    		flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
		</embed>
		</object>
		</html>


6. 	Salve a página HTML e publique em um servidor Web. Vá para a página da Web publicada usando seu navegador de Internet habilitado para Flash&reg; Player favorito (Internet Explorer, Chrome, Firefox etc).
7. 	Divirta-se com o conteúdo de Smooth Streaming no Adobe&reg; Flash&reg; Player.

Para obter mais informações sobre o desenvolvimento de OSMF geral, consulte a [página oficial de desenvolvimento do OSMF](http://osmf.org/resources.html).

<!--HONumber=35.1-->
