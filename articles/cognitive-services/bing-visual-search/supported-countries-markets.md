---
title: Países e idiomas compatíveis na API de Pesquisa Visual do Bing | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Descubra quais países e idiomas são compatíveis com a API de Pesquisa Visual do Bing.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 4723d028cc22caf8be3eb294b52506ec112cbab5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364271"
---
# <a name="bing-visual-search-countries-and-languages"></a>Países e idiomas da Pesquisa Visual do Bing

A API de Pesquisa Visual do Bing dá suporte a mais de trinta países, muitos com mais de um idioma. Cada solicitação deve incluir o país e o idioma de preferência do usuário. O conhecimento do mercado do usuário ajuda o Bing a retornar resultados apropriados. Se você não especificar um país e um idioma, o Bing se esforçará para determinar o país e o idioma do usuário. Como os resultados podem conter links para o Bing, o conhecimento do país e do idioma poderá fornecer uma experiência do usuário preferencial localizada do Bing, caso o usuário clique nos links do Bing.

Para especificar o país e o idioma, defina o parâmetro de consulta `mkt` (mercado) com um código da tabela **Mercados** abaixo. O mercado especifica um país e um idioma. Se o usuário preferir ver e exibir o texto em outro idioma, defina o parâmetro de consulta `setLang` com o código de idioma apropriado.

Como alternativa, você pode especificar o país usando o parâmetro de consulta `cc`. Se você especificar um país, também precisará especificar um ou mais códigos de idioma usando o cabeçalho HTTP `Accept-Language`. Os idiomas compatíveis variam conforme o país; eles são fornecidos para cada país na tabela Mercados.



> [!NOTE]
> As seguintes restrições de mercado se aplicam:
> 
> - As anotações de reconhecimento de imagem só estão disponíveis em inglês. 
> - Insights de receitas, compras e páginas que incluem o termo de pesquisa só estão disponíveis no mercado en-US. 


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
