---
title: Analisadores no Azure Search | Microsoft Docs
description: "Atribua analisadores a campos de pesquisa de texto pesquisáveis em um índice para substituir Lucene Standard padrão com alternativas personalizadas específicas a um idioma ou predefinidas."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/03/2017
ms.author: heidist
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: 6e6c4491b8f66011340d1246495dbded7caf2903
ms.contentlocale: pt-br
ms.lasthandoff: 09/05/2017

---

# <a name="analyzers-in-azure-search"></a>Analisadores no Azure Search

Um *analisador* é um componente do [processamento da pesquisa de texto completo](search-lucene-query-architecture.md) responsável por conversões de texto para token para indexação e para cargas de trabalho de consulta. Durante a indexação, um analisador transforma o texto em termos editáveis, que são gravados no índice. No momento da consulta, um analisador realiza as mesmas transformações (texto em termos com token), mas, desta vez para operações de leitura durante consultas. 

As transformações a seguir são comuns durante a análise:

+ Palavras não essenciais (palavras irrelevantes) e sinais de pontuação são removidos.
+ Frases e termos hifenizados são divididos em partes componentes.
+ Os termos ficam em minúsculas.
+ As palavras são reduzidas para formas raiz, de modo que uma correspondência pode ser encontrada independentemente da conjugação.

O Azure Search fornece um analisador padrão. Você pode substituí-lo em uma base campo por campo com opções alternativas. A finalidade deste artigo é descrever a faixa de opções e fornecer melhores práticas para a adição de um analisador para suas operações de pesquisa. Ele também mostra exemplos de configurações do analisador para os principais cenários.

## <a name="how-analysis-fits-into-full-text-search-processing"></a>Como a análise se encaixa no processamento da pesquisa de texto completo

Analisadores operam em entradas de termo passadas pelo analisador de consulta e retornam termos analisados que são adicionados a um objeto da árvore de consulta.

 ![Diagrama da arquitetura de consulta do Lucene no Azure Search][1]

Os analisadores são usados somente em uma consulta de termo simples ou uma consulta de frase. Os analisadores não são usados para tipos de consulta com termos incompletos – consulta de prefixo, consulta de caractere curinga, consulta regex – ou em consultas difusas. Para esses tipos de consulta, os termos são adicionados diretamente à árvore de consulta, ignorando o estágio da análise. A única transformação realizada em termos de consulta desses tipos é colocá-los em letras minúsculas.

## <a name="supported-analyzers"></a>Analisadores com suporte

A lista a seguir descreve quais analisadores têm suporte no Azure Search.

