---
title: Testar em lote seu aplicativo LUIS – Reconhecimento vocal
titleSuffix: Azure Cognitive Services
description: Use o teste em lote para trabalhar continuamente em seu aplicativo para aprimorá-lo e melhorar o reconhecimento do idioma.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 414862d7ba1956f9449fde3ba063906fae6ef01b
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041601"
---
# <a name="batch-testing-in-luis"></a>Teste em lote no LUIS

O teste em lote valida o modelo treinado [ativo](luis-concept-version.md#active-version) para medir a precisão da previsão. Um teste em lote ajuda a exibir a precisão de cada intenção e entidade no modelo treinado atual em um gráfico. Examine os resultados do teste em lote para tomar a devida ação para melhorar a precisão, como adicionar mais declarações de exemplo a uma intenção, caso seu aplicativo falhe com frequência em identificar a intenção correta.

## <a name="group-data-for-batch-test"></a>Agrupar dados para o teste em lote
É importante que as declarações usadas para o teste em lote sejam novas no LUIS. Se você tiver um conjunto de dados de declarações, divida-as em três conjuntos: declarações adicionadas para um propósito, declarações recebidas do ponto de extremidade publicado e declarações usadas para testar em lote o LUIS depois dele ser treinado. 

## <a name="a-dataset-of-utterances"></a>Um conjunto de dados de declarações
Envie um arquivo de declarações em lote, conhecido como *conjunto de dados*, para o teste em lote. O conjunto de dados é um arquivo no formato JSON que contém um máximo de 1.000 declarações **não duplicadas** rotuladas. Você pode testar até 10 conjuntos de dados em um aplicativo. Se precisar testar mais, exclua um conjunto de dados e adicione um novo.

|**Regras**|
|--|
|*Nenhuma declaração duplicada|
|Sem filhos hierárquicos da entidade|
|1.000 declarações ou menos|

*As duplicatas serão consideradas correspondências da cadeia de caracteres exatas, não correspondências sinalizadas com token primeiro. 

## <a name="entities-allowed-in-batch-tests"></a>Entidades permitidas nos testes em lotes
As entidades incluem pais hierárquicos e composição simples. Todas as entidades desses tipos aparecem no filtro de entidades de teste em lote, mesmo que não haja entidades correspondentes no arquivo em lotes.


<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>
## <a name="batch-file-format"></a>Formato do arquivo em lote
O arquivo em lote consiste em declarações. Cada declaração deve ter uma previsão de intenção esperada junto com qualquer [entidade aprendida por máquina](luis-concept-entity-types.md#types-of-entities) que você espera detectar. 

A seguir, é apresentado um exemplo de um arquivo em lotes com a sintaxe adequada:

```JSON
[
  {
    "text": "Are there any janitorial jobs currently open?",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 14,
            "endPos": 23
        }
    ]
  },
  {
    "text": "I would like a fullstack typescript programming with azure job",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 15,
            "endPos": 46
        }
    ]
  },
  {
    "text": "Is there a database position open in Los Colinas?",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 11,
            "endPos": 18
        }
    ]
  },
  {
    "text": "Please find database jobs open today in Seattle",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 12,
            "endPos": 19
        }
    ]
  }
]
```

## <a name="batch-syntax-template"></a>Modelo de sintaxe em lotes

Use o modelo a seguir para iniciar o arquivo em lotes:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

O arquivo em lotes usa as propriedades **startPos** e **endPos** para observar o início e o fim de uma entidade. Os valores são baseados em zero e não devem iniciar ou terminar em um espaço. 

Isso é diferente dos logs de consulta, que usam as propriedades startIndex e endIndex. 


## <a name="common-errors-importing-a-batch"></a>Erros comuns ao importar um lote
Os erros comuns incluem: 

> * Mais de 1.000 declarações
> * Um objeto JSON de declaração que não tem uma propriedade de entidades
> * Palavra(s) rotulada(s) em várias entidades

## <a name="batch-test-state"></a>Estado do teste em lote
O LUIS controla o estado do último teste de cada conjunto de dados. Isso inclui o tamanho (número de declarações no lote), data da última execução e último resultado (número de declarações previstas com êxito).

<a name="sections-of-the-results-chart"></a>
## <a name="batch-test-results"></a>Resultados do teste em lote
O resultado do teste em lote é um gráfico de dispersão, conhecido como matriz de erro. Esse gráfico é uma comparação de 4 vias das declarações no arquivo, intenção prevista do modelo atual e entidades. 

Os pontos de dados nas seções **Falso Positivo** e **Falso Negativo** indicam os erros que devem ser investigados. Se todos os pontos de dados estiverem nas seções **Verdadeiro Positivo** e **Verdadeiro Negativo**, então a precisão de seu aplicativo será perfeita nesse conjunto de dados.

![Quatro seções do gráfico](./media/luis-concept-batch-test/chart-sections.png)

Esse gráfico ajuda a encontrar as declarações que o LUIS prevê incorretamente com base em seu treinamento atual. Os resultados são exibidos por região do gráfico. Selecione os pontos individuais no gráfico para examinar as informações de declaração ou selecione o nome da região para examinar os resultados da declaração nessa região.

![Teste em lote](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Erros nos resultados
Os erros no teste em lote indicam as intenções que não estão previstas, como indicado no arquivo em lote. Os erros são indicados nas duas seções vermelhas do gráfico. 

A seção de falso positivo indica que uma declaração correspondeu a uma intenção ou entidade quando não deveria. O falso negativo indica que uma declaração não correspondeu a uma intenção ou entidade quando deveria. 

## <a name="fixing-batch-errors"></a>Corrigindo erros em lote
Se houver erros no teste em lote, você poderá adicionar mais declarações a uma intenção e/ou rotular mais declarações com a entidade para ajudar o LUIS a diferenciar as intenções. Se você adicionou declarações, rotulou e ainda recebe erros de previsão no teste em lote, considere adicionar um recurso de [lista de frases](luis-concept-feature.md) com um vocabulário específico do domínio para ajudar o LUIS a aprender mais rápido. 

## <a name="next-steps"></a>Próximas etapas

* Saiba como [testar um lote](luis-how-to-batch-test.md)
