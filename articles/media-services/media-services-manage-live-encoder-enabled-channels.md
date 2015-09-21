<properties 
	pageTitle="Trabalhando com canais habilitados a executar codificação ao vivo com os Serviços de Mídia do Azure" 
	description="Este tópico descreve como configurar um canal que recebe uma transmissão ao vivo com taxa de bits única de um codificador local e, em seguida, executa a codificação ao vivo para fluxo de taxa de bits adaptável com os Serviços de Mídia. O fluxo pode ser entregue para aplicativos de reprodução do cliente por meio de um ou mais pontos de extremidade de Streaming, usando um dos seguintes protocolos de streaming adaptáveis: HLS, Smooth Streaming, MPEG DASH, HDS." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="09/07/2015"
	ms.author="juliako"/>

#Trabalhando com canais habilitados para executar codificação ao vivo com os Serviços de Mídia do Azure (Visualização)

##Visão geral

Nos Serviços de Mídia do Azure, um **Canal** representa um pipeline para processamento de conteúdo de streaming ao vivo. Um **Canal** recebe transmissões de entrada ao vivo de uma das duas maneiras a seguir:

- Um codificador local ao vivo envia **RTMP** ou **Smooth Streaming** (MP4 fragmentado) com múltiplas taxas de bits para o Canal. Você pode usar os codificadores ao vivo a seguir, que produz Smooth Streaming com múltiplas taxas de bits: Elemental, Envivio, Cisco. Os codificadores ao vivo a seguir produzem RTMP: transcodificadores Adobe Flash Live, Telestream Wirecast e Tricaster. Os fluxos ingeridos passam pelos **Canais**sem nenhum processamento adicional. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.
- Um fluxo de taxa de bits única (em um dos seguintes formatos: **RTP** (MPEG TS)), **RTMP** ou **Smooth Streaming** (MP4 fragmentado)) é enviado para o **Canal** que está habilitado a realizar a codificação ao vivo com os Serviços de Mídia. O **Canal** então realiza a codificação ao vivo do fluxo de entrada com taxa de bits única em um fluxo de vídeo (adaptável) de múltiplas taxas de bits. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes. 

	A codificação de um fluxo ao vivo com os Serviços de Mídia está atualmente no modo **Visualização**.

A partir da versão 2.10 dos Serviços de Mídia, quando você cria um canal, você pode especificar de que modo você deseja que o canal receba o fluxo de entrada e se deseja ou não que o canal realize a codificação ao vivo do seu fluxo. Você tem duas opções:

- **Nenhum** – especifique esse valor, se você planeja usar um codificador ao vivo local, que emitirá um fluxo de múltiplas taxas de bits. Nesse caso, o fluxo de entrada foi transmitido para a saída sem qualquer codificação. Esse é o comportamento de um canal em versão anterior à 2.10. Para obter mais informações sobre como trabalhar com canais desse tipo, consulte [Trabalhando com canais que recebem fluxo ao vivo de múltiplas taxas de bits de codificadores locais](media-services-manage-channels-overview.md).

- **Padrão** (Visualização) – escolha esse valor se você planeja usar os Serviços de Mídia para codificar seu fluxo ao vivo de taxa de bits única para fluxo de múltiplas taxas de bits.

	A codificação de um fluxo ao vivo com os Serviços de Mídia está atualmente no modo Visualização.

>[AZURE.NOTE]Este tópico discute os atributos de canais que estão habilitados para executar codificação ao vivo (tipo de codificação **Padrão**). Para obter informações sobre como trabalhar com canais não habilitados a realizar codificação ao vivo, consulte [Trabalhando com canais que recebem fluxo ao vivo de múltiplas taxas de bits de codificadores locais](media-services-manage-channels-overview.md).

O diagrama a seguir representa um fluxo de trabalho de streaming ao vivo em que um canal recebe um fluxo de taxa de bits única em um dos seguintes protocolos: RTMP, Smooth Streaming ou RTP (MPEG TS); em seguida, ele codifica o fluxo em um fluxo de múltiplas taxas de bits.

![Fluxo de trabalho ao vivo][live-overview]

