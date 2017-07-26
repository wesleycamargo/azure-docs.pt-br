---
title: "Otimização de streaming de mídia por meio do Azure CDN"
description: "Otimizando arquivos de mídia de streaming para o fornecimento suave"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: v-semcev
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 582eb69ccd2830643644760753cca1c96d72128d
ms.contentlocale: pt-br
ms.lasthandoff: 06/28/2017

---
# <a name="media-streaming-optimization-via-azure-cdn"></a>Otimização de streaming de mídia por meio do Azure CDN 
 
O vídeo de alta definição está se tornando uma parte cada vez maior da Internet, o que cria várias dificuldades no fornecimento eficiente de tais arquivos grandes através da Internet. Os clientes esperam a reprodução suave de VOD ou ativos de vídeo ao vivo em uma variedade de clientes e redes em todo o mundo. Fornecer um mecanismo de entrega mais rápido e eficiente para os arquivos de streaming de mídia é essencial para garantir a experiência do consumidor agradável e suave.  

A mídia de transmissão ao vivo é especialmente difícil de entregar, não apenas devido aos grandes tamanhos e ao número de espectadores simultâneos, mas também porque atrasos são um empecilho para os usuários. As transmissões ao vivo não podem ser armazenadas em cache antecipadamente e latências grandes não são aceitáveis para os espectadores, portanto, os fragmentos de vídeo devem ser entregues de maneira oportuna. 

Os padrões de solicitação de streaming também fornecem alguns novos desafios. Para uma transmissão ao vivo popular ou quando uma nova série é lançada para VOD (Vídeo por Demanda), pode haver de milhares a milhões de espectadores tentando solicitar o streaming ao mesmo tempo. Nesse caso, a consolidação de solicitações inteligentes é essencial para não sobrecarregar os servidores de origem quando os ativos ainda não estão em cache.
 
O Azure CDN do Akamai agora oferece um recurso fornecido para entregar ativos de mídia de streaming com eficiência para os usuários finais em todo o mundo em grande escala e latências reduzidas enquanto reduz a carga nos servidores de origem. Este recurso está disponível por meio do recurso "Otimizado para" no Ponto de Extremidade do Azure CDN criado em um Perfil do Azure CDN com o tipo de preço “Akamai Standard”. Você deve usar a otimização “Streaming de mídia de vídeo por demanda” para ativos de VOD e o tipo de otimização “Streaming de mídia geral” se fizer uma combinação de streaming de VOD e ao vivo.

O Azure CDN da Verizon pode fornecer mídia de streaming diretamente no tipo de otimização de “entrega Web geral”.
 
## <a name="configuring-cdn-endpoint-to-optimize-delivery-of-media-streaming-in-azure-cdn-from-akamai"></a>Configurando o Ponto de Extremidade CDN para otimizar a entrega de streaming de mídia no Azure CDN do Akamai
 
Você pode configurar o ponto de extremidade CDN para otimizar a entrega de arquivos grandes por meio do Portal do Azure simplesmente selecionando a opção “Streaming de mídia geral” ou “Streaming de mídia de vídeo por demanda” na seleção de propriedade “Otimizado por” durante a criação do ponto de extremidade. Você também pode usar nossas APIs REST ou qualquer um dos SDKs de cliente para fazer isso. As capturas de tela a seguir ilustram o processo por meio do Portal do Azure. 
  
![Adicionando um novo ponto de extremidade](./media/cdn-media-streaming-optimization/01_Adding.png)

*Figura 1: Adicionando um novo ponto de extremidade CDN do Perfil CDN*

![Streaming selecionado](./media/cdn-media-streaming-optimization/02_Creating.png) 
  
*Figura 2: Criando um Ponto de Extremidade CDN com streaming de mídia de vídeo por demanda selecionado* 
 
Depois de criar o ponto de extremidade CDN, ele aplicará a otimização para todos os arquivos que correspondem a certos critérios. A seção a seguir descreve isso em detalhes. 
 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Otimizações de streaming de mídia para Azure CDN do Akamai
 
A otimização de streaming de mídia do Akamai é eficaz para a mídia de streaming de VOD ou ao vivo que usa fragmentos de mídia individuais para entrega, em oposição a um único ativo grande transferido por download progressivo ou usando solicitações de intervalo de bytes. Para esse estilo de entrega de mídia, confira a [otimização de arquivo grande](cdn-large-file-optimization.md).

