---
title: "Transmissão ao vivo com codificadores locais, que criam fluxos de múltiplas taxas de bits – Azure | Microsoft Docs"
description: "Este tópico descreve como configurar um canal que recebe uma transmissão ao vivo com múltiplas taxas de bits de um codificador local. Em seguida, a transmissão pode ser entregue para aplicativos de reprodução do cliente por meio de um ou mais Pontos de Extremidade de Streaming, usando um dos seguintes protocolos de streaming adaptáveis: HLS, Smooth Streaming e MPEG DASH."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/23/2017
ms.author: cenkd;juliako
translationtype: Human Translation
ms.sourcegitcommit: bdf41edfa6260749a91bc52ec0a2b62fcae99fb0
ms.openlocfilehash: 4ebc53439735aa47c5191f7dccb77a3060e0f5f9


---
# <a name="live-streaming-with-on-premise-encoders-that-create-multi-bitrate-streams"></a>Transmissão ao vivo com codificadores locais, que criam fluxos de múltiplas taxas de bits
## <a name="overview"></a>Visão geral
Nos Serviços de Mídia do Azure, um **Canal** representa um pipeline para processamento de conteúdo de streaming ao vivo. Um **Canal** recebe transmissões de entrada ao vivo de uma das duas maneiras a seguir:

* Um codificador ativo local envia múltiplas taxas de bits **RTMP** ou **Smooth Streaming** (MP4 fragmentado) para o Canal que não está habilitado para executar a codificação ativa com o AMS. Os fluxos ingeridos passam pelos **Canais**sem nenhum processamento adicional. Esse método é chamado **passagem**. Você pode usar os codificadores dinâmicos a seguir, que produzem Smooth Streaming com múltiplas taxas de bits: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco e Elemental. Os seguintes codificadores dinâmicos produzem RTMP: Adobe Flash Media Live Encoder (FMLE), Telestream Wirecast, Haivision, Teradek e Tricaster. Um codificador ativo também pode enviar uma transmissão de taxa de bits única para um canal que não está habilitado para a codificação ativa, porém, isso não é recomendado. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.

  > [!NOTE]
  > O uso de um método de passagem é a maneira mais econômica de realizar uma transmissão ao vivo.
  >
  >
* Um codificador ao vivo local envia um fluxo de taxa de bits adaptável única para o Canal que é habilitado para realizar a codificação ao vico com os serviços de mídia em um dos seguintes formatos: RTP (MPEG-TS), RTMP oi Smooth Streaming (MP4 fragmentado). O Canal então realiza a codificação ao vivo do fluxo de entrada com taxa de bits única em um fluxo de vídeo (adaptável) de múltiplas taxas de bits. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.

A partir da versão 2.10 dos Serviços de Mídia, quando você cria um canal, você pode especificar de que modo você deseja que o canal receba o fluxo de entrada e se deseja ou não que o canal realize a codificação ao vivo do seu fluxo. Você tem duas opções:

* **Nenhum** – Especifique esse valor se você planejar usar um codificador ativo local, que emitirá uma transmissão de taxa de bits múltipla (uma transmissão de passagem). Nesse caso, o fluxo de entrada foi transmitido para a saída sem qualquer codificação. Esse é o comportamento de um canal em versão anterior à 2.10.  Este tópico fornece detalhes sobre como trabalhar com canais desse tipo.
* **Standard** – Escolha esse valor se você pretende usar os Serviços de Mídia para codificar sua transmissão ao vivo de taxa de bits única para uma transmissão de múltiplas taxas de bits. Lembre-se de que há um impacto de cobrança para codificação ativa e você deve se lembrar que deixar um canal de codificação ativo no estado "Em execução" incorrerá em cobranças.  É recomendável parar imediatamente seus canais em execução após a conclusão do evento de streaming ativo para evitar cobranças por hora extra. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.

> [!NOTE]
> Este tópico discute os atributos de canais que não estão habilitados para executar a codificação ativa (tipo de codificação**Nenhum** ). Para obter informações sobre como trabalhar com canais habilitados a realizar a codificação ativa, confira [Trabalhando com canais habilitados a executar codificação ao vivo com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).
>
>

O diagrama a seguir representa um fluxo de trabalho de transmissão ao vivo que usa um codificador ao vivo local para gerar fluxos RTMP com múltiplas taxas de bits ou MP4 fragmentado (Smooth Streaming).

