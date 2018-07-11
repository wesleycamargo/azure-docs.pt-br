---
title: Tutorial sobre como criar um aplicativo LUIS para extrair dados - Azure | Microsoft Docs
description: Neste tutorial, saiba como criar um aplicativo LUIS simples usando intenções e uma entidade simple para extrair dados de aprendizado de máquina.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: v-geberr
ms.openlocfilehash: e6ab9d1db0144ffa68fe9dc3381ba31d57aa0cae
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130883"
---
# <a name="tutorial-6-add-simple-entity-and-phrase-list"></a>Tutorial: 6. Adicionar entidade simples e lista de frases
Neste tutorial, crie um aplicativo que demonstra como extrair dados de aprendizado de máquina de um enunciado usando a entidade **Simple**.

<!-- green checkmark -->
> [!div class="checklist"]
> * Entender as entidades simple 
> * Criar um novo aplicativo LUIS para o domínio dos recursos humanos (RH) 
> * Adicionar uma entidade simples para extrair a mensagem do aplicativo
> * Treinar e publicar o aplicativo
> * Consulte ponto de extremidade do aplicativo para ver a resposta JSON do LUIS
> * Adicionar lista de frase para aumentar o sinal de palavras de trabalho
> * Treinar, publicar o aplicativo e repetir a consulta no ponto de extremidade

