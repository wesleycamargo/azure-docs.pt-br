<properties 
	pageTitle="Usar o iOS Media Player Framework com os Serviços de Mídia do Azure" 
	description="Saiba como usar a biblioteca do Media Services iOS Media Player Framework para criar aplicativos avançados e dinâmicos." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>



# Como usar o iOS Media Player Framework dos Serviços de Mídia do Azure

Com a biblioteca do iOS Media Player Framework dos Serviços de Mídia do Azure, os desenvolvedores de iPod, iPhone e iPad podem criar facilmente aplicativos cliente dinâmicos que criam vídeo mix e fluxos de áudio juntos instantaneamente.  Por exemplo, em aplicativos que exibem conteúdo de esportes, eles podem facilmente inserir anúncios onde escolher e controlar a frequência em que esses anúncios são exibidos, mesmo quando o conteúdo principal é retrocedido.  Aplicativos educacionais podem usar a mesma funcionalidade, por exemplo, para criar conteúdo no qual as palestras principais têm itens à parte ou barras laterais, antes de retornar para o conteúdo principal.

Geralmente, é relativamente complexo compilar um aplicativo que possa criar fluxos de conteúdo que resultam de uma interação entre o aplicativo e o usuário - normalmente, você precisa criar o fluxo inteiro do zero e armazená-lo, com antecedência, no servidor.  Usando o iOS Media Player Framework, você pode criar aplicativos cliente que podem fazer tudo isso sem a necessidade de controle ou modificar o fluxo de conteúdo principal.  Você pode:

- Agendar fluxos de conteúdo com antecedência no dispositivo cliente.
- Agendar anúncios ou inserções de pre-roll.
- Agendar anúncios ou inserções de post-roll.
- Agendar anúncios ou inserções de mid-roll para criar ad pods.
- Controlar se o anúncio ou a inserção de mid-roll é reproduzido cada vez que a linha do tempo do conteúdo é retrocedida ou se ele é executado uma vez e removido da linha do tempo.
- Inserir, de forma dinâmica, conteúdo diretamente na linha do tempo como resultado de qualquer evento, caso o usuário pressione um botão ou o aplicativo receba uma notificação de um serviço (por exemplo, um programa de notícias pode enviar notificações das últimas notícias e o aplicativo pode "pausar" o conteúdo principal para carregar dinamicamente um fluxo das últimas notícias). 

Combinando esses recursos com os recursos de reprodução de mídia de dispositivos iOS é possível criar experiências de mídia muito sofisticadas em muito pouco tempo com menos recursos.

O SDK contém um aplicativo SamplePlayer que demonstra como criar um aplicativo do iOS que usa a maior parte desses recursos para criar um fluxo de conteúdo instantâneo, bem como permitir que o usuário dispare uma inserção dinamicamente pressionando um botão.  Este tutorial mostra os principais componentes do aplicativo SamplePlayer e como você pode usá-lo como ponto de partida para seu aplicativo.

## Introdução ao aplicativo Sample Player
As etapas a seguir descrevem como obter o aplicativo e fornece um tour das áreas do aplicativo que usam a estrutura. 

1. Clone o repositório git. 

    `git clone https://github.com/WindowsAzure/azure-media-player-framework`

2. Abra o projeto localizado em `azure-media-player-framework/src/iOS/HLSClient/`:  **SamplePlayer.xcodeproj**.

 
3. Esta é a estrutura do Sample Player:

