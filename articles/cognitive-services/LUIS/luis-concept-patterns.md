---
title: Padrões ajudam na previsão
titleSuffix: Language Understanding - Azure Cognitive Services
description: Um padrão permite que você alcance maior precisão para uma intenção sem fornecer muitos enunciados a mais.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: diberry
ms.openlocfilehash: ed7f50d51b46b9e6204522751fdc1a1e996442f2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207564"
---
# <a name="patterns-improve-prediction-accuracy"></a>Padrões aumentam a precisão da previsão
Os padrões são definidos para aumentar a precisão quando várias expressões são muito similares.  Um padrão permite que você alcance maior precisão para uma intenção sem fornecer muitos enunciados a mais. 

## <a name="patterns-solve-low-intent-confidence"></a>Os padrões resolvem baixa confiabilidade de intenção
Considere um aplicativo de recursos humanos que relata o organograma em relação a um funcionário. Dado o nome e a relação de um funcionário, LUIS retorna os funcionários envolvidos. Considere um funcionário, Tom, com uma gerente chamada Alice e uma equipe de subordinados chamados: Michael, Rebecca e Carl.

![Imagem do organograma](./media/luis-concept-patterns/org-chart.png)

|Declarações|Intenção prevista|Pontuação da intenção|
|--|--|--|
|Quem está subordinado a Tom?|GetOrgChart|.30|
|Quem é subordinado de Tom?|GetOrgChart|.30|

Se um aplicativo tiver entre 10 e 20 expressões com comprimentos diferentes de frase, ordem de palavras diferentes e diferentes palavras (sinônimos de "subordinadas", "gerente", "relatório"), LUIS pode retornar uma baixa pontuação de confiabilidade. Para ajudar LUIS a reconhecer a importância da ordem de palavras, crie um padrão. 

Os padrões resolvem as situações a seguir: 

* Quando a pontuação da intenção é baixa
* Quando a intenção correta não é a pontuação superior, mas está muito próximo à pontuação superior. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Os padrões não são uma garantia de intenção
Os padrões usam uma mistura de tecnologias de previsão. Configurar uma intenção para uma expressão de modelo em um padrão não é uma garantia da previsão da intenção, mas é um sinal forte. 

## <a name="patterns-do-not-improve-entity-detection"></a>Os padrões não melhoram a detecção de entidade
Enquanto padrões requerem entidades, um padrão não ajuda a detectar a entidade. Um padrão destina-se apenas a ajudar a previsão com funções e intenções.  

Não espere ver melhorias na previsão de entidade se você recolher várias declarações em um único padrão. Para entidades simples serem acionadas, você precisará adicionar declarações ou usar entidades de lista; caso contrário, seu padrão não será acionado.

## <a name="patterns-use-entity-roles"></a>Os padrões usam funções de entidades
Se duas ou mais entidades em um padrão estiverem relacionadas contextualmente, os padrões usarão as [funções](luis-concept-roles.md) da entidade para extrair informações contextuais sobre as entidades. Isso é equivalente a filhos hierárquicos de entidade, mas está **somente** disponível em padrões. 

## <a name="prediction-scores-with-and-without-patterns"></a>Pontuação da previsão com e sem padrões
Dadas expressões de exemplo suficientes, LUIS poderá aumentar a confiabilidade da previsão sem os padrões. Os padrões de aumentam a pontuação de confiabilidade sem ter que fornecer tantas expressões.  

## <a name="pattern-matching"></a>Correspondência de padrões
Um padrão é correspondido com base na detecção das entidades dentro do padrão primeiro, em seguida, validando o restante das palavras e a ordem de palavras do padrão. As entidades são exigidas no padrão para que um padrão corresponda. O padrão é aplicado no nível de token, não no nível do caractere. 

## <a name="pattern-syntax"></a>Sintaxe de padrões
Sintaxe de padrão é um modelo para uma expressão. O modelo deverá conter palavras e entidades que você deseja corresponder, bem como palavras e pontuação que você deseja ignorar. **Não** é uma expressão regular. 

As entidades em padrões estão entre chaves, `{}`. Os padrões podem incluir entidades e entidades com funções. Pattern.Any é uma entidade usada apenas em padrões. A sintaxe é explicada nas seções a seguir.

