<properties 
	pageTitle="Conceitos de serviços de mídia do Azure" 
	description="Este tópico oferece uma visão geral dos conceitos de serviços de mídia do Azure" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/11/2016" 
	ms.author="juliako"/>

#Conceitos de serviços de mídia do Azure 

Este tópico fornece uma visão geral dos conceitos mais importantes dos Serviços de Mídia.

##<a id="assets"></a>Ativos e armazenamento

###Ativos

Um [Ativo](https://msdn.microsoft.com/library/azure/hh974277.aspx) contém arquivos digitais (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda oculta) e os metadados sobre esses arquivos. Depois que os arquivos digitais são carregados em um ativo, eles podem ser usados nos fluxos de trabalho de codificação e streaming dos serviços de mídia.

Um ativo é mapeado para um contêiner de blob na conta de armazenamento do Azure e os arquivos no ativo são armazenados como blobs nesse contêiner.

Ao decidir qual conteúdo de mídia para carregar e armazenar em um ativo, as seguintes considerações se aplicam:

- Um ativo deve conter apenas uma instância única e exclusiva do conteúdo de mídia. Por exemplo, uma única edição de um episódio de TV, filme ou anúncio.
- Um ativo não deve conter representações ou edições múltiplas de um arquivo audiovisual. Um exemplo de uso inapropriado de um Ativo é tentar armazenar mais de um episódio de TV, anúncio ou vários ângulos de câmera de uma única produção dentro do ativo. Armazenar representações ou edições diversas de um arquivo audiovisual em um ativo pode resultar em dificuldades ao enviar trabalhos de codificação, transmitir e assegurar a entrega do ativo posteriormente no fluxo de trabalho.  

###Arquivo de ativo 
Um [AssetFile](https://msdn.microsoft.com/library/azure/hh974275.aspx) representa um arquivo de áudio ou vídeo real que é armazenado em um contêiner de blob. Um arquivo de ativo está sempre associado a um ativo, e um ativo pode conter um ou vários arquivos. A tarefa do Codificador dos serviços de mídia falha se um objeto de arquivo de ativo não estiver associado um arquivo digital em um contêiner de blob.

A instância de **AssetFile** e o arquivo de mídia real são dois objetos diferentes. A instância de AssetFile contém metadados sobre o arquivo de mídia, enquanto o arquivo de mídia contém o conteúdo de mídia real.

Você não deve tentar alterar o conteúdo de contêineres de blob que foram gerados pelos serviços de mídia sem o uso de APIs de serviços de mídia.

###Opções de criptografia de ativos

Dependendo do tipo de conteúdo que você deseja carregar, armazenar e fornecer, o Serviços de Mídia fornece várias opções de criptografia dentre as quais você pode escolher.

**None** - nenhuma criptografia é usada. Esse é o valor padrão. Observe que ao usar essa opção, seu conteúdo não é protegido quando está em trânsito ou em repouso no armazenamento.

Se você pretende enviar um MP4 usando o download progressivo, use essa opção para carregar seu conteúdo.

**StorageEncrypted** - use essa opção para criptografar seu conteúdo limpo localmente usando a criptografia AES de 256 bits e, em seguida, carregue-o para o armazenamento do Azure onde ele é armazenado, criptografado em rest. Ativos protegidos pela criptografia de armazenamento são descriptografados automaticamente e posicionados em um sistema de arquivos criptografado antes da codificação, então opcionalmente criptografados novamente antes do carregamento como um novo ativo de saída. O caso de uso primário para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte em repouso no disco.

Para entregar um ativo de armazenamento criptografado, você deve configurar a política de entrega do ativo para que o Serviços de Mídia saiba como você deseja distribuir seu conteúdo. Antes que seu ativo possa ser transmitido, o servidor de streaming remove a criptografia de armazenamento e transmite o conteúdo usando a política de entrega especificada (por exemplo, AES, PlayReady ou sem criptografia).

**CommonEncryptionProtected** - use esta opção se você desejar criptografar conteúdo (ou carregar conteúdo que já foi criptografado) com criptografia comum ou DRM PlayReady (por exemplo, Smooth Streaming protegido com DRM PlayReady).

**EnvelopeEncryptionProtected** – Use esta opção se você deseja proteger conteúdo (ou carregar conteúdo já protegido) HLS (HTTP Live Streaming) criptografado com AES. Observe que se você estiver carregando um HLS já criptografado com AES, ele deve ter sido criptografado pelo Transform Manager.

###Política de acesso 

Uma [AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx) define permissões (como leitura, gravação e lista) e a duração do acesso a um ativo. Normalmente, você passaria um objeto AccessPolicy para um localizador que deve ser usado para acessar os arquivos contidos em um ativo.


###Contêiner de blob

Um contêiner de blob fornece um agrupamento de um conjunto de blobs. Contêineres de blob são usados nos serviços de mídia como ponto limite para controle de acesso e localizadores de assinatura de acesso compartilhado (SAS) em ativos. Uma conta de armazenamento do Azure pode conter um número ilimitado de contêineres de blob. Um contêiner pode armazenar um número ilimitado de blobs.

>[AZURE.NOTE]Você não deve tentar alterar o conteúdo de contêineres de blob que foram gerados pelos serviços de mídia sem o uso de APIs de serviços de mídia.

###<a id="locators"></a>Localizadores

Os [localizadores](https://msdn.microsoft.com/library/azure/hh974308.aspx) fornecem um ponto de entrada para acessar os arquivos contidos em um ativo. Uma política de acesso é usada para definir as permissões e a duração pela qual um cliente tem acesso a um determinado ativo. Os localizadores podem ter de uma a muitas relações com uma política de acesso, de modo que diferentes localizadores podem fornecer diferentes horas de início e tipos de conexão para diferentes clientes, ao mesmo tempo usando a mesma permissão e as mesmas configurações de duração; no entanto, devido a uma restrição de política de acesso compartilhado definida pelos serviços de armazenamento do Azure, você não pode ter mais do que cinco localizadores exclusivos associados a um determinado ativo ao mesmo tempo.

Os Serviços de Mídia suportam dois tipos de localizadores: localizadores OnDemandOrigin, usados para transmitir mídia (por exemplo, MPEG DASH, HLS ou Smooth Streaming) ou baixar, de maneira progressiva, mídia; e localizadores de URL SAS, usados para carregar ou baixar arquivos de mídia para\\do armazenamento do Azure.

Observe que a permissão de lista (AccessPermissions.List) não deve ser usada ao criar um localizador OrDemandOrigin.

###Conta de armazenamento

Todo o acesso ao Armazenamento do Azure ocorre por meio de uma conta de armazenamento. Uma conta do Serviço de Mídia pode ser associada a uma ou mais contas de armazenamento. Uma conta pode conter um número ilimitado de contêineres, desde que seu tamanho total esteja abaixo de 500 TB por conta de armazenamento. Os Serviços de Mídia fornecem ferramentas de nível de SDK que permitem a você gerenciar várias contas de armazenamento e balancear a carga da distribuição de seus ativos durante o carregamento nessas contas com base em métricas ou na distribuição aleatória. Para saber mais, consulte Trabalhando com [Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx).

##Trabalhos e tarefas

Um [trabalho](https://msdn.microsoft.com/library/azure/hh974289.aspx) normalmente é usado para processar (por exemplo, indexar ou codificar) uma apresentação de áudio/vídeo. Se você estiver processando vários vídeos, crie um trabalho para cada vídeo a ser codificado.

Um trabalho contém metadados sobre o processamento a ser realizado. Cada Trabalho contém uma ou mais [tarefas](https://msdn.microsoft.com/library/azure/hh974286.aspx) que especificam uma tarefa de processamento atômica, seus ativos de entrada e ativos de saída, um processador de mídia e suas configurações associadas. Tarefas em um trabalho podem ser encadeadas, em que o ativo de saída de uma tarefa é determinado como o ativo de entrada para a próxima tarefa. Dessa forma, um trabalho pode conter todo o processamento necessário para uma apresentação de mídia.

##<a id="encoding"></a>Codificação 

Os Serviços de Mídia do Azure fornecem várias opções para a codificação de mídia na nuvem.

Ao começar a usar os Serviços de Mídia é importante compreender a diferença entre codecs e formatos de arquivo. Codecs são o software que implementa os algoritmos de compactação/descompactação. Já os formatos de arquivo são contêineres que armazenam o vídeo compactado.

Os Serviços de Mídia fornecem empacotamento dinâmico, que permite a você distribuir o conteúdo de taxa de bits adaptável MP4 ou Smooth Streaming codificado em formatos de streaming suportados pelo Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) sem a necessidade de empacotar novamente nesses formatos de fluxo contínuo.

Para aproveitar os benefícios do [empacotamento dinâmico](media-services-dynamic-packaging-overview.md), você precisa fazer o seguinte:

- Codificar seu arquivo mezanino (fonte) em um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos Smooth Streaming de taxa de bits adaptável (as etapas de codificação são demonstradas mais tarde neste tutorial).
- Obter pelo menos uma unidade de streaming sob demanda para o ponto de extremidade de streaming por meio do qual você planeja fornecer seu conteúdo. Para saber mais, consulte [Como dimensionar unidades reservadas para streaming sob demanda](media-services-manage-origins.md#scale_streaming_endpoints/).

Os Serviços de Mídia são compatíveis com os seguintes codificadores sob demanda descritos neste artigo:

- [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
- [Fluxo de trabalho do Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Para saber mais sobre codificadores com suporte, consulte [Codificadores](media-services-encode-asset.md)


##Transmissão ao vivo

Nos Serviços de Mídia do Azure, um Canal representa um pipeline para processamento de conteúdo de streaming ao vivo. Um Canal recebe transmissões de entrada ao vivo de uma das duas maneiras a seguir:

- Um codificador local ao vivo envia RTMP ou Smooth Streaming (MP4 fragmentado) com múltiplas taxas de bits para o Canal. Você pode usar os codificadores ao vivo a seguir, que produz Smooth Streaming com múltiplas taxas de bits: Elemental, Envivio, Cisco. Os codificadores ao vivo a seguir produzem RTMP: transcodificadores Adobe Flash Live, Telestream Wirecast e Tricaster. Os fluxos ingeridos passam por Canais sem processamento adicional. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.

- Um fluxo de taxa de bits única (em um dos seguintes formatos: RTP (MPEG TS)), RTMP ou Smooth Streaming (MP4 fragmentado)) é enviado para o Canal que está habilitado a realizar a codificação ativa com os Serviços de Mídia. O Canal então realiza a codificação ao vivo do fluxo de entrada com taxa de bits única em um fluxo de vídeo (adaptável) de múltiplas taxas de bits. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.

###Canal

Nos Serviços de Mídia, [Canais](https://msdn.microsoft.com/library/azure/dn783458.aspx) são responsáveis pelo processamento de conteúdo de transmissão ao vivo. Um Canal fornece um ponto de extremidade de entrada (URL de ingestão) que você então fornece a um transcodificador ao vivo. O canal recebe transmissões ao vivo de entrada do transcodificador ao vivo e o torna disponível para streaming por meio de um ou mais StreamingEndpoints. Canais também fornecem um ponto de extremidade de visualização prévia (URL de visualização prévia) que você usa para visualizar e validar seu fluxo antes de processamento e da entrega.

Você pode obter a URL de ingestão e a URL de visualização prévia quando você cria o canal. Para obter essas URLs, o canal não precisa estar no estado iniciado. Quando estiver pronto para começar a enviar dados de um transcodificador ao vivo para o canal, o canal deve ser iniciado. Depois que o transcodificador ao vivo inicia a ingestão de dados, você pode visualizar o fluxo.

Cada conta dos Serviços de Mídia pode conter vários canais, vários programas e vários StreamingEndpoints. Dependendo das necessidades de largura de banda e segurança, serviços de StreamingEndpoint podem ser dedicados a um ou mais canais. Qualquer StreamingEndpoint pode executar pull de qualquer canal.


###Programa 

Um [Programa](https://msdn.microsoft.com/library/azure/dn783463.aspx) permite que você controle a publicação e o armazenamento de segmentos em um fluxo ao vivo. Os canais gerenciam os programas. A relação entre canal e programa é muito semelhante à mídia tradicional, onde um canal tem um fluxo constante de conteúdo e um programa tem como escopo algum evento programado naquele canal. Você pode especificar o número de horas pelo qual deseja manter o conteúdo gravado para o programa, definindo a propriedade **ArchiveWindowLength**. Esse valor pode ser definido entre o mínimo de 5 minutos e o máximo de 25 horas.

ArchiveWindowLength também determina que a quantidade máxima de clientes de tempo pode buscar de volta no tempo a partir da posição atual em tempo real. Programas podem ser executados sobre o período de tempo especificado, mas o conteúdo que sair do comprimento da janela será continuamente descartado. Esse valor desta propriedade também determina por quanto tempo os manifestos do cliente podem crescer.

Cada programa está associado um Ativo. Para publicar o programa, você deve criar um localizador para o ativo associado. Ter esse localizador permitirá que você compile um URL de transmissão que você poderá fornecer aos seus clientes.

Um canal dá suporte a até três programas em execução simultânea, para que você possa criar diversos arquivos no mesmo fluxo de entrada. Isso permite que você publique e arquive diferentes partes de um evento, conforme necessário. Por exemplo, o requisito de negócios é arquivar 6 horas de um programa, mas transmitir apenas os últimos 10 minutos. Para fazer isso, você precisa criar dois programas em execução simultânea. Um programa é definido para arquivar 6 horas do evento, mas o programa não é publicado. Outro programa é definido para 10 minutos e esse programa é publicado.


Para obter mais informações, confira:

- [Trabalhando com canais habilitados a executar codificação ao vivo com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md)
- [Trabalhando com Canais que recebam transmissão ao vivo de múltiplas taxas de bits de codificadores locais](media-services-live-streaming-with-onprem-encoders.md)
- [Cotas e limitações](media-services-quotas-and-limitations.md).  

##Proteção de conteúdo

###Criptografia dinâmica

Os Serviços de Mídia do Azure permitem proteger a mídia desde o momento em que ela deixa computador e durante o armazenamento, processamento e entrega. Os Serviços de Mídia permitem distribuir o conteúdo criptografado dinamicamente com criptografia AES (usando chaves de criptografia de 128 bits) e CENC (criptografia comum) usando PlayReady e/ou Widevine DRM. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças do PlayReady aos clientes autorizados.

Observe que, no momento, você pode criptografar o seguintes formatos de streaming: HLS, MPEG DASH, e Smooth Streaming. Você não pode criptografar o formato de streaming HDSou fazer o download progressivo.

Se você desejar que os Serviços de Mídia criptografem um ativo, você precisa associar uma chave de criptografia (CommonEncryption ou EnvelopeEncryption) ao ativo e também configurar políticas de autorização para a chave.

Para transmitir por streaming de um ativo de armazenamento criptografado, você deve configurar a política de entrega do ativo para especificar como você deseja distribuir seu ativo.

Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando criptografia de envelope (com AES) ou a criptografia comum (com PlayReady ou Widevine). Para descriptografar o fluxo, o player solicitará a chave do serviço de distribuição de chaves. Para decidir se o usuário está autorizado para obter a chave ou não, o serviço avalia as políticas de autorização que você especificou para a chave.


###Restrição de token

A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização: aberta, restrição de token ou restrição de IP. A política restrita do token deve ser acompanhada por um token emitido por um Secure Token Service (STS). Os serviços de mídia oferecem suporte a tokens no formato Simple Web Tokens (SWT) e no formato JSON Web Token (JWT). Os serviços de mídia não fornecem Secure Token Services. Você pode criar um STS personalizado ou usar o Microsoft Azure ACS para emitir tokens. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. O serviço de distribuição de chaves dos Serviços de Mídia retornará a chave de criptografia para o cliente se o token for válido e as declarações no token corresponderem àquelas configuradas para a chave (ou licença).

Ao configurar a política restrita do token, você deve especificar os parâmetros da chave de verificação primária, emissor e audiência. A chave de verificação primária contém a chave que o token foi assinado, o emissor é o serviço de token seguro que emite o token. A audiência (às vezes chamada de escopo) descreve a intenção do token ou o recurso que o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

Para obter mais informações, consulte os seguintes artigos:

[Visão geral da proteção de conteúdo](media-services-content-protection-overview.md) [Proteger com o AES-128](media-services-protect-with-aes128.md) [Proteger com DRM](media-services-protect-with-drm.md)

##Fornecimento

###<a id="dynamic_packaging"></a>Empacotamento dinâmico

Quando trabalhar com os Serviços de Mídia, é recomendado codificar seus arquivos de mezanino em uma conjunto de MP4 de taxa de bits adaptável e, em seguida, converter o conjunto para o formato desejado usando o [Empacotamento Dinâmico](media-services-dynamic-packaging-overview.md).


###Ponto de extremidade de streaming

Um StreamingEndpoint representa um serviço de streaming que pode fornecer conteúdo diretamente a um aplicativo de player de cliente ou para uma CDN (Rede de Distribuição de Conteúdo) para a distribuição (os Serviços de Mídia do Azure agora fornecem a integração com o Azure CDN). O fluxo de saída do serviço StreamingEndpoint pode ser um fluxo ao vivo ou um ativo de vídeo por demanda na sua conta dos Serviços de Mídia. Além disso, você pode controlar a capacidade do serviço StreamingEndpoint para lidar com crescentes necessidades de largura de banda ajustando as unidades de dimensionamento (também conhecido como unidades de streaming). É recomendável alocar uma ou mais unidades de escala para aplicativos no ambiente de produção. As unidades de dimensionamento fornecem capacidade de egresso dedicada que pode ser comprada em incrementos de 200 Mbps e funcionalidade adicional que, atualmente, inclui recursos de empacotamento dinâmico.

É recomendável usar empacotamento dinâmico e/ou criptografia dinâmica. Para usar esses recursos, você deve ter pelo menos uma unidade de streaming para o ponto de extremidade do qual planeja transmitir. Para saber mais, consulte [Dimensionando as unidades de streaming](media-services-manage-origins.md#scale_streaming_endpoints).

Por padrão, você pode ter até dois pontos de extremidade de streaming em sua conta dos Serviços de Mídia. Para solicitar um limite superior, consulte [Cotas e limitações](media-services-quotas-and-limitations.md).

Você será cobrado apenas quando seu StreamingEndpoint estiver em estado de execução.

###Política de fornecimento de ativos

Uma das etapas do fluxo de trabalho de fornecimento de conteúdo de Serviços de Mídia é a configuração de [políticas de entrega de ativos](https://msdn.microsoft.com/library/azure/dn799055.aspx) que você deseja transmitir. A política de entrega de ativos informa aos serviços de mídia como você deseja que o ativo seja entregue: em que protocolo de fluxo seu ativo deve ser dinamicamente empacotado (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos), se você deseja criptografar dinamicamente seu ativo ou não e como (criptografia de envelope ou comum).

Se você tiver um ativo de armazenamento criptografado, antes que possa ser transmitido seu ativo, o servidor de streaming remove a criptografia de armazenamento e transmite o conteúdo usando a política de entrega especificada. Por exemplo, para entregar o ativo criptografado com chave de criptografia AES (criptografia avançada padrão), defina o tipo de política para DynamicEnvelopeEncryption. Para remover a criptografia de armazenamento e transmitir o ativo claro, defina o tipo de política como NoDynamicEncryption.

###Download progressivo 

O download progressivo permite iniciar a reprodução da mídia antes do arquivo inteiro ter sido baixado. Você só pode baixar apenas progressivamente um arquivo MP4.

Observe que é necessário descriptografar ativos criptografados se você deseja que estejam disponíveis para download progressivo.

Para fornecer aos usuários URLs de download progressivo, você deve primeiro criar um localizador OnDemandOrigin. Criar o localizador oferece a você o caminho base para o ativo. Em seguida, é necessário acrescentar o nome do arquivo MP4. Por exemplo:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

###URLs de streaming

Transmitindo seu conteúdo para clientes. Para fornecer aos usuários URLs de streaming, você deve primeiro criar um localizador OnDemandOrigin. Criar o localizador oferece a você o caminho base para o ativo que contém o conteúdo que você deseja transmitir. No entanto, para poder transmitir este conteúdo você precisa modificar esse caminho ainda mais. Para construir uma URL completa para o arquivo de manifesto de streaming, você deve concatenar o valor do caminho do localizador e o nome de arquivo de manifesto (ISM). Em seguida, anexe um formato apropriado (se necessário) ao caminho do localizador.

Você também pode transmitir seu conteúdo por uma conexão SSL. Para fazer isso, certifique-se de que suas URLs de streaming começam com HTTPS.

Observe que você só pode transmitir por SSL se o ponto de extremidade de streaming por meio do qual você pode distribuir o conteúdo tiver sido criado depois de 10 de setembro de 2014. Se suas URLs de streaming baseiam-se nos pontos de extremidade de streaming após 10 de setembro, a URL contém "streaming.mediaservices.windows.net" (o novo formato). URLs de streaming que contêm "origin.mediaservices.windows.net" (o formato antigo) não dão suporte a SSL. Se sua URL está no formato antigo e você deseja ser capaz de transmitir por SSL, crie um novo ponto de extremidade de streaming. Use URLs criadas com base no novo ponto de extremidade de streaming para transmitir seu conteúdo por SSL.

A lista a seguir descreve os diferentes formatos de streaming e fornece exemplos:

- Smooth Streaming

	{nome do ponto de extremidade de streaming - nome de conta do dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arqui}.ism/Manifest
		
		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest


- MPEG DASH

	{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=mpd-time-csf)
 
		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)



- Apple HTTP Live Streaming (HLS) V4

	{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=m3u8-aapl)

		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)



- Apple HTTP Live Streaming (HLS) V3

	{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=m3u8-aapl-v3)

		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

- HDS (apenas para licenciados do Adobe PrimeTime/Access)

	{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=f4m-f4f)

		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f) 

 
##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0518_2016-->