---
title: Países e idiomas compatíveis na API de Pesquisa de Imagem do Bing no Azure | Microsoft Docs
description: Descubra quais países e idiomas são compatíveis com a API de Pesquisa de Imagem do Bing.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: d0d33ee714ba5cd1ce4e846b96c04f755933bee1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364391"
---
# <a name="bing-image-search-countries-and-languages"></a>Países e idiomas da Pesquisa de Imagem do Bing

A API de Pesquisa de Imagem do Bing dá suporte a mais de trinta países, muitos com mais de um idioma. Basicamente, a especificação de um país com uma consulta serve para refinar os resultados da pesquisa com base nos interesses nesse país. Além disso, os resultados podem conter links para o Bing, e esses links podem localizar a experiência do usuário do Bing de acordo com o país ou o idioma especificado.

Para especificar o país e o idioma, defina o parâmetro de consulta `mkt` (mercado) com um código da tabela **Mercados** abaixo. O mercado especifica um país e um idioma. Se o usuário preferir ver e exibir o texto em outro idioma, defina o parâmetro de consulta `setLang` com o código de idioma apropriado.

Como alternativa, você pode especificar o país usando o parâmetro de consulta `cc`. Se você especificar um país, também precisará especificar um ou mais códigos de idioma usando o cabeçalho HTTP `Accept-Language`. Os idiomas compatíveis variam conforme o país; eles são fornecidos para cada país na tabela Mercados.

> [!NOTE]
> Atualmente, a API de Imagens Populares dá suporte apenas aos seguintes mercados:
> - en-US (inglês, Estados Unidos) 
> - en-CA (inglês, Canadá) 
> - en-AU (inglês, Austrália) 
> - zh-CN (chinês, China)

## <a name="countries"></a>Países

|País|Código|
|-------|----|
|Argentina|AR|
|Austrália|AU|
|Áustria|AT|
|Bélgica|BE|
|Brasil|BR|
|Canadá|CA|
|Chile|CL|
|Dinamarca|DK|
|Finlândia|FI|
|França|FR|
|Alemanha|DE|
|Hong Kong|HK|
|Índia|IN|
|Indonésia|ID|
|Itália|IT|
|Japão|JP|
|Coreia do Sul|KR|
|Malásia|MY|
|México|MX|
|Países Baixos|NL|
|Nova Zelândia|NZ|
|Noruega|NÃO|
|China|CN|
|Polônia|PL|
|Portugal|PT|
|Filipinas|PH|
|Rússia|RU|
|Arábia Saudita|SA|
|África do Sul|ZA|
|Espanha|ES|
|Suécia|SE|
|Suíça|CH|
|Taiwan|TW|
|Turquia|TR|
|Reino Unido|GB|
|Estados Unidos|EUA|


## <a name="markets"></a>Mercados

|País|Linguagem|Código de mercado|
|-------|--------|-----------|
|Argentina|Espanhol|es-AR|
|Austrália|Inglês|en-AU|
|Áustria|Alemão|de-AT|
|Bélgica|Holandês|nl-BE|
|Bélgica|Francês|fr-BE|
|Brasil|Português|pt-BR|
|Canadá|Inglês|en-CA|
|Canadá|Francês|fr-CA|
|Chile|Espanhol|es-CL|
|Dinamarca|Dinamarquês|da-DK|
|Finlândia|Finlandês|fi-FI|
|França|Francês|fr-FR|
|Alemanha|Alemão|de-DE|
|Hong Kong|Chinês tradicional|zh-HK|
|Índia|Inglês|en-IN|
|Indonésia|Inglês|en-ID|
|Itália|Italiano|it-IT|
|Japão|Japonês|ja-JP|
|Coreia do Sul|Coreano|ko-KR|
|Malásia|Inglês|en-MY|
|México|Espanhol|es-MX|
|Países Baixos|Holandês|nl-NL|
|Nova Zelândia|Inglês|en-NZ|
|China|Chinês|zh-CN|
|Polônia|Polonês|pl-PL|
|Portugal|Português|pt-PT|
|Filipinas|Inglês|en-PH|
|Rússia|Russo|ru-RU|
|Arábia Saudita|Árabe|ar-SA|
|África do Sul|Inglês|en-ZA|
|Espanha|Espanhol|es-ES|
|Suécia|Sueco|sv-SE|
|Suíça|Francês|fr-CH|
|Suíça|Alemão|de-CH|
|Taiwan|Chinês tradicional|zh-TW|
|Turquia|Turco|tr-TR|
|Reino Unido|Inglês|en-GB|
|Estados Unidos|Inglês|pt-BR|
|Estados Unidos|Espanhol|es-US|

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre os pontos de extremidade da Pesquisa de Notícias do Bing, confira [Referência da API de Pesquisa de Imagem v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).
