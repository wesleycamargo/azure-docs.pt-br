---
title: Criar uma conta de APIs de Serviços Cognitivos no Portal do Azure
titlesuffix: Azure Cognitive Services
description: Como criar uma conta de APIs de Serviços Cognitivos da Microsoft no portal do Azure.
services: cognitive-services
author: garyericson
manager: cgronlun
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: garye
ms.openlocfilehash: 37f53303a3b0c224c1286fb488a796fd5cdee0e5
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386409"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Início rápido: criar uma conta de serviços cognitivos no portal do Azure

Use este início rápido para começar a usar os Serviços Cognitivos do Azure. Esses serviços são representados pelos [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) do Azure, que permitem a conexão a uma ou mais das muitas APIs de Serviços Cognitivos disponíveis.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura válida do Azure. você pode [criar uma conta](https://azure.microsoft.com/free/) gratuitamente.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Criar e assinar um recurso dos Serviços Cognitivos do Azure

1. Entrar para o [portal do Azure](http://portal.azure.com), clique em  **criar+ um recurso**.
    
    ![Selecionar APIs de Serviços Cognitivos](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. Em Azure Marketplace, selecione **AI + Machine Learning**. Se você não encontrar o serviço em que está interessado, clique em **Veja todos** para visualizar o catálogo inteiro de APIs de Serviços Cognitivos.

    ![Selecionar APIs de Serviços Cognitivos](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Na página **Criar**, forneça as seguintes informações:

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para o recurso de serviços cognitivos. É recomendável usar um nome descritivo, por exemplo *MyNameFaceAPIAccount*. |
    | **Assinatura** | Selecione uma das suas assinaturas do Azure disponíveis. |
    | **Localidade** | A localização da sua instância de serviço cognitivo. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de tempo de execução do seu recurso. |
    | **Tipo de preços** | O custo da sua conta de Serviços Cognitivos depende das opções escolhidas e do seu uso. Para obter mais informações, consulte a API [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Grupo de recursos** | O [grupo de recursos do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) que conterá o recurso de serviços Cognitivos. Você pode criar um novo grupo ou adicioná-lo a um grupo pré-existente. |

    ![Tela de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>Acessar seu recurso 

> [!NOTE]
> Proprietários de assinatura podem desabilitar a criação de contas de Serviços Cognitivos para grupos de recursos e inscrições, aplicando [política do Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), atribuindo uma definição de política "Não permitido a tipos de recursos" e especificando **Microsoft.CognitiveServices / accounts** como o tipo de recurso de destino.

Depois de criar seu recurso, você pode acessá-lo no painel do Azure se você fixou-o. Caso contrário, você pode encontrá-lo em **Grupos de Recursos**.

Em seu recurso de Serviços Cognitivos, você pode usar a URL e as chaves do Nó de Extremidade na seção **Visão Geral** para começar a fazer chamadas de API em seus aplicativos.

![Tela de recursos](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Computador visão API tutorial C#](https://docs.microsoft.com/azure/cognitive-services/computer-vision/tutorials/csharptutorial)

## <a name="see-also"></a>Consulte também

* [Início rápido: extrair texto manuscrito de uma imagem](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Tutorial: crie um aplicativo para detectar e enquadrar faces em uma imagem](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Criar uma página da Web de pesquisa personalizada](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Integrar Luis (reconhecimento vocal a linguagem) com um bot usando a estrutura do Bot](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
