---
title: A habilidade de pesquisa cognitiva condicional (Azure Search) | Microsoft Docs
description: Habilidade condicional que permite a filtragem, Criando padrões e mesclando valores.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 6a203a38437ccb6a9c325e6594289744e0148c84
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028419"
---
#   <a name="conditional-skill"></a>Habilidade condicional

O **habilidade condicional** permite uma variedade de cenários que exigem uma operação booliana para decidir os que devem ser atribuídos a uma saída de dados. Esses cenários incluem: filtragem, atribuindo um valor padrão e mesclando dados com base em uma condição.

O pseudocódigo a seguir explica o que realiza a habilidade de condicional:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Essa habilidade não está associada a uma API de Serviços Cognitivos e você não é cobrado por utilizá-la. No entanto, você ainda deverá [anexar um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md) para substituir a opção de recurso **Gratuito** que limita você a um pequeno número de enriquecimentos por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Campos avaliados

Essa habilidade é especial porque suas entradas são campos avaliados.

A seguir estão os valores válidos de uma expressão:

-   Caminhos de anotação (caminhos em expressões devem ser delimitados por "$(" and ")") <br/>
    Exemplos:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literais (cadeias de caracteres, números, true, false, null) <br/>
    Exemplos:
    ```
       "= 'this is a string'"   // string, note the single quotes
       "= 34"                   // number
       "= true"                 // boolean
       "= null"                 // null value
    ```

-  Expressões que usam um operador de comparação (= =,! =, > =, >, < =, <) <br/>
    Exemplos:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Expressões que usam operadores boolianos (& &, | |,!, ^) <br/>
    Exemplos:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Expressões que usam um operador numérico (+, -, \*, /, %) <br/>
    Exemplos: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Por causa de avaliação com suporte, a habilidade de condicional pode ser usada para cenários de transformação secundárias. Consulte a amostra [definição de habilidades 4](#transformation-examples) para obter um exemplo.

## <a name="skill-inputs"></a>Entradas de habilidades
As entradas diferenciam maiúsculas de minúsculas.

| Entradas      | DESCRIÇÃO |
|-------------|-------------|
| condition   | Essa entrada é um [avaliado campo](#evaluated-fields) que representa a condição a ser avaliada. Essa condição deve ser avaliada como um valor booliano (verdadeiro ou falso).   <br/>  Exemplos: <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | Essa entrada é um [avaliado campo](#evaluated-fields). O valor a ser retornado se a condição é avaliada como true. Cadeias de caracteres constantes devem ser retornadas em ' ' aspas. <br/>Valores de exemplo: <br/> "= 'contract'"<br/>"= $(/ documentos/contractType)" <br/> "= $(/documentos/entidades/\*)" <br/> |
| whenFalse   | Essa entrada é um [avaliado campo](#evaluated-fields). O valor a ser retornado se a condição é avaliada como false.  <br/>Valores de exemplo: <br/> "= 'contract'"<br/>"= $(/ documentos/contractType)" <br/> "= $(/documentos/entidades/\*)" <br/>

## <a name="skill-outputs"></a>Saídas de habilidades
Há uma única saída chamada 'output'. Ele retornará o valor de whenFalse se a condição for falsa ou whenTrue se a condição for verdadeira.

## <a name="examples"></a>Exemplos

### <a name="sample-skill-definition-1-filtering-documents-to-return-only-french-documents"></a>Definição da habilidade do exemplo 1: Filtragem de documentos para retornar apenas os documentos "Francês"

A saída a seguir retornará uma matriz de frases ("/ documentos/frenchSentences") se o idioma do documento estiver em francês. Se o idioma não for francesa, esse valor será definido como null.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Se "/ documentos/frenchSentences" é usado como o *contexto* de outra habilidade, essa habilidade será executada somente se "/ documentos/frenchSentences" não está definido como null


### <a name="sample-skill-definition-2-setting-a-default-value-when-it-does-not-exist"></a>Definição da habilidade do exemplo 2: Definindo um valor padrão quando não existe.

A saída a seguir criará uma anotação ("/ documentos/languageWithDefault") que é definida como a linguagem do documento, ou "es", se o idioma não está definido.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merging-values-from-two-different-fields-into-a-single-field"></a>Definição de habilidade de exemplo 3: Mesclando valores de dois campos diferentes em um único campo

Neste exemplo, algumas frases têm uma *frenchSentiment* propriedade. Sempre que o *frenchSentiment* propriedade for nula, gostaríamos de usar o *englishSentiment* valor. Podemos atribuir a saída a um membro chamado simplesmente *sentimento* ("/ documentos/sentimento / * / sentimento").

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-examples"></a>Exemplos de transformação
### <a name="sample-skill-definition-4-performing-data-transformations-on-a-single-field"></a>Definição da habilidade do exemplo 4: Executar transformações de dados em um único campo

Neste exemplo, recebemos um sentimento entre 0 e 1 e gostaríamos de transformá-lo para que fique entre -1 e 1. Isso é uma transformação matemática pequeno que poderíamos fazer usando a habilidade de condicional.

Neste exemplo específico, nunca usamos o aspecto condicional da habilidade como a condição é sempre verdadeira. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```


## <a name="special-considerations"></a>Considerações especiais
Observe que alguns dos parâmetros são avaliadas, portanto, você precisa ter um cuidado especial seguindo o padrão documentado. Expressões devem começar com um igual a entrada "=" e caminhos devem ser delimitados por "$(" and ")". Certifique-se de colocar suas cadeias de caracteres em 'aspas simples', pois isso ajudará o avaliador de distinguir entre cadeias de caracteres e caminhos reais e operadores. Além disso, certifique-se de colocar um espaço em branco em torno de operadores (por exemplo um * em um caminho tem um significado diferente do que o operador de multiplicação).


## <a name="next-steps"></a>Próximas etapas

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