![HLS Sample Code structure](http://mingfeiy.com/wp-content/uploads/2013/01/HLS-Structure.png)

4. Na pasta iPad, existem dois arquivos .xib:  **SeekbarViewController** e **SamplePlayerViewController**.  Eles criam o layout da interface do usuário do aplicativo iPad.  Da mesma forma, existem dois arquivos .xib na pasta iPhone definindo a barra de busca e o controlador. 

6. A lógica de aplicativo principal reside no **SamplePlayerViewController.m** na pasta `Shared`.  A maior parte dos trechos de código descritos abaixo está localizada nesse arquivo. 

## Compreendendo o layout da interface do usuário
Existem dois arquivos de .xib que definem nossa interface do player.  (A discussão a seguir usa o layout do iPad como exemplo; mas o layout do iPhone é muito semelhante e os princípios são os mesmos.)
### SamplePlayerViewController_iPad.xib
![Sample Player Address Bar](http://mingfeiy.com/wp-content/uploads/2013/01/addressbar.png)

* A **URL da Mídia** é a URL usada para carregar um fluxo de mídia.  O aplicativo possui uma lista pré-populada de URLs de mídia que você pode usar por meio dos botões de seleção de URL.  Como alternativa, você pode digitar sua própria URL de conteúdo HLS (Transmissão ao vivo Http).  Esse conteúdo de mídia será usado como o conteúdo principal. 
**Observação: não deixe esta URL vazia.**

* Os botões de **Seleção de URL** permitem selecionar URLs alternativas da lista de URLs de mídia.

### SeekbarViewController_iPad.xib
![Seek Bar Controller](http://mingfeiy.com/wp-content/uploads/2013/01/controller.png)
* Use o **Botão Reproduzir** para reproduzir e pausar a reprodução de mídia.

* A **Barra de busca** projeta toda a linha do tempo da reprodução.  Ao buscar, mantenha pressionado, arraste para a posição desejada e solte o botão de busca na barra de busca. 

**Observação**:  quando o visualizador busca em um anúncio, uma nova barra de busca é exibida com a duração do anúncio.  A barra de busca Principal apresenta apenas a duração do conteúdo principal (ou seja, um anúncio tem uma duração de 0 na barra de busca principal).

* O controle de **Tempo do Player** mostra dois horários (`Label:playerTime`), como 00h23/02h10.  Nesse caso, 0h23 é a hora da reprodução atual e 2h10 é a duração total da mídia. 

* **Os botões SkipFroward e SkipBackward** não funcionam como o esperado atualmente. Uma versão atualizada será lançada em breve.

* O pressionamento do **botão Agendar Agora** enquanto o conteúdo principal está reproduzindo insere um anúncio (você pode definir a URL de origem do anúncio no code-behind).  Observação:  Na versão atual, você não pode agendar um anúncio enquanto outro anúncio está sendo reproduzido. 

### Como agendar o conteúdo principal
Agendado um clipe de conteúdo de 0 a 80 segundos:

    //Schedule the main content
    MediaTime *mediaTime = [[[MediaTime alloc] init] autorelease];
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
        
    int clipId = 0;
    if (![framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

No código de exemplo anterior:

* O objeto **MediaTime** controla o clipe de vídeo que você deseja agendar como o conteúdo principal.  No exemplo anterior, o clipe de vídeo será agendado para ter uma duração de 80 segundos (de 0 a 80 segundos);
* **clipBeginMediaTime** representa a hora de início da reprodução de um vídeo.  Por exemplo, se **clipBeginMediaTime** = 5, então o clipe de vídeo será iniciado em 5 segundos do clipe de vídeo.
* **clipEndMediaTime** representa a hora de término da reprodução de um vídeo.  Se **clipEndMediaTime**= 100, a reprodução do vídeo termina no 100º segundo do clipe de vídeo.
*Em seguida, agendamos o **MediaTime** solicitando que a estrutura execute **appendContentClip**.  No exemplo anterior, a URL do conteúdo principal é fornecida em `[NSURL URLWithString:url]`, e o agendamento dessa mídia é definido usando **withMedia**.
 `[framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])`.

**Observação:** sempre agende o conteúdo principal antes de agendar qualquer anúncio (incluindo anúncio de pre-roll). 

### Variação:  se tiver dois clipes de conteúdo principais em reprodução, você também poderá agendar um segundo clipe depois do primeiro com o código a seguir:

    //Schedule second content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 30;
    mediaTime.clipEndMediaTime = 80;
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

Fazer isso seguindo o código anterior agenda dois fluxos de conteúdo na linha do tempo do conteúdo principal.  Primeiro é agendado com base em `URLWithString:url` e o segundo conteúdo é agendado com base em `URLWithString:secondContent`.  Para o segundo, o conteúdo se inicia a partir de um ponto em 30 segundos do fluxo de mídia e termina em 80 segundos dentro dele. 

## Agendamento do anúncio 
Na versão atual, apenas um anúncio **pauseTimeline = false** é suportado, o que significa que após o término de um anúncio, o player irá continuar de onde o conteúdo principal parou. 

Estes são alguns pontos importantes:
<ul><li> Todo **LinearTime.duration** precisa ser 0 ao agendar um anúncio.</li>
<li> Quando **clipEndMediaTime** for mais longo que a duração do anúncio, o anúncio terminará depois que for concluído e nenhuma exceção será gerada.  É aconselhável verificar se a duração natural do anúncio está dentro do tempo de renderização (**clipEndMediaTime**) para que você não perca uma oportunidade de anunciar.</li> 
<li> Há suporte para anúncios de pre-roll, mid-roll e post-roll.  Pre-roll pode ser agendado apenas no início de todo o conteúdo.  Por exemplo, você não pode agendar um pre-roll para o segundo conteúdo em um cenário RCE (edição de corte irregular). </li>
<li> Anúncios adesivos e de uma única reprodução têm suporte e podem ser usados em conjunto com o anúncio de pre-roll, mid-roll ou post-roll.</li>
<li> O formato do anúncio pode ser .Mp4 ou HLS.</li>
</ul>
### Como agendar anúncios de pre-roll, mid-roll e post-roll e ad pods

#### Agendando anúncios de pre-roll

    LinearTime *adLinearTime = [[[LinearTime alloc] init] autorelease];
    NSString *adURLString = @"http://smoothstreamingdemo.blob.core.windows.net/videoasset/WA-BumpShort_120530-1.mp4";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.clipBeginMediaTime = 0;
    adInfo.mediaTime.clipEndMediaTime = 5;
    adInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O objeto **AdInfo** representa todas as informações sobre o clipe do anúncio:
* **ClipURL** é a URL da origem do clipe.
* A propriedade **mediaTime** indica por quanto tempo um anúncio é reproduzido.  (**clipBeginMediaTime** é a hora de início de um anúncio e **clipEndMediaTime** define o final do anúncio.)  No código de exemplo anterior, agendamos um anúncio por cinco segundos, começando em 0 até o quinto segundo da duração do anúncio.
* O objeto **Policy** não é usado atualmente pela estrutura.
* Você deve definir o valor de **appendTo** como -1 se não for um pacote de anúncios. 
* O valor de **type** pode ser pre-roll, mid-roll, post-roll ou pacote de anúncios.  Para pre-roll ou post-roll, como não há nenhum tempo associado, especifique o tipo. 

#### Agendando anúncios de mid-roll

Se você adicionar `adLinearTime.startTime = 23;` ao código de exemplo anterior, o anúncio começará a ser reproduzido em 23 segundos na linha do tempo do conteúdo principal.

#### Agendando anúncios de post-roll

    //Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    postAdInfo.mediaTime.clipEndMediaTime = 5;
    postAdInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

A única diferença do agendamento de anúncio de pre-roll é `postAdInfo.type = AdType_Postroll;`.  O código anterior agendou um anúncio de cinco segundos como post-roll. 

#### Agendando Ad Pods
Pacotes de anúncios são uma quebra de anúncio com vários anúncios sendo reproduzidos consecutivamente.  Este é o código para o agendamento de dois anúncios em um pacote de anúncios. 

    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    adpodInfo1.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.mediaTime = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    int32_t adIndex = 0;
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
        
    NSString *adpodSt2=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-532531b8-fca4-4c15-86f6-45f9f45ec980/Windows%208_%20Sign%20in%20with%20a%20Smile.mp4?st=2012-11-28T16%3A35%3A26Z&se=2014-11-28T16%3A35%3A26Z&sr=c&si=c6ede35c-f212-4ccd-84da-805c4ebf64be&sig=zcWsj1JOHJB6TsiQL5ZbRmCSsEIsOJOcPDRvFVI0zwA%3D";
    AdInfo *adpodInfo2 = [[[AdInfo alloc] init] autorelease];
    adpodInfo2.clipURL = [NSURL URLWithString:adpodSt2];
    adpodInfo2.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo2.mediaTime.clipBeginMediaTime = 0;
    adpodInfo2.mediaTime.clipEndMediaTime = 17;
    adpodInfo2.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo2.type = AdType_Pod;
    adpodInfo2.appendTo = adIndex;
    if (![framework scheduleClip:adpodInfo2 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

Há algumas coisas a serem observadas aqui:
* Para o primeiro clipe, **appendTo** é -1.  E quando chamamos `[framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex],` `adIndex` recebe um valor único que indica o fim do primeiro clipe no ad pod.  Em seguida, para o segundo clipe no pacote de anúncios, alinhe o início do segundo anúncio com o fim do primeiro definindo **appendTo** como `adpodInfo2.appendTo = adIndex;`, o que especifica a posição final do primeiro como o local para iniciar o segundo clipe. 
* Em seguida, você deve definir o tipo como `AdType_Pod` para indicar que este é um pacote de anúncios. 

### Como agendar um anúncio de uma única reprodução ou "adesivo"
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    oneTimeInfo.deleteAfterPlay = YES;

Conforme ilustrado no código de exemplo anterior, se você definir **deleteAfterPlay** como **YES**, esse anúncio será executado apenas uma vez.  E se você definir **deleteAfterPlay** como **NO**, esse anúncio continuará a ser executado, o que chamamos de "anúncio adesivo".
### Consulte o [wiki do Media Player Framework do Azure (a página pode estar em inglês)](https://github.com/WindowsAzure/azure-media-player-framework/wiki) para obter mais informações.

<!--HONumber=45--> 