| Categoria | Descrição |
|----------|-------------|
| [Analisador Lucene padrão](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Padrão. Usada automaticamente para indexação e consultas. Nenhuma especificação ou a configuração é necessária. Esse analisador de uso geral funciona bem para a maioria dos idiomas e cenários.|
| Analisadores predefinidos | Oferecidos como um produto acabado se destina a ser usado no estado em que se encontra, com personalização limitada. <br/>Há dois tipos: especializado e de idioma. O que os torna "predefinidos" é que você os referencia por nome, sem nenhuma personalização. <br/><br/>[Analisadores especializados (independentes de idioma)](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) para entradas de texto que exigem processamento especializados ou o mínimo de processamento. Analisadores de idioma não predefinidos incluem **Asciifolding**, **Palavra-chave**, **Padrão**, **Simples**, **Interromper**, **Espaço em branco**.<br/><br/>[Analisadores de idioma](https://docs.microsoft.com/rest/api/searchservice/language-support) fornecem suporte linguístico avançado para idiomas individuais. O Azure Search dá suporte a 35 analisadores de idioma Lucene e 50 analisadores de processamento de idioma natural Microsoft. |
|[Analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Uma configuração definida pelo usuário de uma combinação de elementos existentes, consistindo de um tokenizador (obrigatório) e filtros opcionais (char ou token).|

Você pode personalizar um analisador predefinido, como **Padrão** ou **Interromper**, para usar opções alternativas documentadas em [Referência do analisador predefinido](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable). Apenas alguns dos analisadores predefinidos têm opções que você pode definir. Como com qualquer personalização, forneça sua nova configuração com um nome, como *myPatternAnalyzer* para distingui-lo do analisador de Padrões Lucene.

## <a name="how-to-specify-analyzer"></a>Como especificar o analisador

1. Para apenas analisadores personalizados, crie uma definição `analyzer` do índice. Para obter mais informações, consulte [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index) e também [Analisadores Personalizados > Criar](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer).

2. Em cada campo para o qual você deseja usar o analisador, defina a propriedade `analyzer` para o nome de um analisador de destino em um [definição de campo no índice](https://docs.microsoft.com/rest/api/searchservice/create-index). Os valores válidos incluem um analisador predefinido, um analisador de idioma ou um analisador personalizado definido anteriormente no esquema de índice.

 Como alternativa, em vez de uma propriedade `analyzer`, você pode definir diferentes analisadores para indexação e consulta usando os parâmetros de campo `indexAnalyzer` e `searchAnalyzer`. 

3. Recompile o índice para invocar os novos comportamentos de processamento de texto.

## <a name="best-practices"></a>Práticas recomendadas

Esta seção fornece conselhos sobre como trabalhar com analisadores com mais eficiência.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Um analisador para leitura/gravação, a menos que você tenha requisitos específicos

O Azure Search permite especificar diferentes analisadores para indexação e pesquisa através dos parâmetros de campo adicionais `indexAnalyzer` e `searchAnalyzer`. Se não forem especificados, o analisador definido com a propriedade `analyzer` é usado para indexação e pesquisa. Se `analyzer` não for especificado, o analisador padrão para Lucene Standard será usado.

Uma regra geral é usar o mesmo analisador para indexação e consulta, a menos que requisitos específicos estabeleçam o contrário. Normalmente, é mais eficiente se o analisador criando o token for o mesmo usado para localizar símbolos posteriormente durante o tempo de consulta. 

### <a name="test-during-active-development"></a>Testar durante o desenvolvimento ativo

Substituir o analisador padrão requer um rebuild de índice. Se possível, escolha os analisadores a usar durante o desenvolvimento ativo antes de acumular um índice para produção.

### <a name="compare-analyzers-side-by-side"></a>Comparar analisadores lado a lado

É recomendável usar a [API de análise](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). A resposta consiste em termos com token, gerados por um analisador específico para o texto fornecido por você. 

> [!Tip]
> A [demonstração do analisador de pesquisa](http://alice.unearth.ai/) mostra uma comparação lado a lado do analisador Lucene padrão, do analisador de idioma inglês do Lucene e do processador de idioma inglês natural da Microsoft. Para cada entrada de pesquisa fornecida por você, resultados de cada analisador são exibidos nos painéis adjacentes.

## <a name="examples"></a>Exemplos

Os exemplos abaixo mostram definições do analisador para alguns cenários principais.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>Exemplo 1: opções personalizadas

Este exemplo ilustra uma definição de analisador com opções personalizadas. Opções personalizadas para filtros de caractere, tokenizadores e filtros de token são especificadas separadamente como constructos nomeados e, depois, referenciadas na definição do analisador. Elementos predefinidos são usados no estado em que se encontram e simplesmente referenciados por nome.

Percorrendo este exemplo:

* Analisadores são uma propriedade da classe do campo para um campo pesquisável.
* Um analisador personalizado faz parte de uma definição de índice. Ele pode ser levemente personalizado (por exemplo, personalizando uma única opção em um filtro) ou personalizado em vários locais.
* Nesse caso, o analisador personalizado é "my_analyzer" que, por sua vez, usa um tokenizador padrão personalizado "my_standard_tokenizer" e dois filtros de token: filtro de letras minúsculas e filtro asciifolding personalizado "my_asciifolding".
* Ele também define um filtro de caractere personalizado "map_dash" para substituir todos os traços com sublinhados antes da geração de tokens (o tokenizer padrão faz uma quebra ao encontrar traço, mas não ao encontrar sublinhado).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Example2"></a>
### <a name="example-2-override-the-default-analyzer"></a>Exemplo 2: substituir o analisador padrão

O analisador Standard é o padrão. Suponha que você deseja substituir o padrão com um analisador predefinido diferente, como o analisador de padrões. Se você não está definindo opções personalizadas, você só precisa especificá-lo pelo nome na definição do campo.

O elemento "analisador" substitui o analisador Standard, campo por campo. Não há nenhuma substituição global. Neste exemplo, `text1` usa o analisador de padrões; já `text2`, que não especifica um analisador, usa o padrão.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Example3"></a>
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>Exemplo 3: analisadores diferentes para operações de indexação e pesquisa

As APIs de versão prévia incluem atributos de índice adicionais para especificar diferentes analisadores para indexação e pesquisa. Os atributos `searchAnalyzer` e `indexAnalyzer` devem ser especificados como um par, substituindo o atributo `analyzer` único.


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Example4"></a>
### <a name="example-4-language-analyzer"></a>Exemplo 4: analisador de linguagem

Campos que contêm cadeias de caracteres em idiomas diferentes podem usar um analisador de idioma, enquanto outros campos mantêm o padrão (ou usam algum outro analisador predefinido ou personalizado). Se você usar um analisador de idioma, ele deverá ser usado para operações de indexação e de pesquisa. Campos que usam um analisador de linguagem não podem ter analisadores diferentes para indexação e pesquisa.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "IndexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="next-steps"></a>Próximas etapas

+ Examine nossa explicação abrangente sobre [como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md). Esse artigo usa exemplos para explicar comportamentos que, à primeira vista, podem parecer contraintuitivos.

+ Tente outras sintaxes de consulta a partir da seção de exemplo [Pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) da [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) no gerenciador de pesquisa no portal.

+ Saiba como aplicar [analisadores léxicos específico do idioma](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Configurar analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) para o mínimo de processamento ou processamento especializado em campos individuais.

+ [Compare os analisadores padrão e inglês](http://alice.unearth.ai/) em painéis adjacentes neste site da Web de demonstração. 

## <a name="see-also"></a>Consulte também

 [API REST para pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Sintaxe de consulta Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [Controlar os resultados da pesquisa](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png

