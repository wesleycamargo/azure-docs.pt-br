---
title: Tutorial para criar um aplicativo de LUIS que retorna frases-chave - Azure | Microsoft Docs
description: Neste tutorial, saiba como adicionar e retornar a entidade keyPhrase para seu aplicativo de LUIS para analisar enunciados com os principais assuntos.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 12c306b5199da5862302c28d1690b81c6e1edb0e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264608"
---
# <a name="tutorial-create-app-that-returns-keyphrases-entity-data-found-in-utterances"></a>Tutorial: Criar um aplicativo que retorna dados da entidade keyPhrases encontrados nos enunciados
Neste tutorial, crie um aplicativo que demonstra como extrair os principais assuntos dos enunciados.

<!-- green checkmark -->
> [!div class="checklist"]
> * Entender as entidades keyPhrase 
> * Crie um novo aplicativo LUIS para o domínio de recursos humanos
> * Adicionar a intenção _Nenhuma_ e adicione os enunciados de exemplo
> * Adicione a entidade keyPhrase para extrair o conteúdo do enunciado
> * Treinar e publicar o aplicativo
> * Consulte ponto de extremidade do aplicativo para ver a resposta JSON do LUIS

Para este artigo, você precisará de uma conta gratuita do [LUIS][LUIS] para criar o seu aplicativo de LUIS.

## <a name="keyphrase-entity-extraction"></a>Extração da entidade keyPhrase
O assunto principal é fornecido pela entidade predefinida, **keyPhrase**. Esta entidade retorna o assunto principal no enunciado

Os enunciados a seguir mostram exemplos de frases-chave:

|Enunciado|Valores da entidade KeyPhrase|
|--|--|
|Há um novo plano de saúde com uma franquia menor oferecida para o próximo ano?|"franquia menor"<br>"novo plano de saúde"<br>"ano"|
|Terapia visual possui cobertura no plano de médicos de franquia alta?|"plano de saúde de franquia alta"<br>"terapia visual"|

O chatbot pode considerar esses valores, além de todas as outras entidades extraídas, ao decidir a próxima etapa na conversa.

## <a name="download-sample-app"></a>Baixar aplicativo de exemplo
Baixe o aplicativo [Recursos humanos](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) e salve-o em um arquivo com a extensão *.json. Esta exemplo de aplicativo reconhece enunciados relevantes para benefícios de funcionários, organogramas e ativos físicos.

## <a name="create-a-new-app"></a>Criar um novo aplicativo
1. Faça logon no site do [LUIS][LUIS]. Certifique-se de fazer logon na [região][LUIS-regions] onde você precisa dos pontos de extremidade de LUIS publicados.

2. No site do [LUIS][LUIS], selecione **Importar novo aplicativo** para importar o aplicativo de Recursos humanos baixado na seção anterior. 

    [![](media/luis-quickstart-intent-and-key-phrase/app-list.png "Captura de tela de Listas de aplicativos")](media/luis-quickstart-intent-and-key-phrase/app-list.png#lightbox)

3. Na caixa de diálogo **Importar novo aplicativo**, nomeie o aplicativo `Human Resources with Key Phrase entity`. 

    ![Imagem da caixa de diálogo Criar novo aplicativo](./media/luis-quickstart-intent-and-key-phrase/import-new-app-inline.png)

    Quando o processo de criação do aplicativo for concluído, LUIS exibe a lista de intenções.

    [![](media/luis-quickstart-intent-and-key-phrase/intents-list.png "Captura de tela de Listas de intenções")](media/luis-quickstart-intent-and-key-phrase/intents-list.png#lightbox)

## <a name="add-keyphrase-entity"></a>Adicionar entidade KeyPhrase 
Adicione a entidade predefinida keyPhrase para extrair o assunto dos enunciados.

1. Selecione **Entidades** no menu à esquerda.

    [ ![Captura de tela de Entidades destacadas à esquerda da seção de Compilação](./media/luis-quickstart-intent-and-key-phrase/select-entities.png)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png#lightbox)

2. Selecione **Gerenciar entidades predefinidas**.

    [ ![Caixa de diálogo pop-up de Lista de entidades](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png#lightbox)

3. Na caixa de diálogo pop-up, selecione **keyPhrase**, em seguida, selecione **Concluído**. 

    [ ![Caixa de diálogo pop-up de Lista de entidades](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS
LUIS não sabe sobre essa alteração para o modelo até que seja treinado. 

1. No canto superior direito do site do LUIS, selecione o botão **Train** (Treinar).

    ![Captura de tela do botão Treinar realçado](./media/luis-quickstart-intent-and-key-phrase/train-button-expanded.png)

2. O treinamento é concluído quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

    ![Captura de tela da barra de notificação de Treinamento com êxito ](./media/luis-quickstart-intent-and-key-phrase/trained-inline.png)

## <a name="publish-app-to-endpoint"></a>Publicar o aplicativo para o ponto de extremidade

1. Selecione **Publicar** no painel de navegação superior direito.

    ![Captura de tela da página Entidade com o botão Publicar expandido ](./media/luis-quickstart-intent-and-key-phrase/publish-expanded.png)

2. Selecione o Slot de produção e o botão **Publicar**.

    [![](media/luis-quickstart-intent-and-key-phrase/publish-to-production-inline.png "Captura de tela da página Publicar com o botão Publicar no slot de produção realçado")](media/luis-quickstart-intent-and-key-phrase/publish-to-production-expanded.png#lightbox)

3. A publicação é concluída quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

## <a name="query-the-endpoint-with-an-utterance"></a>Consultar o ponto de extremidade com um enunciado

1. Na página **Publicar**, selecione o link do **ponto de extremidade** na parte inferior da página. Essa ação abre outra janela do navegador com a URL de ponto de extremidade na barra de endereços. 

    ![Captura de tela da página Publicar com a URL de ponto de extremidade realçada](media/luis-quickstart-intent-and-key-phrase/endpoint-url-inline.png )

2. Vá até o final da URL no endereço e insira `Is there a new medical plan with a lower deductible offered next year?`. O último parâmetro de querystring é `q`, o enunciado **consulta**. 

```
{
  "query": "Is there a new medical plan with a lower deductible offered next year?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.216838628
  },
  "entities": [
    {
      "entity": "lower deductible",
      "type": "builtin.keyPhrase",
      "startIndex": 35,
      "endIndex": 50
    },
    {
      "entity": "new medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 26
    },
    {
      "entity": "year",
      "type": "builtin.keyPhrase",
      "startIndex": 65,
      "endIndex": 68
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>O que esse aplicativo de LUIS realizou?
Este aplicativo, com a detecção da entidade keyPhrase, identificou uma intenção da consulta de linguagem natural e retornou os dados extraídos, incluindo o assunto principal. 

Seu chatbot agora tem informações suficientes para determinar a próxima etapa na conversa. 

## <a name="where-is-this-luis-data-used"></a>Onde esses dados do LUIS são usados? 
O LUIS é feito com essa solicitação. O aplicativo de chamada, como um chatbot, pode levar o resultado de topScoringIntent e os dados de keyPhrase do enunciado para realizar a próxima etapa. O LUIS não realiza esse trabalho de programação para o bot ou para o aplicativo de chamada. O LUIS só determina qual é a intenção do usuário. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o aplicativo do LUIS. Para fazer isso, selecione o menu de três pontos (...) à direita do nome do aplicativo na lista de aplicativos e selecione **Excluir**. Na caixa de diálogo pop-up **Excluir aplicativo?**, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar o aplicativo que retorna o sentimento junto com a previsão da intenção](luis-quickstart-intent-and-sentiment-analysis.md)

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
