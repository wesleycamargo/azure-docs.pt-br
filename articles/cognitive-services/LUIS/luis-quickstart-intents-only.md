---
title: 'Tutorial 1: Encontrar intenções no aplicativo LUIS personalizado'
titleSuffix: Azure Cognitive Services
description: Crie um aplicativo personalizado que prevê a intenção de um usuário. Este aplicativo é o tipo mais simples de aplicativo LUIS porque ele não extrai vários elementos de dados do texto do enunciado, como endereços de email ou datas.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: cc631f139e7f11d82f2caac83770754060353d07
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277708"
---
# <a name="tutorial-1-build-custom-app-to-determine-user-intentions"></a>Tutorial 1: Criar um aplicativo personalizado para determinar as intenções do usuário

Neste tutorial, você criará um aplicativo personalizado de RH (Recursos Humanos) que prevê a intenção de um usuário com base no enunciado (texto). Quando terminar, você terá um ponto de extremidade do LUIS em execução na nuvem.

A finalidade do aplicativo é determinar a intenção do texto de idioma natural e conversação. Essas intenções são categorizadas em **Intenções**. Este aplicativo possui algumas intenções. A primeira intenção, **`GetJobInformation`**, identifica quando um usuário deseja obter informações sobre trabalhos disponíveis dentro de uma empresa. A segunda intenção, **`None`**, é usada para os enunciados do usuário que estão fora do _domínio_ (escopo) desse aplicativo. Posteriormente, uma terceira intenção, **`ApplyForJob`**, é adicionada aos enunciados referentes à candidatura a um trabalho. Essa terceira intenção é diferente de `GetJobInformation` porque as informações sobre o trabalho já devem ser conhecidas quando alguém se candidata ao trabalho. No entanto, dependendo da escolha de palavras, determinar a intenção pode ser difícil, porque ambas as intenções referem-se a um trabalho.

Depois que o LUIS retornar a resposta JSON, ele terá concluído esta solicitação. O LUIS não fornece respostas para os enunciados do usuário; só identifica qual tipo de informação está sendo solicitado em idioma natural. 

**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Criar um novo aplicativo 
> * Criar intenções
> * Adicionar enunciados de exemplo
> * Treinar o aplicativo
> * Publicar aplicativo
> * Obter a intenção do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Criar um novo aplicativo

