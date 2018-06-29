---
title: Tutorial sobre como criar um aplicativo LUIS para obter dados listados de correspondência exata do texto - Azure | Microsoft Docs
description: Neste tutorial, saiba como criar um aplicativo LUIS simples usando intenções e entidades de lista para extrair dados.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 33394dff1091f27c79c74d8648a90724ba8d6698
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264820"
---
# <a name="tutorial-create-app-using-a-list-entity"></a>Tutorial: Criar um aplicativo usando uma entidade de lista
Neste tutorial, crie um aplicativo que demonstra como obter dados que correspondam a uma lista predefinida. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Entender as entidades de lista 
> * Criar um novo aplicativo LUIS para o domínio de bebidas com a intenção OrderDrinks
> * Adicionar a intenção _Nenhuma_ e adicione os enunciados de exemplo
> * Adicionar a entidade de lista para extrair itens de bebida do enunciado
> * Treinar e publicar o aplicativo
> * Consulte ponto de extremidade do aplicativo para ver a resposta JSON do LUIS

Para este artigo, você precisa de uma conta gratuita de [LUIS][LUIS] para criar o seu aplicativo LUIS.

## <a name="purpose-of-the-list-entity"></a>Finalidade da entidade de lista
Este aplicativo recebe pedidos de bebidas como `1 coke and 1 milk please` e retorna os dados, por exemplo, o tipo da bebida. Um entidade de **lista** de bebidas procura por correspondências exatas do texto e as retorna. 

Uma entidade de lista será uma boa opção para esse tipo de dados quando os valores dos dados forem um conjunto conhecido. Os nomes das bebidas podem variar, incluindo gíria e abreviações, mas os nomes não mudam com frequência. 

