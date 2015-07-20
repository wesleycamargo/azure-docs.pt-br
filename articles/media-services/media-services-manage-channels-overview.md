<properties 
	pageTitle="Trabalhando com Canais que recebam transmissão ao vivo de múltiplas taxas de bits de codificadores locais" 
	description="Este tópico descreve como configurar um canal que recebe uma transmissão ao vivo com múltiplas taxas de bits de um codificador local. O fluxo pode ser entregue para aplicativos de reprodução do cliente por meio de um ou mais pontos de extremidade de Streaming, usando um dos seguintes protocolos de streaming adaptáveis: HLS, Smooth Streaming, MPEG DASH, HDS." 
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
	ms.date="05/23/2015" 
	ms.author="juliako"/>

#Trabalhando com Canais que recebam transmissão ao vivo de múltiplas taxas de bits de codificadores locais

##Visão geral

Nos Serviços de Mídia do Azure, um **Canal** representa um pipeline para processamento de conteúdo de streaming ao vivo. Um **Canal** recebe transmissões de entrada ao vivo das seguintes maneiras:

- Um codificador local ao vivo envia **RTMP** ou **Smooth Streaming** (MP4 fragmentado) com múltiplas taxas de bits para o Canal. Você pode usar os codificadores ao vivo a seguir, que produz Smooth Streaming com múltiplas taxas de bits: Elemental, Envivio, Cisco. Os codificadores ao vivo a seguir produzem RTMP: transcodificadores Adobe Flash Live, Telestream Wirecast e Tricaster. Os fluxos ingeridos passam pelos **Canais**sem nenhum processamento adicional. Seu codificador ao vivo pode também enviar um fluxo de taxa de bits única, mas isso não é recomendado. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.


O diagrama a seguir representa um fluxo de trabalho de transmissão ao vivo que usa um codificador ao vivo local para gerar fluxos RTMP com múltiplas taxas de bits ou MP4 fragmentado (Smooth Streaming).

![Fluxo de trabalho ao vivo][live-overview]

Este tópico aborda o seguinte:

