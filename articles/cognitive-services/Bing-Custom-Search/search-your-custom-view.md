---
title: Pesquisar uma exibição personalizada – Pesquisa Personalizada do Bing
titlesuffix: Azure Cognitive Services
description: Descreve como pesquisar um modo de exibição personalizado da Web.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: maheshb
ms.openlocfilehash: 58472d535be41fdd3e1139756f867f683509d9a3
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161728"
---
# <a name="call-your-custom-search"></a>Chamar sua pesquisa personalizada

Antes de fazer sua primeira chamada para a API de Pesquisa Personalizada para obter resultados de pesquisa para sua instância, você precisa obter uma chave de assinatura dos Serviços Cognitivos. Para obter uma chave de API de Pesquisa Personalizada, consulte [Testar os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).


## <a name="try-it-out"></a>Experimentar

Depois de configurar sua experiência de pesquisa personalizada, você pode testar a configuração a partir do portal de Pesquisa Personalizada. 

1. Entre na [Pesquisa Personalizada](https://customsearch.ai).
2. Clique em uma instância da Pesquisa Personalizada da sua lista de instâncias.
3. Clique na guia **Produção**. 
4. Na guia **Pontos de extremidade**, selecione um ponto de extremidade (por exemplo, API Web). Sua assinatura determina quais pontos de extremidade são mostrados (veja [preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) para conhecer as opções de assinatura). 
5. Especifique os valores de parâmetro. 

    Estes são os possíveis parâmetros que você pode definir (a lista real depende do ponto de extremidade selecionado). Para obter informações adicionais sobre esses parâmetros, consulte a referência [API de Pesquisa Personalizada](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).

    - **Consulta**: o termo de pesquisa a ser pesquisado. Disponível somente para pontos de extremidade da Web, de Imagem, de Vídeo e da Sugestão Automática.
    - **ID de Configuração Personalizada**: a ID de configuração da instância selecionada de Pesquisa Personalizada. Este campo é somente leitura.
    - **Mercado**: o mercado de onde vêm os resultados. Disponível somente para pontos de extremidade da Web, de Imagem, de Vídeo e de Interface do Usuário Hospedada.
    - **Chave de Assinatura**: a chave de assinatura para o teste. Você pode selecionar uma chave na lista suspensa ou inserir uma manualmente.  
      
    Clicar em **Parâmetros adicionais** revela os seguintes parâmetros:  
      
    - **Pesquisa Segura**: um filtro usado para filtrar as páginas da Web para conteúdo adulto. Disponível somente para pontos de extremidade da Web, de Imagem, de Vídeo e de Interface do Usuário Hospedada.
    - **Idioma da Interface do usuário**: o idioma usado para cadeias de caracteres da interface do usuário. Por exemplo, se você habilitar imagens e vídeos na interface do usuário hospedada, as guias **Imagem** e **Vídeo** usarão o idioma especificado.
    - **Contagem**: o número de resultados da pesquisa a ser retornado na resposta. Disponível somente para pontos de extremidade da Web, de Imagem e de Vídeo.
    - **Deslocamento**: o número de resultados da pesquisa a ignorar antes de retornar resultados. Disponível somente para pontos de extremidade da Web, de Imagem e de Vídeo.

6. Depois de especificar todas as opções necessárias, clique em **Chamar** para exibir a resposta JSON no painel direito. 

Se você selecionar o ponto de extremidade da Interface do Usuário Hospedada, poderá testar a experiência de pesquisa no painel inferior.

## <a name="next-steps"></a>Próximas etapas

- [Chamar o modo de exibição personalizado com C#](./call-endpoint-csharp.md)
- [Chamar o modo de exibição personalizado com Java](./call-endpoint-java.md)
- [Chamar o modo de exibição personalizado com NodeJs](./call-endpoint-nodejs.md)
- [Chamar o modo de exibição personalizado com Python](./call-endpoint-python.md)

- [Chamar a exibição personalizada com o SDK do C#](./sdk-csharp-quick-start.md)