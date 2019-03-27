---
title: 'Início rápido: Implantar aplicativo com o portal do LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Depois que o aplicativo estiver pronto para retornar previsões de expressão para um aplicativo de cliente, como um chat bot, você precisará implantar o aplicativo para o ponto de extremidade de previsão. Neste início rápido, você aprenderá a implantar um aplicativo ao criar um recurso de ponto de extremidade de previsão, atribuindo o recurso ao aplicativo, treinando e publicando o aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 0cf7464188bb502d77c673284cba226ce91a5725
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259713"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Início Rápido: Implantar um aplicativo no portal do LUIS

Depois que o aplicativo estiver pronto para retornar previsões de expressão para um aplicativo de cliente, como um chat bot, você precisará implantar o aplicativo para o ponto de extremidade de previsão. 

Neste início rápido, você aprenderá a implantar um aplicativo ao criar um recurso de ponto de extremidade de previsão, atribuindo o recurso ao aplicativo, treinando e publicando o aplicativo. 

## <a name="prerequisites"></a>Pré-requisitos

* [Assinatura do Azure](https://azure.microsoft.com/free).
* Conclua o [início rápido do portal anterior](get-started-portal-build-app.md) ou [baixe e importe o aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json). 


## <a name="create-endpoint-resource"></a>Crie o recurso do ponto de extremidade

O recurso do ponto de extremidade de previsão é criado no portal do Azure. Esse recurso deve ser usado somente para consultas de previsão de ponto de extremidade. Não use esse recurso para criação de alterações para o aplicativo. 

1. Entre no **[Portal do Azure](https://ms.portal.azure.com/)**. 

1. Selecione a entrada **+** verde no painel superior esquerdo e pesquise `Cognitive Services` no marketplace, em seguida, selecione-o. 

1. Configure a assinatura com as seguintes definições:

    |Configuração|Valor|Finalidade|
    |--|--|--|
    |NOME|`my-cognitive-service-resource`|O nome do recurso do Azure. Você precisa desse nome ao atribuir o recurso ao aplicativo no portal do LUIS.|
    |Assinatura|Sua assinatura|Selecione uma das assinaturas associadas à sua conta.|
    |Local padrão|**Oeste dos EUA**|Região do Azure para esse recurso.|
    |Tipo de preço|**S0**|O padrão de camada de preços para esse recurso.|
    |Grupo de recursos|`my-cognitive-service-resource-group`|Crie um novo grupo de recursos para todos os recursos de serviço cognitivo. Quando você terminar os recursos, poderá excluir o grupo de recursos para limpar sua assinatura. | 

    ![Opção de API do Azure](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png) 

1. Selecione **Criar** para criar o recurso do Azure. 

    Na próxima seção, você aprenderá como conectar esse novo recurso a um aplicativo do LUIS no portal do LUIS. 

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Atribuir a chave de recurso para o aplicativo LUIS no portal do LUIS

Sempre que você criar um novo recurso para LUIS, será necessário atribuir o recurso para o aplicativo LUIS. Depois que for atribuído, você não precisará realizar esta etapa novamente, a menos que você crie um novo recurso. Você pode criar um novo recurso para expandir as regiões do seu aplicativo ou para dar suporte a um número maior de consultas de previsão. 

1. Entre no [portal LUIS](https://www.luis.ai), escolha o aplicativo **myEnglishApp** na lista de aplicativos.

1. Selecione **Gerenciar** no menu superior direito e, em seguida, selecione **Chaves e pontos de extremidade**.

1. Para adicionar o LUIS, selecione **Atribuir Recurso +**.

    [![Atribuir um recurso ao seu aplicativo](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. Selecione seu nome de locatário, assinatura e recursos e selecione **Atribuir recursos**. 

    ![Atribuir um recurso ao seu aplicativo](./media/get-started-portal-deploy-app/assign-resource.png)

1. Localize a nova linha na tabela e copie a URL de ponto de extremidade. Ela é construída corretamente para fazer uma solicitação GET de HTTP para o ponto de extremidade de API do LUIS para uma previsão. 

## <a name="train-and-publish-the-app"></a>Treinar e publicar o aplicativo 

Treine sempre que estiver pronto para testá-lo. Você deve publicar o aplicativo sempre que quiser que a versão atualmente treinada esteja disponível para aplicativos cliente provenientes do tempo de execução de ponto de extremidade de previsão. 

1. Se o aplicativo não estiver treinado, selecione **Treinar** no menu superior direito.

1. Selecione **Publicar** no menu superior direito. Aceite as configurações de ambiente padrão e, em seguida, selecione **Publicar**.

1. Quando a barra de notificação de êxito verde aparecer na parte superior da janela do navegador, selecione o link **Consultar a lista dos pontos de extremidade**. 

    ![Barra de notificação do aplicativo publicado com êxito no navegador](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Isso leva você para a página **configurações de chaves e ponto de extremidade**. A lista de recursos atribuídos e URLs de ponto de extremidade correspondente está na parte inferior da página. 

1. Selecione a URL do ponto de extremidade associada ao seu novo nome de recurso. Isso abre um navegador da web com uma URL construída corretamente para fazer uma solicitação de **GET** para o tempo de execução do ponto de extremidade de previsão. 

1. O `q=` no final da URL é a abreviação de **consulta** e é onde a expressão do usuário é anexada à solicitação GET. Após `q=`, insira a mesma expressão de usuário usado no final do guia de início rápido anterior:

    ```Is there a form named hrf-234098```

    A resposta do navegador é o mesmo JSON que o seu aplicativo cliente receberá:

    ```JSON
    {
    "query": "Is there a form named hrf-234098",
    "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9768753
    },
    "intents": [
        {
        "intent": "FindForm",
        "score": 0.9768753
        },
        {
        "intent": "None",
        "score": 0.0216071066
        }
    ],
    "entities": [
        {
        "entity": "hrf-234098",
        "type": "Human Resources Form Number",
        "startIndex": 22,
        "endIndex": 31
        }
      ]
    }
    ```

    Essa resposta fornece mais informações que o painel de **Teste** padrão no tutorial anterior. Se você quiser ver esse mesmo nível de informações no painel de Teste, o aplicativo deve ser publicado. Em seguida, no painel de Teste **Compare com o publicado**. Use **Mostrar exibição JSON** no painel de teste publicado para ver o mesmo JSON da etapa anterior. Isso permite que você compare o aplicativo atual que você está trabalhando e o aplicativo que é publicado para o ponto de extremidade.

    [![Compare editando no momento versus a versão publicada do aplicativo](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)


## <a name="clean-up-resources"></a>Limpar recursos
Quando você concluir este guia de início rápido, selecione **Meus aplicativos** no menu de navegação superior. Em seguida, selecione na lista e à esquerda a caixa de seleção do aplicativo e selecione **Excluir** na barra de ferramentas de contexto acima da lista. 

[![Excluir aplicativo da lista Meus aplicativos](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Identificar intenções e entidades comuns](luis-tutorial-prebuilt-intents-entities.md)