- [Cenário comum de streaming ao vivo](media-services-manage-channels-overview.md#scenario)
- [Descrição de um Canal e seus componentes relacionados](media-services-manage-channels-overview.md#channel)
- [Considerações](media-services-manage-channels-overview.md#considerations)
- [Tarefas relacionadas ao streaming ao vivo](media-services-manage-channels-overview.md#tasks)

##<a id="scenario"></a>Cenário comum de streaming ao vivo
As etapas a seguir descrevem as tarefas envolvidas na criação de aplicativos comuns de transmissão ao vivo.

1. Conecte uma câmera de vídeo a um computador. Inicie e configure um codificador ao vivo local que gere um fluxo RTMP com múltiplas taxas de bits ou MP4 fragmentado (Smooth Streaming). Para obter mais informações, consulte [Suporte RTMP dos Serviços de Mídia do Azure e Codificadores ao Vivo](http://go.microsoft.com/fwlink/?LinkId=532824).
	
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

##<a id="channel"></a>Descrição de um Canal e seus componentes relacionados

###<a id="channel_input"></a>Configurações de entrada de canal (ingestão)

####<a id="ingest_protocols"></a>Protocolo de streaming de ingestão

Os Serviços de Mídia dão suporte à ingestão de feeds ao vivo usando o seguinte protocolo de transmissão:

- **MP4 fragmentado com múltiplas taxas de bits**
 
- **RTMP com múltiplas taxas de bits**

	Quando o protocolo de transmissão de ingestão **RTMP** é selecionado, dois pontos de extremidade de ingestão (entrada) são criados para o canal:
	
	**URL principal**: especifica a URL totalmente qualificada do ponto de extremidade de ingestão de RTMP principal do canal.

	**URL secundária** (opcional): especifica a URL totalmente qualificada do ponto de extremidade de ingestão de RTMP secundário do canal.


	Use a URL secundária se você quiser melhorar a durabilidade e a tolerância a falhas do fluxo de ingestão, bem como o failover e a tolerância a falhas do codificador, especialmente para os cenários a seguir.

	- Codificador único duplo enviando URLs primária e secundária:
	
		O objetivo principal é fornecer mais flexibilidade ás flutuações de rede e jitters. Alguns codificadores RTMP não tratam bem da desconexão de rede. Quando uma ocorre uma desconexão de rede, um codificador pode parar de codificar e não enviará os dados armazenados no buffer ao após a reconexão, isso provoca descontinuidades e perdas de dados. As desconexões da rede podem ocorrer devido a problemas na rede ou uma manutenção no lado do Azure. As URLs primária/secundária reduzem os problemas de rede e também fornecem um processo controlado de atualização. Sempre que ocorrer uma desconexão da rede agendada, os serviços de mídia gerenciam a desconexão primária e secundária e proporcionam uma desconexão de atraso entre os dois, o que dá tempo para codificadores continuarem enviando dados e reconectarem novamente. A ordem de desconexão pode ser aleatória, mas haverá sempre um atraso entre primária/secundária ou secundária/primária. Neste cenário, o codificador ainda é o único ponto de falha.
	 
	- Vários codificadores, cada codificador enviando para um ponto dedicado:
		
		Este cenário fornece redundância de codificador e de inclusão. Neste cenário, o condificador1 envia para a URL principal e o codificador2 envia a URL secundária. Quando houver uma falha de codificador, o outro codificador ainda pode continuar enviando dados. A redundância de dados pode ainda ser mantida porque os serviços de mídia não desconecta a primária e a secundária ao mesmo tempo. Este cenário pressupõe que os codificadores são sincronizados e fornecem exatamente os mesmos dados.
 
	- Codificador múltiplo duplo enviando URLs primária e secundária:
	
		Neste cenário, os codificadores enviam dados para as URLs primárias e secundárias. Isso oferece maior confiabilidade e tolerância a falhas, bem como redundância de dados. São toleradas falhas do codificador, bem como é feita a desconexão mesmo se um codificador para de funcionar. Este cenário pressupõe que os codificadores são sincronizados e fornecem exatamente os mesmos dados.

Para obter informações sobre codificadores ao vivo de RTMP, consulte [Suporte a RTMP dos Serviços de Mídia do Azure e codificadores ao vivo](http://go.microsoft.com/fwlink/?LinkId=532824).

As seguintes considerações se aplicam:

- Verifique se que você tem conectividade com a Internet livre suficiente para enviar dados aos pontos de ingestão. 
- O uso de uma URL de ingestão secundária requer largura de banda adicional. 
- O fluxo com múltiplas taxas de bits de entrada pode ter no máximo 10 níveis de qualidade de vídeo (também conhecidos como camadas) e no máximo cinco faixas de áudio.
- A taxa de bits média mais alta para qualquer um dos níveis ou camadas de qualidade de vídeo deve estar abaixo de 10 Mbps.
- A agregação das taxas de bits médias para todos os fluxos de vídeo e áudio deve estar abaixo de 25 Mbps.
- Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se você precisar de protocolos diferentes, você deve criar canais separados para cada protocolo de entrada. 
- Você pode incluir uma única taxa de bits em seu canal, mas como o fluxo não é processado pelo canal, os aplicativos de cliente também receberão um fluxo de taxa de bits única (essa opção não é recomendada).

####URLs de ingestão (pontos de extremidade) 

Um canal fornece um ponto de extremidade de entrada (URL de ingestão) que você especifica no codificador ao vivo, assim, o codificador pode enviar fluxos a seus canais.

Você pode obter as URLs de ingestão ao criar o canal. Para obter essas URLs, o canal não precisa estar no estado **Executando**. Quando estiver pronto para começar a enviar dados por push para o canal, ele deve estar no estado **Executando**. Depois que o canal iniciar a ingestão de dados, você poderá visualizar o fluxo por meio da URL de visualização.

Você tem a opção de ingerir a transmissão ao vivo de MP4 fragmentado (Smooth Streaming) em uma conexão SSL. Para inserir por SSL, certifique-se de atualizar a URL de inserção para HTTPS. No momento, você não pode ingerir RTMP sobre SSL.

####<a id="keyframe_interval"></a>Intervalo de quadro-chave

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


####Endereços IP permitidos

Você pode definir os endereços IP que têm permissão para publicar vídeo para esse canal. Os endereços IP permitidos podem ser especificados como um endereço IP individual (por exemplo, '10.0.0.1'), um intervalo de IPs usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ‘10.0.0.1/22’), ou um intervalo de IPs usando um endereço IP e uma máscara de sub-rede decimal com pontos (por exemplo, ‘10.0.0.1(255.255.252.0)’).

Se nenhum endereço IP for especificado e não houver definição de regra, nenhum endereço IP será permitido. Para permitir qualquer endereço IP, crie uma regra e defina 0.0.0.0/0.

###Visualização de canal 

####URLs de visualização

Os canais fornecem um ponto de extremidade de visualização (URL de visualização) que você usa para visualizar e validar seu fluxo antes do processamento e da entrega.

Você pode obter a URL de visualização quando você cria o canal. Para obter a URL, o canal não precisa estar no estado **Executando**.

Depois que o canal inicia a ingestão de dados, você pode visualizar o fluxo.

Observe que, no momento, o fluxo de visualização só pode ser entregue no formato MP4 fragmentado (Smooth Streaming), independentemente do tipo de entrada especificado. Você pode usar o player [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) para testar o Smooth Stream. Você também pode usar um player hospedado no Portal de Gerenciamento do Azure para exibir o fluxo.


####Endereços IP permitidos

Você pode definir os endereços IP que têm permissão para conectar-se ao ponto de extremidade de visualização. Se nenhum endereço IP for especificado, qualquer endereço IP será permitido. Os endereços IP permitidos podem ser especificados como um endereço IP individual (por exemplo, '10.0.0.1'), um intervalo de IPs usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ‘10.0.0.1/22’), ou um intervalo de IPs usando um endereço IP e uma máscara de sub-rede decimal com pontos (por exemplo, ‘10.0.0.1(255.255.252.0)’).

###Saída do canal

Para obter mais informações, consulte a seção [intervalo de quadro-chave de configuração](#keyframe_interval).


###Programas do canal

Um canal é associado a programas que permitem que você controle a publicação e o armazenamento de segmentos em um fluxo ao vivo. Canais gerenciam programas. A relação entre canal e programa é muito semelhante à mídia tradicional, onde um canal tem um fluxo constante de conteúdo e um programa tem como escopo algum evento programado naquele canal.

Você pode especificar o número de horas pelo qual você deseja manter o conteúdo gravado para o programa, definindo a duração da **Janela de Arquivo**. Esse valor pode ser definido entre um mínimo de 5 minutos e um máximo de 25 horas. A duração da janela de arquivo também determina que a quantidade máxima de tempo que os clientes podem pesquisar na posição atual em tempo real. Os programas podem ser executados pelo período de tempo especificado, mas o conteúdo que estiver por trás da janela de tamanho será continuamente descartado. Esse valor desta propriedade também determina por quanto tempo os manifestos do cliente podem crescer.

Cada programa está associado um ativo que armazena o conteúdo transmitido. Um ativo é mapeado para um contêiner de blob na conta de Armazenamento do Azure e os arquivos no ativo são armazenados como blobs nesse contêiner. Para publicar o programa para que seus clientes possam exibir o fluxo, você deve criar um localizador OnDemand para o ativo associado. Ter esse localizador permitirá que você crie uma URL de transmissão que você pode fornecer aos seus clientes.

Um canal dá suporte a até três programas em execução simultânea, para que você possa criar diversos arquivos no mesmo fluxo de entrada. Isso permite que você publique e arquive diferentes partes de um evento, conforme necessário. Por exemplo, o requisito de negócios é arquivar 6 horas de um programa, mas transmitir apenas os últimos 10 minutos. Para fazer isso, você precisa criar dois programas em execução simultânea. Um programa é definido para arquivar 6 horas do evento, mas o programa não é publicado. Outro programa é definido para 10 minutos e esse programa é publicado.

Você não deve reutilizar os programas existentes para novos eventos. Em vez disso, crie e inicie um novo programa para cada evento, conforme descrito na seção Programação de aplicativos de streaming ao vivo.

Inicie o programa quando estiver pronto para iniciar o streaming e o arquivamento. Interrompa o programa sempre que você deseja parar o streaming e o arquivamento do evento.

Para excluir o conteúdo arquivado, interrompa e exclua o programa e, em seguida, exclua o ativo associado. Não é possível excluir um ativo se este for usado por um programa; o programa deve ser excluído primeiro.

Mesmo depois que você parar e excluir o programa, os usuários poderão transmitir seu conteúdo arquivado como vídeo por demanda enquanto você não excluir o ativo.

Se desejar manter o conteúdo arquivado mas ele não está disponível para streaming, exclua o localizador de streaming.

##<a id="states"></a>Estados de canal e como os estados são mapeados para o modo de cobrança 

O estado atual de um canal. Os valores possíveis incluem:

- **Parado**. Este é o estado inicial do canal após sua criação. Nesse estado, as propriedades do canal podem ser atualizadas, mas streaming não é permitido.
- **Iniciando**. O canal está sendo iniciado. Nenhuma atualização ou streaming é permitido durante esse estado. Se ocorrer um erro, o canal retorna para o estado Parado.
- **Executando**. O canal é capaz de processar transmissões ao vivo.
- **Parando**. O canal está sendo parado. Nenhuma atualização ou streaming é permitido durante esse estado.
- **Excluindo**. O canal está sendo excluído. Nenhuma atualização ou streaming é permitido durante esse estado.

A tabela a seguir mostra como os estados de canal são mapeados para o modo de cobrança.
 
<table border="1">
<tr><th>Estado de canal</th><th>Indicadores da interface do usuário do portal</th><th>Cobrado?</th></tr>
<tr><td>Iniciando</td><td>Iniciando</td><td>Nenhum (estado transitório)</td></tr>
<tr><td>Executando</td><td>Pronto (nenhum programa em execução)<br/>ou o<br/>Streaming (há pelo menos um programa em execução)</td><td>Sim</td></tr>
<tr><td>Parando</td><td>Parando</td><td>Nenhum (estado transitório)</td></tr>
<tr><td>Parada</td><td>Parada</td><td>Não</td></tr>
</table>

###Legendagem oculta e inserção de anúncios 

A tabela a seguir demonstra os padrões com suporte de legendagem oculta e inserção de anúncios.

<table border="1">
<tr><th>Padrão</th><th>Observações</th></tr>
<tr><td>CEA-708 e EIA-608 (708/608)</td><td>CEA-708 e EIA-608 são padrões de legendagem oculta para os Estados Unidos e o Canadá.<br/>Atualmente, as legendas têm suporte somente se incluídas no fluxo de entrada codificado. Você precisa usar um codificador de mídia ao vivo que possa inserir legendas 608 ou 708 no fluxo codificado que é enviado aos Serviços de Mídia. Os Serviços de Mídia entregarão o conteúdo com legendas inseridas a seus usuários.</td></tr>
<tr><td>TTML em ismt (faixas de texto de Smooth Streaming)</td><td>O empacotamento dinâmico dos Serviços de Mídia habilita os clientes a transmitir conteúdo em qualquer um dos seguintes formatos: MPEG DASH, HLS ou Smooth Streaming. No entanto, se ingerir MP4 fragmentado (Smooth Streaming) com legendas em .ismt (faixas de texto de Smooth Streaming), você só poderá entregar o fluxo a clientes de Smooth Streaming.</td></tr>
<tr><td>SCTE-35</td><td>O sistema de sinalização digital usado para a inserção de anúncios de indicação. Receptores de downstream usam o sinal para unir a publicidade ao fluxo pelo tempo alocado. SCTE-35 deve ser enviado como uma faixa esparsa no fluxo de entrada.<br/>Observe que, no momento, o único formato de fluxo de entrada com suporte que transporta sinais de anúncios é o MP4 fragmentado (Smooth Streaming). O único formato de saída com suporte também é Smooth Streaming.</td></tr>
</table>

##<a id="Considerations"></a>Considerações

Ao se usar um codificador ao vivo local para enviar um fluxo com múltiplas taxas de bits a um canal, as seguintes restrições são aplicáveis:

- Verifique se você tem conectividade com a Internet livre suficiente para enviar dados aos pontos de ingestão. 
- O fluxo com múltiplas taxas de bits de entrada pode ter no máximo 10 níveis de qualidade de vídeo (10 camadas) e no máximo cinco faixas de áudio.
- A taxa de bits média mais alta para qualquer um dos níveis ou camadas de qualidade de vídeo deve estar abaixo de 10 Mbps
- A agregação das taxas de bits médias para todos os fluxos de vídeo e áudio deve estar abaixo de 25 Mbps
- Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se você precisar de protocolos diferentes, você deve criar canais separados para cada protocolo de entrada. 


Outras considerações relacionadas ao trabalho com canais e componentes relacionados:

- Sempre que você reconfigurar o codificador ao vivo, chame o método **Redefinir** no canal. Antes de redefinir o canal, você precisa interromper o programa. Antes de redefinir o canal, reinicie o programa. 
- Um canal pode ser interrompido somente quando estiver no estado Executando e todos os programas no canal tiverem sido interrompidos.
- Por padrão, você só pode adicionar cinco canais à sua conta dos Serviços de Mídia. Para obter mais informações, consulte [Cotas e limitações](media-services-quotas-and-limitations.md).
- Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se você precisar de protocolos diferentes, você deve criar canais separados para cada protocolo de entrada. 
- Você será cobrado apenas quando o canal estiver no estado **Executando**. Para obter mais informações, consulte [esta](media-services-manage-channels-overview.md#states) seção.

##<a id="tasks"></a>Tarefas relacionadas ao streaming ao vivo

###Criar uma conta de serviços de mídia

[Criar uma conta de Serviços de Mídia do Azure](media-services-create-account.md).

###Configurando pontos de extremidade de streaming

Para obter uma visão geral sobre streaming de pontos de extremidade e informações sobre como gerenciá-los, consulte [Como gerenciar pontos de extremidade de streaming em uma conta de Serviços de Mídia](media-services-manage-origins.md)

###Configurando o ambiente de desenvolvimento  

Escolha **.NET** ou **API REST** como seu ambiente de desenvolvimento.

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

###Conectando por meio de programação  

Escolha **.NET** ou **API REST** para conectar-se aos Serviços de Mídia do Azure por meio de programação.

[AZURE.INCLUDE [media-services-selector-connect](../../includes/media-services-selector-connect.md)]

###Criando Canais que recebam transmissão ao vivo de múltiplas taxas de bits de codificadores locais

Para obter mais informações codificadores ao vivo locais, consulte [Usando 3ª parte dos codificadores ao vivo com os serviços de mídia do Azure](https://msdn.microsoft.com/library/azure/dn783464.aspx).

Escolha **Portal**, **.NET** e **API REST** para saber como criar e gerenciar canais e programas.

[AZURE.INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]

###Protegendo ativos

**Visão geral**:

[Visão geral da proteção de conteúdo](media-services-content-protection-overview.md)


Se você deseja criptografar um ativo associado um programa à criptografia AES (de padrão avançado, usando chaves de criptografia de 128 bits) ou DRM PlayReady, você precisa criar uma chave de conteúdo.

Use **.NET** ou **API REST** para criar chaves.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

Depois de criar a chave de conteúdo, você pode configurar a política de autorização de chave usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

####Integração com parceiros

[Usando o castLabs para fornecer licenças DRM para os serviços de mídia do Azure](media-services-castlabs-integration.md)

###Publicando e fornecendo ativos

**Visão geral**:

- [Visão geral do empacotamento dinâmico](media-services-dynamic-packaging-overview.md)

Configure a política de fornecimento de ativos usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Publicar ativos (pela criação de localizadores) usando o **Portal de Gerenciamento do Azure** ou **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]

Fornecer Conteúdo

> [AZURE.SELECTOR]
- [Overview](media-services-deliver-content-overview.md)


###Habilitando o CDN do Azure

Os Serviços de Mídia dão suporte à integração com o CDN do Azure. Para obter informações sobre como habilitar o CDN do Azure, consulte [Como gerenciar pontos de extremidade de Streaming em uma conta de Serviços de Mídia](media-services-manage-origins.md#enable_cdn).

###Dimensionamento de uma conta de serviços de mídia

Você pode dimensionar **Serviços de Mídia** especificando o número de **Unidades Reservadas de Streaming** com o qual você gostaria que sua conta fosse provisionada.

Para obter informações sobre unidades de streaming de dimensionamento, consulte: [Como dimensionar unidades de streaming](media-services-manage-origins.md#scale_streaming_endpoints.md).

##Tópicos relacionados

[Especificação de ingestão dinâmica de MP4 fragmentado dos Serviços de Mídia do Azure](media-services-fmp4-live-ingest-overview.md)

[Trabalhando com Eventos de Live Streaming com os Serviços de Mídia do Azure](media-services-live-streaming-workflow.md)

[Conceitos de Serviços de Mídia](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
 

<!---HONumber=July15_HO2-->