---
title: Tutorial para criar um aplicativo de LUIS que retorna a análise de sentimento - Azure | Microsoft Docs
description: Neste tutorial, saiba como adicionar análise de sentimento ao seu aplicativo LUIS para analisar enunciados para sentimentos positivos, negativos e neutros.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: d000637312619fc493e2f7bad8e8edf0d8d0d94b
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265327"
---
# <a name="tutorial-create-app-that-returns-sentiment-along-with-intent-prediction"></a>Tutorial: Criar o aplicativo que retorna o sentimento junto com a previsão da intenção
Neste tutorial, crie um aplicativo que demonstra como extrair sentimentos positivos, negativos e neutros dos enunciados.

<!-- green checkmark -->
> [!div class="checklist"]
> * Entender as entidades hierárquicas e filhos contextualmente adquiridos 
> * Criar novo aplicativo de LUIS para domínio de viagem com intenção de reservar um voo
> * Adicionar a intenção _Nenhuma_ e adicione os enunciados de exemplo
> * Adicionar entidade hierárquica local com os filhos de origem e destino
> * Treinar e publicar o aplicativo
> * Consulte o ponto de extremidade do aplicativo para ver a resposta JSON do LUIS incluindo os filhos hierárquicos 

Para este artigo, você precisa de uma conta gratuita de [LUIS][LUIS] para criar o seu aplicativo LUIS.

## <a name="sentiment-analysis"></a>Análise de sentimento
A análise de sentimento é a capacidade de determinar se o enunciado de um usuário é positivo, negativo ou neutro. 

Os enunciados a seguir mostram exemplos de sentimentos:

|Sensibilidade e pontuação|Enunciado|
|:--|--|
|positivo - 0.89 |A combinação sopa e salada estava ótima.|
|negativo - 0.07 |Não gostei dos aperitivos durante o jantar que foi servido.|

A análise de sentimento é como uma configuração de aplicativo que se aplica a cada enunciado. Você não precisa localizar as palavras que indicam o sentimento no enunciado e rotulá-las. O LUIS fará isso para você.

## <a name="create-a-new-app"></a>Criar um novo aplicativo
1. Faça logon no site do [LUIS][LUIS]. Certifique-se de fazer logon na [região][LUIS-regions] onde você precisa dos pontos de extremidade de LUIS publicados.