1. Entre no portal do LUIS com a URL [https://www.luis.ai](https://www.luis.ai). 

2. Selecione **Criar novo aplicativo**.  

    [![](media/luis-quickstart-intents-only/app-list.png "Captura de tela da página Meus Aplicativos do LUIS (Reconhecimento vocal)")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. Na caixa de diálogo pop-up, insira o nome `HumanResources` e mantenha a cultura padrão, **Inglês**. Deixe a descrição em branco.

    ![Novo aplicativo LUIS](./media/luis-quickstart-intents-only/create-app.png)

    Em seguida, o aplicativo mostra a página **Intenções** com a Intenção **Nenhuma**.

## <a name="getjobinformation-intent"></a>Intenção GetJobInformation

1. Selecione **Criar nova intenção**. Insira o nome da nova intenção `GetJobInformation`. Essa intenção é prevista sempre que um usuário deseja obter informações sobre vagas abertas na empresa.

    ![](media/luis-quickstart-intents-only/create-intent.png "Captura de tela da caixa de diálogo Nova Intenção do LUIS (Reconhecimento vocal)")

2. Fornecendo _enunciados de exemplo_, você está treinando o LUIS nos tipos de enunciados que devem ser previstos para essa intenção. Adicione vários enunciados de exemplo a essa intenção a qual você espera que um usuário solicite, por exemplo:

    | Exemplo de enunciados|
    |--|
    |Nenhuma nova vaga publicada hoje?|
    |Quais cargos estão disponíveis para engenheiros seniores?|
    |Há algum trabalho com bancos de dados?|
    |Procurando uma nova vaga com responsabilidades de contabilidade|
    |Onde estão as listagens de vagas|
    |Novas vagas?|
    |Existe alguma nova vaga no escritório de Seattle?|

    [![](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Captura de tela da inserção de novos enunciados para o MyStore")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    [!INCLUDE[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    


## <a name="none-intent"></a>Intenção None 
O aplicativo cliente precisa saber se um enunciado está fora do domínio de assuntos do aplicativo. Se o LUIS retornar a intenção **Nenhuma** para um enunciado, o aplicativo cliente poderá perguntar se o usuário deseja terminar a conversa. O aplicativo cliente também poderá fornecer mais orientações para continuar a conversa, caso o usuário não deseje terminá-la. 

Esses enunciados de exemplo, fora do domínio de assuntos, são agrupados na intenção **Nenhuma**. Não deixe em branco. 

1. Selecione **Intenções** no painel esquerdo.

2. Selecione a intenção **None**. Adicione três enunciados que o usuário pode inserir, mas que não são relevantes para o aplicativo de Recursos Humanos. Se o aplicativo destina-se a publicações de vagas de emprego, alguns enunciados com a intenção **Nenhuma** são:

    | Exemplo de enunciados|
    |--|
    |Cachorros que latem demais são irritantes|
    |Peça uma pizza para mim|
    |Pinguins no oceano|


## <a name="train"></a>Treinar 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicar

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent"></a>Obter a intenção

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá até o final da URL na barra de endereços e insira `I'm looking for a job with Natural Language Processing`. O último parâmetro da cadeia de consulta é `q`, a declaração **query**. Esse enunciado não é o mesmo de nenhum dos enunciados de exemplo. Ele é um bom teste e deve retornar a intenção `GetJobInformation` como a intenção com a maior pontuação. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.8965092
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.8965092
        },
        {
          "intent": "None",
          "score": 0.147104025
        }
      ],
      "entities": []
    }
    ```

    Os resultados incluirão **todas as intenções** no aplicativo, atualmente, 2. A matriz de entidades está vazia porque esse aplicativo no momento não tem nenhuma entidade. 

    O resultado em JSON identifica a intenção com a maior pontuação como a propriedade **`topScoringIntent`**. Todas as pontuações estão entre 1 e 0, com a melhor pontuação mais próxima a 1. 

## <a name="applyforjob-intent"></a>Intenção ApplyForJob
Retorne ao site do LUIS e crie uma intenção para determinar se o enunciado do usuário referentes à candidatura a um trabalho.

1. Selecione **Compilar** no menu no canto superior direito para retornar  parte superior, com o botão direito menu para retornar à compilação de aplicativos.

2. Selecione **Intents** no menu esquerdo.

3. Selecione **Criar nova intenção** e insira o nome `ApplyForJob`. 

    ![Caixa de diálogo LUIS para criar a nova intenção](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

4. Adicione vários enunciados a essa intenção a qual você espera que um usuário solicite, por exemplo:

    | Exemplo de enunciados|
    |--|
    |Desejo me candidatar para o novo trabalho de contabilidade|
    |Preencher o aplicativo para a vaga 123456|
    |Enviar currículo para o cargo de engenharia|
    |Este é o meu CV para a vaga 654234|
    |Vaga 567890 e minha papelada|

    [![](media/luis-quickstart-intents-only/utterance-applyforjob.png "Captura de tela da inserção de novos enunciados para a intenção ApplyForJob")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    A intenção rotulada é contornada em vermelho porque LUIS não está certo de que esta é a intenção correta. Treinar o aplicativo informa LUIS que os enunciados estão na intenção correta. 

## <a name="train-again"></a>Treinar novamente

[!INCLUDE[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Publicar novamente

[!INCLUDE[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-again"></a>Obter a intenção novamente

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na nova janela do navegador, digite `Can I submit my resume for job 235986` no final da URL. 

    ```JSON
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9166808
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9166808
        },
        {
          "intent": "GetJobInformation",
          "score": 0.07162977
        },
        {
          "intent": "None",
          "score": 0.0262826588
        }
      ],
      "entities": []
    }
    ```

    Os resultados incluem a nova intenção **ApplyForJob**, bem como as intenções existentes. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Este tutorial criou o aplicativo de RH (Recursos Humanos), criou duas intenções, adicionou enunciados de exemplo a cada intenção, adicionou enunciados de exemplo à intenção Nenhuma, além de treiná-la, publicá-la e testá-la no ponto de extremidade. Essas são as etapas básicas da criação de um modelo LUIS. 

> [!div class="nextstepaction"]
> [Adicionar intenções e entidades predefinidas a este aplicativo](luis-tutorial-prebuilt-intents-entities.md)
