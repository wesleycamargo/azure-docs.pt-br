<properties 
	pageTitle="Inserindo um vídeo de streaming adaptável MPEG-DASH em um aplicativo HTML5 com DASH.js" 
	description="Este tópico demonstra como inserir um vídeo de streaming adaptável MPEG-DASH em um aplicativo HTML5 com DASH.js." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/07/2015" 
	ms.author="juliako"/>


#Inserindo um vídeo de streaming adaptável MPEG-DASH em um aplicativo HTML5 com DASH.js

##Visão geral

O MPEG-DASH é um padrão ISO para streaming adaptável de conteúdo de vídeo, que oferece benefícios significativos para quem deseja fornecer saída de streaming de vídeo adaptável de alta qualidade. Com o MPEG-DASH, o fluxo de vídeo cairá automaticamente para uma definição inferior quando a rede ficar congestionada. Dessa maneira, é menor a probabilidade de o vídeo ser pausado enquanto o player baixa os próximos segundos a serem reproduzidos (também conhecido como buffer). À medida que o congestionamento de rede é reduzido, o player de vídeo retorna para um fluxo de qualidade mais alta. Essa capacidade de adaptação da largura de banda necessária também resulta em um tempo de início mais rápido do vídeo. Isso significa que os primeiros segundos podem ser reproduzidos em um segmento de qualidade inferior para rápido download e, em seguida, o vídeo passará a ter uma qualidade mais alta assim que conteúdo suficiente tiver sido armazenado em buffer.

O dash.js é um player de vídeo MPEG-DASH de software livre escrito em JavaScript. Seu objetivo é fornecer um player robusto de plataforma cruzada que pode ser reutilizado livremente em aplicativos que exigem reprodução de vídeo. Ele fornece reprodução de MPEG-DASH em qualquer navegador que ofereça suporte ao MSE (Media Source Extensions) da W3C, que hoje são o Chrome e o IE11 (outros navegadores demonstraram intenção de oferecer suporte ao MSE). Para obter mais informações sobre o DASH.js, consulte o repositório do dash.js do GitHub.


##Criando um player de vídeo de streaming com base no navegador

Para criar uma página da Web simples que exiba um player de vídeo com os controles esperados, como reproduzir, pausar, retroceder, etc., você precisará:

1. Criar uma página HTML
1. Adicionar a marca de vídeo
1. Adicionar o player dash.js
1. Inicializar o player
1. Adicionar estilo CSS
1. Exibir os resultados em um navegador que implemente o MSE

A inicialização do player pode ser concluída em algumas linhas de código do JavaScript. Usando o dash.js, é realmente simples inserir vídeo MPEG-DASH em seus aplicativos baseados em navegador.


##Criando uma página HTML

A primeira etapa é criar uma página padrão HTML contendo o <video> elemento, salvar esse arquivo como basicPlayer.html, como ilustrado no exemplo a seguir:
	
	<!DOCTYPE html>
	<html>
	  <head><title>Adaptive Streaming in HTML5</title></head>
	  <body>
	    <h1>Adaptive Streaming with HTML5</h1>
	    <video id="videoplayer" controls></video>
	  </body>
	</html>

##Adicionando o player DASH.js

Para adicionar a implementação de referência do dash.js ao aplicativo, você precisará obter o arquivo dash.all.js da versão 1.0 do projeto dash.js. O arquivo deve ser salvo na pasta JavaScript do seu aplicativo. Esse arquivo é um arquivo de conveniência que reúne todo o código dash.js necessário em um único arquivo. Ao explorar o repositório do dash.js, você encontrará os arquivos individuais, o código de teste e muito mais, mas se tudo o que você quer é usar o dash.js, o arquivo dash.all.js é do que você precisa.

Para adicionar o player dash.js aos seus aplicativos, adicione uma marca de script à seção de cabeçalho do basicPlayer.html:

	<!-- DASH-AVC/265 reference implementation -->
	< script src="js/dash.all.js"></script>


Em seguida, crie uma função para inicializar o player quando a página for carregada. Adicione o seguinte script após a linha na qual você carregou o dash.all.js:

	<script>
	// setup the video element and attach it to the Dash player
	function setupVideo() {
	  var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
	  var context = new Dash.di.DashContext();
	  var player = new MediaPlayer(context);
	                  player.startup();
	                  player.attachView(document.querySelector("#videoplayer"));
	                  player.attachSource(url);
	}
	</script>

Primeiramente, essa função cria um DashContext. Ele é usado para configurar o aplicativo para um ambiente de tempo de execução específico. De um ponto de vista técnico, ele define as classes que a estrutura de injeção de dependência deve usar ao construir o aplicativo. Na maioria dos casos, você usará o Dash.di.DashContext.

Em seguida, instancie a classe primária da estrutura dash.js, o MediaPlayer. Essa classe contém os principais métodos necessários, como reproduzir e pausar, gerencia a relação com o elemento de vídeo e também gerencia a interpretação do arquivo MPD (Media Presentation Description) que descreve o vídeo a ser reproduzido.

A função startup() da classe MediaPlayer é chamada para garantir que o player esteja pronto para reproduzir o vídeo. Entre outras coisas, essa função garante que todas as classes necessárias (conforme definido pelo contexto) tenham sido carregadas. Assim que o vídeo estiver pronto, você poderá anexar o elemento de vídeo a ele usando a função attachView(). Isso permite que o MediaPlayer injete o fluxo de vídeo no elemento e também controle a reprodução conforme a necessidade.

Passe a URL do arquivo MPD para o MediaPlayer de modo que este saiba sobre o vídeo que vai reproduzir. A função setupVideo() que acabou de ser criada precisará ser executada assim que a página tiver sido completamente carregada. Faça isso usando o evento onload do elemento body. Altere o elemento <body> para:

	<body onload="setupVideo()">

Por fim, defina o tamanho do elemento de vídeo usando CSS. Em um ambiente de streaming adaptável, isso é importante principalmente porque o tamanho do vídeo que está sendo reproduzido pode mudar conforme a reprodução se adapta às condições em constante mudança da rede. Nesta simples demonstração, basta forçar o elemento de vídeo a ser 80% da janela do navegador disponível adicionando o seguinte CSS à seção de cabeçalho da página:
	
	<style>
	video {
	  width: 80%;
	  height: 80%;
	}
	</style>

##Reproduzindo um vídeo

Para reproduzir um vídeo, aponte o navegador no arquivo basicPlayback.html e clique em Reproduzir no player de vídeo exibido.

##Consulte também

[Desenvolver aplicativos de player de vídeo](media-services-develop-video-players.md)

[Repositório do dash.js do GitHub](https://github.com/Dash-Industry-Forum/dash.js)

<!---HONumber=July15_HO4-->