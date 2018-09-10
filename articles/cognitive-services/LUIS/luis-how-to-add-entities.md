---
title: Adicionar entidades em aplicativos de LUIS | Microsoft Docs
titleSuffix: Azure
description: Adicionar entidades (dados chave no domínio do aplicativo) em aplicativos LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: e97f9a5391799849983bd98db5400e0a842627b7
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224119"
---
# <a name="manage-entities"></a>Gerenciar entidades
Após identificar as [intenções](luis-concept-intent.md) do aplicativo, será necessário [rotular enunciados de exemplo](luis-concept-utterance.md) com [entidades](luis-concept-entity-types.md). As entidades são as partes importantes de um comando ou pergunta e podem ser essenciais para que o aplicativo cliente execute a tarefa. 

É possível adicionar, editar ou excluir entidades no aplicativo por meio da **Lista de Entidades** na página **Entidades**. O LUIS oferece dois tipos principais de entidades: [entidades pré-compiladas](luis-reference-prebuilt-entities.md) e suas próprias entidades personalizadas.

As seções a seguir estão disponíveis apenas dentro de um aplicativo de LUIS, na seção **Compilar**. O link **Compilar** está na barra de navegação superior. Uma vez na seção **Compilar**, selecione **Entidades** do menu de navegação esquerdo. Quando uma entidade é adicionada ao aplicativo, se a entidade for de aprendizado de máquina será possível [rotular a entidade](luis-how-to-add-example-utterances.md) no enunciado. Depois que o aplicativo é treinado e publicado, você poderá receber dados da entidade [extraídos](luis-concept-data-extraction.md) da previsão. 

## <a name="add-prebuilt-entity"></a>Adicionar entidade pré-compilada
Entidades pré-compiladas são definidas no projeto [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). Entidades pré-compiladas comuns adicionadas a um aplicativo são *número* e *datetimeV2*. 

1. No aplicativo, na seção **Compilar**, clique em **Entidades** no painel esquerdo.
 
2. Na página **Entidades**, selecione **Gerenciar entidades pré-compiladas**.

    ![Captura de tela da adição de entidade pré-compilada na página Entidades](./media/add-entities/manage-prebuilt-entities-button.png)