O uso do tipo de otimização de entrega de mídia geral ou de entrega de mídia de VOD usará uma rede CDN com otimizações de back-end para fornecer ativos de mídia mais rápido, bem como configurações de ativos de mídia com base em melhores práticas aprendidas ao longo do tempo.

### <a name="caching"></a>Cache

Se o Azure CDN do Akamai detectar que o ativo é um fragmento ou manifesto de streaming (veja a lista completa abaixo), o CDN usará tempos de expiração de cache diferentes da entrega geral. Como sempre, o controle de cache ou cabeçalhos de expiração enviados da origem serão respeitados e se o ativo não for um ativo de mídia, ele será armazenado em cache usando os tempos de expiração para entrega Web geral.

O tempo de cache negativo curto é útil para o descarregamento de origem quando muitos usuários solicitam um fragmento que ainda não existe, como uma transmissão ao vivo em que os pacotes não estão disponíveis na origem nesse segundo. O intervalo de cache mais longo também ajuda a descarregar as solicitações da origem, uma vez que o conteúdo de vídeo normalmente não é modificado.
 

|    | Geral<br> web<br>entrega contínua | Geral<br> mídia<br> streaming | VOD<br>mídia<br> streaming  
--- | --- | --- | ---
Cache – positivo <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 dias |365 dias | 365 dias   
Cache – negativo <br> HTTP 204, 305, 404, <br> e 405 | nenhum | 1 segundo | 1 segundo
 
### <a name="dealing-with-origin-failure"></a>Lidando com falhas de origem  

A entrega de mídia geral e a entrega de mídia de VOD também têm um log de tempo limite de origem e novas tentativas com base nas melhores práticas para padrões de solicitação típicos. Por exemplo, como a entrega de mídia geral é direcionada para a entrega de mídia de VOD e ao vivo, ela usa um tempo limite de conexão muito menor devido à natureza sensível ao tempo da transmissão ao vivo.

Quando uma conexão atingir o tempo limite, o CDN tentará novamente um determinado número de vezes antes de enviar o erro 504 Tempo Limite do Gateway para o cliente. 

Quando um arquivo corresponde ao tipo de arquivo e à lista de condições de tamanho, o CDN usa o comportamento de streaming de mídia e o fornecimento de Web geral caso contrário. 
   
### <a name="conditions-for-media-streaming-optimization"></a>Condições para otimização de streaming de mídia 

A tabela a seguir lista o conjunto de critérios a serem atendidos para essa otimização 
 
Tipos de streaming com suporte | Extensões de arquivo  
--- | ---  
HLS da Apple | m3u8, m3u, m3ub, key, ts, aac
HDS da Adobe | f4m, f4x, drmmeta, bootstrap, f4f,<br>Estrutura de URL Seg-Frag <br> (expressão regular correspondente: ^(/.*)Seq(\d+)-Frag(\d+)
DASH | mpd, dash, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Smooth Streaming | /manifest/,/QualityLevels/Fragments/
  

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Otimizações de streaming de mídia para Azure CDN da Verizon

O Azure CDN da Verizon pode fornecer ativos de mídia de streaming diretamente usando o tipo de otimização “entrega Web geral”. Também há alguns recursos no CDN por padrão que ajudam diretamente na entrega de ativos de mídia.

### <a name="partial-cache-sharing"></a>Compartilhamento de cache parcial

Isso permite que o conteúdo parcialmente em cache seja servido do CDN para novas solicitações. Isso significa que, por exemplo, a primeira solicitação para o CDN resulta em uma perda no cache e a solicitação é enviada para a origem. Enquanto esse conteúdo ainda está carregado no cache do CDN (mas ainda está incompleto), outras solicitações para o CDN podem começar a obter esses dados. 

### <a name="cache-fill-wait-time"></a>Tempo de espera de preenchimento do cache

Útil em conjunto com o compartilhamento de cache parcial, o recurso de tempo de espera do preenchimento do cache força o servidor de borda a manter quaisquer solicitações subsequentes para o mesmo recurso até os cabeçalhos de resposta HTTP chegarem do servidor de origem. Se os cabeçalhos de resposta HTTP do servidor de origem chegarem antes de o temporizador expirar, todas as solicitações que foram colocadas em espera serão atendidas do cache crescente (ao mesmo tempo que é preenchido pelos dados da origem). Por padrão, o Tempo de Espera de Preenchimento de Cache é definido como 3000 milissegundos. 


