---
title: Tutorial sobre como criar um aplicativo de LUIS para obter os dados de correspondência da expressão regular – Azure | Microsoft Docs
description: Neste tutorial, saiba como criar um aplicativo de LUIS simples usando intenções e uma entidade de expressão regular para extrair dados.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/18/2018
ms.author: v-geberr
ms.openlocfilehash: 317d5b37b90f6c436e3cecf0486d587f54960598
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316535"
---
# <a name="tutorial-use-regular-expression-entity"></a>Tutorial: usar a entidade de expressão regular
Neste tutorial, você criará um aplicativo que demonstra como extrair dados formatados de forma consistente a partir de um enunciado usando a entidade de **Expressão Regular**.


<!-- green checkmark -->
> [!div class="checklist"]
> * Compreender entidades de expressão regular 
> * Usar um aplicativo de LUIS para um domínio de recursos humanos (RH) com a intenção FindForm
> * Adicionar entidade de expressão regular para extrair o número do formulário do enunciado
> * Treinar e publicar o aplicativo
> * Consulte ponto de extremidade do aplicativo para ver a resposta JSON do LUIS

Para este artigo, você precisa de uma conta gratuita de [LUIS](luis-reference-regions.md#luis-website) para criar o seu aplicativo LUIS.

## <a name="before-you-begin"></a>Antes de começar
Caso não tenha o aplicativo de recursos humanos do tutorial de [domínio personalizado](luis-tutorial-prebuilt-intents-entities.md) das entidades predefinidas, [importe](create-new-app.md#import-new-app) o JSON em um novo aplicativo no site do [LUIS](luis-reference-regions.md#luis-website) do repositório Github de [exemplos do LUIS](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-prebuilts-HumanResources.json).

Caso queira manter o aplicativo de recursos humanos original, clone a versão na página [Configurações](luis-how-to-manage-versions.md#clone-a-version) e nomeie-a como `regex`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. 


## <a name="purpose-of-the-regular-expression-entity"></a>Finalidade da entidade de expressão regular
A finalidade de uma entidade é obter dados importantes contidos no enunciado. O uso da entidade de expressão regular pelo aplicativo é para obter números formatados de formulário de recursos humanos (RH) de um enunciado. Não se trata de aprendizado de máquina. 

Enunciados simples de exemplo incluem:

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
```

Versões abreviadas ou com gírias de enunciados incluem:

```
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
A entidade de expressão regular para corresponder ao número de formulário é `hrf-[0-9]{6}`. Essa expressão regular corresponde aos caracteres literais `hrf -`, mas ignora variantes de caixa e cultura. Ela corresponde aos dígitos 0-9 de exatamente 6 dígitos.

HRF significa formulário de recursos humanos.

### <a name="tokenization-with-hyphens"></a>Geração de tokens com hifens
O LUIS cria tokens do enunciado quando a expressão é adicionada a uma intenção. A geração de tokens desses enunciados adiciona espaços antes e depois do hífen `Where is HRF - 123456?` A expressão regular é aplicada ao enunciado em formato bruto antes que sejam criados tokens para ele. Por ser aplicada ao formulário _bruto_, a expressão regular não precisa lidar com limites de palavras. 


## <a name="add-findform-intent"></a>Adicionar intenção FindForm

1. Verifique se o seu aplicativo de recursos humanos está na seção **Compilar** do LUIS. Você pode alterar essa seção selecionando **Compilar** na barra de menus da parte superior direita. 

    [ ![Captura de tela do aplicativo de LUIS com Compilar realçado na barra de navegação superior direita](./media/luis-quickstart-intents-regex-entity/first-image.png)](./media/luis-quickstart-intents-regex-entity/first-image.png#lightbox)

2. Selecione **Criar nova intenção**. 

    [ ![Captura de tela da página Intenções com o botão Criar nova intenção realçado](./media/luis-quickstart-intents-regex-entity/create-new-intent-button.png) ](./media/luis-quickstart-intents-regex-entity/create-new-intent-button.png#lightbox)

3. Insira `FindForm` na caixa de diálogo pop-up, depois selecione **Concluído**. 

    ![Captura de tela da caixa de diálogo Criar nova tentativa com utilitários na caixa de pesquisa](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. Adicione enunciados de exemplo para a intenção.

    |Exemplo de enunciados|
    |--|
    |Qual é a URL para hrf-123456?|
    |Onde está a hrf-345678?|
    |Quando hrf-456098 foi atualizada?|
    |John Smith atualizou a hrf-234639 na semana passada?|
    |Quantas versões da hrf-345123 existem?|
    |Quem deve autorizar o formulário hrf-123456?|
    |Quantas pessoas precisam se desconectar da hrf-345678?|
    |Data da hrf-234123?|
    |Autor da hrf-546234?|
    |Título da hrf-456234?|

    [ ![Captura de tela da página Intenção com novos enunciados realçados](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    O aplicativo tem o número da entidade predefinida adicionado do tutorial anterior, portanto, cada número de formulário está marcado. Isso pode ser suficiente para seu aplicativo cliente, mas o número não será rotulado com o tipo de número. Criar uma nova entidade com um nome apropriado permite que o aplicativo cliente processe a entidade adequadamente ao ser retornado do LUIS.

## <a name="create-a-hrf-number-regular-expression-entity"></a>Criar uma entidade de expressão regular HRF-número 
Crie uma entidade de expressão regular para informar ao LUIS o que é um formato HRF-número nas etapas a seguir:

1. Selecione **Entidades** no painel esquerdo.

2. Selecione o botão **Criar nova entidade** na página Entidades. 

    [ ![Captura de tela da página Entidades com o botão Criar nova entidade realçado](./media/luis-quickstart-intents-regex-entity/create-new-entity-1.png)](./media/luis-quickstart-intents-regex-entity/create-new-entity-1.png#lightbox)

3. Na caixa de diálogo pop-up, insira o novo nome de entidade `HRF-number`, selecione **RegEx** como o tipo de entidade, insira `hrf-[0-9]{6}` como o Regex e, depois, selecione **Concluído**.

    ![Captura de tela com caixa de diálogo pop-up configurando as propriedades da entidade](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. Selecione **Intenções**, em seguida, a intenção **FindForm** para ver a expressão regular rotulada nos enunciados. 

    [![Captura de tela do enunciado Rótulo com um padrão existente de entidade e regex](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Como a entidade não é uma entidade de aprendizado de máquina, o rótulo é aplicado aos enunciados e exibido no site LUIS assim que ela for criada.

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS
Uma entidade de expressão regular não exige treinamento, mas a nova intenção e os novos enunciados precisam de treinamento. 

1. No canto superior direito do site do LUIS, selecione o botão **Train** (Treinar).

    ![Imagem do botão de treinamento](./media/luis-quickstart-intents-regex-entity/train-button.png)

2. O treinamento é concluído quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

    ![Imagem da barra de notificação de sucesso](./media/luis-quickstart-intents-regex-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar o aplicativo para obter a URL do ponto de extremidade
Para obter uma previsão do LUIS em um chatbot ou outro aplicativo, você precisa publicar o aplicativo. 

1. No canto superior direito do site do LUIS, selecione o botão **Publish** (Publicar). 

    ![Captura de tela de FindKnowledgeBase com o botão de navegação Publicar realçado na parte superior](./media/luis-quickstart-intents-regex-entity/publish-button.png)

2. Selecione o Slot de produção e o botão **Publicar**.

    ![Captura de tela da página Publicar com o botão Publicar ao slot de produção realçado](./media/luis-quickstart-intents-regex-entity/publish-to-production.png)

3. A publicação é concluída quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Consultar o ponto de extremidade com um enunciado diferente
1. Na página **Publicar**, selecione o link do **ponto de extremidade** na parte inferior da página. Essa ação abre outra janela do navegador com a URL de ponto de extremidade na barra de endereços. 

    ![Captura de tela da página Publicar com a URL de ponto de extremidade realçada](./media/luis-quickstart-intents-regex-entity/publish-select-endpoint.png)

2. Vá até o final da URL no endereço e insira `When were HRF-123456 and hrf-234567 published?`. O último parâmetro de querystring é `q`, o enunciado **consulta**. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, ele é um bom teste e deve retornar a intenção `FindForm` com os números de formulário de `HRF-123456` e `hrf-234567`.

    ```
    {
      "query": "When were HRF-123456 and hrf-234567 published?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.970179737
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.970179737
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0131893409
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00364777143
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0024568392
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00173760345
        },
        {
          "intent": "None",
          "score": 0.00173070864
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00130692765
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00130328839
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0006671795
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    Os números no enunciado são retornados duas vezes: uma vez como a nova entidade `hrf-number` e uma vez como uma entidade predefinida `number`. Um enunciado pode ter mais de uma entidade e mais de uma entidade do mesmo tipo, como mostra o exemplo. Usando uma entidade de expressão regular, o LUIS extrai dados nomeados, o que é mais programaticamente útil para o aplicativo cliente recebendo a resposta JSON.

## <a name="what-has-this-luis-app-accomplished"></a>O que esse aplicativo de LUIS realizou?
Esse aplicativo identificou a intenção e retornou os dados extraídos. 

Agora seu chatbot tem informações suficientes para determinar a ação primária, `FindForm`, e quais números de formulário estavam na pesquisa. 

## <a name="where-is-this-luis-data-used"></a>Onde esses dados do LUIS são usados? 
O LUIS é feito com essa solicitação. O aplicativo de chamada, como um chatbot, pode pegar o resultado de topScoringIntent e os números de formulário e pesquisar uma API de terceiros. O LUIS não faz esse trabalho. O LUIS apenas determina qual é a intenção do usuário e extrai os dados sobre essa intenção. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o aplicativo LUIS. Para fazer isso, selecione o menu de três pontos (...) à direita do nome do aplicativo na lista de aplicativos e selecione **Excluir**. Na caixa de diálogo pop-up **Excluir aplicativo?**, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre a entidade KeyPhrase](luis-quickstart-intent-and-key-phrase.md)

