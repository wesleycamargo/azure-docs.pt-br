---
title: Testar em lote seu aplicativo LUIS - Azure | Microsoft Docs
description: Use o teste em lote para trabalhar continuamente em seu aplicativo para aprimorá-lo e melhorar o reconhecimento do idioma.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 3803df32d6431b8413e8df0837ed62b2e4344cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364007"
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

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>
## <a name="batch-file-format"></a>Formato do arquivo em lote
O arquivo em lote consiste em declarações. Cada declaração deve ter uma previsão de intenção esperada junto com qualquer [entidade aprendida por máquina](luis-concept-entity-types.md#types-of-entities) que você espera detectar. 

Segue um exemplo de arquivo em lote:

   [!code-json[Valid batch test](~/samples-luis/documentation-samples/batch-testing/travel-agent-1.json)]


## <a name="common-errors-importing-a-batch"></a>Erros comuns ao importar um lote
Os erros comuns incluem: 

> * Mais de 1.000 declarações
> * Um objeto JSON de declaração que não tem uma propriedade de entidades

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