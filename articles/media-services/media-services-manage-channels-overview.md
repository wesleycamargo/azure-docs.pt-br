<properties 
	pageTitle="Usando codificadores locais para enviar uma transmissão ao vivo com múltiplas taxas de bits a um canal" 
	description="Este tópico descreve como configurar um canal que recebe uma transmissão ao vivo com múltiplas taxas de bits de um codificador local. O fluxo pode então ser entregue a aplicativos de reprodução do cliente por meio de um ou mais pontos de extremidade de streaming, usando um dos seguintes protocolos de streaming adaptáveis: HLS, Smooth Stream, MPEG DASH, HDS." 
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
	ms.date="04/06/2015" 
	ms.author="juliako"/>

#Usando codificadores locais para enviar uma transmissão ao vivo com múltiplas taxas de bits a um canal

##Visão geral

No AMS (Serviços de Mídia do Azure), um **Canal** representa um pipeline para o processamento de conteúdo de transmissão ao vivo. Um **Programa** permite que você controle a publicação e o armazenamento de segmentos em um fluxo ao vivo. Os canais gerenciam os programas. A relação entre canal e programa é muito semelhante à mídia tradicional, em que um canal tem um fluxo constante de conteúdo e um programa tem como escopo algum evento programado naquele canal. 

Ao se trabalhar com a transmissão ao vivo, um dos componentes envolvidos no fluxo de trabalho é um codificador de vídeo ao vivo que converte os sinais da câmera em fluxos que são enviados a um serviço de transmissão ao vivo. Um **Canal** pode receber fluxos de entrada ao vivo de um codificador ao vivo local que produz um fluxo RTMP com múltiplas taxas de bits ou MP4 fragmentado (Smooth Streaming). Você pode usar os seguintes codificadores ao vivo que geram saída em Smooth Streaming: Elemental, Envivio, Cisco.  Os seguintes codificadores ao vivo geram saída em RTMP: Transcodificadores Adobe Flash Live, Telestream Wirecast e Tricaster. Os fluxos ingeridos passam por **Canais** sem processamento adicional. O codificador ao vivo também pode ingerir um fluxo de taxa de bits única, mas como o fluxo não é processado, os aplicativos de cliente também receberão um fluxo de taxa de bits única (essa opção não é recomendada). Depois que o conteúdo recebido é publicado, ele pode ser transmitido para aplicativos de reprodução do cliente por meio de um ou mais **pontos de extremidade de streaming**. Os seguintes protocolos de streaming adaptáveis podem ser usados para reproduzir o fluxo: HLS, MPEG DASH, Smooth Stream, HDS. 