2. No site do [LUIS][LUIS], selecione **Create new app** (Criar novo aplicativo). 

    [![](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png "Captura de tela de Listas de aplicativos")](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png#lightbox)

3. Na caixa de diálogo **Criar novo aplicativo**, nomeie o aplicativo `Restaurant Reservations With Sentiment` e selecione **Concluído**. 

    ![Imagem da caixa de diálogo Criar novo aplicativo](./media/luis-quickstart-intent-and-sentiment-analysis/create-app-ddl.png)

    Quando o processo de criação do aplicativo for concluído, LUIS exibe a lista de intenções contendo a intenção Nenhuma.

    [![](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png "Captura de tela de Listas de intenções")](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png#lightbox)

## <a name="add-a-prebuilt-domain"></a>Adicionar um domínio predefinido
Adicione um domínio predefinido para adicionar rapidamente intenções, entidades e enunciados rotulados.

1. Selecione **Domínios predefinidos** no menu à esquerda.

    [ ![Captura de tela do botão de Domínio predefinido](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-expanded.png#lightbox)

2. Selecione **Adicionar domínio** para o domínio predefinido **RestaurantReservation**. Aguarde até que o domínio seja adicionado.

    [ ![Captura de tela da lista de Domínios predefinidos](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-expanded.png#lightbox)

3. Selecione **Intenções** no painel de navegação esquerdo. Este domínio predefinido tem uma intenção.

    [ ![Captura de tela da lista de Domínios predefinidos com Intenções realçadas na barra de navegação à esquerda](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png#lightbox)

4.  Selecione a intenção **RestaurantReservation.Reserve**. 

    [ ![Captura de tela da lista de Intenções com RestaurantReservation.Reserve realçada](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png#lightbox)

5. Ative/desative a **Exibição de entidades** para ver os vários enunciados fornecidos com entidades específicas ao domínio rotuladas.

    [ ![Captura de tela da intenção RestaurantReservation.Reserve com a Exibição de entidades alternada para Exibição de tokens realçada](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-expanded.png#lightbox)

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS
O LUIS não fica ciente das alterações nas intenções e entidades (o modelo) até que seja treinado. 

1. No canto superior direito do site do LUIS, selecione o botão **Train** (Treinar).

    ![Captura de tela do botão Treinar realçado](./media/luis-quickstart-intent-and-sentiment-analysis/train-button-expanded.png)

2. O treinamento é concluído quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

    ![Captura de tela da barra de notificação de Treinamento com êxito ](./media/luis-quickstart-intent-and-sentiment-analysis/trained-expanded.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurar aplicativo para incluir a análise de sentimento
A análise de sentimento é ativada na página **Publicar**. 

1. Selecione **Publicar** no painel de navegação superior direito.

    ![Captura de tela da página de Intenção com o botão Publicar expandido ](./media/luis-quickstart-intent-and-sentiment-analysis/publish-expanded.png)

2. Selecione **Ativar Análise de sentimento**.

    ![Captura de tela da página Publicar com Ativar Análise de Sentimento realçada ](./media/luis-quickstart-intent-and-sentiment-analysis/enable-sentiment-expanded.png)

3. Selecione o Slot de produção e o botão **Publicar**.

    [![](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-inline.png "Captura de tela da página Publicar com o botão Publicar no slot de produção realçado")](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-expanded.png#lightbox)

4. A publicação é concluída quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

## <a name="query-the-endpoint-with-an-utterance"></a>Consultar o ponto de extremidade com um enunciado

1. Na página **Publicar**, selecione o link do **ponto de extremidade** na parte inferior da página. Essa ação abre outra janela do navegador com a URL de ponto de extremidade na barra de endereços. 

    ![Captura de tela da página Publicar com a URL de ponto de extremidade realçada](media/luis-quickstart-intent-and-sentiment-analysis/endpoint-url-inline.png)

2. Vá até o final da URL no endereço e insira `Reserve table for  10 on upper level away from kitchen`. O último parâmetro de querystring é `q`, o enunciado **consulta**. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, ele é um bom teste e deve retornar a intenção `RestaurantReservation.Reserve` com a extração da análise de sentimento.

```
{
  "query": "Reserve table for 10 on upper level away from kitchen",
  "topScoringIntent": {
    "intent": "RestaurantReservation.Reserve",
    "score": 0.9926384
  },
  "intents": [
    {
      "intent": "RestaurantReservation.Reserve",
      "score": 0.9926384
    },
    {
      "intent": "None",
      "score": 0.00961109251
    }
  ],
  "entities": [],
  "sentimentAnalysis": {
    "label": "neutral",
    "score": 0.5
  }
}
```

## <a name="what-has-this-luis-app-accomplished"></a>O que esse aplicativo de LUIS realizou?
Este aplicativo, com a análise de sentimento ativada, identificou uma intenção da consulta de linguagem natural e retornou os dados extraídos, incluindo o sentimento geral como uma pontuação. 

Seu chatbot agora tem informações suficientes para determinar a próxima etapa na conversa. 

## <a name="where-is-this-luis-data-used"></a>Onde esses dados do LUIS são usados? 
O LUIS é feito com essa solicitação. O aplicativo de chamada, como um chatbot, pode levar o resultado de topScoringIntent e os dados de sentimento do enunciado para realizar a próxima etapa. O LUIS não realiza esse trabalho de programação para o bot ou para o aplicativo de chamada. O LUIS só determina qual é a intenção do usuário. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o aplicativo do LUIS. Para fazer isso, selecione o menu de três pontos (...) à direita do nome do aplicativo na lista de aplicativos e selecione **Excluir**. Na caixa de diálogo pop-up **Excluir aplicativo?**, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Chamar a API de ponto de extremidade de LUIS com C#](luis-get-started-cs-get-intent.md) 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