>[AZURE.NOTE]Nem todos os data centers oferecem suporte à Codificação ao Vivo com os Serviços de Mídia do Azure.
>
>Se você estiver usando o Portal de gerenciamento do Azure para criar canais, terá duas opções de codificação tipo disponíveis de canal: **Nenhum** e **Padrão**. Se você vir somente a opção **Nenhum**, isso significa seu data center não oferece suporte à Codificação ao Vivo com o AMS.
>
>Se você estiver usando o SDK .NET ou API REST, faça o seguinte para verificar:
>
>1. Tente criar um Canal com o tipo de codificação definido como padrão. 
>2. Se o resultado retornado for HTTP Error Code 412 (Falha na pré-condição) com a seguinte mensagem: *"Codificação ao vivo não tem suporte nesta região; EncodingType deve ser definido como 'Nenhum'."*, seu data center não oferece suporte para Codificação ao Vivo.


##Neste tópico

- Visão geral de um [Cenário comum de streaming ao vivo](media-services-manage-live-encoder-enabled-channels.md#scenario)
- [Descrição de um Canal e seus componentes relacionados](media-services-manage-live-encoder-enabled-channels.md#channel)
- [Considerações](media-services-manage-live-encoder-enabled-channels.md#considerations)
- [Tarefas relacionadas ao streaming ao vivo](media-services-manage-live-encoder-enabled-channels.md#tasks)

##<a id="scenario"></a>Cenário comum de streaming ao vivo

A seguir, as etapas gerais envolvidas na criação de aplicativos comuns de streaming ao vivo.

1. Conecte uma câmera de vídeo a um computador. Inicie e configure um codificador ao vivo local que possa produzir um fluxo de taxa de bits **única** em um dos seguintes protocolos: RTMP, Smooth Streaming ou RTP (MPEG-TS). Para obter mais informações, consulte [Suporte RTMP dos Serviços de Mídia do Azure e Codificadores ao Vivo](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Essa etapa também pode ser realizada após a criação do canal.

1. Crie e inicie um Canal.

1. Recupere a URL de ingestão do canal.

	A URL de ingestão é usada pelo codificador ao vivo para enviar o fluxo para o canal.
1. Recupere a URL de visualização do canal. 

	Use essa URL para verificar se o canal está recebendo corretamente o fluxo ao vivo.

3. Crie um programa.

	Ao usar o Portal de Gerenciamento do Azure, criar um programa também cria um ativo.

	Ao usar o SDK do .NET ou REST, você precisa criar um ativo e especificar o uso desse ativo durante a criação de um programa. 
1. Publique o ativo associado ao programa.   

	Certifique-se de ter pelo menos uma unidade reservada para streaming no ponto de extremidade de streaming por meio do qual você deseja transmitir o conteúdo.
1. Inicie o programa quando estiver pronto para iniciar o streaming e o arquivamento.
2. Opcionalmente, o codificador ao vivo pode ser sinalizado para iniciar um anúncio. O anúncio é inserido no fluxo de saída.
1. Interrompa o programa sempre que você deseja parar o streaming e o arquivamento do evento.
1. Exclua o programa (e, opcionalmente, exclua o ativo).   

A seção [tarefas de streaming ao vivo](media-services-manage-channels-overview.md#tasks) conduz a tópicos que demonstram como realizar as tarefas descritas acima.


##<a id="channel"></a>Configurações de entrada (ingestão) do canal

###<a id="Ingest_Protocols"></a>Protocolo de streaming de ingestão

Se o **Tipo de codificador** está definido como **Padrão**, as opções válidas são:

- **RTP** (MPEG-TS): fluxo de transporte de MPEG-2 por RTP.  
- **RTMP** de taxa de bits única
- **MP4 fragmentado** de taxa de bits única (Smooth Streaming)

Para obter mais informações, consulte [Suporte RTMP dos Serviços de Mídia do Azure e Codificadores ao Vivo](http://go.microsoft.com/fwlink/?LinkId=532824).

####RTP (MPEG TS) - fluxo de transporte de MPEG-2 por RTP.  

Caso de uso típico:

Difusores profissionais geralmente trabalham com codificadores ao vivo locais de alto nível de fornecedores como Elemental Technologies, Ericsson, Ateme, Imagine ou Envivio para enviar um fluxo. Geralmente usado em conjunto com o departamento de TI e redes privadas.

Considerações:

- O uso de um fluxo de transporte de programa único (SPTS) de entrada é altamente recomendável. Há suporte, no entanto, para uso de faixas de áudio em múltiplos idiomas
- O fluxo de vídeo deve ter uma taxa de bits média abaixo de 15 Mbps
- A taxa de bits média agregada dos fluxos de áudio deve estar abaixo de 1 Mbps
- A seguir, a lista dos codecs com suporte:
	- Vídeo MPEG-2 / H.262 
		
		- Perfil Principal (4:2:0)
		- Perfil Alto (4:2:0, 4:2:2)
		- Perfil 422 (4:2:0, 4:2:2)

	- Vídeo MPEG-4 AVC / H.264
	
		- Linha de base, Principal, Perfil Alto (8 bits 4:2:0)
		- Perfil Alto 10 (10 bits 4:2:0)
		- Perfil Alto 422 (10 bits 4:2:2)


	- Áudio MPEG-2 AAC-LC
	
		- Mono, Estéreo, Surround (5.1, 7.1)
		- Empacotamento de ADTS estilo MPEG-2

	- Áudio Dolby Digital (AC-3)

		- Mono, Estéreo, Surround (5.1, 7.1)

	- Áudio MPEG (camada II e III)
			
		- Mono, estéreo

- Os codificadores para difusão recomendados incluem:
	- Ateme AM2102
	- Ericsson AVP2000
	- eVertz 3480
	- Ericsson RX8200
	- Imagine Communications Selenio ENC 1
	- Imagine Communications Selenio ENC 2
	- AdTec EN-30
	- AdTec EN-91P
	- AdTec EN-100
	- Harmonic ProStream 1000
	- Thor H-2 4HD-EM
	- eVertz 7880 SLKE
	- Cisco Spinnaker
	- Elemental Live

####<a id="single_bitrate_RTMP"></a>RTMP de taxa de bits única

Considerações:

- O fluxo de entrada não pode conter vídeo com múltiplas taxas de bits
- O fluxo de vídeo deve ter uma taxa de bits média abaixo de 15 Mbps
- O fluxo de áudio deve ter uma taxa de bits média abaixo de 1 Mbps
- A seguir, a lista dos codecs com suporte:

	- Vídeo MPEG-4 AVC / H.264  
	
		- Linha de base, Principal, Perfil Alto (8 bits 4:2:0)
		- Perfil Alto 10 (10 bits 4:2:0)
		- Perfil Alto 422 (10 bits 4:2:2)

	- Áudio MPEG-2 AAC-LC

		- Mono, Estéreo, Surround (5.1, 7.1)
		- Taxa de amostragem de 44,1 kHz
		- Empacotamento de ADTS estilo MPEG-2
	
- Os codificadores recomendados incluem:

	- Telestream Wirecast
	- Flash Media Live Encoder
	- Tricaster

####MP4 fragmentado de taxa de bits única (Smooth Streaming)

Caso de uso típico:

Use codificadores ao vivo locais de fornecedores como Elemental Technologies, Ericsson, Ateme e Envivio para enviar o fluxo de entrada pela Internet aberta para um data center próximo do Azure.

Considerações:

O mesmo que o mencionado para [RTMP com taxa de bits única](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

####Outras considerações

- Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se você precisar de protocolos diferentes, você deve criar canais separados para cada protocolo de entrada. 
- A resolução máxima para o fluxo de entrada de vídeo é 1920x1080, e no máximo 60 campos/segundo se entrelaçado ou 30 quadros por segundo se progressivo.


###URLs de ingestão (pontos de extremidade) 

Um canal fornece um ponto de extremidade de entrada (URL de ingestão) que você especifica no codificador ao vivo, de modo que o codificador possa enviar fluxos por push para seus canais.

Você pode obter as URLs de ingestão depois de criar um canal. Para obter essas URLs, o canal não precisa estar no estado **Executando**. Quando estiver pronto para começar a enviar dados por push para o canal, ele deve estar no estado **Executando**. Depois que o canal inicia a ingestão de dados, você pode visualizar o fluxo por meio da URL de visualização.

Você tem a opção de ingerir fluxo ao vivo (Smooth Streaming) de MP4 fragmentado por uma conexão SSL. Para inserir por SSL, certifique-se de atualizar a URL de inserção para HTTPS.

###Endereços IP permitidos

Você pode definir os endereços IP que têm permissão para publicar vídeo para esse canal. Os endereços IP permitidos podem ser especificados como um endereço IP individual (por exemplo, '10.0.0.1'), um intervalo de IPs usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ‘10.0.0.1/22’), ou um intervalo de IPs usando um endereço IP e uma máscara de sub-rede decimal com pontos (por exemplo, ‘10.0.0.1(255.255.252.0)’).

Se nenhum endereço IP for especificado e não houver definição de regra, nenhum endereço IP será permitido. Para permitir qualquer endereço IP, crie uma regra e defina 0.0.0.0/0.


##Visualização de canal 

###URLs de visualização

Os canais fornecem um ponto de extremidade de visualização (URL de visualização) que você usa para visualizar e validar seu fluxo antes do processamento e da entrega.

Você pode obter a URL de visualização quando você cria o canal. Para obter a URL, o canal não precisa estar no estado **Executando**.

Depois que o canal inicia a ingestão de dados, você pode visualizar o fluxo.

**Observação** atualmente o fluxo de visualização só pode ser entregue em formato MP4 fragmentado (Smooth Streaming), independentemente do tipo de entrada especificado. Você pode usar o player [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) para testar o Smooth Stream. Você também pode usar um player hospedado no Portal de Gerenciamento do Azure para exibir o fluxo.

###Endereços IP permitidos

Você pode definir os endereços IP que têm permissão para conectar-se ao ponto de extremidade de visualização. Se nenhum endereço IP for especificado, qualquer endereço IP será permitido. Os endereços IP permitidos podem ser especificados como um endereço IP individual (por exemplo, '10.0.0.1'), um intervalo de IPs usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ‘10.0.0.1/22’), ou um intervalo de IPs usando um endereço IP e uma máscara de sub-rede decimal com pontos (por exemplo, ‘10.0.0.1(255.255.252.0)’).

##Configurações de codificação ao vivo

Esta seção descreve como as configurações para o codificador ao vivo no canal podem ser ajustadas, quando o **Tipo de codificação** de um canal é definido como **Padrão**.

###Origem do marcador de anúncio

Você pode especificar a origem para sinais de marcadores de anúncio. O valor padrão é **Api**, que indica que o codificador ao vivo no canal deve escutar buscando um **API do marcador de anúncio** assíncrono.

A outra opção válida é **Scte35** (permitida apenas se o protocolo de transmissão de ingestão está definido como RTP (MPEG-TS). Quando Scte35 for especificado, o codificador ao vivo analisará sinais SCTE-35 do fluxo de entrada RTP (MPEG-TS).

###Legendas CEA 708

Um sinalizador opcional que informa o codificador ao vivo para ignorar quaisquer dados de legendas CEA 708 incorporados no vídeo de entrada. Quando o sinalizador é definido como false (padrão), o codificador vai detectar e inserir novamente os dados CEA 708 nos fluxos de vídeo de saída.

###Transmissão de vídeo

Opcional. Descreve o fluxo de vídeo de entrada. Se esse campo não for especificado, o valor padrão é usado. Essa configuração só será permitida se o protocolo de transmissão de entrada for definida como RTP (MPEG-TS).

####Índice

Um índice baseado em zero que especifica qual fluxo de vídeo de entrada deve ser processado pelo codificador ao vivo no canal. Essa configuração se aplica apenas se o protocolo de streaming de ingestão é RTP (MPEG-TS).

O valor padrão é zero. É recomendável para enviar um fluxo de transporte de programa único (SPTS). Se o fluxo de entrada contém vários programas, o codificador ao vivo analisa a PMT (tabela de mapa de programa) na entrada, identifica as entradas que têm um nome de tipo de fluxo de vídeo MPEG-2 ou H.264 e organiza-os na ordem especificada no PMT. O índice baseado em zero, em seguida, é usado para acompanhar a enésima entrada nesse arranjo.

###Fluxo de áudio

Opcional. Descreve os fluxos de áudio de entrada. Se esse campo não for especificado, os valores padrão são usados. Essa configuração só será permitida se o protocolo de transmissão de entrada for definida como RTP (MPEG-TS).

####Índice

É recomendável para enviar um fluxo de transporte de programa único (SPTS). Se o fluxo de entrada contém vários programas, o codificador ao vivo no canal analisa a PMT (tabela de mapa de programa) na entrada, identifica as entradas que têm um nome de tipo de fluxo de MPEG-2 AAC ADTS ou AC-3 System-A ou AC-3 System-B ou MPEG-2 Private PES ou áudio MPEG-1 ou áudio MPEG-2, e organiza-os na ordem especificada na PMT. O índice baseado em zero, em seguida, é usado para acompanhar a enésima entrada nesse arranjo.

####Linguagem

O identificador de idioma do fluxo de áudio, em conformidade com ISO 639-2, como ENG. Se não estiver presente, o padrão é UND (indefinido).

Pode haver até 8 conjuntos de fluxo de áudio especificados se a entrada para o canal for MPEG-2 TS por RTP. No entanto, não pode haver nenhum par de entradas com o mesmo valor de Índice.

###<a id="preset"></a>Predefinição do sistema

Especifica a predefinição a ser usada pelo codificador ao vivo dentro deste canal. Atualmente, o único valor permitido é **Default720p** (padrão).

**Default720p** codificará o vídeo nas 7 camadas a seguir.


####Fluxo de vídeo de saída

Taxa de bits|Largura|Altura|MáxFPS|Perfil|Nome do fluxo de saída
---|---|---|---|---|---
3500|1280|720|30|Alto|Video\_1280x720\_30fps\_3500kbps
2200|960|540|30|Principal|Video\_960x540\_30fps\_2200kbps
1350|704|396|30|Principal|Video\_704x396\_30fps\_1350kbps
850|512|288|30|Principal|Video\_512x288\_30fps\_850kbps
550|384|216|30|Principal|Video\_384x216\_30fps\_550kbps
350|340|192|30|Linha de base|Video\_340x192\_30fps\_350kbps
200|340|192|30|Linha de base|Video\_340x192\_30fps\_200kbps


####Fluxo de áudio de saída

O áudio é codificado como estéreo AAC-LC a 64 kbps, com taxa de amostragem de 44,1 kHz.

##Sinalização de anúncios

Quando o canal está com codificação ao vivo habilitada, você tem um componente em seu pipeline de processamento de vídeo e pode manipulá-lo. Você pode sinalizar para o canal inserir slates e/ou anúncios no fluxo de saída de taxa de bits adaptável. Slates ainda são imagens que você pode usar para cobrir a transmissão de entrada ao vivo em certos casos (por exemplo, durante um intervalo comercial). Sinais de publicidade, são sinais sincronizados que você insere no fluxo de saída para informar o player de vídeo para realizar ações especiais – por exemplo, para alternar para um anúncio no momento apropriado. Consulte este [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) para uma visão geral do mecanismo de sinalização SCTE-35 usado para essa finalidade. Abaixo está um cenário típico que você poderia implementar em seu evento ao vivo.

1. Faça com que seus visualizadores obtenham uma imagem PRÉ-EVENTO antes do início do evento.
1. Faça com que seus visualizadores obtenham uma imagem PÓS-EVENTO após o término do evento.
1. Faça com que seus visualizadores obtenham uma imagem do EVENTO-DE-ERRO se houver um problema durante o evento (por exemplo, falta de energia no Estádio).
1. Envie uma imagem de INTERVALO-COMERCIAL para ocultar a transmissão do evento ao vivo durante um intervalo comercial.

A seguir estão as propriedades que você pode definir ao sinalizar anúncios.

###Duração

A duração, em segundos, do intervalo comercial. Isso deve ser um valor positivo diferente de zero para iniciar o intervalo comercial. Quando um intervalo comercial está em andamento e a duração é definida como zero com o CueId correspondente ao intervalo comercial em curso, esse intervalo é cancelado.

###CueId

Uma ID exclusiva para o intervalo comercial, para ser usado pelo aplicativo downstream para executar as ações apropriadas. Deve ser um número inteiro positivo. Você pode definir esse valor como qualquer inteiro positivo aleatório ou usar um sistema de upstream para acompanhar as IDs de indicação. Certifique-se normalizar quaisquer IDs para números inteiros positivos antes de enviá-las pela API.

###Mostrar slate

Opcional. Sinaliza o codificador ao vivo para alternar para a imagem [fixa padrão](media-services-manage-live-encoder-enabled-channels.md#default_slate) durante um intervalo comercial e ocultar a transmissão de vídeo de entrada. O áudio também é desligado durante o slate. O padrão é **false**.
 
A imagem usada será aquela especificada por meio da propriedade de ID do ativo de slate padrão no momento da criação do canal. O slate será estendido para ajustar-se ao tamanho da imagem de exibição.


##Inserir imagens Slate

O codificador ao vivo no canal pode ser sinalizado para alternar para uma imagem slate. Ele também pode ser sinalizado para encerrar um slate em curso.

O codificador ao vivo pode ser configurado para alternar para uma imagem slate e ocultar o sinal de vídeo de entrada em determinadas situações – por exemplo, durante um intervalo comercial. Se um slate desse tipo não estiver configurado, o vídeo de entrada não é mascarado durante esse intervalo comercial.

###Duração

A duração do slate em segundos. Isso deve ser um valor positivo diferente de zero para iniciar o slate. Se houver um slate em andamento e uma duração de zero for especificada, esse slate será encerrado.

###Inserir o slate no marcador de anúncio

Quando definida como true, essa configuração configura o codificador ao vivo para inserir uma imagem slate durante um intervalo comercial. O valor padrão é true.

###<a id="default_slate"></a>ID de ativo de imagem fixa padrão

Opcional. Especifica a ID do ativo de Serviços de Mídia que contém a imagem do slate. O padrão é nulo.

**Observação**: antes de criar o canal, a imagem fixa com as restrições a seguir deve ser carregada como um ativo dedicado (nenhum outro arquivo deve estar nesse ativo).

- No máximo 1920x1080 na resolução.
- No máximo 3 megabytes de tamanho.
- O nome do arquivo deve ter uma extensão *.jpg.
- A imagem deve ser carregada em um ativo como o único AssetFile que ativo e esse AssetFile deve ser marcado como o arquivo primário. Esse ativo não pode ser armazenado criptografado.

Se a **ID padrão do ativo do slate** não for especificada, e **Inserir slate no marcador de anúncio** for definido como **true**, uma imagem padrão dos Serviços de Mídia do Azure será usada para ocultar o fluxo de entrada de vídeo. O áudio também é desligado durante o slate.


##Programas do canal

Um canal é associado a programas que permitem que você controle a publicação e o armazenamento de segmentos em um fluxo ao vivo. Canais gerenciam programas. A relação entre canal e programa é muito semelhante à mídia tradicional, onde um canal tem um fluxo constante de conteúdo e um programa tem como escopo algum evento programado naquele Canal.

Você pode especificar o número de horas pelo qual você deseja manter o conteúdo gravado para o programa, definindo a duração da **Janela de Arquivo**. Esse valor pode ser definido entre um mínimo de 5 minutos e um máximo de 25 horas. A duração da janela de arquivo também determina que a quantidade máxima de tempo que os clientes podem pesquisar na posição atual em tempo real. Os programas podem ser executados pelo período de tempo especificado, mas o conteúdo que estiver por trás da janela de tamanho será continuamente descartado. Esse valor desta propriedade também determina por quanto tempo os manifestos do cliente podem crescer.

Cada programa está associado um ativo que armazena o conteúdo transmitido. Um ativo é mapeado para um contêiner de blob na conta de Armazenamento do Azure e os arquivos no ativo são armazenados como blobs nesse contêiner. Para publicar o programa para que seus clientes possam exibir o fluxo, você deve criar um localizador OnDemand para o ativo associado. Ter esse localizador permitirá que você crie uma URL de transmissão que você pode fornecer aos seus clientes.

Um Canal dá suporte a até três programas em execução simultânea, para que você possa criar diversos arquivos no mesmo fluxo de entrada. Isso permite que você publique e arquive diferentes partes de um evento, conforme necessário. Por exemplo, o requisito de negócios é arquivar 6 horas de um programa, mas transmitir apenas os últimos 10 minutos. Para fazer isso, você precisa criar dois programas em execução simultânea. Um programa é definido para arquivar 6 horas do evento, mas o programa não é publicado. Outro programa é definido para 10 minutos e esse programa é publicado.

Você não deve reutilizar os programas existentes para novos eventos. Em vez disso, crie e inicie um novo programa para cada evento, conforme descrito na seção Programação de aplicativos de streaming ao vivo.

Inicie o programa quando estiver pronto para iniciar o streaming e o arquivamento. Interrompa o programa sempre que você deseja parar o streaming e o arquivamento do evento.

Para excluir o conteúdo arquivado, interrompa e exclua o programa e, em seguida, exclua o ativo associado. Não é possível excluir um ativo se este for usado por um programa; o programa deve ser excluído primeiro.

Mesmo depois que você parar e excluir o programa, os usuários poderão transmitir seu conteúdo arquivado como vídeo por demanda enquanto você não excluir o ativo.

Se desejar manter o conteúdo arquivado mas ele não está disponível para streaming, exclua o localizador de streaming.


##Obtenção uma visualização em miniatura de uma transmissão ao vivo

Quando a codificação ao vivo está habilitada, agora você pode obter uma visualização da transmissão ao vivo quando ela atinge o canal. Isso pode ser uma ferramenta valiosa para verificar se sua transmissão ao vivo está realmente chegando ao canal.

##<a id="states"></a>Estados de canal e como os estados são mapeados para o modo de cobrança 

O estado atual de um canal. Os valores possíveis incluem:

- **Parado**. Este é o estado inicial do canal após sua criação. Nesse estado, as propriedades do canal podem ser atualizadas, mas streaming não é permitido.
- **Iniciando**. O canal está sendo iniciado. Nenhuma atualização ou streaming é permitido durante esse estado. Se ocorrer um erro, o canal retorna para o estado Parado.
- **Executando**. O canal é capaz de processar transmissões ao vivo.
- **Parando**. O canal está sendo parado. Nenhuma atualização ou streaming é permitido durante esse estado.
- **Excluindo**. O canal está sendo excluído. Nenhuma atualização ou streaming é permitido durante esse estado.

A tabela a seguir mostra como os estados de canal são mapeados para o modo de cobrança.
 
Estado de canal|Indicadores da interface do usuário do portal|Cobrado?
---|---|---
Iniciando|Iniciando|Nenhum (estado transitório)
Executando|Pronto (nenhum programa em execução)<br/>ou<br/>Streaming (pelo menos um programa em execução)|Sim
Parando|Parando|Nenhum (estado transitório)
Parada|Parada|Não


>[AZURE.NOTE]Atualmente na visualização, a inicialização do canal pode levar até mais de 20 minutos. A redefinição de canal pode levar até 5 minutos.


##<a id="Considerations"></a>Considerações

- Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se você precisar de protocolos diferentes, você deve criar canais separados para cada protocolo de entrada. 
- Sempre que você reconfigurar o codificador ao vivo, chame o método **Redefinir** no canal. Antes de redefinir o canal, você precisa interromper o programa. Antes de redefinir o canal, reinicie o programa. 
- Um canal pode ser interrompido somente quando estiver no estado Executando e todos os programas no canal tiverem sido interrompidos.
- Por padrão, você pode adicionar somente 5 canais à sua conta de Serviços de Mídia. Essa é uma cota flexível em todas as novas contas. Para obter mais informações, consulte [Cotas e limitações](media-services-quotas-and-limitations.md).
- Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se você precisar de protocolos diferentes, você deve criar canais separados para cada protocolo de entrada.
- Você será cobrado apenas quando o canal estiver no estado **Executando**. Para obter mais informações, consulte [esta](media-services-manage-live-encoder-enabled-channels.md#states) seção.

##Problemas conhecidos

- A inicialização do canal pode levar mais de 20 minutos.
- O suporte RTP é fornecido na para difusores profissionais. Leia as notas de RTP [neste](http://azure.microsoft.com/blog/2015/04/13/an-introduction-to-live-encoding-with-azure-media-services/) blog.
- As imagens fixas devem estar de acordo com as restrições descritas [aqui](media-services-manage-live-encoder-enabled-channels.md#default_slate). Se você tentar criar um Canal com um slate padrão que seja maior que 1920 x 1080, a solicitação será um erro.

###Como criar canais que realizam codificação ativas, de um fluxo com taxa de bits única para fluxo com taxa de bits adaptável 

Escolha **Portal**, **.NET** e **API REST** para saber como criar e gerenciar canais e programas.

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)


##Roteiros de aprendizagem dos Serviços de Mídia

Você pode exibir os roteiros de aprendizagem do AMS aqui:

- [Fluxo de trabalho do streaming ao vivo do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Fluxo de trabalho do streaming sob demanda do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Tópicos relacionados

[Trabalhando com Eventos de Live Streaming com os Serviços de Mídia do Azure](media-services-live-streaming-workflow.md)

[Conceitos de Serviços de Mídia](media-services-concepts.md)

[Especificação de ingestão dinâmica de MP4 fragmentado dos Serviços de Mídia do Azure](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png
 

<!---HONumber=Sept15_HO2-->