Um **Ponto de Extremidade de Streaming** representa um serviço de streaming que pode entregar conteúdo diretamente a um aplicativo de player do cliente ou a uma CDN (Rede de Entrega de Conteúdo) para a distribuição posterior. O fluxo de saída de um serviço de ponto de extremidade de streaming pode ser uma transmissão ao vivo ou um ativo de vídeo sob demanda em sua conta dos Serviços de Mídia. Além disso, você pode controlar a capacidade do serviço de ponto de extremidade de streaming para lidar com necessidades crescentes de largura de banda ajustando as unidades reservadas de streaming. Você deve alocar pelo menos uma unidade reservada para aplicativos em um ambiente de produção. Para obter mais informações, consulte [Como dimensionar um serviço de mídia](media-services-manage-origins.md#scale_streaming_endpoints).

O diagrama a seguir representa um fluxo de trabalho de transmissão ao vivo que usa um codificador ao vivo local para gerar fluxos RTMP com múltiplas taxas de bits ou MP4 fragmentado (Smooth Streaming). 

![Fluxo de trabalho ao vivo][live-overview]

Este tópico fornece uma visão geral de um canal que recebe uma transmissão ao vivo com múltiplas taxas de bits de um codificador local. O tópico também aborda os componentes relacionados aos canais.

##<a id="scenarios"></a>Um cenário comum de transmissão ao vivo

As etapas a seguir descrevem as tarefas envolvidas na criação de aplicativos comuns de transmissão ao vivo.

1. Conecte uma câmera de vídeo a um computador. Inicie e configure um codificador ao vivo local que gere um fluxo RTMP com múltiplas taxas de bits ou MP4 fragmentado (Smooth Streaming). Para obter mais informações, consulte [Suporte a RTMP dos Serviços de Mídia do Azure e codificadores ao vivo](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Essa etapa também pode ser realizada após a criação do canal.

1. Criar e iniciar um canal.
1. Recupere a URL de ingestão do canal. 

	A URL de ingestão é usada pelo codificador ao vivo para enviar o fluxo ao canal.
1. Recupere a URL de visualização do canal. 

	Use essa URL para verificar se o canal está recebendo corretamente a transmissão ao vivo.

2. Crie um ativo.
3. Para que o ativo seja criptografado dinamicamente durante a reprodução, faça o seguinte: 	
	
	1. 	Crie uma chave de conteúdo. 
	1. 	Configure a política de autorização da chave de conteúdo.
1. Configure a política de entrega de ativos (usada pelo empacotamento dinâmico e pela criptografia dinâmica).
3. Crie um programa e especifique o uso do ativo que você criou.
1. Publique o ativo associado ao programa criando um localizador OnDemand.  

	Verifique se você tem pelo menos uma unidade reservada de streaming no ponto de extremidade de streaming do qual você deseja transmitir conteúdo.
1. Inicie o programa quando estiver pronto para iniciar a transmissão e o arquivamento.
1. Interrompa o programa sempre que você deseja parar o streaming e o arquivamento do evento.
1. Exclua o programa (e, opcionalmente, exclua o ativo).   

A seção de [tarefas de transmissão ao vivo](media-services-manage-channels-overview.md#tasks) fornece links para tópicos que demonstram como realizar as tarefas descritas acima.


##<a id="channel_input"></a>Configurações de entrada de canal (ingestão)

###<a id="ingest_protocols"></a>Protocolo de streaming de ingestão

Os Serviços de Mídia dão suporte à ingestão de feeds ao vivo usando o seguinte protocolo de transmissão: 

- **MP4 fragmentado com múltiplas taxas de bits**
 
- **RTMP com múltiplas taxas de bits** 

	Quando o protocolo de transmissão de ingestão **RTMP** é selecionado, dois pontos de extremidade de ingestão (entrada) são criados para o canal: 
	
	**URL principal**: especifica a URL totalmente qualificada do ponto de extremidade de ingestão de RTMP principal do canal.

	**URL secundária**(opcional): especifica a URL totalmente qualificada do ponto de extremidade de ingestão de RTMP secundária do canal. 

	Use a URL secundária para melhorar a durabilidade e a tolerância a falhas do fluxo de ingestão, bem como o failover e a tolerância a falhas do codificador. 
	
	- Para melhorar a durabilidade e a tolerância a falhas da ingestão:
		
		Use um codificador para enviar os mesmos dados às URLs de ingestão primária e secundária. A maioria dos codificadores de RTMP (por exemplo, o Flash Media Encoder ou Wirecast) tem a capacidade de usar URLs principais e secundárias.

	- Para lidar com o failover e a tolerância a falhas do codificador:
		
		Use vários codificadores para gerar os mesmos dados e enviá-los às URLs de ingestão principal e secundária. Essa abordagem melhora a durabilidade da ingestão e a alta disponibilidade do codificador. OBSERVAÇÃO: o codificador deve dar suporte ao cenário de alta disponibilidade e também precisa ter sincronização de tempo interna (para obter detalhes, consulte o manual do codificador).
	
 	O uso de uma URL de ingestão secundária requer largura de banda adicional. 

Observe que você pode incluir uma única taxa de bits em seu canal, mas como o fluxo não é processado pelo canal, os aplicativos de cliente também receberão um fluxo de taxa de bits única (essa opção não é recomendada).

Para obter informações sobre codificadores ao vivo de RTMP, consulte [Suporte a RTMP dos Serviços de Mídia do Azure e codificadores ao vivo](http://go.microsoft.com/fwlink/?LinkId=532824).

As seguintes considerações se aplicam:

- Verifique se que você tem conectividade com a Internet livre suficiente para enviar dados aos pontos de ingestão. 
- O fluxo com múltiplas taxas de bits de entrada pode ter no máximo 10 níveis de qualidade de vídeo (também conhecidos como camadas) e no máximo cinco faixas de áudio.
- A taxa de bits média mais alta para qualquer um dos níveis ou camadas de qualidade de vídeo deve estar abaixo de 10 Mbps.
- A agregação das taxas de bits médias para todos os fluxos de vídeo e áudio deve estar abaixo de 25 Mbps.
- Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se precisar de protocolos diferentes, você deverá criar canais separados para cada protocolo de entrada. 


###URLs de ingestão (pontos de extremidade) 

Um canal fornece um ponto de extremidade de entrada (URL de ingestão) que você especifica no codificador ao vivo, assim, o codificador pode enviar fluxos a seus canais.   

Você pode obter as URLs de ingestão ao criar o canal. Para obter essas URLs, o canal não precisa estar no estado iniciado. Quando você estiver pronto para começar a enviar dados ao o canal, o canal deverá ser iniciado. Depois que o canal iniciar a ingestão de dados, você poderá visualizar o fluxo por meio da URL de visualização.

Você tem a opção de ingerir a transmissão ao vivo de MP4 fragmentado (Smooth Streaming) em uma conexão SSL. Para inserir por SSL, certifique-se de atualizar a URL de inserção para HTTPS. No momento, você não pode ingerir RTMP sobre SSL. 

###<a id="keyframe_interval"></a>Intervalo de quadro-chave

Ao se usar um codificador ao vivo local para gerar um fluxo com múltiplas taxas de bits, o intervalo de quadro-chave especifica a duração de GOP (conforme usado pelo codificador externo). Depois que esse fluxo de entrada for recebido pelo canal, você poderá entregar sua transmissão ao vivo aos aplicativos de reprodução de cliente em qualquer um dos seguintes formatos: Smooth Streaming, DASH e HLS. Ao fazer streaming ao vivo, o HLS é sempre empacotado dinamicamente. Por padrão, os Serviços de Mídia calculam automaticamente a taxa de empacotamento de segmento HLS (fragmentos por segmento) com base no intervalo de quadros-chave, também conhecido como GOP (grupo de imagens), que é recebido do codificador ao vivo. 

A tabela a seguir mostra como a duração do segmento é calculada:

<table border="1">
<tr><th>Intervalo de quadro-chave</th><th>Taxa de empacotamento de segmento HLS (FragmentsPerSegment)</th><th>Exemplo</th></tr>
<tr><td>menor ou igual a 3 segundos</td><td>3:1</td><td>Se KeyFrameInterval (ou GOP) for de 2 segundos, a taxa de empacotamento padrão de segmento HLS será de 3 para 1, o que criará um segmento HLS de 6 segundos.</td></tr>
<tr><td>3 a 5 segundos</td><td>2:1</td><td>Se KeyFrameInterval (ou GOP) for de 4 segundos, a taxa de empacotamento padrão de segmento HLS será de 2 para 1, o que criará um segmento HLS de 8 segundos.</td></tr>
<tr><td>mais de 5 segundos</td><td>1:1</td><td>Se KeyFrameInterval (ou GOP) for de 6 segundos, a taxa de empacotamento padrão de segmento HLS será de 1 para 1, o que criará um segmento HLS de 6 segundos.</td></tr>
</table>

Você pode alterar a taxa de fragmentos por segmento configurando a saída do canal e definindo FragmentsPerSegment em ChannelOutputHls. 

Você também pode alterar o valor do intervalo de quadro-chave definindo a propriedade KeyFrameInterval em ChanneInput. 

Se você definir explicitamente o KeyFrameInterval, o FragmentsPerSegment da taxa de empacotamento de segmento HLS será calculado usando as regras descritas acima.  

Se você definir explicitamente KeyFrameInterval e FragmentsPerSegment, os Serviços de Mídia usarão os valores definidos por você. 


###Endereços IP permitidos

Você pode definir os endereços IP que têm permissão para publicar vídeo nesse canal. Os endereços IP permitidos podem ser especificados como um único endereço IP (por exemplo, '10.0.0.1'), um intervalo de IP usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, '10.0.0.1/22') ou um intervalo de IP usando um endereço IP e uma máscara de sub-rede em notação decimal com ponto (por exemplo, '10.0.0.1(255.255.252.0)'). 

Se nenhum endereço IP for especificado e não houver definição de regra, nenhum endereço IP será permitido. Para permitir qualquer endereço IP, crie uma regra e defina 0.0.0.0/0.

##Visualização de canal 

###URLs de visualização

Os canais também fornecem um ponto de extremidade de visualização (URL de visualização) que você usa para visualizar e validar seu fluxo antes do processamento e da entrega.

Você pode obter a URL de visualização ao criar o canal. Para obter a URL, o canal não precisa estar no estado iniciado. 

Depois que o canal inicia a ingestão de dados, você pode visualizar o fluxo.

Observe que, no momento, o fluxo de visualização só pode ser entregue no formato MP4 fragmentado (Smooth Streaming), independentemente do tipo de entrada especificado. Você pode usar o player [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) para testar o Smooth Stream. Você também pode usar um player hospedado no Portal de Gerenciamento do Azure para exibir o fluxo.


###Endereços IP permitidos

Você pode definir os endereços IP que têm permissão para se conectar ao ponto de extremidade de visualização. Se nenhum endereço IP for especificado, qualquer endereço IP será permitido. Os endereços IP permitidos podem ser especificados como um único endereço IP (por exemplo, '10.0.0.1'), um intervalo de IP usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, '10.0.0.1/22') ou um intervalo de IP usando um endereço IP e uma máscara de sub-rede em notação decimal com ponto (por exemplo, '10.0.0.1(255.255.252.0)').

##Saída do canal

Para obter mais informações, consulte o [intervalo de quadro-chave de configuração](#keyframe_interval) .


##Programas do canal

Um canal é associado a programas que o habilitam a controlar a publicação e o armazenamento de segmentos em um fluxo ao vivo. Os canais gerenciam os programas. A relação entre canal e programa é muito semelhante à mídia tradicional, onde um canal tem um fluxo constante de conteúdo e um programa tem como escopo algum evento programado naquele canal.

Você pode especificar o número de horas para manter o conteúdo gravado para o programa definindo a propriedade de **Janela de Arquivo Morto**. Esse valor pode ser definido entre o mínimo de 5 minutos e o máximo de 25 horas. A janela de arquivo morto também determina o tempo máximo pelo qual os clientes podem buscar de volta no tempo a partir da posição ativa atual. Programas podem ser executados sobre o período de tempo especificado, mas o conteúdo que sair do comprimento da janela será continuamente descartado. O valor dessa propriedade também determina por quanto tempo os manifestos do cliente podem crescer.

Cada programa está associado um Ativo. Para publicar o programa, você deve criar um localizador OnDemand para o ativo associado. Ter esse localizador permitirá que você compile um URL de transmissão que você poderá fornecer aos seus clientes.

Um canal dá suporte a até três programas em execução simultânea, de modo que você pode criar diversos arquivos por meio do mesmo fluxo de entrada. Isso permite que você publique e arquive diferentes partes de um evento, conforme necessário. Por exemplo, o requisito de negócios é arquivar 6 horas de um programa, mas para transmitir, é de apenas os últimos 10 minutos. Para fazer isso, você precisa criar dois programas em execução simultânea. Um programa é definido para arquivar 6 horas do evento, mas o programa não é publicado. Outro programa é definido para arquivar para 10 minutos, e esse programa é publicado.

Você não deve reutilizar programas existentes para novos eventos. Em vez disso, crie e inicie um novo programa para cada evento, conforme descrito na seção Programando aplicativos de transmissão ao vivo.

Inicie o programa quando estiver pronto para iniciar a transmissão e o arquivamento. Interrompa o programa sempre que você deseja parar o streaming e o arquivamento do evento. 

Para excluir o conteúdo arquivado, pare e exclua o programa e, em seguida, exclua o ativo associado. Não será possível excluir um ativo se ele for usado por um programa; o programa deverá ser excluído primeiro. 

Mesmo depois que você parar e excluir o programa, os usuários poderão transmitir seu conteúdo arquivado como vídeo sob demanda, enquanto você não excluir o ativo.

Se desejar manter o conteúdo arquivado, mas não permitir que ele esteja disponível para streaming, exclua o localizador de streaming.

##Estado do canal

O estado atual do canal. Os valores possíveis incluem:

- Parado. Esse é o estado inicial do canal após sua criação. Nesse estado, as propriedades do canal podem ser atualizadas, mas o streaming não é permitido.
- Iniciando. O canal está sendo iniciado. Não são permitidas atualizações nem streaming durante esse estado. Se ocorrer um erro, o canal retornará ao estado Parado.
- Em execução. O canal é capaz de processar transmissões ao vivo.
- Parando. O canal está sendo parado. Não são permitidas atualizações nem streaming durante esse estado.
- Excluindo. O canal está sendo excluído. Não são permitidas atualizações nem streaming durante esse estado. 

##Legendagem oculta e inserção de anúncios 

A tabela a seguir demonstra os padrões com suporte de legendagem oculta e inserção de anúncios.

<table border="1">
<tr><th>Padrão</th><th>Observações</th></tr>
<tr><td>CEA-708 e EIA-608 (708/608)</td><td>CEA-708 e EIA-608 são padrões de legendagem oculta para os Estados Unidos e o Canadá.<br/>Atualmente, as legendas têm suporte somente se incluídas no fluxo de entrada codificado. Você precisa usar um codificador de mídia ao vivo que possa inserir legendas 608 ou 708 no fluxo codificado que é enviado aos Serviços de Mídia. Os Serviços de Mídia entregarão o conteúdo com legendas inseridas a seus usuários.</td></tr>
<tr><td>TTML em ismt (faixas de texto de Smooth Streaming)</td><td>O empacotamento dinâmico dos Serviços de Mídia habilita os clientes a transmitir conteúdo em qualquer um dos seguintes formatos: MPEG DASH, HLS ou Smooth Streaming. No entanto, se ingerir MP4 fragmentado (Smooth Streaming) com legendas em .ismt (faixas de texto de Smooth Streaming), você só poderá entregar o fluxo a clientes de Smooth Streaming.</td></tr>
<tr><td>SCTE-35</td><td>O sistema de sinalização digital usado para a inserção de anúncios de indicação. Receptores de downstream usam o sinal para unir a publicidade ao fluxo pelo tempo alocado. SCTE-35 deve ser enviado como uma faixa esparsa no fluxo de entrada.<br/>Observe que, no momento, o único formato de fluxo de entrada com suporte que transporta sinais de anúncios é o MP4 fragmentado (Smooth Streaming). O único formato de saída com suporte também é Smooth Streaming.</td></tr>
</table>

##Considerações

Ao se usar um codificador ao vivo local para enviar um fluxo com múltiplas taxas de bits a um canal, as seguintes restrições são aplicáveis:

- Verifique se você tem conectividade com a Internet livre suficiente para enviar dados aos pontos de ingestão. 
- O fluxo com múltiplas taxas de bits de entrada pode ter no máximo 10 níveis de qualidade de vídeo (10 camadas) e no máximo cinco faixas de áudio.
- A taxa de bits média mais alta para qualquer um dos níveis ou camadas de qualidade de vídeo deve estar abaixo de 10 Mbps
- A agregação das taxas de bits médias para todos os fluxos de vídeo e áudio deve estar abaixo de 25 Mbps
- Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se precisar de protocolos diferentes, você deverá criar canais separados para cada protocolo de entrada. 


Outras considerações relacionadas ao trabalho com canais e componentes relacionados:


- Você será cobrado apenas quando seu canal estiver em estado de execução.
- Sempre que você reconfigurar o codificador ao vivo, chame o método **Redefinir** no canal. Antes de redefinir o canal, você precisa parar o programa. Após redefinir o canal, reinicie o programa. 
- Um canal só poderá ser parado quando estiver no estado Em execução e todos os programas no canal tiverem sido parados.
- Por padrão, você só pode adicionar cinco canais à sua conta dos Serviços de Mídia. Para obter mais informações, consulte [Cotas e limitações](media-services-quotas-and-limitations.md).
- Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se precisar de protocolos diferentes, você deverá criar canais separados para cada protocolo de entrada. 

##<a id="tasks"></a>Tarefas relacionadas à transmissão ao vivo

###Configurando seu computador

Para obter informações sobre como configurar seu computador, consulte [Configurar seu computador](media-services-set-up-computer.md).

###Configurando pontos de extremidade de streaming

Para obter uma visão geral sobre pontos de extremidade de streaming e obter informações sobre como gerenciá-los, consulte [Como gerenciar pontos de extremidade de streaming em uma conta dos Serviços de Mídia](media-services-manage-origins.md)

###Usando codificadores ao vivo locais para gerar um fluxo com múltiplas taxas de bits para um canal

Para obter mais informações, consulte [Usando 3ª parte dos codificadores ao vivo com os serviços de mídia do Azure](https://msdn.microsoft.com/library/azure/dn783464.aspx).

###Gerenciar canais, programas, ativos
Para obter uma visão detalhada, consulte [Visão geral sobre o gerenciamento de canais e programas](media-services-manage-channels-overview.md).

Escolha **Portal**, **.NET**, **API REST** para ver exemplos.

[AZURE.INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]

###Configurando a política de fornecimento de ativos

Configurar a política de fornecimento de ativos usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

###Criando uma chave de conteúdo

Crie uma chave de conteúdo com a qual você deseja criptografar seu ativo usando o **.NET** ou a **API REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

###Configurando a política de autorização de chave de conteúdo 

Configurar a proteção de conteúdo e a política de autorização de chaves usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


###Publicando ativos

Publicar ativos (ao criar localizadores) usando o **Portal de Gerenciamento do Azure** ou **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


###Habilitando a CDN do Azure

Os Serviços de Mídia dão suporte à integração com a CDN do Azure. Para obter informações sobre como habilitar a CDN do Azure, consulte [Como gerenciar pontos de extremidade de streaming em uma conta dos Serviços de Mídia](media-services-manage-origins.md#enable_cdn).

###Dimensionamento de uma conta de serviços de mídia

Você pode dimensionar os **serviços de mídia** especificando o número de **unidades reservadas de Streaming**, você gostaria que sua conta fosse provisionada. 

Para obter informações sobre dimensionamento de unidades de streaming, consulte: [Como dimensionar unidades de streaming](media-services-manage-origins.md#scale_streaming_endpoints.md).


[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png


<!--HONumber=52--> 