![Fluxo de trabalho ao vivo][live-overview]

Este tópico aborda o seguinte:

* [Cenário comum de streaming ao vivo](media-services-live-streaming-with-onprem-encoders.md#scenario)
* [Descrição de um Canal e seus componentes relacionados](media-services-live-streaming-with-onprem-encoders.md#channel)
* [Considerações](media-services-live-streaming-with-onprem-encoders.md#considerations)

## <a name="a-idscenarioacommon-live-streaming-scenario"></a><a id="scenario"></a>Cenário comum de streaming ao vivo
As etapas a seguir descrevem as tarefas envolvidas na criação de aplicativos comuns de transmissão ao vivo.

1. Conecte uma câmera de vídeo a um computador. Inicie e configure um codificador ao vivo local que gere um fluxo RTMP com múltiplas taxas de bits ou MP4 fragmentado (Smooth Streaming). Para obter mais informações, consulte [Suporte RTMP dos Serviços de Mídia do Azure e Codificadores ao Vivo](http://go.microsoft.com/fwlink/?LinkId=532824).

    Essa etapa também pode ser realizada após a criação do canal.
2. Crie e inicie um Canal.
3. Recupere a URL de ingestão do canal.

    A URL de ingestão é usada pelo codificador ao vivo para enviar o fluxo para o canal.
4. Recupere a URL de visualização do canal.

    Use essa URL para verificar se o canal está recebendo corretamente o fluxo ao vivo.
5. Crie um programa.

    Ao usar o Portal do Azure, a criação de um programa também cria um ativo.

    Ao usar o SDK do .NET ou REST, você precisa criar um ativo e especificar o uso desse ativo durante a criação de um programa.
6. Publique o ativo associado ao programa.   

    >[!NOTE]
    >Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. O ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar no estado **Executando**. 
    
7. Inicie o programa quando estiver pronto para iniciar o streaming e o arquivamento.
8. Opcionalmente, o codificador ao vivo pode ser sinalizado para iniciar um anúncio. O anúncio é inserido no fluxo de saída.
9. Interrompa o programa sempre que você deseja parar o streaming e o arquivamento do evento.
10. Exclua o programa (e, opcionalmente, exclua o ativo).     

## <a name="a-idchanneladescription-of-a-channel-and-its-related-components"></a><a id="channel"></a>Descrição de um Canal e seus componentes relacionados
### <a name="a-idchannelinputachannel-input-ingest-configurations"></a><a id="channel_input"></a>Configurações de entrada de canal (ingestão)
#### <a name="a-idingestprotocolsaingest-streaming-protocol"></a><a id="ingest_protocols"></a>Protocolo de streaming de ingestão
Os Serviços de Mídia dão suporte à ingestão de feeds ao vivo usando o seguinte protocolo de transmissão:

* **MP4 fragmentado com múltiplas taxas de bits**
* **RTMP com múltiplas taxas de bits**

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

* Verifique se que você tem conectividade com a Internet livre suficiente para enviar dados aos pontos de ingestão.
* O uso de uma URL de ingestão secundária requer largura de banda adicional.
* O fluxo com múltiplas taxas de bits de entrada pode ter no máximo 10 níveis de qualidade de vídeo (também conhecidos como camadas) e no máximo cinco faixas de áudio.
* A taxa de bits média mais alta para qualquer um dos níveis ou camadas de qualidade de vídeo deve estar abaixo de 10 Mbps.
* A agregação das taxas de bits médias para todos os fluxos de vídeo e áudio deve estar abaixo de 25 Mbps.
* Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se você precisar de protocolos diferentes, você deve criar canais separados para cada protocolo de entrada.
* Você pode incluir uma única taxa de bits em seu canal, mas como o fluxo não é processado pelo canal, os aplicativos de cliente também receberão um fluxo de taxa de bits única (essa opção não é recomendada).

#### <a name="ingest-urls-endpoints"></a>URLs de ingestão (pontos de extremidade)
Um canal fornece um ponto de extremidade de entrada (URL de ingestão) que você especifica no codificador ao vivo, assim, o codificador pode enviar fluxos a seus canais.   

Você pode obter as URLs de ingestão ao criar o canal. Para obter essas URLs, o canal não precisa estar no estado **Executando** . Quando estiver pronto para começar a enviar dados por push para o canal, ele deve estar no estado **Executando** . Depois que o canal iniciar a ingestão de dados, você poderá visualizar o fluxo por meio da URL de visualização.

Você tem a opção de ingerir a transmissão ao vivo de MP4 fragmentado (Smooth Streaming) em uma conexão SSL. Para inserir por SSL, certifique-se de atualizar a URL de inserção para HTTPS. No momento, você não pode ingerir RTMP sobre SSL.

#### <a name="a-idkeyframeintervalakeyframe-interval"></a><a id="keyframe_interval"></a>Intervalo de quadro-chave
Ao se usar um codificador ao vivo local para gerar um fluxo com múltiplas taxas de bits, o intervalo de quadro-chave especifica a duração de GOP (conforme usado pelo codificador externo). Depois que esse fluxo de entrada for recebido pelo canal, você poderá entregar sua transmissão ao vivo aos aplicativos de reprodução de cliente em qualquer um dos seguintes formatos: Smooth Streaming, DASH e HLS. Ao fazer streaming ao vivo, o HLS é sempre empacotado dinamicamente. Por padrão, os Serviços de Mídia calculam automaticamente a taxa de empacotamento de segmento HLS (fragmentos por segmento) com base no intervalo de quadros-chave, também conhecido como GOP (grupo de imagens), que é recebido do codificador ao vivo.

A tabela a seguir mostra como a duração do segmento é calculada:

| Intervalo de quadro-chave | Taxa de empacotamento de segmento HLS (FragmentsPerSegment) | Exemplo |
| --- | --- | --- |
| menor ou igual a 3 segundos |3:1 |Se KeyFrameInterval (ou GOP) for de 2 segundos, a taxa de empacotamento padrão de segmento HLS será de 3 para 1, o que criará um segmento HLS de 6 segundos. |
| 3 a 5 segundos |2:1 |Se KeyFrameInterval (ou GOP) for de 4 segundos, a taxa de empacotamento padrão de segmento HLS será de 2 para 1, o que criará um segmento HLS de 8 segundos. |
| mais de 5 segundos |1:1 |Se KeyFrameInterval (ou GOP) for de 6 segundos, a taxa de empacotamento padrão de segmento HLS será de 1 para 1, o que criará um segmento HLS de 6 segundos. |

Você pode alterar a taxa de fragmentos por segmento configurando a saída do canal e definindo FragmentsPerSegment em ChannelOutputHls.

Você também pode alterar o valor do intervalo de quadro-chave definindo a propriedade KeyFrameInterval em ChanneInput.

Se você definir explicitamente o KeyFrameInterval, o FragmentsPerSegment da taxa de empacotamento de segmento HLS será calculado usando as regras descritas acima.  

Se você definir explicitamente KeyFrameInterval e FragmentsPerSegment, os Serviços de Mídia usarão os valores definidos por você.

#### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Você pode definir os endereços IP que têm permissão para publicar vídeo para esse canal. Os endereços IP permitidos podem ser especificados como um endereço IP individual (por exemplo, '10.0.0.1'), um intervalo de IPs usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ‘10.0.0.1/22’), ou um intervalo de IPs usando um endereço IP e uma máscara de sub-rede decimal com pontos (por exemplo, ‘10.0.0.1(255.255.252.0)’).

Se nenhum endereço IP for especificado e não houver definição de regra, nenhum endereço IP será permitido. Para permitir qualquer endereço IP, crie uma regra e defina 0.0.0.0/0.

### <a name="channel-preview"></a>Visualização de canal
#### <a name="preview-urls"></a>URLs de visualização
Os canais fornecem um ponto de extremidade de visualização (URL de visualização) que você usa para visualizar e validar seu fluxo antes do processamento e da entrega.

Você pode obter a URL de visualização quando você cria o canal. Para obter a URL, o canal não precisa estar no estado **Executando** .

Depois que o canal inicia a ingestão de dados, você pode visualizar o fluxo.

Observe que, no momento, o fluxo de visualização só pode ser entregue no formato MP4 fragmentado (Smooth Streaming), independentemente do tipo de entrada especificado. Você pode usar o player [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) para testar o Smooth Stream. Você também pode usar um player hospedado no Portal do Azure para exibir a transmissão.

#### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Você pode definir os endereços IP que têm permissão para conectar-se ao ponto de extremidade de visualização. Se nenhum endereço IP for especificado, qualquer endereço IP será permitido. Os endereços IP permitidos podem ser especificados como um endereço IP individual (por exemplo, '10.0.0.1'), um intervalo de IPs usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ‘10.0.0.1/22’), ou um intervalo de IPs usando um endereço IP e uma máscara de sub-rede decimal com pontos (por exemplo, ‘10.0.0.1(255.255.252.0)’).

### <a name="channel-output"></a>Saída do canal
Para saber mais, consulte a seção [intervalo de quadro-chave de configuração](#keyframe_interval) .

### <a name="channels-programs"></a>Programas do canal
Um canal é associado a programas que permitem que você controle a publicação e o armazenamento de segmentos em um fluxo ao vivo. Canais gerenciam programas. A relação entre canal e programa é muito semelhante à mídia tradicional, onde um canal tem um fluxo constante de conteúdo e um programa tem como escopo algum evento programado naquele canal.

Você pode especificar o número de horas pelo qual você deseja manter o conteúdo gravado para o programa, definindo a duração da **Janela de Arquivo** . Esse valor pode ser definido entre um mínimo de 5 minutos e um máximo de 25 horas. A duração da janela de arquivo também determina que a quantidade máxima de tempo que os clientes podem pesquisar na posição atual em tempo real. Os programas podem ser executados pelo período de tempo especificado, mas o conteúdo que estiver por trás da janela de tamanho será continuamente descartado. Esse valor desta propriedade também determina por quanto tempo os manifestos do cliente podem crescer.

Cada programa está associado um ativo que armazena o conteúdo transmitido. Um ativo é mapeado para um contêiner de blob de blocos na conta de Armazenamento do Azure e os arquivos no ativo são armazenados como blobs nesse contêiner. Para publicar o programa para que seus clientes possam exibir o fluxo, você deve criar um localizador OnDemand para o ativo associado. Ter esse localizador permitirá que você crie uma URL de transmissão que você pode fornecer aos seus clientes.

Um canal dá suporte a até três programas em execução simultânea, para que você possa criar diversos arquivos no mesmo fluxo de entrada. Isso permite que você publique e arquive diferentes partes de um evento, conforme necessário. Por exemplo, o requisito de negócios é arquivar 6 horas de um programa, mas transmitir apenas os últimos 10 minutos. Para fazer isso, você precisa criar dois programas em execução simultânea. Um programa é definido para arquivar 6 horas do evento, mas o programa não é publicado. Outro programa é definido para 10 minutos e esse programa é publicado.

Você não deve reutilizar os programas existentes para novos eventos. Em vez disso, crie e inicie um novo programa para cada evento, conforme descrito na seção Programação de aplicativos de streaming ao vivo.

Inicie o programa quando estiver pronto para iniciar o streaming e o arquivamento. Interrompa o programa sempre que você deseja parar o streaming e o arquivamento do evento.

Para excluir o conteúdo arquivado, interrompa e exclua o programa e, em seguida, exclua o ativo associado. Não é possível excluir um ativo se este for usado por um programa; o programa deve ser excluído primeiro.

Mesmo depois que você parar e excluir o programa, os usuários poderão transmitir seu conteúdo arquivado como vídeo por demanda enquanto você não excluir o ativo.

Se desejar manter o conteúdo arquivado mas ele não está disponível para streaming, exclua o localizador de streaming.

## <a name="a-idstatesachannel-states-and-how-states-map-to-the-billing-mode"></a><a id="states"></a>Estados de canal e como os estados são mapeados para o modo de cobrança
O estado atual de um canal. Os valores possíveis incluem:

* **Parado**. Este é o estado inicial do canal após sua criação. Nesse estado, as propriedades do canal podem ser atualizadas, mas streaming não é permitido.
* **Iniciando**. O canal está sendo iniciado. Nenhuma atualização ou streaming é permitido durante esse estado. Se ocorrer um erro, o canal retorna para o estado Parado.
* **Executando**. O canal é capaz de processar transmissões ao vivo.
* **Parando**. O canal está sendo parado. Nenhuma atualização ou streaming é permitido durante esse estado.
* **Excluindo**. O canal está sendo excluído. Nenhuma atualização ou streaming é permitido durante esse estado.

A tabela a seguir mostra como os estados de canal são mapeados para o modo de cobrança.

| Estado de canal | Indicadores da interface do usuário do portal | Cobrado? |
| --- | --- | --- | --- |
| Iniciando |Iniciando |Nenhum (estado transitório) |
| Executando |Pronto (nenhum programa em execução)<p>ou o<p>Streaming (há pelo menos um programa em execução) |SIM |
| Parando |Parando |Nenhum (estado transitório) |
| Parado |Parado |Não |

## <a name="a-idccandadsaclosed-captioning-and-ad-insertion"></a><a id="cc_and_ads"></a>Legendagem oculta e inserção de anúncios
A tabela a seguir demonstra os padrões com suporte de legendagem oculta e inserção de anúncios.

| Standard | Observações |
| --- | --- |
| CEA-708 e EIA-608 (708/608) |CEA-708 e EIA-608 são padrões de legendagem oculta para os Estados Unidos e o Canadá.<p><p>Atualmente, as legendas têm suporte somente se incluídas no fluxo de entrada codificado. Você precisa usar um codificador de mídia ao vivo que possa inserir legendas 608 ou 708 no fluxo codificado que é enviado aos Serviços de Mídia. Os Serviços de Mídia entregarão o conteúdo com legendas inseridas a seus usuários. |
| TTML em ismt (faixas de texto de Smooth Streaming) |O empacotamento dinâmico dos Serviços de Mídia habilita os clientes a transmitir conteúdo em qualquer um dos seguintes formatos: MPEG DASH, HLS ou Smooth Streaming. No entanto, se ingerir MP4 fragmentado (Smooth Streaming) com legendas em .ismt (faixas de texto de Smooth Streaming), você só poderá entregar o fluxo a clientes de Smooth Streaming. |
| SCTE-35 |O sistema de sinalização digital usado para a inserção de anúncios de indicação. Receptores de downstream usam o sinal para unir a publicidade ao fluxo pelo tempo alocado. SCTE-35 deve ser enviado como uma faixa esparsa no fluxo de entrada.<p><p>Observe que, no momento, o único formato de fluxo de entrada com suporte que transporta sinais de anúncios é o MP4 fragmentado (Smooth Streaming). O único formato de saída com suporte também é Smooth Streaming. |

## <a name="a-idconsiderationsaconsiderations"></a><a id="considerations"></a>Considerações
Ao se usar um codificador ao vivo local para enviar um fluxo com múltiplas taxas de bits a um canal, as seguintes restrições são aplicáveis:

* Verifique se que você tem conectividade com a Internet livre suficiente para enviar dados aos pontos de ingestão.
* O fluxo com múltiplas taxas de bits de entrada pode ter no máximo 10 níveis de qualidade de vídeo (10 camadas) e no máximo cinco faixas de áudio.
* A taxa de bits média mais alta para qualquer um dos níveis ou camadas de qualidade de vídeo deve estar abaixo de 10 Mbps
* A agregação das taxas de bits médias para todos os fluxos de vídeo e áudio deve estar abaixo de 25 Mbps
* Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se você precisar de protocolos diferentes, você deve criar canais separados para cada protocolo de entrada.

Outras considerações relacionadas ao trabalho com canais e componentes relacionados:

* Sempre que você reconfigurar o codificador ao vivo, chame o método **Redefinir** no canal. Antes de redefinir o canal, você precisa interromper o programa. Antes de redefinir o canal, reinicie o programa.
* Um canal pode ser interrompido somente quando estiver no estado Executando e todos os programas no canal tiverem sido interrompidos.
* Por padrão, você só pode adicionar cinco canais à sua conta dos Serviços de Mídia. Para obter mais informações, consulte [Cotas e limitações](media-services-quotas-and-limitations.md).
* Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se você precisar de protocolos diferentes, você deve criar canais separados para cada protocolo de entrada.
* Você será cobrado apenas quando o canal estiver no estado **Executando** . Para obter mais informações, consulte [esta](media-services-live-streaming-with-onprem-encoders.md#states) seção.

## <a name="using-3rd-party-live-encoders"></a>Uso de codificadores dinâmicos de terceiros

Para saber mais sobre codificadores locais ativos, consulte [Usando codificadores ativos de terceiros com os Serviços de Mídia do Azure](https://azure.microsoft.com/blog/azure-media-services-rtmp-support-and-live-encoders/).


## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Especificação de ingestão dinâmica de MP4 fragmentado dos Serviços de Mídia do Azure](media-services-fmp4-live-ingest-overview.md)

[Trabalhando com Eventos de Live Streaming com os Serviços de Mídia do Azure](media-services-overview.md)

[Conceitos de Serviços de Mídia](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png



<!--HONumber=Jan17_HO4-->


