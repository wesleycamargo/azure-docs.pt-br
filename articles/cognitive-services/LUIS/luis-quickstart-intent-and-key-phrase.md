---
title: Tutorial para criar um aplicativo de LUIS que retorna frases-chave - Azure | Microsoft Docs
description: Neste tutorial, saiba como adicionar e retornar a entidade keyPhrase para seu aplicativo de LUIS para analisar enunciados com os principais assuntos.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: v-geberr
ms.openlocfilehash: 9acdfdde667d37bac5b96e4497b3e86d2cdeccb8
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063401"
---
# <a name="tutorial-learn-how-to-return-data-from-keyphrase-entity"></a>Tutorial: Saiba como retornar dados da entidade keyPhrase 
Neste tutorial, use um aplicativo que demonstra como extrair os principais assuntos dos enunciados.

<!-- green checkmark -->
> [!div class="checklist"]
> * Entender as entidades keyPhrase 
> * Usar o aplicativo LUIS no domínio de RH (recursos humanos) 
> * Adicione a entidade keyPhrase para extrair o conteúdo do enunciado
> * Treinar e publicar o aplicativo
> * Consulte o ponto de extremidade do aplicativo para ver a resposta JSON do LUIS incluindo as frases-chave

Para este artigo, você precisará de uma conta gratuita do [LUIS](luis-reference-regions.md#publishing-regions) para criar o seu aplicativo de LUIS.

## <a name="before-you-begin"></a>Antes de começar
Caso não tenha o aplicativo de recursos humanos do tutorial da [entidade simples](luis-quickstart-primary-and-secondary-data.md), [importe](create-new-app.md#import-new-app) o JSON em um aplicativo novo no site do [LUIS](luis-reference-regions.md#luis-website). O aplicativo a ser importado pode ser encontrado no repositório Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-simple-HumanResources.json).

Caso queira manter o aplicativo de recursos humanos original, clone a versão na página [Configurações](luis-how-to-manage-versions.md#clone-a-version) e nomeie-a como `keyphrase`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. 

## <a name="keyphrase-entity-extraction"></a>Extração da entidade keyPhrase
O assunto principal é fornecido pela entidade predefinida, **keyPhrase**. Esta entidade retorna o assunto principal no enunciado.

Os enunciados a seguir mostram exemplos de frases-chave:

|Enunciado|Valores da entidade KeyPhrase|
|--|--|
|Há um novo plano de saúde com uma franquia menor oferecida para o próximo ano?|"franquia menor"<br>"novo plano de saúde"<br>"ano"|
|Terapia visual possui cobertura no plano de médicos de franquia alta?|"plano de saúde de franquia alta"<br>"terapia visual"|

O aplicativo cliente pode usar esses valores, junto com outras entidades extraídas, para decidir a próxima etapa na conversa.

## <a name="add-keyphrase-entity"></a>Adicionar entidade KeyPhrase 
Adicione a entidade predefinida keyPhrase para extrair o assunto dos enunciados.

1. Verifique se o seu aplicativo de recursos humanos está na seção **Compilar** do LUIS. Você pode alterar essa seção selecionando **Compilar** na barra de menus da parte superior direita. 

    [ ![Captura de tela do aplicativo de LUIS com Compilar realçado na barra de navegação superior direita](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png)](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png#lightbox)

2. Selecione **Entidades** no menu à esquerda.

    [ ![Captura de tela de Entidades destacadas à esquerda da seção de Compilação](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png#lightbox)

3. Selecione **Gerenciar entidades predefinidas**.

    [ ![Caixa de diálogo pop-up de Lista de entidades](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png#lightbox)

4. Na caixa de diálogo pop-up, selecione **keyPhrase**, em seguida, selecione **Concluído**. 

    [ ![Caixa de diálogo pop-up de Lista de entidades](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->
5. Selecione **Intenções** no menu à esquerda e selecione a intenção **Utilities.Confirm**. A entidade keyPhrase é rotulada em diversos enunciados. 

    [ ![Captura de tela da intenção Utilities.Confirm com keyPhrases rotuladas no enunciado](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS
A nova versão `keyphrase` do aplicativo precisa ser treinada.  

1. No canto superior direito do site do LUIS, selecione o botão **Train** (Treinar).

    ![Treinar o aplicativo](./media/luis-quickstart-intent-and-key-phrase/train-button.png)

2. O treinamento é concluído quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

    ![Treinamento bem-sucedido](./media/luis-quickstart-intent-and-key-phrase/trained.png)

## <a name="publish-app-to-endpoint"></a>Publicar o aplicativo para o ponto de extremidade

1. Selecione **Publicar** no painel de navegação superior direito.

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png "Captura de tela da página Publicar com o botão Publicar no slot de produção realçado")](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png#lightbox)

2. Selecione o Slot de produção e o botão **Publicar**.

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png "Captura de tela da página Publicar com o botão Publicar no slot de produção realçado")](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png#lightbox)

3. A publicação é concluída quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

## <a name="query-the-endpoint-with-an-utterance"></a>Consultar o ponto de extremidade com um enunciado

1. Na página **Publicar**, selecione o link do **ponto de extremidade** na parte inferior da página. Essa ação abre outra janela do navegador com a URL de ponto de extremidade na barra de endereços. 

    ![Captura de tela da página Publicar com a URL de ponto de extremidade realçada](media/luis-quickstart-intent-and-key-phrase/hr-endpoint-url-inline.png )

2. Vá até o final da URL no endereço e insira `does form hrf-123456 cover the new dental benefits and medical plan`. O último parâmetro de querystring é `q`, o enunciado **consulta**. 

```
{
  "query": "does form hrf-123456 cover the new dental benefits and medical plan",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.9300641
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.9300641
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0359598845
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0141798034
    },
    {
      "intent": "MoveEmployee",
      "score": 0.0112197418
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00507669244
    },
    {
      "intent": "None",
      "score": 0.00238501839
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00202810857
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00102957746
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0008688423
    },
    {
      "intent": "Utilities.Confirm",
      "score": 3.557994E-05
    }
  ],
  "entities": [
    {
      "entity": "hrf-123456",
      "type": "HRF-number",git 
      "startIndex": 10,
      "endIndex": 19
    },
    {
      "entity": "new dental benefits",
      "type": "builtin.keyPhrase",
      "startIndex": 31,
      "endIndex": 49
    },
    {
      "entity": "medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 55,
      "endIndex": 66
    },
    {
      "entity": "hrf",
      "type": "builtin.keyPhrase",
      "startIndex": 10,
      "endIndex": 12
    },
    {
      "entity": "-123456",
      "type": "builtin.number",
      "startIndex": 13,
      "endIndex": 19,
      "resolution": {
        "value": "-123456"
      }
    }
  ]
}
```

Ao procurar um formulário, o usuário forneceu mais informações do que era necessário para localizar o formulário. As informações adicionais são retornadas como **builtin.keyPhrase**. O aplicativo cliente pode usar essas informações adicionais para uma pergunta de acompanhamento, como “Você deseja conversar com um representante dos Recursos Humanos sobre novos benefícios de plano odontológico” ou forneça um menu com mais opções, incluindo “Obter mais informações sobre novos benefícios de plano odontológico ou plano de saúde”.

## <a name="what-has-this-luis-app-accomplished"></a>O que esse aplicativo de LUIS realizou?
Este aplicativo, com a detecção da entidade keyPhrase, identificou uma intenção da consulta de linguagem natural e retornou os dados extraídos, incluindo o assunto principal. 

Seu chatbot agora tem informações suficientes para determinar a próxima etapa na conversa. 

## <a name="where-is-this-luis-data-used"></a>Onde esses dados do LUIS são usados? 
O LUIS é feito com essa solicitação. O aplicativo de chamada, como um chatbot, pode levar o resultado de topScoringIntent e os dados de keyPhrase do enunciado para realizar a próxima etapa. O LUIS não realiza esse trabalho de programação para o bot ou para o aplicativo de chamada. O LUIS só determina qual é a intenção do usuário. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o aplicativo LUIS. Para fazer isso, selecione o menu de três pontos (...) à direita do nome do aplicativo na lista de aplicativos e selecione **Excluir**. Na caixa de diálogo pop-up **Excluir aplicativo?**, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar o aplicativo que retorna o sentimento junto com a previsão da intenção](luis-quickstart-intent-and-sentiment-analysis.md)