3. Na caixa de diálogo **Adicionar ou remover entidades pré-compiladas**, selecione as entidades pré-compiladas **número** e **datetimeV2**. Em seguida, selecione **Concluído**.

    ![Captura de tela da caixa de diálogo Adicionar entidade pré-compilada](./media/add-entities/list-of-prebuilt-entities.png)

    Consulte [Extração de Dados](luis-concept-data-extraction.md#prebuilt-entity-data) para saber mais sobre como extrair a entidade pré-compilada da resposta da consulta JSON do ponto de extremidade.

## <a name="add-simple-entities"></a>Adicionar entidades simples
Uma entidade simples é uma entidade genérica que descreve um conceito único. 

1. No aplicativo, na seção **Compilar**, clique em **Entidades** no painel esquerdo e selecione **Criar nova entidade**.

    ![Captura de tela da página Entidades com o botão Criar nova entidade realçado](./media/add-entities/create-new-entity-button.png)

2. Na caixa de diálogo pop-up, digite `Airline` na caixa **Nome da entidade**, selecione **Simples** na lista **Tipo de entidade** e, em seguida, selecione **Concluído**.

    ![Captura de tela da caixa de diálogo para criar uma entidade simples de Companhia Aérea](./media/add-entities/create-simple-airline-entity.png)

    Veja [Extração de Dados](luis-concept-data-extraction.md#simple-entity-data) para saber mais sobre como extrair a entidade simples da resposta da consulta JSON do ponto de extremidade. Experimente o [início rápido](luis-quickstart-primary-and-secondary-data.md) da entidade simples para saber mais sobre como usar uma entidade simples.

## <a name="add-regular-expression-entities"></a>Adicionar entidades de expressão regular
Uma entidade de expressão regular é usada para extrair dados do enunciado com base em uma expressão regular fornecida por você. 

1. No aplicativo, selecione **Entidades** na navegação esquerda e, em seguida, selecione **Criar nova entidade**.

2. Na caixa de diálogo pop-up, digite `AirFrance Flight` na caixa **Nome da entidade**, selecione **Expressão regular** na lista **Tipo de entidade**, insira a expressão regular `AFR[0-9]{3,4}` e, em seguida, selecione **Concluído**. 

    Essa expressão regular Voo AirFrance espera três caracteres, literalmente `AFR` e, em seguida, 3 ou 4 dígitos. Os dígitos podem ser qualquer número entre 0 e 9. A expressão regular corresponde aos números de voo da AirFrance, como: "AFR101", "ARF1302" e "AFR5006". Consulte [Extração de Dados](luis-concept-data-extraction.md) para saber mais sobre como extrair a entidade da resposta da consulta JSON do ponto de extremidade.

    ![Imagem da caixa de diálogo para criar entidade de expressão regular](./media/add-entities/regex-entity-create-dialog.png)

    Consulte [Extração de Dados](luis-concept-data-extraction.md#regular-expression-entity-data) para saber mais sobre como extrair a entidade de expressão regular da resposta da consulta JSON do ponto de extremidade. Experimente o [início rápido](luis-quickstart-intents-regex-entity.md) da entidade de expressão regular para saber mais sobre como usar uma entidade de expressão regular.

## <a name="add-hierarchical-entities"></a>Adicionar entidades hierárquicas
Uma entidade hierárquica é uma categoria de entidades contextualmente aprendidas e conceitualmente relacionadas. No exemplo a seguir, a entidade contém locais de origem e de destino. 

No enunciado `Book 2 tickets from Seattle to Cairo`, Seattle é o local de origem e Cairo é o local de destino. Cada local é contextualmente diferente e aprendido pela escolha de palavras e pela ordem de palavras no enunciado.

Para adicionar entidades hierárquicas, conclua as etapas a seguir: 

1. No aplicativo, selecione **Entidades** na navegação esquerda e, em seguida, selecione **Criar nova entidade**.

2. Na caixa de diálogo pop-up, digite `Location` na caixa **Nome da entidade** e, em seguida, selecione **Hierárquico** na lista **Tipo de entidade**.

    ![Adicionar entidade hierárquica](./media/add-entities/hier-location-entity-creation.png)

3. Selecione **Adicionar Filho** e, em seguida, digite "Origem" na caixa **Filho Nº 1**. 

4. Selecione **Adicionar Filho** e, em seguida, digite "Destino" na caixa **Filho Nº 2**. Selecione **Concluído**.
5. 
    >[!NOTE]
    >Para excluir um filho, selecione o ícone da lixeira ao lado dele.

    >[!CAUTION]
    >Nomes de entidade filho devem ser exclusivos em todas as entidades em um único aplicativo. Duas entidades hierárquicas diferentes não podem conter entidades filho com o mesmo nome. 

    Veja [Extração de Dados](luis-concept-data-extraction.md#hierarchical-entity-data) para saber mais sobre como extrair a entidade hierárquica da resposta da consulta JSON do ponto de extremidade. Experimente o [início rápido](luis-quickstart-intent-and-hier-entity.md) da entidade hierárquica para saber mais sobre como usar uma entidade hierárquica.

## <a name="add-composite-entities"></a>Adicionar entidades compostas
É possível definir relacionamentos entre várias entidades existentes, criando uma entidade composta. No exemplo a seguir, a entidade contém a contagem de tíquetes, origem e locais de destino. 

No enunciado `Book 2 tickets from Seattle to Cairo`, o número 2 é correspondido a uma entidade pré-compilada, Seattle é o local de origem e Cairo é o local de destino. Cada entidade fará parte de uma entidade principal maior, depois que a entidade composta for criada.

1. No aplicativo, adicione a entidade pré-compilada **número**. Para mais instruções, consulte [Adicionar entidades pré-compiladas](#add-prebuilt-entity). 

2. Adicione a entidade hierárquica `Location`, incluindo os subtipos: `origin`, `destination`. Para mais instruções, consulte [Adicionar entidades hierárquicas](#add-hierarchical-entities). 

3. Selecione **Entidades** na navegação esquerda e, em seguida, selecione **Criar nova entidade**.

4. Na caixa de diálogo pop-up, digite `TicketsOrder` na caixa **Nome da entidade** e, em seguida, selecione **Composição** na lista **Tipo de entidade**.

5. Selecione **Adicionar Filho** para adicionar um novo filho.

6. Em **Filho Nº 1**, selecione a entidade **número** da lista.

7. Em **Filho Nº 2**, selecione a entidade **Location::Origin** da lista. 

8. Em **Filho Nº 3**, selecione a entidade **Location::Destination** da lista. 

9. Selecione **Concluído**.

    ![Imagem da caixa de diálogo para criar uma entidade composta](./media/add-entities/ticketsorder-composite-entity.png)

    >[!NOTE]
    >Para excluir um filho, selecione o botão da lixeira ao lado dele.

    Veja [Extração de Dados](luis-concept-data-extraction.md#composite-entity-data) para saber mais sobre como extrair a entidade composta da resposta da consulta JSON do ponto de extremidade. Experimente o [tutorial](luis-tutorial-composite-entity.md) da entidade composta para saber mais sobre como usar uma entidade composta.


## <a name="add-patternany-entities"></a>Adicionar entidades Pattern.any
Entidades [Pattern.any](luis-concept-entity-types.md) são válidas apenas em [padrões](luis-how-to-model-intent-pattern.md). Essa entidade ajuda o LUIS a localizar o fim de entidades de comprimento variável e escolha de palavras. Como essa entidade é usada em um padrão, o LUIS sabe onde o final da entidade está no enunciado.

Se um aplicativo tiver uma intenção `FindBookInfo`, o título do livro poderá interferir na previsão de intenção. Para esclarecer quais palavras estão no título do livro, use um Pattern.any dentro de um padrão. A previsão do LUIS começa com o enunciado. Primeiro, o enunciado é verificado e correspondido para entidades, quando as entidades são localizadas, então, o padrão é verificado e correspondido. 

No enunciado `Who wrote the book Ask and when was it published?`, o título do livro, Ask, é complicado porque não é contextualmente óbvio onde o título termina e onde o restante do enunciado começa. Os títulos de livros podem ser qualquer ordem de palavras, incluindo uma única palavra, frases complexas com pontuação e ordenação de palavras sem sentido. Um padrão permite que você crie uma entidade, em que a entidade completa e exata possa ser extraída. Quando o título do livro é localizado, a intenção `FindBookInfo`é prevista porque essa é a intenção do padrão.

1. No aplicativo, na seção **Compilar**, clique em **Entidades** no painel esquerdo e selecione **Criar nova entidade**.

2. Na caixa de diálogo **Adicionar Entidade**, digite `BookTitle` na caixa **Nome da entidade** e selecione **Pattern.any** como o **Tipo de entidade**.
 
    ![Captura de tela da criação de uma entidade pattern.any](./media/add-entities/create-pattern-any-entity.png)

    Para usar a entidade pattern.any, adicione um padrão na página **Padrões** (na seção **Melhorar o desempenho do aplicativo**) com a sintaxe de chave correta, como "Para **{BookTitle}** quem é o autor?".

    Consulte [Extração de Dados](luis-concept-data-extraction.md#patternany-entity-data) para saber mais sobre como extrair a entidade Pattern.any da resposta da consulta JSON do ponto de extremidade. Experimente o tutorial [Padrão](luis-tutorial-pattern.md) para saber mais sobre como usar uma entidade Pattern.any.

Se você achar que seu padrão, quando ele incluir um Pattern.any, extrai as entidades incorretamente, use uma [lista explícita](luis-concept-patterns.md#explicit-lists) para corrigir esse problema. 

## <a name="add-role-to-pattern-based-entity"></a>Adicionar função à entidade baseada em padrão
Uma função é um subtipo nomeado de uma entidade com base no contexto. É comparável a uma entidade [hierárquica](#add-hierarchical-entities), mas as funções são usadas apenas em [padrões](luis-how-to-model-intent-pattern.md). 

Por exemplo, uma passagem aérea tem uma *cidade de origem* e uma *cidade de destino*, mas ambas são cidades. O LUIS determina que ambas são cidades e podem determinar as cidades de origem e destino com base no contexto da ordem das palavras e da escolha de palavras. 

A sintaxe de uma função é **{Entity name:Role name}**, em que o nome da entidade é seguido por dois-pontos e, em seguida, o nome da função. Por exemplo, "Reserve um bilhete de {Local:Origem} para {Local:Destino}".

1. No aplicativo, na seção **Compilar**, selecione **Entidades** no painel esquerdo.

2. Selecione **Criar nova entidade**. Insira o nome de `Location`. Selecione o tipo **Simples** e selecione **Concluído**

3. Selecione **Entidades** no painel esquerdo, selecione a nova entidade **Local** criada na etapa 2.

4. Na caixa de texto **Nome da função**, insira o nome da função `Origin` e insira. Adicione um segundo nome de função `Destination`. Por exemplo, uma viagem de avião pode ter uma origem e uma cidade de destino. As duas funções são "Origem" e "Destino".

    ![Captura de tela da adição da função Origem à entidade Local](./media/add-entities/roles-enter-role-name-text.png)

    Consulte [Extração de Dados](luis-concept-data-extraction.md) para saber mais sobre como extrair funções da resposta da consulta JSON do ponto de extremidade. Experimente o tutorial de padrão para aprender mais sobre como usar uma entidade Pattern.any.

## <a name="add-list-entities"></a>Adicionar entidades de lista
As entidades de lista representam um conjunto fixo e fechado de palavras relacionadas no sistema. 

Para uma entidade de lista de bebidas, você pode ter dois valores normalizados: água e refrigerante. Cada nome normalizado tem sinônimos. Para água, os sinônimos são H20, gás, simples. Para refrigerante, os sinônimos são fruta, cola, refresco. Você não precisa conhecer todos os valores ao criar a entidade. Você pode adicionar mais depois de revisar os enunciados reais do usuário com sinônimos.

|Nome normalizado|Sinônimos|
|--|--|
|Água|H20, gás, simples|
|Refrigerante|Fruta, cola, refresco|

1. No aplicativo, na seção **Compilar**, clique em **Entidades** no painel esquerdo e selecione **Criar nova entidade**.

2. Na caixa de diálogo **Adicionar Entidade**, digite `Drinks` na caixa **Nome da entidade** e selecione **Lista** como o **Tipo de entidade**. Selecione **Concluído**.
 
    ![Imagem da caixa de diálogo criando a entidade lista de Bebidas](./media/add-entities/menu-list-dialog.png)
  
3.  A página da entidade de lista permite adicionar nomes normalizados. Na caixa de texto **Valores**, insira um item para a lista, como `Water` para a lista de bebidas e pressione Enter no teclado. 

    ![Captura de tela da entidade lista de Bebidas com valor normalizado de água na caixa de texto](./media/add-entities/entity-list-normalized-name.png)

4. À direita do valor normalizado **água**, insira sinônimos `h20`, `flat` e `gas`, pressionando Enter no teclado após cada item.

    ![Captura de tela da entidade lista de Bebidas com itens de sinônimo para água destacada](./media/add-entities/menu-list-synonyms.png)

5. Se você quiser mais itens normalizados para a lista, selecione **Recomendar**para ver as opções do [dicionário semântico](luis-glossary.md#semantic-dictionary).

    ![Captura de tela da entidade de lista de Bebidas com itens recomendados realçados](./media/add-entities/entity-list-recommended-list.png)

6. Selecione um item na lista recomendada para adicioná-lo como um valor normalizado ou selecione **Adicionar todos** para adicionar todos os itens. 

    ![Captura de tela da entidade de lista de Bebidas com o item de cerveja recomendado e o botão Adicionar todos realçado](./media/add-entities/list-entity-add-suggestions.png)

    Veja [Extração de Dados](luis-concept-data-extraction.md#list-entity-data) para saber mais sobre como extrair entidades de lista da resposta da consulta JSON do ponto de extremidade. Experimente o [início rápido](luis-quickstart-intent-and-list-entity.md) para saber mais sobre como usar uma entidade de lista.

## <a name="import-list-entity-values"></a>Importar valores de entidade de lista
É possível importar valores para uma entidade de lista existente.

 1. Na página da entidade de lista, selecione **Importar Listas**.

 2. Na caixa de diálogo **Importar Novas Entradas**, selecione **Escolher Arquivo** e selecione o arquivo JSON que inclui a lista.

    ![Captura de tela da caixa de diálogo pop-up Importar valores de entidade de lista](./media/add-entities/menu-list-import-json-dialog-with-file.png)

    >[!NOTE]
    >O LUIS importa apenas arquivos com a extensão ".json".

 3. Para saber mais sobre a sintaxe de lista com suporte em JSON, selecione **Saiba mais sobre sintaxe de lista com suporte** para expandir a caixa de diálogo e exibir um exemplo de sintaxe permitida. Para recolher a caixa de diálogo e ocultar a sintaxe, selecione o título do link novamente.

 4. Selecione **Concluído**.

    Um exemplo de json válido para uma entidade de lista **Cores** é mostrado no código formatado em JSON a seguir:

    ```
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

## <a name="edit-entity-name"></a>Editar nome da entidade
1. Na página da lista **Entidades**, selecione a entidade na lista. Esta ação direciona-o para a página **Entidade**.

2. Na página **Entidade**, você edita o nome da entidade selecionando o ícone de edição ao lado do nome da entidade. O tipo de entidade não é editável. 

## <a name="delete-entity"></a>Excluir entidade

Na página **Entidade**, selecione o botão **Excluir entidade**. Em seguida, selecione **Ok** na mensagem de confirmação para confirmar a exclusão.
 
![Captura de tela da página da entidade Local com o botão Excluir Entidade destacado](./media/add-entities/entity-delete.png)

>[!NOTE]
>* Excluir uma entidade hierárquica exclui todas as entidades filhas.
>* Excluir uma entidade composta exclui apenas a composição e quebra o relacionamento de composição, mas não exclui as entidades que o formam.

## <a name="changing-entity-type"></a>Alterar o tipo de entidade
O LUIS não permite alterar o tipo da entidade porque não sabe o que adicionar ou remover para construir essa entidade. Para alterar o tipo, é melhor criar uma nova entidade do tipo correto com um nome ligeiramente diferente. Depois que a entidade é criada, em cada enunciado, remova o antigo nome da entidade rotulada e adicione o novo nome da entidade. Depois que todos os enunciados tiverem sido remarcados, exclua a entidade antiga. 

## <a name="create-a-pattern-from-an-utterance"></a>Criar um padrão de um enunciado
Veja [Adicionar padrão de enunciado existente na página de intenção ou de entidade](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="search-utterances"></a>Pesquisar enunciados
É possível pesquisar e filtrar enunciados com o ícone de lupa na barra de ferramentas. 

## <a name="train-your-app-after-changing-model-with-entities"></a>Treine o aplicativo após alterar o modelo com entidades
Após adicionar, editar ou remover entidades, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) o aplicativo para que as alterações afetem as consultas de ponto de extremidade. 

## <a name="next-steps"></a>Próximas etapas
Agora que você adicionou intenções, enunciados e entidades, você tem um aplicativo LUIS básico. Saiba como [treinar](luis-how-to-train.md), [testar](luis-interactive-test.md) e [publicar](luis-how-to-publish-app.md) o aplicativo.
 
