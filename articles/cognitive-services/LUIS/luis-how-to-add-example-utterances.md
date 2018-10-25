---
title: Adicionar enunciados de exemplo em aplicativos LUIS
titleSuffix: Azure Cognitive Services
description: Saiba como adicionar enunciados em aplicativos LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: adb44dcc8c41b1a7846ff346d141dc0c4b028e96
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888281"
---
# <a name="add-example-utterances-and-label-with-entities"></a>Adicionar enunciados de exemplo e rótulo com entidades

Enunciados de exemplo são exemplos de texto de comandos ou perguntas do usuário. Para ensinar o LUIS (Serviço Inteligente de Reconhecimento Vocal), você precisa adicionar [enunciados de exemplo](luis-concept-utterance.md) a uma [intenção](luis-concept-intent.md).

Em geral, adicione um enunciado de exemplo a uma intenção primeiro, então crie entidades e rotule enunciados na página de intenção. Se você preferir criar entidades primeiro, veja [Adicionar entidades](luis-how-to-add-entities.md).

## <a name="add-an-utterance"></a>Adicionar um enunciado
Na página de intenções, insira um enunciado relevante que você espera dos seus usuários, como `book 2 adult business tickets to Paris tomorrow on Air France` na caixa de texto abaixo do nome da intenção, e pressione Enter. 
 
>[!NOTE]
>O LUIS converte todos os enunciados em letras minúsculas.

![Captura de tela da página Detalhes de intenções, com o enunciado realçado](./media/luis-how-to-add-example-utterances/add-new-utterance-to-intent.png) 

Os enunciados são adicionados à lista de enunciados para a intenção atual. 

