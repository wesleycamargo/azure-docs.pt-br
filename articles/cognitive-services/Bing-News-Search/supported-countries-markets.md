---
title: Países e idiomas compatíveis na API de Pesquisa de Notícias do Bing no Azure | Microsoft Docs
description: Descubra quais países e idiomas são compatíveis com a API de Pesquisa de Imagem do Bing.
services: cognitive-services
author: MikeDodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 12/20/2017
ms.author: v-gedod
ms.openlocfilehash: 80326d66e509b64efd5d386fe793bc9942b29ae3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363524"
---
# <a name="bing-news-search-countries-and-languages"></a>Países e idiomas da Pesquisa de Notícias do Bing

A API de Pesquisa de Notícias do Bing dá suporte a vários países, muitos com mais de um idioma. Basicamente, a especificação de um país com uma consulta serve para refinar os resultados da pesquisa com base nos interesses nesse país. Além disso, os resultados podem conter links para o Bing, e esses links podem localizar a experiência do usuário do Bing de acordo com o país ou o idioma especificado.

Você pode especificar um país usando o parâmetro de consulta `cc`. Se você especificar um país, também precisará especificar um ou mais códigos de idioma usando o cabeçalho HTTP `Accept-Language`. Os idiomas compatíveis variam conforme o país; eles são fornecidos para cada país na tabela Mercados.

Como alternativa, você pode especificar o mercado usando o parâmetro de consulta `mkt` e um código da tabela **Mercados**. Especificar um mercado simultaneamente especifica um país e um idioma preferencial. O parâmetro de consulta `setLang` pode ser definido para um código de idioma nesse caso; geralmente, é o mesmo idioma especificado pelo `mkt`, a menos que o usuário prefira consultar o Bing em outro idioma.

## <a name="supported-markets-for-news-search-endpoint"></a>Mercados com suporte para ponto de extremidade de pesquisa de notícias

Para o ponto de extremidade `/news/search`, a tabela a seguir lista os valores de código de mercado que você pode usar para especificar o parâmetro de consulta `mkt`. O Bing retorna o conteúdo somente para esses mercados. A lista está sujeita a alterações.  
  
Para uma lista de códigos de país que você pode especificar no parâmetro de consulta `cc`, consulte [Códigos de país](#countrycodes).  
  
|País/Região|Linguagem|Código de mercado|  
|---------------------|--------------|-----------------| 
|Dinamarca|Dinamarquês|da-DK|
|Áustria|Alemão|de-AT| 
|Suíça|Alemão|de-CH|
|Alemanha|Alemão|de-DE|
|Austrália|Inglês|en-AU|
|Canadá|Inglês|en-CA|
|Reino Unido|Inglês|en-GB|
|Indonésia|Inglês|en-ID|
|Irlanda|Inglês|en-IE|
|Índia|Inglês|en-IN|
|Malásia|Inglês|en-MY|
|Nova Zelândia|Inglês|en-NZ|
|República das Filipinas|Inglês|en-PH|
|Cingapura|Inglês|en-SG|
|Estados Unidos|Inglês|pt-BR|
|Inglês|geral|en-WW|
|Inglês|geral|en-XA|
|África do Sul|Inglês|en-ZA|
|Argentina|Espanhol|es-AR|
|Chile|Espanhol|es-CL|
|Espanha|Espanhol|es-ES|
|México|Espanhol|es-MX|
|Estados Unidos|Espanhol|es-US| 
|Espanhol|geral|es-XL|
|Finlândia|Finlandês|fi-FI|  
|França|Francês|fr-BE|
|Canadá|Francês|fr-CA| 
|Bélgica|Holandês|nl-BE|
|Suíça|Francês|fr-CH|
|França|Francês|fr-FR|  
|Itália|Italiano|it-IT| 
|RAE de Hong Kong|Chinês tradicional|zh-HK|  
|Taiwan|Chinês tradicional|zh-TW|
|Japão|Japonês|ja-JP|  
|Coreia do Sul|Coreano|ko-KR|  
|Países Baixos|Holandês|nl-NL|  
|República Popular da China|Chinês|zh-CN|  
|Brasil|Português|pt-BR|
|Rússia|Russo|ru-RU|  
|Suécia|Sueco|sv-SE|  
|Turquia|Turco|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Mercados com suporte para ponto de extremidade de notícias
Para o ponto de extremidade `/news`, a tabela a seguir lista os valores de código de mercado que você pode usar para especificar o parâmetro de consulta `mkt`. O Bing retorna o conteúdo somente para esses mercados. A lista está sujeita a alterações.  
  
Para uma lista de códigos de país que você pode especificar no parâmetro de consulta `cc`, consulte [Códigos de país](#countrycodes).  
  
|País/Região|Linguagem|Código de mercado|  
|---------------------|--------------|-----------------| 
|Dinamarca|Dinamarquês|da-DK|
|Alemanha|Alemão|de-DE|
|Austrália|Inglês|en-AU|
|Reino Unido|Inglês|en-GB|
|Estados Unidos|Inglês|pt-BR|
|Inglês|geral|en-WW|
|Chile|Espanhol|es-CL|
|México|Espanhol|es-MX|
|Estados Unidos|Espanhol|es-US| 
|Finlândia|Finlandês|fi-FI|  
|Canadá|Francês|fr-CA|
|França|Francês|fr-FR|  
|Itália|Italiano|it-IT| 
|Brasil|Português|pt-BR|
|República Popular da China|Chinês|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Mercados com suporte para ponto de extremidade de tendências de notícias
Para o ponto de extremidade `/news/trendingtopics`, a tabela a seguir lista os valores de código de mercado que você pode usar para especificar o parâmetro de consulta `mkt`. O Bing retorna o conteúdo somente para esses mercados. A lista está sujeita a alterações.  
  
Para uma lista de códigos de país que você pode especificar no parâmetro de consulta `cc`, consulte [Códigos de país](#countrycodes).  
  
|País/Região|Linguagem|Código de mercado|  
|---------------------|--------------|-----------------| 
|Alemanha|Alemão|de-DE|
|Austrália|Inglês|en-AU|
|Reino Unido|Inglês|en-GB|
|Estados Unidos|Inglês|pt-BR|
|Canadá|Inglês|en-CA|
|Índia|Inglês|en-IN|
|França|Francês|fr-FR|
|Canadá|Francês|fr-CA|
|Brasil|Português|pt-BR|
|República Popular da China|Chinês|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Códigos de país  

A seguir estão os códigos de país que você pode especificar no parâmetro de consulta `cc`. A lista está sujeita a alterações.  
  
|País/Região|Código do país|  
|---------------------|------------------|  
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
|RAE de Hong Kong|HK|  
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
|República Popular da China|CN|  
|Polônia|PL|  
|Portugal|PT|  
|República das Filipinas|PH|  
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

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre os pontos de extremidade da Pesquisa de Notícias do Bing, confira [Referência da API de Pesquisa de Notícias v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).