### <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Sintaxe para adicionar uma entidade em um modelo de padrão
Para adicionar uma entidade no modelo de padrão, coloque o nome de entidade entre chaves, como `Who does {Employee} manage?`. 

|Padrão com entidade|
|--|
|`Who does {Employee} manage?`|

### <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Sintaxe para adicionar uma entidade e função em um modelo de padrão
Uma função de entidade é denotada como `{entity:role}` com o nome de entidade, seguido por dois-pontos e, em seguida, o nome da função. Para adicionar uma entidade com uma função no modelo de padrão, coloque o nome de entidade e o nome da função entre chaves, como `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Padrão com funções de entidade|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

### <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Sintaxe para adicionar um pattern.any em um modelo de padrão
A entidade Pattern.any permite que você adicione uma entidade de comprimento variado para o padrão. O pattern.any pode ser qualquer comprimento desde que o modelo de padrão seja seguido. 

Para adicionar uma entidade **Pattern.any** no modelo padrão, coloque a entidade Pattern.any com as chaves, como `How much does {Booktitle} cost and what format is it available in?`.  

|Padrão com a entidade Pattern.any|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Títulos de livro no padrão|
|--|
|Quanto custa **roubar este livro** e em que formato está disponível?|
|Quanto custa **perguntar** e em que formato está disponível?|
|Quanto custa **O curioso incidente do cachorro no tempo de noite** e em que formato está disponível?| 

Nestes exemplos de título de livro, as palavras contextuais do título do livro não são confusas para LUIS. LUIS sabe onde o título de livro termina porque está em um padrão e marcado com a entidade Pattern.any.

### <a name="explicit-lists"></a>Listas explícitas
Se o padrão contém um Pattern.any e a sintaxe padrão permite a possibilidade de uma extração de entidade incorreto com base na expressão, crie uma [lista explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) por meio da API de criação para permitir a exceção. 

Por exemplo, supondo que você tem um padrão que com sintaxe opcional, `[]`, e sintaxe de entidade, `{}`, combinadas de uma forma para extrair dados incorretamente.

Considere o padrão '[localizar] email sobre {assunto} [de {pessoa}]'. Nas expressões a seguir, as entidades **assunto** e **pessoa** são extraídas correta e incorretamente:

|Enunciado|Entidade|Extração correta|
|--|--|:--:|
|email sobre cachorros de Chris|assunto=cachorros<br>pessoa=Chris|✔|
|email sobre o homem de La Mancha|assunto=o homem<br>pessoa=La Mancha|X|

Na tabela anterior, a expressão `email about the man from La Mancha`, o assunto deveria ser `the man from La Mancha` (um título de livro), mas como o assunto inclui a palavra opcional `from`, o título é previsto incorretamente. 

Para corrigir esta expressão no padrão, adicione `the man from la mancha` como uma correspondência de lista explícita para a entidade {assunto} usando a [API de criação para lista explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

### <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Sintaxe para marcar texto opcional em uma expressão de exemplo
Marque texto opcional na expressão usando a sintaxe colchete de expressão regular, `[]`. O texto opcional pode aninhar colchetes até apenas dois colchetes.

|Padrão com texto opcional|
|--|
|`[find] email about {subject} [from {person}]`|

Sinais de pontuação, como `.`, `!`, e `?` podem ser ignorados com os colchetes. Para ignorar essas marcas, cada marca deve estar em um padrão separado. A sintaxe opcional atualmente não dá suporte para ignorar um item em uma lista de vários itens.

## <a name="patterns-only"></a>Somente padrões
LUIS permite um aplicativo sem qualquer expressão de exemplo na intenção. Esse uso é permitido somente se os padrões são usados. Os padrões requerem no mínimo uma entidade em cada padrão. Para um aplicativo somente padrão, o padrão não deve conter entidades de aprendizado de máquina porque elas requerem expressões de exemplo. 

## <a name="best-practices"></a>Práticas recomendadas
Conheça as [práticas recomendadas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como implementar padrões neste tutorial](luis-tutorial-pattern.md)
