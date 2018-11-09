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
ms.date: 10/24/2018
ms.author: diberry
ms.openlocfilehash: caf8628569481149ee41e8253f4759e15e49150f
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139116"
---
# <a name="add-an-entity-to-example-utterances"></a>Adicionar uma entidade a enunciados de exemplo 

Enunciados de exemplo são exemplos de texto de comandos ou perguntas do usuário. Para ensinar o LUIS (Serviço Inteligente de Reconhecimento Vocal), você precisa adicionar [enunciados de exemplo](luis-concept-utterance.md) a uma [intenção](luis-concept-intent.md).

Geralmente, adicione um enunciado de exemplo a uma intenção primeiro, então crie entidades e rotule enunciados na página de intenção. Se você preferir criar entidades primeiro, veja [Adicionar entidades](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Marcar entidades em enunciados de exemplo

Quando você seleciona um determinado texto na declaração de exemplo a marcar para uma entidade, um menu pop-up no local é exibido. Use esse menu para criar ou selecionar uma entidade. 

Certos tipos de entidade, como as entidades predefinidas e entidades de expressão regular, não podem ser marcados na declaração de exemplo porque são marcados automaticamente. 

## <a name="add-a-simple-entity"></a>Adicionar uma entidade simples

No procedimento a seguir, você cria e marca uma entidade personalizada dentro do enunciado a seguir na página de intenção:

```text
Does John Smith work in Seattle?
```

1. Selecione `Seattle` no enunciado para rotulá-lo como uma entidade simples.

    [![Captura de tela da seleção de texto na declaração de entidade simples](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)

    > [!NOTE]
    > Ao selecionar palavras para marcá-las como entidades:
    > * Para uma única palavra, apenas selecione-a. 
    > * Para um conjunto de duas ou mais palavras, selecione no início e então no fim do conjunto.

1. Na caixa suspensa da entidade que é exibida, você pode selecionar uma entidade existente ou adicionar uma nova entidade. Para adicionar uma nova entidade, digite seu nome na caixa de texto e, em seguida, selecione **Criar nova entidade**. 

    ![Captura de tela de inserção do nome de entidade](./media/luis-how-to-add-example-utterances/hr-create-simple-2.png)

1. Na caixa de pop-up **Que tipo de entidade você deseja criar?**, verifique o nome da entidade e selecione o tipo de entidade **Simples**, então selecione **Concluído**.

    Uma [lista de frases](luis-concept-feature.md) normalmente é usada para melhorar o sinal de uma entidade simples.

## <a name="add-a-list-entity"></a>Adicionar uma entidade de lista

Entidades de lista representam um conjunto fixo e fechado (correspondências de texto exato) de palavras relacionadas no seu sistema. 

Para obter lista de departamentos da empresa, você pode ter valores normalizados: `Accounting` e `Human Resources`. Cada nome normalizado tem sinônimos. Para um departamento, esses sinônimos podem incluir quaisquer acrônimos, números ou gírias de departamento. Você não precisa conhecer todos os valores ao criar a entidade. Você pode adicionar mais depois de revisar os enunciados reais do usuário com sinônimos.

1. Na lista de enunciados de exemplo, para um enunciado específico, selecione a palavra ou frase que você deseja na nova lista. Em seguida, insira o nome da lista na caixa de texto superior e selecione **criar nova entidade**.   

    ![Captura de tela de inserção do nome de entidade de lista](./media/luis-how-to-add-example-utterances/hr-create-list-1.png)


1. Na caixa pop-up **Que tipo de entidade você deseja criar?**, nomeie a entidade e selecione **Lista** como o tipo. Adicione sinônimos deste item de lista e, em seguida, selecione **Concluído**. 

    ![Captura de tela de inserção de sinônimos de entidade de lista](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Você pode adicionar mais itens de lista ou mais sinônimos de item ao rotular outros enunciados ou editar a entidade em **Entidades** no painel de navegação esquerdo. [Editar](luis-how-to-add-entities.md#add-list-entities) as entidades dá as opções de inserir itens adicionais com sinônimos correspondentes ou importar uma lista. 

## <a name="add-composite-entity"></a>Adicionar entidade composta

Entidades compostas são criadas de **Entidades** existentes em uma entidade pai. 

Supondo o enunciado `Does John Smith work in Seattle?`, um enunciado composto pode retornar informações de entidade do nome do funcionário e a localização em um único objeto pai. 

O nome do funcionário, John Smith, é uma entidade [personName](luis-reference-prebuilt-person.md) predefinida. A localização, Seattle, é uma entidade simples personalizada. Depois que essas duas entidades são criadas e marcadas em uma expressão de exemplo, essas entidades podem ser encapsuladas em uma entidade composta. 

1. Para encapsular as entidades individuais em uma composição, selecione a **primeira** entidade rotulada (aquela mais à esquerda) no enunciado para a entidade composta. Uma lista suspensa é exibida mostrando as opções para essa seleção.

1. Selecione **Encapsular entidade composta** na lista suspensa. 

    ![Captura de tela da seleção de "Encapsular em entidade composta"](./media/luis-how-to-add-example-utterances/hr-create-composite-1.png)

1. Selecione a última palavra da entidade composta (aquela mais à direita). Observe que uma linha verde segue a entidade composta.

1. Insira o nome da entidade composta na lista suspensa.

    ![Captura de tela de inserção do nome de entidade](./media/luis-how-to-add-example-utterances/hr-create-composite-2.png)

    Ao encapsular as entidades corretamente, uma linha verde fica sob toda a frase.

1. Valide os detalhes de entidade composta na caixa pop-up **Que tipo de entidade você deseja criar?** e, em seguida, selecione **Concluído**.

    ![Captura de tela do pop-up de detalhes da entidade](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. A entidade composta é exibida com destaques em azul para entidades individuais e um sublinhado verde para a entidade composta inteira. 

    ![Captura de tela da página Detalhes de intenções, com entidade composta](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-hierarchical-entity"></a>Adicionar entidade hierárquica

Uma entidade hierárquica é uma categoria de entidades contextualmente aprendidas e conceitualmente relacionadas. No exemplo a seguir, a entidade contém locais de origem e de destino. 

No enunciado `Move John Smith from Seattle to Cairo`, Seattle é o local de origem e Cairo é o local de destino. Cada local é contextualmente diferente e aprendido pela escolha de palavras e pela ordem de palavras no enunciado.

1. Na página Intenção, no enunciado, selecione `Seattle`, insira o nome da entidade `Location` e, em seguida, pressione Enter no teclado.

    ![Captura de tela da caixa de diálogo Criar Rotulagem da Entidade Hierárquica](./media/luis-how-to-add-example-utterances/hr-hier-1.png)

1. Na caixa pop-up **Que tipo de entidade você deseja criar?**, selecione _hierárquica_ como **Tipo da entidade** e, em seguida, adicione `Origin` e `Destination` como filhos e selecione **Concluído**.

    ![Captura de tela da página de detalhes de Intenções, com a entidade ToLocation realçada](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

1. A palavra no enunciado foi rotulada com a entidade hierárquica pai. Você precisa atribuir a palavra a uma entidade filho. Retorne ao enunciado na página de detalhes da intenção. Selecionar a palavra e, em seguida, na lista suspensa, escolha o nome de entidade que você criou e siga o menu à direita para escolher a entidade filho correta.

    ![Captura de tela da página de detalhes de Intenções, com a entidade ToLocation realçada](./media/luis-how-to-add-example-utterances/hr-hier-3.png)

    >[!CAUTION]
    >Nomes de entidade filho devem ser exclusivos em todas as entidades em um único aplicativo. Duas entidades hierárquicas diferentes não podem conter entidades filho com o mesmo nome. 

## <a name="entity-status-predictions"></a>Previsões do status da entidade

Quando você insere um novo enunciado no portal LUIS, o enunciado pode ter erros de previsão de entidade. O erro de previsão é uma diferença entre como uma entidade é rotulada em comparação com a forma como o LUIS previu a entidade. 

Essa diferença é representada visualmente no portal LUIS com um sublinhado vermelho no enunciado. O sublinhado vermelho pode aparecer em colchetes de entidades ou fora dos colchetes. 

![Captura de tela da discrepância de previsão do status da entidade](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Selecione as palavras que estão sublinhadas em vermelho na frase. 

A caixa de entidade exibe o **status de Entidade** com um ponto de exclamação vermelho, se houver uma discrepância de previsão. Para ver o status Entidade com informações sobre a diferença entre entidades rotuladas e previstas, selecione **Status da entidade** e, em seguida, selecione o item à direita.

![Captura de tela da discrepância de previsão do status da entidade](./media/luis-how-to-add-example-utterances/entity-status.png)

A linha vermelha pode aparecer em qualquer um dos seguintes horários:

   * Quando um enunciado é inserido, mas antes que a entidade seja rotulada
   * Quando o rótulo da entidade é aplicado
   * Quando o rótulo da entidade é removido
   * Quando mais de um rótulo de entidade é previsto para esse texto 

As soluções a seguir ajudam a resolver a discrepância de previsão da entidade:

|Entidade|Indicador Visual|Previsão|Solução|
|--|--|--|--|
|A enunciação entrou, entidade ainda não está rotulada.|sublinhado em vermelho|Previsão está correta.|Rotule a entidade com o valor previsto.|
|Texto sem rótulo|sublinhado em vermelho|Previsão incorreta|Os enunciados atuais que usam essa entidade incorreta precisam ser revisados em todas as intenções. As declarações atuais têm equivocado LUIS que este texto é a entidade prevista.
|Texto corretamente rotulado|destaque da entidade azul, sublinhado em vermelho|Previsão incorreta|Forneça mais enunciados com a entidade corretamente rotulada em uma variedade de lugares e usos. Os enunciados atuais não são suficientes para ensinar ao LUIS que esta é a entidade ou entidades semelhantes aparecem no mesmo contexto. Entidade semelhante deve ser combinada em uma única entidade, portanto, LUIS não é confuso. Outra solução é adicionar uma lista de frases para aumentar o significado das palavras. |
|Texto etiquetado incorretamente|destaque da entidade azul, sublinhado em vermelho|Previsão correta| Forneça mais enunciados com a entidade corretamente rotulada em uma variedade de lugares e usos. 

## <a name="other-actions"></a>Outras ações

Você pode executar ações em enunciados de exemplo como um grupo selecionado ou como um item individual. Grupos de enunciados de exemplo selecionados alteram o menu contextual acima da lista. Itens únicos podem usar o menu contextual acima da lista e o botão de reticências contextual individual no final de cada linha de enunciado. 

### <a name="remove-entity-labels-from-utterances"></a>Remover rótulos de entidade de enunciados

Você pode remover rótulos de entidade de aprendizado de máquina de um enunciado na página Intenção. Se a entidade não for de aprendizado de máquina, ela não poderá ser removida de um enunciado. Se você precisar remover uma entidade que não seja de aprendizado de máquina do enunciado, precisará excluir a entidade de todo o aplicativo. 

Para remover um rótulo de entidade de aprendizado de máquina de um enunciado, selecione a entidade no enunciado. Em seguida, selecione **Remover Rótulo** na caixa suspensa da entidade que é exibida.

![Captura de tela da página Detalhes de intenções, com Remover Rótulo realçado](./media/luis-how-to-add-example-utterances/remove-label.png) 

### <a name="add-prebuilt-entity-label"></a>Adicionar um rótulo de entidade predefinida

Quando adicionar as entidades predefinidas ao seu aplicativo de LUIS, você não precisará marcar enunciados com essas entidades. Para saber mais sobre entidades predefinidas e como adicioná-las, veja [Adicionar entidades](luis-how-to-add-entities.md#add-prebuilt-entity).

### <a name="add-regular-expression-entity-label"></a>Adicionar rótulo de entidade de expressão regular

Se você adicionar as entidades de expressão regular ao seu aplicativo de LUIS, não precisará marcar enunciados com essas entidades. Para saber mais sobre entidades de expressão regular e como adicioná-las, veja [Adicionar entidades](luis-how-to-add-entities.md#add-regular-expression-entities).


### <a name="create-a-pattern-from-an-utterance"></a>Criar um padrão de um enunciado

Veja [Adicionar padrão de enunciado existente na página de intenção ou de entidade](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-patternany-entity"></a>Adicionar entidade pattern.any

Se você adicionar entidades pattern.any ao seu aplicativo de LUIS, não será possível rotular enunciados com essas entidades. Elas só são válidas em padrões. Para saber mais sobre entidades pattern.any e como adicioná-las, veja [Adicionar entidades](luis-how-to-add-entities.md#add-patternany-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Treinar seu aplicativo após alterar o modelo com enunciados

Depois de adicionar, editar ou remover enunciados, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que as suas alterações afetem as consultas de ponto de extremidade. 

## <a name="next-steps"></a>Próximas etapas

Depois de rotular enunciados em suas intenções, você pode criar uma [entidade composta](luis-how-to-add-entities.md).
