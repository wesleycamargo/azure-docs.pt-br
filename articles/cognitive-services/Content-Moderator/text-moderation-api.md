---
title: Content Moderator do Azure - Moderação de texto | Microsoft Docs
description: Use a moderação de texto para possíveis textos indesejados, PII e listas de termos personalizada.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/30/2018
ms.author: sajagtap
ms.openlocfilehash: 5783a7a06d75a409969abad011de3bbd31dec292
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363799"
---
# <a name="text-moderation"></a>Moderação de texto

Use a moderação de texto assistida por computador do Content Moderator e os recursos [human-in-the-loop](Review-Tool-User-Guide/human-in-the-loop.md) para moderar o conteúdo de texto.

As empresas usam o serviço de moderação de texto para bloquear, aprovar ou analisar o conteúdo com base em suas políticas e limites. O serviço de moderação de texto pode ser usado para aumentar a moderação humana de ambientes que exigem que parceiros, funcionários e consumidores gerem conteúdo de texto. Isso inclui salas de chat, fóruns de discussão, chatbots, catálogos de comércio eletrônico, documentos e muito mais. 

A API examina o texto recebido (máximo de 1024 caracteres) para conteúdo ofensivo, classifica o possível texto indesejado (versão prévia), corrige automaticamente o texto e detecta PII (Informações de Identificação Pessoal). Ele também corresponde a listas de termos personalizada. O recurso de autocorreção ajuda a capturar deliberadamente palavras com erros ortográficos. Depois que o conteúdo é processado, o serviço retorna uma resposta detalhada. Você usa a resposta para criar uma análise humana na ferramenta de análise ou removê-la, etc.

A resposta de serviço inclui as informações a seguir:

- Conteúdo ofensivo: correspondência baseada em termos com lista interna de termos ofensivos em vários idiomas
- Classificação: classificação assistida por computador em três categorias
- PII (Informações de Identificação Pessoal)
- Texto corrigido automaticamente
- Texto original
- Linguagem

## <a name="profanity"></a>Conteúdo ofensivo

Se a API detectar termos ofensivos em qualquer um dos [idiomas com suporte](Text-Moderation-API-Languages.md), esses termos serão incluídos na resposta. A resposta também contém a localização (`Index`) no texto original. O `ListId` no JSON de exemplo a seguir refere-se aos termos encontrados nas [listas de termos personalizadas](try-terms-list-api.md), se disponíveis.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Para o parâmetro **language**, atribua `eng` ou deixe-o vazio para ver a resposta de **classificação** assistida por computador (versão prévia do recurso). **Este recurso dá somente para inglês**.
>
> Para detecção de **termos obscenos**, use o [código ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) dos idiomas com suporte listados neste artigo, ou deixe-o vazio.

## <a name="classification"></a>classificação

O **recurso de classificação de texto** assistido por computador do Content Moderator dá suporte **somente em inglês** e ajuda a detectar conteúdo potencialmente indesejado. O conteúdo sinalizado pode ser considerado inadequado, dependendo do contexto. Além de transmitir a probabilidade de cada categoria, pode recomendar uma análise humana do conteúdo. O recurso utiliza um modelo treinado para identificar possíveis linguagem abusiva, depreciativa ou discriminatória. Isso inclui gírias, palavras abreviadas, palavras ofensivas e intencionalmente incorretas para análise. 

A extração a seguir na extração JSON mostra uma saída de exemplo:

> [!NOTE]
> O recurso 'Classificação' assistido por computador está em versão prévia.

    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
            },
        "Category2": {
            "Score": 0.12747249007225037
            },
        "Category3": {
            "Score": 0.98799997568130493
        }
    }

### <a name="explanation"></a>Explicação

- `Category1` representa a presença potencial de linguagem que pode ser considerada sexualmente explícita ou de conteúdo para adulto em determinadas situações.
- `Category2` representa a presença potencial de linguagem que pode ser considerada sexualmente sugestiva ou para adulto em determinadas situações.
- `Category3` representa a presença potencial de linguagem que pode ser considerada ofensiva em determinadas situações.
- `Score` é entre 0 e 1. Quanto maior a pontuação, maior o modelo estará prevendo que a categoria pode ser aplicável. Essa versão prévia depende de um modelo estatístico, em vez de resultados codificados manualmente. É recomendável testar com o seu próprio conteúdo para determinar como cada categoria alinha-se aos seus requisitos.
- `ReviewRecommended` é verdadeiro ou falso, dependendo dos limites da pontuação interna. Os clientes devem avaliar se desejam usar esse valor ou escolher limites personalizados com base nas políticas de conteúdo.

## <a name="personally-identifiable-information-pii"></a>PII (Informações de Identificação Pessoal)

O recurso PII detecta a presença potencial dessa informação:

- Endereço de email
- Endereço para correspondência
- Endereço IP
- Número de telefone dos EUA
- Número de telefone no Reino Unido
- CPF

O exemplo a seguir mostra uma resposta de exemplo:

    "PII": {
        "Email": [{
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 32
            }],
        "IPA": [{
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 72
            }],
        "Phone": [{
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 56
            }, {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 212
            }, {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 208
            }, {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 228
            }, {
            "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 245
            }],
        "Address": [{
            "Text": "1 Microsoft Way, Redmond, WA 98052",
            "Index": 89
            }],
        "SSN": [{
            "Text": "999999999",
            "Index": 56
            }, {
            "Text": "999-99-9999",
            "Index": 267
            }]
        }

## <a name="auto-correction"></a>Correção automática

Suponha que o texto de entrada seja (o 'lzay' e 'f0x' são intencionais):

    The qu!ck brown f0x jumps over the lzay dog.

Se você solicitar correção automática, a resposta conterá a versão corrigida do texto:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Criar e gerenciar as listas de termos personalizadas

Embora a lista global de termos padrão funcione muito bem na maioria dos casos, convém filtrar os termos específicos das necessidades da sua empresa. Por exemplo, é possível querer filtrar qualquer marca competitiva das postagens dos usuários. O limite de conteúdo de texto permitido pode ser diferente da lista padrão.

> [!NOTE]
> Há um limite máximo de **5 listas de termos** com cada lista para **não exceder 10.000 termos**.
>

O exemplo a seguir mostra a ID da lista correspondente:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

O Content Moderator fornece uma [API de Lista de Termos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) com operações para gerenciar listas de termos personalizadas. Inicie com o [Console de API de Listas de Termos](try-terms-list-api.md) e use os exemplos de código de API REST. Consulte também o [Início rápido do .NET das Listas de Termos](term-lists-quickstart-dotnet.md), se você estiver familiarizado com Visual Studio e C#.

## <a name="next-steps"></a>Próximas etapas

Faça test drive do [Console de API de moderação de texto](try-text-api.md) e use os exemplos de código de API REST. Consulte também o [Início rápido do .NET de moderação de texto](text-moderation-quickstart-dotnet.md), se você estiver familiarizado com Visual Studio e C#.