## <a name="app-intents"></a>Intenções de aplicativo
As intenções são categorias do que o usuário deseja. Este aplicativo tem suas intenções: OrderDrink e None. A intenção [None](luis-concept-intent.md#none-intent-is-fallback-for-app) é proposital, e indica qualquer coisa fora do aplicativo.  

## <a name="list-entity-is-an-exact-text-match"></a>A entidade de lista é uma correspondência exata do texto
A finalidade da entidade é localizar e categorizar partes do texto no enunciado. Uma entidade de [lista](luis-concept-entity-types.md) permite uma correspondência exata de palavras ou frases.  

Para este aplicativo de bebida, o LUIS extrai o pedido de bebida de tal forma que é possível criar e preencher um pedido padrão. O LUIS permite que o enunciado tenha variações, abreviações e gíria. 

Os exemplos de enunciados simples dos usuários incluem:

```
2 glasses of milk
3 bottles of water
2 cokes
```

Versões abreviadas ou com gírias de enunciados incluem:

```
5 milk
3 h2o
1 pop
```
 
A entidade da lista corresponde `h2o` a água, e `pop` a refrigerantes.  

## <a name="what-luis-does"></a>O que o LUIS faz
Quando a intenção e as entidades do enunciado são identificadas, [extraídas](luis-concept-data-extraction.md#list-entity-data) e retornadas em JSON do [ponto de extremidade](https://aka.ms/luis-endpoint-apis), o LUIS conclui sua ação. O aplicativo de chamada ou chatbot usa essa resposta JSON e atende a solicitação, conforme a maneira como o aplicativo ou chatbot foram projetados. 

## <a name="create-a-new-app"></a>Criar um novo aplicativo
1. Faça logon no site do [LUIS][LUIS]. Certifique-se de fazer logon na [região][LUIS-regions] onde você precisa dos pontos de extremidade de LUIS publicados.

2. No site do [LUIS][LUIS], selecione **Create new app** (Criar novo aplicativo).  

    ![Criar novo aplicativo](./media/luis-quickstart-intent-and-list-entity/app-list.png)

3. Na caixa de diálogo pop-up, insira o nome `MyDrinklist`. 

    ![Nome do aplicativo MyDrinkList](./media/luis-quickstart-intent-and-list-entity/create-app-dialog.png)

4. Após a conclusão desse processo, o aplicativo mostrará a página **Intenções** com a intenção **None**. 

    [![](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png "Captura de tela da página Intents")](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>Criar uma nova intenção

1. Na página **Inteções**, selecione **Criar nova intenção**. 

    [![](media/luis-quickstart-intent-and-list-entity/create-new-intent.png "Captura de tela da página Intents com o botão Create new intent realçado")](media/luis-quickstart-intent-and-list-entity/create-new-intent.png#lightbox)

2. Insira o nome da nova intenção `OrderDrinks`. Essa intenção deve ser selecionada sempre que um usuário quiser pedir uma bebida.

    Ao criar uma intenção, você está criando a categoria principal de informações que você deseja identificar. Ao nomear a categoria, qualquer outro aplicativo que use os resultados de consulta do LUIS poderá usar esse nome de categoria para encontrar uma resposta ou executar uma ação apropriada. O LUIS não responderá a essas perguntas, apenas identifica o tipo de informação que está sendo solicitado em linguagem natural. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png "Captura de tela da criação da nova intenção OrderDrinks")](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png#lightbox)

3. Adicione vários enunciados à intenção `OrderDrinks` os quais você espera que um usuário solicite, por exemplo:

    | Exemplo de enunciados|
    |--|
    |Enviar duas cocas e uma garrafa de água para o meu quarto|
    |Duas perriers com um toque de limão|
    |h20|

    [![](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png "Captura de tela de inserção do enunciadona página da intenção OrderDrinks")](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Adicionar enunciados à intenção None

No momento, o aplicativo LUIS não tem enunciados para a intenção **None**. Ele precisa de enunciados que você não deseja que o aplicativo responda, então ele deve ter enunciados na intenção **None**. Não deixe em branco. 

1. Selecione **Intenções** no painel esquerdo. 

    [![](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png "Captura de tela da seleção do link Intents no painel esquerdo")](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png#lightbox)

2. Selecione a intenção **None**. Adicione três enunciados que o usuário pode inserir, mas que não são relevantes para o seu aplicativo:

    | Exemplo de enunciados|
    |--|
    |Cancelar!|
    |Até logo|
    |O que está acontecendo?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>Quando o enunciado for previsto para a intenção None
No aplicativo que chama o LUIS (como um chatbot), quando o LUIS retornar a intenção **None** para um enunciado, o bot poderá perguntar se o usuário deseja terminar a conversa. O bot também pode fornecer mais orientações para continuar a conversa, se o usuário não quiser encerrá-lo. 

Trabalho das entidades na intenção **None**. Se a intenção com maior pontuação for **None**, mas ocorrer a extração de uma entidade que faça sentido para seu chatbot, o chatbot poderá enviar uma pergunta seguinte enfatizando a intenção do cliente. 

## <a name="create-a-menu-entity-from-the-intent-page"></a>Criar uma entidade de menu na página Intent
Agora que as duas intenções têm enunciados, o LUIS precisa entender o que é uma bebida. Volte até a intenção `OrderDrinks` e rotule (marque) as bebidas em um enunciado executando estas etapas:

1. Volte para a intenção `OrderDrinks` selecionando **Intenções** no painel esquerdo.

2. Selecione `OrderDrinks` na lista de intenções.

3. No enunciado, `Please send 2 cokes and a bottle of water to my room`, selecione a palavra `water`. Um menu suspenso é exibido com uma caixa de texto na parte superior para criar uma nova entidade. Insira o nome da entidade `Drink` na caixa de texto, selecione **Criar nova entidade** no menu suspenso. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png "Captura de tela da criação de nova entidade selecionando a palavra no enunciado")](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png#lightbox)

4. Na janela pop-up, selecione o tipo de entidade **List** (Lista). Adicionar sinônimo `h20`. Pressione a tecla enter após cada sinônimo. Não adicione `perrier` à lista de sinônimos. Isso será adicionado na próxima etapa, como um exemplo. Selecione **Concluído**.

    [![](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png "Captura de tela da configuração de nova entidade")](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png#lightbox)

5. Agora que a entidade foi criada, rotule os outros sinônimos de água selecionando o sinônimo para água e `Drink` na lista suspensa. Siga o menu à direita e selecione `Set as synonym`, depois, selecione `water`.

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png "Captura de tela de rótulo do enunciado com uma entidade existente")](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png#lightbox)

## <a name="modify-the-list-entity-from-the-entity-page"></a>Modificar a entidade de lista na página Entity
A entidade de lista de bebidas é criada, mas não tem muitos itens e sinônimos. Se você conhecer alguns termos, abreviações e gírias, será mais rápido preencher a lista na página **Entity**. 

1. Selecione **Entities** (Entidades) no painel à esquerda.

    [![](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png "Captura de tela da seleção de Entities no painel à esquerda")](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png#lightbox)

2. Selecione `Drink` na lista de entidades.

    [![](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png "Captura de tela de seleção da entidade Drink na lista de entidades")](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png#lightbox)

3. Na caixa de texto, insira `Soda pop` e depois pressione Enter. Este é um termo amplamente usado para bebidas gaseificadas. Cada cultura tem um apelido ou gíria para esse tipo de bebida.

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png "Captura de tela da inserção do nome canônico")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png#lightbox)

4. Na mesma linha que `Soda pop`, insira os sinônimos, como: 

    ```
    coke
    cokes
    coca-cola
    coca-colas
    ```

    Os sinônimos podem incluir frases, pontuação, possessivos e plurais. Como a entidade de lista é uma correspondência exata do texto (exceto com relação à capitalização), os sinônimos precisam mostrar todas as variações. Você pode expandir a lista à medida que aprende mais variações nos logs de consulta ou revisa as ocorrências do ponto de extremidade. 

    Este artigo tem alguns sinônimos, para encurtar o exemplo. Um aplicativo LUIS de nível de produção teria vários sinônimos, e deve ser revisado e expandido regularmente. 

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png "Captura de tela de adição de sinônimos")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png#lightbox)

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS
O LUIS não fica ciente das alterações nas intenções e entidades (o modelo) até que seja treinado. 

1. No canto superior direito do site do LUIS, selecione o botão **Train** (Treinar).

    ![Treinar o aplicativo](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. O treinamento é concluído quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

    ![Treinamento bem-sucedido](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar o aplicativo para obter a URL do ponto de extremidade
Para obter uma previsão do LUIS em um chatbot ou outro aplicativo, você precisa publicar o aplicativo. 

1. No canto superior direito do site do LUIS, selecione o botão **Publish** (Publicar). 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "Captura de tela de seleção do botão Publish")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. Selecione o Slot de produção e o botão **Publicar**. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "Captura de tela de seleção do botão Publish to production slot (Publicar no espaço de produção)")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. A publicação é concluída quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Consultar o ponto de extremidade com um enunciado diferente
1. Na página **Publicar**, selecione o link do **ponto de extremidade** na parte inferior da página. Essa ação abre outra janela do navegador com a URL de ponto de extremidade na barra de endereços. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "Captura de tela da URL do ponto de extremidade na página Publish")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

2. Vá até o final da URL no endereço e insira `2 cokes and 3 waters`. O último parâmetro de querystring é `q`, o enunciado **q**uery. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, ele é um bom teste e deve retornar a intenção `OrderDrinks` com os dois tipos de bebida `cokes` e `waters`.

```
{
  "query": "2 cokes and 3 waters",
  "topScoringIntent": {
    "intent": "OrderDrinks",
    "score": 0.999998569
  },
  "intents": [
    {
      "intent": "OrderDrinks",
      "score": 0.999998569
    },
    {
      "intent": "None",
      "score": 0.23884207
    }
  ],
  "entities": [
    {
      "entity": "cokes",
      "type": "Drink",
      "startIndex": 2,
      "endIndex": 6,
      "resolution": {
        "values": [
          "Soda pop"
        ]
      }
    },
    {
      "entity": "waters",
      "type": "Drink",
      "startIndex": 14,
      "endIndex": 19,
      "resolution": {
        "values": [
          "h20"
        ]
      }
    }
  ]
}
```

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Onde está o processamento de linguagem natural na entidade de lista? 
Como a entidade de lista é uma correspondência exata do texto, ela não depende de processamento de linguagem natural (ou aprendizado de máquina). O LUIS usa o processamento de linguagem natural (ou o aprendizado de máquina) para selecionar a intenção correta com maior pontuação. Além disso, um enunciado pode ser uma mistura de mais de uma entidade, ou até mesmo de mais de um tipo de entidade. Cada enunciado é processado para todas as entidades no aplicativo, incluindo entidades de processamento de linguagem natural (ou aprendizado de máquina), como a entidade **Simple**.

## <a name="what-has-this-luis-app-accomplished"></a>O que esse aplicativo de LUIS realizou?
Esse aplicativo, com apenas duas intenções e uma entidade de lista, identificou uma intenção de consulta de linguagem natural e retornou os dados extraídos. 

Agora, seu chatbot tem informações suficientes para determinar a ação primária, `OrderDrinks`, e quais tipos de bebidas foram pedidos na entidade de lista Drink. 

## <a name="where-is-this-luis-data-used"></a>Onde esses dados do LUIS são usados? 
O LUIS é feito com essa solicitação. O aplicativo de chamada, como um chatbot, pode levar o resultado de topScoringIntent e os dados da entidade para realizar a próxima etapa. O LUIS não realiza esse trabalho de programação para o bot ou para o aplicativo de chamada. O LUIS só determina qual é a intenção do usuário. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o aplicativo LUIS. Para fazer isso, selecione o menu de três pontos (...) à direita do nome do aplicativo na lista de aplicativos e selecione **Excluir**. Na caixa de diálogo pop-up **Excluir aplicativo?**, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como adicionar uma entidade de expressão regular](luis-quickstart-intents-regex-entity.md)

Adicione a **entidade predefinida** [number](luis-how-to-add-entities.md#add-prebuilt-entity) para extrair o número. 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