## <a name="ignoring-words-and-punctuation"></a>Ignorando palavras e pontuação
Se você quiser ignorar palavras específicas ou pontuação no enunciado de exemplo, use um [padrão](luis-concept-patterns.md#pattern-syntax) com a sintaxe _ignore_. 

## <a name="add-simple-entity-label"></a>Adicionar rótulo de entidade simples
No procedimento a seguir, você cria e rotula entidades personalizadas dentro do enunciado a seguir na página de intenção:

```
book me 2 adult business tickets to Paris tomorrow on Air France
```

1. Selecione "Air France" no enunciado para rotulá-lo como uma entidade simples.

    > [!NOTE]
    > Ao selecionar palavras para rotulá-las como entidades:
    > * Para uma única palavra, apenas selecione-a. 
    > * Para um conjunto de duas ou mais palavras, selecione no início e então no fim do conjunto.

2. Na caixa suspensa da entidade que é exibida, você pode selecionar uma entidade existente ou adicionar uma nova entidade. Para adicionar uma nova entidade, digite seu nome na caixa de texto e, em seguida, selecione **Criar nova entidade**. 
 
    ![Captura de tela da página Detalhes de intenções, com a opção de rotulagem de entidade simples realçada](./media/luis-how-to-add-example-utterances/create-airline-simple-entity.png)

3. Na caixa de diálogo pop-up **Que tipo de entidade você deseja criar?**, verifique o nome da entidade e selecione o tipo de entidade simples, então selecione **Concluído**.

    ![Imagem da caixa de diálogo de confirmação](./media/luis-how-to-add-example-utterances/create-simple-airline-entity.png)

    Veja [Extração de Dados](luis-concept-data-extraction.md#simple-entity-data) para saber mais sobre como extrair a entidade simples da resposta da consulta JSON do ponto de extremidade. Experimente o [início rápido](luis-quickstart-primary-and-secondary-data.md) da entidade simples para saber mais sobre como usar uma entidade simples.


## <a name="add-list-entity-and-label"></a>Adicionar entidade de lista e rótulo
Entidades de lista representam um conjunto fixo e fechado (correspondências de texto exato) de palavras relacionadas no seu sistema. 

Para uma entidade de lista de bebidas, você pode ter dois valores normalizados: água e refrigerante. Cada nome normalizado tem sinônimos. Para água, os sinônimos são H20, gás, simples. Para refrigerante, os sinônimos são fruta, cola, refresco. Você não precisa conhecer todos os valores ao criar a entidade. Você pode adicionar mais depois de revisar os enunciados reais do usuário com sinônimos.

|Nome normalizado|Sinônimos|
|--|--|
|Água|H20, gás, simples|
|Refrigerante|Fruta, cola, refresco|

Ao criar uma nova entidade de lista usando a página de intenção, você está fazendo duas coisas que podem não ser óbvias. Primeiro, você está criando uma nova lista adicionando o primeiro item de lista. Segundo, o primeiro item de lista é nomeado com a palavra ou frase selecionada no enunciado. Embora você possa alterar isso mais tarde na página de entidade, pode ser mais rápido selecionar um enunciado que tenha a palavra que você deseja para o nome do item de lista.

Por exemplo, se você quisesse criar uma lista de tipos de bebida e tivesse selecionado a palavra `h2o` do enunciado para criar a entidade, a lista teria um item, cujo nome seria h20. Se você quisesse um nome mais genérico, deveria escolher um enunciado que usasse o nome mais genérico. 

1. No enunciado, selecione a palavra que é o primeiro item na lista e, em seguida, digite o nome da lista na caixa de texto e selecione **Criar nova entidade**.   

    ![Captura de tela da página Detalhes de intenções, com Criar nova entidade realçada](./media/luis-how-to-add-example-utterances/create-drink-list-entity.png)

2. Na caixa de diálogo **Que tipo de entidade você deseja criar?**, adicionar sinônimos desse item de lista. Para o item de água em uma lista de bebidas, adicione `h20`, `perrier` e `waters` e selecione **Concluído**. Observe que "águas" é adicionado porque os sinônimos da lista são combinados no nível do token. Na cultura inglesa, esse nível estará no nível da palavra, assim "águas" não corresponderia a "água", a menos que ela estivesse na lista. 

    ![Captura de tela da caixa de diálogo Que tipo de entidade você deseja criar](./media/luis-how-to-add-example-utterances/drink-list-ddl.png)

    Esta lista de bebidas tem apenas um tipo de bebida: água. Você pode adicionar mais tipos de bebida rotulando outros enunciados ou editando a entidade em **Entidades** no painel de navegação esquerdo. [Editar](luis-how-to-add-entities.md#add-list-entities) as entidades dá as opções de inserir itens adicionais com sinônimos correspondentes ou [importar](luis-how-to-add-entities.md#import-list-entity-values) uma lista. 

    Veja [Extração de Dados](luis-concept-data-extraction.md#list-entity-data) para saber mais sobre como extrair entidades de lista da resposta da consulta JSON do ponto de extremidade. Experimente o [início rápido](luis-quickstart-intent-and-list-entity.md) para saber mais sobre como usar uma entidade de lista.

## <a name="add-synonyms-to-the-list-entity"></a>Adicionar sinônimos à entidade de lista 
Adicione um sinônimo à entidade da lista selecionando a palavra ou a frase no enunciado. Se você tiver uma entidade de Lista de bebidas e quiser adicionar `agua` como sinônimo de água, siga as etapas:

No enunciado, selecione a palavra sinônimo, como `aqua` para água, então selecione o nome da entidade de lista na lista suspensa, como **Bebida**, selecione **Definir como sinônimo** e selecione o item de lista do qual ela é sinônimo, como **água**.

![Captura de tela da página de Detalhes de intenções, com Criar uma nova visualização normalizada realçado](./media/luis-how-to-add-example-utterances/set-agua-as-synonym.png)

## <a name="create-new-item-for-list-entity"></a>Criar novo item para a entidade de lista
Crie um novo item para uma entidade de lista existente selecionando a palavra ou a frase no enunciado. Se você tiver uma lista de Funcionários e quiser adicionar `Bob Smith` como um novo item, siga as etapas:

No enunciado, selecione a palavra ou frase para o novo item de lista, como `Bob Smith`, e selecione o nome da entidade de lista na lista suspensa, como **Funcionário** e selecione **Criar uma nova visualização normalizada**. 

![Captura de tela de adicionar novo item de lista](./media/luis-how-to-add-example-utterances/list-entity-create-new-item.png)

A palavra agora está realçada em azul. Se você passar o mouse sobre a palavra, uma marcação será exibida mostrando o nome do item de lista, como chá.

![Captura de tela da nova marcação de item de lista](./media/luis-how-to-add-example-utterances/list-entity-item-name-tag.png)

## <a name="wrap-entities-in-composite-label"></a>Encapsular entidades no rótulo composto
Entidades compostas são criadas com base em **Entidades**. Você não pode criar uma entidade composta usando a página Intenção. Depois que a entidade composta for criada, você poderá encapsular as entidades em um enunciado na página de Intenção. 

Presumindo o enunciado, `book 2 tickets from Seattle to Cairo`, um enunciado composto pode retornar informações de entidade dos locais de contagem de tíquetes (2), da origem (Seattle) e do destino (Cairo) em uma entidade única pai. 

Siga estas [etapas](luis-how-to-add-entities.md#add-prebuilt-entity) para adicionar a entidade predefinida **número**. Depois que a entidade é criada, o `2` no enunciado fica azul, indicando que é uma entidade rotulada. Entidades predefinidas são rotuladas pelo LUIS. Você não pode adicionar nem remover o rótulo de entidade predefinida de um único enunciado. Você somente pode adicionar ou remover todos os rótulos predefinidos adicionando ou removendo a entidade predefinida do aplicativo.

Siga estas [etapas](#add-hierarchical-entity-and-label) para criar uma entidade hierárquica de **Local**. Rótulo nos locais de origem e destino no enunciado de exemplo. 

Antes de encapsular as entidades em uma entidade composta, garanta que todas as entidades filho estejam realçadas em azul, o que significa que elas foram rotuladas no enunciado.

1. Para encapsular as entidades individuais em uma composição, selecione a primeira entidade rotulada no enunciado para a entidade composta. No enunciado de exemplo, `book 2 tickets from Seattle to Cairo`, a primeira entidade é o número 2. Uma lista suspensa é exibida mostrando as opções para essa seleção.

    ![Captura de tela de opções suspensas e selecionadas por número realçadas](./media/luis-how-to-add-example-utterances/wrap-1.png)

2. Selecione **Encapsular entidade composta** na lista suspensa. 

    ![Captura de tela de opções de menu suspenso para encapsular entidade composta com Encapsular na entidade composta realçada](./media/luis-how-to-add-example-utterances/wrap-2.png)

3. Selecione a última palavra da entidade composta. No enunciado deste exemplo, selecione "Location::Destination" (representando Cairo). A linha verde agora está sob todas as palavras, incluindo palavras não de entidade, no enunciado que constituem a composição.

    ![Captura de tela da página Intenção de BookFlight, com número realçado](./media/luis-how-to-add-example-utterances/wrap-composite.png)

4. Selecione o nome da entidade composta na lista suspensa. Para este exemplo, isso é **TicketOrder**.

    ![Captura de tela de quebra de palavras com entidade composta, com o nome da entidade composta realçado na lista suspensa](./media/luis-how-to-add-example-utterances/wrap-4.png)

    Ao encapsular as entidades corretamente, uma linha verde fica sob toda a frase.

    ![Captura de tela de enunciado a entidade composta realçada](./media/luis-how-to-add-example-utterances/wrap-5.png)

    Veja [Extração de Dados](luis-concept-data-extraction.md#composite-entity-data) para saber mais sobre como extrair a entidade composta da resposta da consulta JSON do ponto de extremidade. Experimente o [tutorial](luis-tutorial-composite-entity.md) da entidade composta para saber mais sobre como usar uma entidade composta.

## <a name="add-hierarchical-entity-and-label"></a>Adicionar uma entidade hierárquica e rótulo
Uma entidade hierárquica é uma categoria de entidades contextualmente aprendidas e conceitualmente relacionadas. No exemplo a seguir, a entidade contém locais de origem e de destino. 

No enunciado `Book 2 tickets from Seattle to Cairo`, Seattle é o local de origem e Cairo é o local de destino. Cada local é contextualmente diferente e aprendido pela escolha de palavras e pela ordem de palavras no enunciado.

1. Na página Intenção, no enunciado, selecione "Seattle" e insira o nome da entidade de Local e, em seguida, selecione **Criar nova entidade**.

    ![Captura de tela da caixa de diálogo Criar Rotulagem da Entidade Hierárquica](./media/luis-how-to-add-example-utterances/create-hier-ent-1.png)

2. Na caixa de diálogo pop-up, selecione hierárquica para **Tipo de entidade** e, em seguida, adicione `Origin` e `Destination` como filhos e selecione **Concluído**.

    ![Captura de tela da página de detalhes de Intenções, com a entidade ToLocation realçada](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

3. A palavra no enunciado foi rotulada com a entidade hierárquica pai. Você precisa atribuir a palavra a uma entidade filho. Retorne ao enunciado na página de intenção. Selecionar a palavra e, em seguida, na lista suspensa, escolha o nome de entidade que você criou e siga o menu à direita para escolher a entidade filho correta.

    ![Captura de tela da página de detalhes de Intenções, com a entidade ToLocation realçada](./media/luis-how-to-add-example-utterances/label-tolocation.png)

    >[!CAUTION]
    >Nomes de entidade filho devem ser exclusivos em todas as entidades em um único aplicativo. Duas entidades hierárquicas diferentes não podem conter entidades filho com o mesmo nome. 

    Veja [Extração de Dados](luis-concept-data-extraction.md#hierarchical-entity-data) para saber mais sobre como extrair a entidade hierárquica da resposta da consulta JSON do ponto de extremidade. Experimente o [início rápido](luis-quickstart-intent-and-hier-entity.md) da entidade hierárquica para saber mais sobre como usar uma entidade hierárquica.


## <a name="remove-entity-labels-from-utterances"></a>Remover rótulos de entidade de enunciados
Você pode remover rótulos de entidade de aprendizado de máquina de um enunciado na página Intenção. Se a entidade não for de aprendizado de máquina, ela não poderá ser removida de um enunciado. Se você precisar remover uma entidade que não seja de aprendizado de máquina do enunciado, precisará excluir a entidade de todo o aplicativo. 

Para remover um rótulo de entidade de aprendizado de máquina de um enunciado, selecione a entidade no enunciado. Em seguida, selecione **Remover Rótulo** na caixa suspensa da entidade que é exibida.

![Captura de tela da página Detalhes de intenções, com Remover Rótulo realçado](./media/luis-how-to-add-example-utterances/remove-label.png) 

## <a name="add-prebuilt-entity-label"></a>Adicionar um rótulo de entidade predefinida
Se você adicionar as entidades predefinidas ao seu aplicativo de LUIS, não precisará rotular enunciados com essas entidades. Para saber mais sobre entidades predefinidas e como adicioná-las, veja [Adicionar entidades](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="add-regular-expression-entity-label"></a>Adicionar rótulo de entidade de expressão regular
Se você adicionar as entidades de expressão regular ao seu aplicativo de LUIS, não precisará rotular enunciados com essas entidades. Para saber mais sobre entidades de expressão regular e como adicioná-las, veja [Adicionar entidades](luis-how-to-add-entities.md#add-regular-expression-entities).

## <a name="create-a-pattern-from-an-utterance"></a>Criar um padrão de um enunciado
Veja [Adicionar padrão de enunciado existente na página de intenção ou de entidade](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="add-patternany-entity-label"></a>Adicionar um rótulo de entidade pattern.any
Se você adicionar entidades pattern.any ao seu aplicativo de LUIS, não será possível rotular enunciados com essas entidades. Elas só são válidas em padrões. Para saber mais sobre entidades pattern.any e como adicioná-las, veja [Adicionar entidades](luis-how-to-add-entities.md#add-patternany-entities).

<!--
Fix this - moved to luis-how-to-add-intents.md - how ?

## Search in utterances
## Prediction discrepancy errors
## Filter by intent prediction discrepancy errors
## Filter by entity type
## Switch to token view
## Delete utterances
## Edit an utterance
## Reassign utterances

-->
## <a name="train-your-app-after-changing-model-with-utterances"></a>Treinar seu aplicativo após alterar o modelo com enunciados
Depois de adicionar, editar ou remover enunciados, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que as suas alterações afetem as consultas de ponto de extremidade. 

## <a name="next-steps"></a>Próximas etapas

Depois de rotular enunciados em suas intenções, você pode criar uma [entidade composta](luis-how-to-add-entities.md).
