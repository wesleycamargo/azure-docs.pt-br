---
title: "Otimizar a distribuição de conteúdo do Azure para seu cenário"
description: "Como otimizar a distribuição de conteúdo para cenários específicos"
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: rli
ms.openlocfilehash: fa4cf306eeb1a8372da5b2a86ac73fbba2832666
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2018
---
# <a name="optimize-azure-content-delivery-for-your-scenario"></a>Otimizar a distribuição de conteúdo do Azure para seu cenário

Quando você distribui conteúdo para um público global grande, é essencial garantir a distribuição otimizada do conteúdo. A Rede de Distribuição de Conteúdo do Azure pode otimizar a experiência de distribuição de acordo com o tipo de conteúdo existente. O conteúdo pode ser um site, uma transmissão ao vivo, um vídeo ou um arquivo grande para download. Quando você cria um ponto de extremidade CDN (rede de distribuição de conteúdo), você especifica um cenário na opção **Otimizado para**. Sua escolha determina qual otimização é aplicada ao conteúdo distribuído do ponto de extremidade CDN.

As opções de otimização foram projetadas para usar os comportamentos de melhores práticas, a fim de melhorar o desempenho de distribuição de conteúdo e obter um melhor descarregamento da origem. Suas escolhas de cenário afetam o desempenho modificando as configurações de cache parcial, o agrupamento de objeto e a política de repetição de falha da origem. 

Este artigo fornece uma visão geral dos diversos recursos de otimização e quando eles devem ser usados. Para obter mais informações sobre os recursos e as limitações, consulte os respectivos artigos em cada tipo de otimização individual.

> [!NOTE]
> As opções **Otimizado para** podem variar de acordo com o provedor selecionado. Os provedores de CDN aplicam a melhoria de maneiras diferentes, dependendo do cenário. 

## <a name="provider-options"></a>Opções de provedor

A **Rede de Distribuição de Conteúdo do Azure da Akamai** tem suporte para as seguintes otimizações:

* [Entrega na Web geral ](#general-web-delivery) 

* [Streaming de mídia geral](#general-media-streaming)

* [Streaming de mídia de vídeo sob demanda](#video-on-demand-media-streaming)

* [Download de arquivo grande](#large-file-download)

* [Aceleração de site dinâmica](#dynamic-site-acceleration) 

A **Rede de Distribuição de Conteúdo do Azure da Akamai** tem suporte para as seguintes otimizações:

* [Entrega na Web geral](#general-web-delivery) (também pode ser usado para streaming de mídia e conteúdo de download de arquivo grande)

* [Aceleração de site dinâmica](#dynamic-site-acceleration) 

É altamente recomendável testar as variações de desempenho entre diferentes provedores para escolher o provedor ideal para a distribuição.

## <a name="select-and-configure-optimization-types"></a>Selecionar e configurar os tipos de otimização

Para criar um novo ponto de extremidade, selecione um tipo de otimização que melhor corresponda ao cenário e ao tipo de conteúdo que você deseja que seja distribuído pelo ponto de extremidade. **Distribuição na Web geral** é a seleção padrão. Para pontos de extremidade existentes da **Rede de Distribuição de Conteúdo do Azure da Akamai**, é possível atualizar a opção de otimização a qualquer momento. Essa alteração não interrompe a distribuição da CDN. 

1. Em um perfil da **Rede de Entrega de Conteúdo do Azure da Akamai**, selecione um ponto de extremidade.

    ![Seleção de ponto de extremidade ](./media/cdn-optimization-overview/01_Akamai.png)

2. Em **CONFIGURAÇÕES**, selecione **Otimização**. Em seguida, selecione um tipo na lista suspensa **Otimizado para**.

    ![Otimização e seleção de tipo](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Otimização para cenários específicos

Você pode otimizar o ponto de extremidade CDN para um desses cenários. 

### <a name="general-web-delivery"></a>Distribuição na Web geral

A distribuição na Web geral é a opção mais comum de otimização. Ela foi projetada para otimização de conteúdo da Web geral, como páginas da Web e aplicativos Web. Essa otimização também pode ser usada para downloads de arquivo e vídeo.

Um site típico contém conteúdo estático e dinâmico. O conteúdo estático inclui imagens, bibliotecas JavaScript e folhas de estilo que podem ser armazenadas em cache e distribuídas para diferentes usuários. O conteúdo dinâmico é personalizado para um usuário individual, como itens de notícias adaptados a um perfil do usuário. O conteúdo dinâmico não é armazenado em cache, pois é exclusivo para cada usuário, como o conteúdo do carrinho de compras. A distribuição na Web geral pode otimizar todo o site. 

> [!NOTE]
> Se você usar a **Rede de Distribuição de Conteúdo do Azure da Akamai**, talvez deseje usar essa otimização caso o tamanho médio do arquivo for menor que 10 MB. Se o tamanho médio do arquivo for maior que 10 MB, selecione **Download de arquivos grandes** na lista suspensa **Otimizado para**.

### <a name="general-media-streaming"></a>Streaming de mídia geral

Se você precisar usar o ponto de extremidade para transmissão ao vivo e streaming de vídeo por demanda, recomendamos a otimização de streaming de mídia geral.

O streaming de mídia é sensível ao tempo, pois os pacotes que chegam tarde no cliente podem causar uma experiência de exibição degradada, como buffer frequente do conteúdo de vídeo. A otimização de streaming de mídia reduz a latência da distribuição de conteúdo de mídia e fornece uma experiência de streaming suave para os usuários. 

Esse cenário é comum para clientes do serviço de mídia do Azure. Quando você usa os serviços de mídia do Azure, você pode obter um ponto de extremidade de streaming, que pode ser usado para streaming sob demanda e ao vivo. Neste cenário, os clientes não precisam alternar para outro ponto de extremidade quando alteram de streaming sob demanda para ao vivo. A otimização de streaming de mídia geral dá suporte a esse tipo de cenário.

A **Rede de Distribuição de Conteúdo do Azure da Verizon** usa o tipo de otimização da Web geral para entregar conteúdo de mídia de streaming.

Para obter mais informações sobre a otimização de streaming de mídia, consulte [Otimização de streaming de mídia](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Streaming de mídia de vídeo por demanda

A otimização de streaming de mídia de vídeo por demanda melhora o conteúdo de streaming de vídeo por demanda. Se você usar um ponto de extremidade para streaming de vídeo por demanda, talvez você deseje usar essa opção.

A **Rede de Distribuição de Conteúdo do Azure da Verizon** usa o tipo de otimização da Web geral para distribuir conteúdo de mídia de streaming.

Para obter mais informações sobre a otimização de streaming de mídia, consulte [Otimização de streaming de mídia](cdn-media-streaming-optimization.md).

> [!NOTE]
> Se o ponto de extremidade fornecer principalmente o conteúdo de vídeo por demanda, use esse tipo de otimização. A principal diferença entre essa otimização e a otimização de streaming de mídia geral é o tempo limite de repetição de conexão. O tempo limite é muito menor para trabalhar com cenários de transmissão ao vivo.

### <a name="large-file-download"></a>Download de arquivos grandes

Se você usar a **Rede de Entrega de Conteúdo do Azure do Akamai**, você deve usar o download de arquivo grande para distribuir arquivos maiores do que 1,8 GB. A **Rede de Distribuição de Conteúdo do Azure da Verizon** não tem uma limitação de tamanho de download de arquivo em sua otimização geral de distribuição na Web.

Se você usar a **Rede de Distribuição de Conteúdo do Azure da Akamai**, os downloads de arquivos grandes são otimizados para conteúdo de mais de 10 MB. Se o tamanho médio do arquivo for menor que 10 MB, talvez você deseje usar a distribuição na Web geral. Se os tamanhos médios dos arquivos forem consistentemente maiores que 10 MB, talvez seja mais eficiente criar um ponto de extremidade separado para arquivos grandes. Por exemplo, atualizações de firmware ou software geralmente são arquivos grandes.

A **Rede de Distribuição de Conteúdo do Azure da Verizon** usa o tipo de otimização da Web geral para distribuir conteúdo de download de arquivo grande.

Para saber mais sobre a otimização de arquivos grandes, consulte [Otimização de arquivos grandes](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Aceleração de site dinâmica

 A aceleração de site dinâmico está disponível nos perfis da **Rede de Distribuição de Conteúdo do Azure do Akamai** e da **Rede de Distribuição de Conteúdo do Azure da Verizon**. Essa otimização envolve um valor adicional para ser usado; para obter mais informações, consulte [Preço da Rede de Distribuição de Conteúdo](https://azure.microsoft.com/pricing/details/cdn/).

A aceleração de site dinâmica inclui várias técnicas que trazem vantagens para a latência e o desempenho do conteúdo dinâmico. As técnicas incluem otimização de rota e de rede, otimização de TCP e muito mais. 

Use essa otimização para acelerar um aplicativo Web que inclui várias respostas que não são armazenáveis em cache. Entre os exemplos estão resultados da pesquisa, transações de check-out ou dados em tempo real. Continue usando as principais funcionalidades de cache CDN para dados estáticos. 

Para obter mais informações sobre aceleração de site dinâmico, consulte [Aceleração de site dinâmico](cdn-dynamic-site-acceleration.md).



