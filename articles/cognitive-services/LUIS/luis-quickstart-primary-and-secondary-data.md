---
title: Tutorial sobre como criar um aplicativo LUIS para extrair dados - Azure | Microsoft Docs
description: Neste tutorial, saiba como criar um aplicativo LUIS simples usando intenções e uma entidade simple para extrair dados de aprendizado de máquina.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: v-geberr
ms.openlocfilehash: 1e8647e34da3d34946a4f6ac298017f6d4c99de6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265353"
---
# <a name="tutorial-create-app-that-uses-simple-entity"></a>Tutorial: Criar um aplicativo que usa uma entidade simple
Neste tutorial, crie um aplicativo que demonstra como extrair dados de aprendizado de máquina de um enunciado usando a entidade **Simple**.

<!-- green checkmark -->
> [!div class="checklist"]
> * Entender as entidades simple 
> * Criar novo aplicativo LUIS para o domínio de comunicação com a intenção SendMessage
> * Adicionar a intenção _Nenhuma_ e adicione os enunciados de exemplo
> * Adicionar entidade simple para extrair o conteúdo da mensagem do enunciado
> * Treinar e publicar o aplicativo
> * Consulte ponto de extremidade do aplicativo para ver a resposta JSON do LUIS

Para este artigo, você precisa de uma conta gratuita de [LUIS][LUIS] para criar o seu aplicativo LUIS.

## <a name="purpose-of-the-app"></a>Finalidade do aplicativo
Este aplicativo demonstra como efetuar pull dos dados fora de um enunciado. Considere o seguinte enunciado de um chatbot:

```JSON
Send a message telling them to stop
```

A intenção é enviar uma mensagem. Os dados importantes do enunciado são a própria mensagem, `telling them to stop`.  

## <a name="purpose-of-the-simple-entity"></a>Finalidade da entidade simple
A finalidade da entidade simple é ensinar ao LUIS o que é uma mensagem e onde ela pode ser encontrada em um enunciado. A parte do enunciado que é a mensagem pode mudar de acordo com o enunciado e com base no tamanho do enunciado e na escolha de palavras. LUIS precisa de exemplos de mensagens em qualquer enunciado em todas as intenções.  

Para este aplicativo simples, a mensagem estará no final do enunciado. 

## <a name="create-a-new-app"></a>Criar um novo aplicativo
1. Faça logon no site do [LUIS][LUIS]. Certifique-se de fazer logon na região onde você precisa dos pontos de extremidade de LUIS publicados.

2. No site do [LUIS][LUIS], selecione **Create new app** (Criar novo aplicativo).  

    ![Lista de aplicativos LUIS](./media/luis-quickstart-primary-and-secondary-data/app-list.png)

3. Na caixa de diálogo pop-up, insira o nome `MyCommunicator`. 

    ![Lista de aplicativos LUIS](./media/luis-quickstart-primary-and-secondary-data/create-new-app-dialog.png)

