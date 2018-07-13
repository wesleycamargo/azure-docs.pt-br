---
title: 'Pesquisa Personalizada do Bing: pesquisar uma exibição personalizada | Microsoft Docs'
description: Descreve como pesquisar um modo de exibição personalizado da Web
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 75f6c8d299c7eed901dda0631fca74b040f72e30
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363841"
---
# <a name="call-your-custom-search"></a>Chamar sua pesquisa personalizada
Antes de fazer sua primeira chamada para a API de Pesquisa Personalizada para obter resultados de pesquisa para sua instância, você precisa obter uma chave de assinatura dos Serviços Cognitivos. Para obter uma chave de API de Pesquisa Personalizada, consulte [Testar os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).

> [!NOTE]
> Os clientes da Pesquisa Personalizada do Bing que têm a chave da versão prévia provisionada em ou antes de 15 de outubro de 2017 poderão usar suas chaves até 30 de novembro de 2017 ou até que eles tenham usado o número máximo de consultas permitidas. Depois disso, eles precisam migrar para a versão geralmente disponível no Azure.

## <a name="try-it-out"></a>Experimentar
Depois de configurar sua experiência de pesquisa personalizada, você pode testar a configuração a partir do portal de Pesquisa Personalizada. Entre na [Pesquisa Personalizada](https://customsearch.ai), clique em uma instância da Pesquisa Personalizada e clique na guia **Produção**. A guia **Pontos de Extremidade** é exibida. Sua assinatura determinará quais pontos de extremidade estão disponíveis para tentar, veja [páginas de preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/). Para testar um ponto de extremidade, selecione-o na lista suspensa e defina as opções de configuração associadas. 

A seguir estão as opções disponíveis.

- **Consulta**: o termo de pesquisa a ser pesquisado. Disponível somente para pontos de extremidade da Web, de Imagem e da Sugestão Automática.
- **ID de Configuração Personalizada**: a ID de configuração da instância selecionada de Pesquisa Personalizada. Este campo é somente leitura.
- **Mercado**: o mercado de onde vêm os resultados. Disponível somente para pontos de extremidade da Web, de Imagem e da Interface do Usuário Hospedada.
- **Chave de Assinatura**: a chave de assinatura para o teste. Você pode selecionar uma chave na lista suspensa ou inserir uma manualmente.
- **Pesquisa Segura**: um filtro usado para filtrar as páginas da Web para conteúdo adulto. Disponível somente para pontos de extremidade da Web, de Imagem e da Interface do Usuário Hospedada.
- **Contagem**: o número de resultados da pesquisa a ser retornado na resposta. Disponível somente para pontos de extremidade da Web e de Imagem.
- **Deslocamento**: o número de resultados da pesquisa a ser retornado na resposta. Disponível somente para pontos de extremidade da Web e de Imagem.

Depois de especificar todas as opções necessárias para Web, Imagem ou Sugestão Automática, clique em **Chamar** para exibir a resposta JSON no painel direito. 

Se você selecionar o ponto de extremidade da Interface do Usuário Hospedada, poderá testar a experiência de pesquisa do painel direito.

## <a name="next-steps"></a>Próximas etapas
- [Chamar o modo de exibição personalizado com C#](./call-endpoint-csharp.md)
- [Chamar o modo de exibição personalizado com Java](./call-endpoint-java.md)
- [Chamar o modo de exibição personalizado com NodeJs](./call-endpoint-nodejs.md)
- [Chamar o modo de exibição personalizado com Python](./call-endpoint-python.md)