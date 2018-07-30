---
title: Tutorial para criar um aplicativo de LUIS que retorna a análise de sentimento - Azure | Microsoft Docs
description: Neste tutorial, saiba como adicionar análise de sentimento ao seu aplicativo LUIS para analisar enunciados para sentimentos positivos, negativos e neutros.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: diberry
ms.openlocfilehash: 1fa27cf04e136033c51b951271a3d329a910a720
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223612"
---
# <a name="tutorial-9--add-sentiment-analysis"></a>Tutorial: 9.  Adicionar análise de sentimento
Neste tutorial, crie um aplicativo que demonstra como extrair sentimentos positivos, negativos e neutros dos enunciados.

<!-- green checkmark -->
> [!div class="checklist"]
> * Entender a análise de sentimento
> * Usar o aplicativo LUIS no domínio de RH (recursos humanos) 
> * Adicionar análise de sentimento
> * Treinar e publicar o aplicativo
> * Consulte ponto de extremidade do aplicativo para ver a resposta JSON do LUIS 

Para este artigo, você precisa de uma conta gratuita de [LUIS](luis-reference-regions.md#luis-website) para criar o seu aplicativo LUIS.

## <a name="before-you-begin"></a>Antes de começar
Caso não tenha o aplicativo de recursos humanos do tutorial de [entidades keyphrase predefinidas](luis-quickstart-intent-and-key-phrase.md), [importe](luis-how-to-start-new-app.md#import-new-app) o JSON em um aplicativo novo no site do [LUIS](luis-reference-regions.md#luis-website). O aplicativo a ser importado pode ser encontrado no repositório Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-keyphrase-HumanResources.json).

Caso queira manter o aplicativo de recursos humanos original, clone a versão na página [Configurações](luis-how-to-manage-versions.md#clone-a-version) e nomeie-a como `sentiment`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. 

## <a name="sentiment-analysis"></a>Análise de sentimento
A análise de sentimento é a capacidade de determinar se o enunciado de um usuário é positivo, negativo ou neutro. 

Os enunciados a seguir mostram exemplos de sentimentos:

|Sentimento|Pontuação|Enunciado|
|:--|:--|:--|
|positivo|0.91 |John W. Smith fez um ótimo trabalho na apresentação em Paris.|
|positivo|0.84 |jill-jones@mycompany.com fez um fabuloso trabalho na apresentação de vendas de Parker.|

A análise de sentimento é como uma configuração de aplicativo que se aplica a cada enunciado. Não é necessário localizar as palavras que indicam o sentimento no enunciado e rotulá-las, já que a análise de sentimento é aplicada no enunciado como um todo. 

## <a name="add-employeefeedback-intent"></a>Adicionar intenção EmployeeFeedback 
Adicione uma nova intenção para capturar comentários do funcionário entre os membros da empresa. 

1. Verifique se o seu aplicativo de recursos humanos está na seção **Compilar** do LUIS. Você pode alterar essa seção selecionando **Compilar** na barra de menus da parte superior direita. 

    [ ![Captura de tela do aplicativo de LUIS com Compilar realçado na barra de navegação superior direita](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png#lightbox)

2. Selecione **Criar nova intenção**.

    [ ![Captura de tela do aplicativo de LUIS com Compilar realçado na barra de navegação superior direita](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png#lightbox)

3. Insira o nome da nova intenção `EmployeeFeedback`.

    ![Criar nova caixa de diálogo de intenção com EmployeeFeedback como nome](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Adicione vários enunciados que indicam que um funcionário está fazendo algo bem ou uma área que precise de melhorias:

    Lembre-se que neste aplicativo de recursos humanos, os funcionários são definidos na entidade de lista, `Employee`, pelo nome, email, ramal telefônico, número de telefone celular e seus números do seguro social dos EUA. 

    |Declarações|
    |--|
    |425-555-1212 fez um ótimo trabalho de boas-vindas para uma colega de trabalho que estava de licença-maternidade|
    |234-56-7891 fez um ótimo trabalho consolando um colega de trabalho que estava de luto.|
    |jill-jones@mycompany.com não tem todas as faturas necessárias para a documentação.|
    |john.w.smith@mycompany.com entregou os formulários necessários um mês atrasado sem nenhuma assinatura|
    |x23456 não compareceu à reunião importante de marketing externa.|
    |x12345 faltou a reunião para as análises do mês de junho.|
    |Jill Jones deu um show na apresentação de vendas em Harvard|
    |John W. Smith fez um ótimo trabalho na apresentação em Stanford|

    [ ![Captura de tela do aplicativo LUIS com exemplos de enunciados na intenção EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS
LUIS não sabe sobre a nova intenção e seus exemplos de enunciado até que seja treinado. 

1. No canto superior direito do site do LUIS, selecione o botão **Train** (Treinar).

    ![Captura de tela do botão Treinar realçado](./media/luis-quickstart-intent-and-sentiment-analysis/train-button.png)

2. O treinamento é concluído quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

    ![Captura de tela da barra de notificação de Treinamento com êxito ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-trained-inline.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurar aplicativo para incluir a análise de sentimento
Configure a análise de sentimento na página **Publicar**. 

1. Selecione **Publicar** no painel de navegação superior direito.

    ![Captura de tela da página de Intenção com o botão Publicar expandido ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-button-in-top-nav-highlighted.png)

2. Selecione **Ativar Análise de sentimento**. Selecione o Slot de produção e o botão **Publicar**.

    [![](media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-to-production-expanded.png "Captura de tela da página Publicar com o botão Publicar no slot de produção realçado")](media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-to-production-expanded.png#lightbox)

4. A publicação é concluída quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

## <a name="query-the-endpoint-with-an-utterance"></a>Consultar o ponto de extremidade com um enunciado

1. Na página **Publicar**, selecione o link do **ponto de extremidade** na parte inferior da página. Essa ação abre outra janela do navegador com a URL de ponto de extremidade na barra de endereços. 

    ![Captura de tela da página Publicar com a URL de ponto de extremidade realçada](media/luis-quickstart-intent-and-sentiment-analysis/hr-endpoint-url-inline.png)

2. Vá até o final da URL no endereço e insira `Jill Jones work with the media team on the public portal was amazing`. O último parâmetro de querystring é `q`, o enunciado **consulta**. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, ele é um bom teste e deve retornar a intenção `EmployeeFeedback` com a extração da análise de sentimento.

```
{
  "query": "Jill Jones work with the media team on the public portal was amazing",
  "topScoringIntent": {
    "intent": "EmployeeFeedback",
    "score": 0.4983256
  },
  "intents": [
    {
      "intent": "EmployeeFeedback",
      "score": 0.4983256
    },
    {
      "intent": "MoveEmployee",
      "score": 0.06617523
    },
    {
      "intent": "GetJobInformation",
      "score": 0.04631853
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0103248553
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.007531875
    },
    {
      "intent": "FindForm",
      "score": 0.00344597152
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00337914471
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0026357458
    },
    {
      "intent": "None",
      "score": 0.00214573368
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00157622492
    },
    {
      "intent": "Utilities.Confirm",
      "score": 7.379545E-05
    }
  ],
  "entities": [
    {
      "entity": "jill jones",
      "type": "Employee",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          "Employee-45612"
        ]
      }
    },
    {
      "entity": "media team",
      "type": "builtin.keyPhrase",
      "startIndex": 25,
      "endIndex": 34
    },
    {
      "entity": "public portal",
      "type": "builtin.keyPhrase",
      "startIndex": 43,
      "endIndex": 55
    },
    {
      "entity": "jill jones",
      "type": "builtin.keyPhrase",
      "startIndex": 0,
      "endIndex": 9
    }
  ],
  "sentimentAnalysis": {
    "label": "positive",
    "score": 0.8694164
  }
}
```

O sentimentAnalysis é positivo com uma pontuação de 0.86. 

## <a name="what-has-this-luis-app-accomplished"></a>O que esse aplicativo de LUIS realizou?
Este aplicativo, com a análise de sentimento ativada, identificou uma intenção da consulta de linguagem natural e retornou os dados extraídos, incluindo o sentimento geral como uma pontuação. 

Seu chatbot agora tem informações suficientes para determinar a próxima etapa na conversa. 

## <a name="where-is-this-luis-data-used"></a>Onde esses dados do LUIS são usados? 
O LUIS é feito com essa solicitação. O aplicativo de chamada, como um chatbot, pode levar o resultado de topScoringIntent e os dados de sentimento do enunciado para realizar a próxima etapa. O LUIS não realiza esse trabalho de programação para o bot ou para o aplicativo de chamada. O LUIS só determina qual é a intenção do usuário. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o aplicativo LUIS. Selecione **Meus aplicativos** no menu superior esquerdo. Selecione as reticências (***...***) à direita do nome do aplicativo na lista de aplicativos e selecione **Excluir**. Na caixa de diálogo pop-up **Excluir aplicativo?**, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Examinar enunciados de ponto de extremidade no aplicativo de RH](luis-tutorial-review-endpoint-utterances.md) 