Para este artigo, você precisa de uma conta gratuita de [LUIS](luis-reference-regions.md#luis-website) para criar o seu aplicativo LUIS.

## <a name="before-you-begin"></a>Antes de começar
Caso não tenha o aplicativo de recursos humanos do tutorial da [entidade hierárquica](luis-quickstart-intent-and-hier-entity.md), [importe](create-new-app.md#import-new-app) o JSON em um aplicativo novo no site do [LUIS](luis-reference-regions.md#luis-website). O aplicativo a ser importado pode ser encontrado no repositório Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json).

Caso queira manter o aplicativo de recursos humanos original, clone a versão na página [Configurações](luis-how-to-manage-versions.md#clone-a-version) e nomeie-a como `simple`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original.  

## <a name="purpose-of-the-app"></a>Finalidade do aplicativo
Este aplicativo demonstra como efetuar pull dos dados fora de um enunciado. Considere os seguintes enunciados de um chatbot:

|Enunciado|Nome do trabalho extraível|
|:--|:--|
|Desejo me candidatar para o novo trabalho de contabilidade.|contabilidade|
|Envie meu currículo para o cargo de engenharia.|engenharia|
|Preencher o aplicativo para o trabalho 123456|123456|

Este tutorial adiciona uma nova entidade para extrair o nome do trabalho. 

## <a name="purpose-of-the-simple-entity"></a>Finalidade da entidade simple
A finalidade da entidade simples no aplicativo LUIS é ensinar ao LUIS o que é um nome trabalho e onde um enunciado pode ser encontrado. A parte do enunciado que é o trabalho pode mudar de acordo com o enunciado e com base no tamanho do enunciado e na escolha de palavras. O LUIS precisa de exemplos de trabalhos em qualquer enunciado em todas as intenções.  

É difícil determinar o nome do trabalho porque um nome pode ser um substantivo, um verbo ou uma frase com várias palavras. Por exemplo: 

|Trabalhos|
|--|
|engenheiro|
|engenheiro de software|
|engenheiro de software sênior|
|líder de equipe de engenharia |
|controlador de tráfego aéreo|
|operador de veículo motorizado|
|motorista de ambulância|
|vigia|
|extrusor|
|construtor de moinhos|

Esse aplicativo LUIS tem nomes de trabalho em várias intenções. Ao rotular essas palavras em todos os enunciados de intenções, o LUIS aprende mais sobre o que é um trabalho e onde ele é encontrado em enunciados.

## <a name="create-job-simple-entity"></a>Criar uma entidade simple de trabalho

1. Verifique se o seu aplicativo de recursos humanos está na seção **Compilar** do LUIS. Você pode alterar essa seção selecionando **Compilar** na barra de menus da parte superior direita. 

    [ ![Captura de tela do aplicativo LUIS com Compilar realçado na barra de navegação superior direita](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png)](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png#lightbox)

2. Na página **Intenções**, selecione a intenção **ApplyForJob**. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png "Captura de tela do LUIS com intenção de “ApplyForJob” realçada")](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png#lightbox)

3. No enunciado, `I want to apply for the new accounting job`, selecione `accounting`, digite `Job` no campo superior do menu pop-up, depois selecione **Criar nova entidade** no menu pop-up. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Captura de tela do LUIS com intenção de “ApplyForJob” com as etapas para criar a entidade realçadas")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. Na janela pop-up, verifique o nome e o tipo da entidade e selecione **Concluído**.

    ![Criar diálogo modal pop-up da entidade simple com o nome do Job e tipo de simple](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. No enunciado, `Submit resume for engineering position`, rotule a palavra `engineering` como uma entidade Job. Selecione a palavra `engineering`, depois selecione **Job** no menu pop-up. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Captura de tela do LUIS rotulando a entidade Job realçada")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    Todos os enunciados são rotulados, mas cinco enunciados não são suficientes para ensinar o LUIS sobre palavras e frases relacionadas a trabalho. Os trabalhos que usam o valor de número não precisam de mais exemplos porque usam uma entidade de expressão regular. Os trabalhos que são palavras ou frases precisam de pelo menos mais 15 exemplos. 

6. Adicione mais enunciados e marque as palavras ou frases de trabalho como entidade **Job**. Os tipos de trabalho são gerais entre empregos de um serviço de emprego. Se você quisesse trabalhos relacionados a um setor específico, as palavras de trabalho devem retratá-lo. 

    |Enunciado|Entidade Job|
    |:--|:--|
    |Estou me candidatando para o suporte do Gerente de Programas em P&D|Gerente de Programas|
    |Aqui está minha candidatura para cozinheiro de linha.|cozinheiro de linha|
    |Meu currículo para o monitor de campo está anexado.|monitor de acampamento|
    |Este é o meu CV para assistente administrativo.|assistente administrativo|
    |Desejo me candidatar para o trabalho de gerenciamento em vendas.|gerenciamento, vendas|
    |Este é o meu currículo para o novo cargo de contabilidade.|contabilidade|
    |Minha candidatura para assistente de bar está inclusa.|assistente de bar|
    |Estou enviando minha candidatura para carpinteiro e armador.|carpinteiro, armador|
    |Meu CV para motorista de ônibus está aqui.|motorista de ônibus|
    |Sou enfermeiro registrado. Aqui está o meu currículo.|enfermeiro registrado|
    |Gostaria de enviar minha documentação para o cargo de professor que vi no jornal.|professor|
    |Este é o meu CV para o cargo de estoquista de frutas e legumes.|estoquista|
    |Candidatar-se ao trabalho de azulejista.|bloco|
    |CV anexo para arquiteto paisagista.|arquiteto paisagista|
    |Meu currículo para professor de biologia está anexado.|professor de biologia|
    |Gostaria de me candidatar para o cargo em fotografia.|fotografia|git 

## <a name="label-entity-in-example-utterances-for-getjobinformation-intent"></a>Entidade de rótulo em enunciados de exemplo para a intenção GetJobInformation
1. Selecione **Intents** no menu esquerdo.

2. Selecione **GetJobInformation** na lista de intenções. 

3. Rotule os trabalhos nos enunciados de exemplo:

    |Enunciado|Entidade Job|
    |:--|:--|
    |Há algum trabalho em bancos de dados?|databases|
    |Procurando uma nova vaga com responsabilidades de contabilidade|contabilidade|
    |Quais cargos estão disponíveis para engenheiros seniores?|engenheiros seniores|

    Há outros enunciados de exemplo, mas eles não contêm palavras de trabalho.

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

Essa ação abre outra janela do navegador com a URL de ponto de extremidade na barra de endereços. Vá até o final da URL no endereço e insira `Here is my c.v. for the programmer job`. O último parâmetro de querystring é `q`, o enunciado **consulta**. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, é um bom teste e deve retornar os enunciados `ApplyForJob`.

```JSON
{
  "query": "Here is my c.v. for the programmer job",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.9826467
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.9826467
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0218927357
    },
    {
      "intent": "MoveEmployee",
      "score": 0.007849265
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00349470088
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.00348804821
    },
    {
      "intent": "None",
      "score": 0.00319909188
    },
    {
      "intent": "FindForm",
      "score": 0.00222647213
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00211193133
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00172086991
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.00138010911
    }
  ],
  "entities": [
    {
      "entity": "programmer",
      "type": "Job",
      "startIndex": 24,
      "endIndex": 33,
      "score": 0.5230502
    }
  ]
}
```

## <a name="names-are-tricky"></a>Nomes são complicados
O aplicativo LUIS encontrou a intenção correta com alta confiança e extraiu o nome do trabalho, mas nomes são complicados. Tente usar o enunciado `This is the lead welder paperwork`.  

No JSON a seguir, o LUIS responde com a intenção correta, `ApplyForJob`, mas não extrai o nome de trabalho `lead welder`. 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

Como um nome pode ser qualquer coisa, o LUIS prevê as intenções mais precisamente caso tenha uma lista de frase de palavras para melhorar o sinal.

## <a name="to-boost-signal-add-jobs-phrase-list"></a>Para melhorar o sinal, adicione uma lista de frases de trabalhos
Abra o [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) do repositório Github LUIS-Samples. A lista tem mais de mil palavras de trabalho e frases. Examine a lista de palavras em busca de palavras de trabalho que sejam significativas para você. Se suas palavras ou frases não estiverem na lista, adicione suas próprias.

1. Na seção **Compilar** do aplicativo LUIS, selecione **Listas de frase**, encontrada no menu **Melhorar o desempenho do aplicativo**.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png "Captura de tela com o botão Listas de frase realçado no painel de navegação à esquerda")](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png#lightbox)

2. Selecione **Criar nova lista de frase**. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png "Captura de tela do botão Criar nova lista de frase realçado")](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png#lightbox)

3. Nomeie a nova lista de frase como `Jobs` e copie a lista de jobs-phrase-list.csv para a caixa de texto **Valores**. Selecione enter. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Captura da caixa de diálogo pop-up Criar nova lista de frase")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Se você quiser adicionar mais palavras à lista de frases, revise os **Valores Relacionados** e adicione os que forem relevantes. 

4. Selecione **Salvar** para ativar a lista de frases.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Captura de tela da caixa de diálogo pop-up Criar nova lista de frases com palavras na caixa Valores da lista de frases")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. [Treine](#train-the-luis-app) e [publique](#publish-the-app-to-get-the-endpoint-URL) o aplicativo novamente para usar a lista de frases.

6. Repita a consulta no ponto de extremidade com o mesmo enunciado: `This is the lead welder paperwork.`

    A resposta JSON inclui a entidade extraída:

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="phrase-lists"></a>Listas de frase
A adição da lista de frases aumentou o sinal das palavras na lista, mas **não** é usada como uma correspondência exata. A lista de frases tem vários trabalhos com a primeira palavra `lead`, e também tem o trabalho `welder`, mas não tem o `lead welder`. Essa lista de frases de trabalhos pode não ser concluída. À medida que você [examinar enunciados de ponto de extremidade](label-suggested-utterances.md) regularmente e encontrar outras palavras de trabalho, adicione-as à sua lista de frases. Em seguida, treine e publique novamente.

## <a name="what-has-this-luis-app-accomplished"></a>O que esse aplicativo de LUIS realizou?
Esse aplicativo, com uma entidade simple e uma lista de frases com palavras, identificou uma intenção de consulta de linguagem natural e retornou os dados do trabalho. 

Agora, seu chatbot tem informações suficientes para determinar a ação primária de se candidatar e um parâmetro dessa ação, à qual o trabalho está referenciado. 

## <a name="where-is-this-luis-data-used"></a>Onde esses dados do LUIS são usados? 
O LUIS é feito com essa solicitação. O aplicativo de chamada, como um chatbot, pode usar o resultado de topScoringIntent e os dados da entidade para usar uma API de terceiros para enviar as informações do trabalho para um representante dos recursos humanos. Se houver outras opções de programação para o aplicativo de chamada ou o bot, o LUIS não executará esse trabalho. O LUIS só determina qual é a intenção do usuário. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o aplicativo LUIS. Selecione **Meus aplicativos** no menu superior esquerdo. Selecione o menu de três pontos (...) à direita do nome do aplicativo na lista de aplicativos e selecione **Excluir**. Na caixa de diálogo pop-up **Excluir aplicativo?**, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar uma entidade de keyphrase predefinida](luis-quickstart-intent-and-key-phrase.md)