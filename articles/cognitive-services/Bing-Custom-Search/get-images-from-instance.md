---
title: Obter imagens da sua visualização personalizada | Microsoft Docs
titleSuffix: Cognitive Services
description: Visão geral de alto nível sobre o uso da Pesquisa Personalizada do Bing para obter imagens de sua exibição personalizada da Web.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: ba836bbafaf67238664862ee2afce7840a573e44
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953873"
---
# <a name="get-images-from-your-custom-view"></a>Obter imagens do modo de exibição personalizado

A Pesquisa de Imagens Personalizadas do Bing permite que você aprimore sua experiência de pesquisa personalizada com imagens. Semelhante aos resultados da Web, a pesquisa personalizada oferece suporte à pesquisa de imagens na lista de sites da sua instância. Você pode obter as imagens usando a API de pesquisa de imagens personalizadas do Bing ou por meio do recurso de interface do usuário hospedada. Usar o recurso da interface do host é simples de usar e recomendado para colocar sua experiência de pesquisa em funcionamento rapidamente.  Para obter informações sobre como configurar sua UI hospedada para incluir imagens, consulte [Configure sua experiência de UI hospedada](hosted-ui.md).

Se você quiser mais controle sobre a exibição dos resultados da pesquisa, poderá usar a API de pesquisa de imagens personalizadas do Bing. Como chamar a API é semelhante a chamar a API de pesquisa de imagens do Bing, confira [Pesquisa de Imagens do Bing](../Bing-Image-Search/overview.md) para obter exemplos chamando a API. Mas, antes disso, familiarize-se com o [conteúdo da referência da API de pesquisa de imagens personalizadas](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference). As principais diferenças são os parâmetros de consulta suportados (você deve incluir o parâmetro de consulta customConfig) e o terminal para o qual envia solicitações.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->