4. Após a conclusão desse processo, o aplicativo mostrará a página **Intenções** com a intenção **None**. 

    [![](media/luis-quickstart-primary-and-secondary-data/intents-list.png "Captura de tela da página Intents do LUIS com a intenção None")](media/luis-quickstart-primary-and-secondary-data/intents-list.png#lightbox)

## <a name="create-a-new-intent"></a>Criar uma nova intenção

1. Na página **Inteções**, selecione **Criar nova intenção**. 

    [![](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png "Captura de tela do LUIS com o botão \"Create new intent\" realçado")](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png#lightbox)

2. Insira o nome da nova intenção `SendMessage`. Essa intenção deve ser selecionada sempre que um usuário quiser enviar uma mensagem.

    Ao criar uma intenção, você está criando a categoria principal de informações que você deseja identificar. Ao nomear a categoria, qualquer outro aplicativo que use os resultados de consulta do LUIS poderá usar esse nome de categoria para encontrar uma resposta ou executar uma ação apropriada. O LUIS não responderá a essas perguntas, apenas identifica o tipo de informação que está sendo solicitado em linguagem natural. 

    ![Insira o nome de intenção SendMessage](./media/luis-quickstart-primary-and-secondary-data/create-new-intent-popup-dialog.png)

3. Adicione sete enunciados à intenção `SendMessage` os quais você espera que um usuário solicite, por exemplo:

    | Exemplo de enunciados|
    |--|
    |Responder com recebi a mensagem, terei a resposta amanhã|
    |Enviar mensagem de Quando você estará em casa?|
    |Enviar SMS informando que estou ocupado|
    |Informar que isso precisa ser feito hoje|
    |Enviar mensagem instantânea de que estou dirigindo e responderei mais tarde|
    |Escrever mensagem para David perguntando Quando isso aconteceu?|
    |diga oi para o greg|

    [![](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png "Captura de tela do LUIS após inserir enunciados")](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Adicionar enunciados à intenção None

No momento, o aplicativo LUIS não tem enunciados para a intenção **None**. Ele precisa de enunciados que você não deseja que o aplicativo responda, então ele deve ter enunciados na intenção **None**. Não deixe em branco. 
    
1. Selecione **Intenções** no painel esquerdo. 

    [![](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png "Captura de tela do LUIS com o botão \"Intents\" realçado")](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png#lightbox)

2. Selecione a intenção **None**. 

    [![](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png "Captura de tela da seleção da intenção None")](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png#lightbox)

3. Adicione três enunciados que o usuário pode inserir, mas que não são relevantes para o seu aplicativo. Entre os enunciados **None** válidas estão:

    | Exemplo de enunciados|
    |--|
    |Cancelar!|
    |Até logo|
    |O que está acontecendo?|
    
    No aplicativo que chama o LUIS, como um chatbot, se o LUIS retornar a intenção **None** para um enunciado, o bot poderá perguntar se o usuário deseja terminar a conversa. O bot também pode fornecer mais orientações para continuar a conversa, se o usuário não quiser encerrá-lo. 

    [![](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png "Captura de tela do LUIS após inserir enunciados para a intenção None")](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png#lightbox)

## <a name="create-a-simple-entity-to-extract-message"></a>Criar uma entidade simple para extrair a mensagem 
1. Selecione **Intents** no menu esquerdo.

    ![Selecione o link Intents](./media/luis-quickstart-primary-and-secondary-data/select-intents-from-none-intent.png)

2. Selecione `SendMessage` na lista de intenções.

    ![Selecione a intenção SendMessage](./media/luis-quickstart-primary-and-secondary-data/select-sendmessage-intent.png)

3. No enunciado, `Reply with I got your message, I will have the answer tomorrow`, selecione a primeira palavra do corpo da mensagem, `I`, e a última palavra do corpo da mensagem, `tomorrow`. Todas essas palavras são selecionadas para a mensagem e um menu suspenso é exibido com uma caixa de texto na parte superior.

    [![](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png "Captura de tela da seleção de palavras no enunciado da mensagem")](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png#lightbox)

4. Insira o nome da entidade `Message` na caixa de texto.

    [![](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png "Captura de tela de inserção do nome de entidade na caixa")](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png#lightbox)

5. Selecione **Create new entity** no menu suspenso. A finalidade da entidade é efetuar pull do texto que faz parte do corpo da mensagem. Nesse aplicativo LUIS, a mensagem de texto está no final do enunciado, mas o enunciado e a mensagem podem ter qualquer tamanho. 

    [![](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png "Captura de tela da criação de nova entidade do enunciado")](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png#lightbox)

6. Na janela pop-up, o tipo de entidade padrão é **Simple** e o nome da entidade é `Message`. Mantenha essas configurações e selecione **Done** (Concluído).

    ![Verificar o tipo de entidade](./media/luis-quickstart-primary-and-secondary-data/entity-type.png)

7. Agora que a entidade foi criada, e um enunciado foi rotulada, rotule o restante dos enunciados com essa entidade. Selecione um enunciado e depois selecione a primeira e última palavra de uma mensagem. No menu suspenso, selecione a entidade, `Message`. Agora, a mensagem está rotulada na entidade. Continue rotulando todas as frases de mensagem nos enunciados restantes.

    [![](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png "Captura de tela de todas os enunciados de mensagem rotuladas")](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png#lightbox)

    A exibição padrão dos enunciados é **Entities view** (Modo de exibição de entidades). Selecione o controle **Entities view** acima dos enunciados. O **Modo de exibição de tokens** exibe o texto do enunciado. 

    [![](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png "Captura de tela de enunciados no modo de exibição de tokens")](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png#lightbox)

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS
O LUIS não fica ciente das alterações nas intenções e entidades (o modelo) até que seja treinado. 

1. No canto superior direito do site do LUIS, selecione o botão **Train** (Treinar).

    ![Selecione o botão treinar](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. O treinamento é concluído quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

    ![Notificação de sucesso do treinamento](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar o aplicativo para obter a URL do ponto de extremidade
Para obter uma previsão do LUIS em um chatbot ou outro aplicativo, você precisa publicar o aplicativo. 

1. No canto superior direito do site do LUIS, selecione o botão **Publish** (Publicar). 

2. Selecione o Slot de produção e o botão **Publicar**.

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "Captura de tela da página Publicar com o botão Publicar no slot de produção realçado")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. A publicação é concluída quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Consultar o ponto de extremidade com um enunciado diferente
Na página **Publicar**, selecione o link do **ponto de extremidade** na parte inferior da página. 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "Captura de tela da página Publicar com o ponto de extremidade realçado")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

Essa ação abre outra janela do navegador com a URL de ponto de extremidade na barra de endereços. Vá até o final da URL no endereço e insira `text I'm driving and will be 30 minutes late to the meeting`. O último parâmetro de querystring é `q`, o enunciado **consulta**. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, é um bom teste e deve retornar os enunciados `SendMessage`.

```
{
  "query": "text I'm driving and will be 30 minutes late to the meeting",
  "topScoringIntent": {
    "intent": "SendMessage",
    "score": 0.987501
  },
  "intents": [
    {
      "intent": "SendMessage",
      "score": 0.987501
    },
    {
      "intent": "None",
      "score": 0.111048922
    }
  ],
  "entities": [
    {
      "entity": "i ' m driving and will be 30 minutes late to the meeting",
      "type": "Message",
      "startIndex": 5,
      "endIndex": 58,
      "score": 0.162995353
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>O que esse aplicativo de LUIS realizou?
Esse aplicativo, com apenas duas intenções e uma entidade, identificou uma intenção de consulta de linguagem natural e retornou dados da mensagem. 

O resultado em JSON identifica a intenção com maior pontuação `SendMessage`, com uma pontuação de 0,987501. Todas as pontuações estão entre 1 e 0, com a melhor pontuação mais próxima a 1. A pontuação da intenção `None` é 0,111048922, muito mais próxima a zero. 

Os dados da mensagem têm um tipo, `Message`, bem como um valor, `i ' m driving and will be 30 minutes late to the meeting`. 

Agora, seu chatbot tem informações suficientes para determinar a ação primária, `SendMessage`, e um parâmetro dessa ação, o texto da mensagem. 

## <a name="where-is-this-luis-data-used"></a>Onde esses dados do LUIS são usados? 
O LUIS é feito com essa solicitação. O aplicativo de chamada, como um chatbot, pode usar o resultado de topScoringIntent e os dados da entidade para enviar a mensagem por uma API de terceiros. Se houver outras opções de programação para o aplicativo de chamada ou o bot, o LUIS não executará esse trabalho. O LUIS só determina qual é a intenção do usuário. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o aplicativo LUIS. Para fazer isso, selecione o menu de três pontos (...) à direita do nome do aplicativo na lista de aplicativos e selecione **Excluir**. Na caixa de diálogo pop-up **Excluir aplicativo?**, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como adicionar uma entidade hierárquica](luis-quickstart-intent-and-hier-entity.md)


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
