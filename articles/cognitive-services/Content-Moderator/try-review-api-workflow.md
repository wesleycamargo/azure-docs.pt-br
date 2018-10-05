---
title: Fluxos de trabalho de moderação de conteúdo no console da API – Content Moderator
titlesuffix: Azure Cognitive Services
description: Saiba como usar fluxos de trabalho de moderação de conteúdo no console do API.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: sajagtap
ms.openlocfilehash: 4ef8951b30fa7aede08a1af3c834192b5ed18649
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223843"
---
# <a name="workflows-from-the-api-console"></a>Fluxo de trabalho do console de API

Use as [operações de fluxo de trabalho](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) no Content Moderator do Azure para criar ou atualizar um fluxo de trabalho ou obter os detalhes de fluxo de trabalho usando a API de Revisão. Você pode definir expressões simples, complexas e inclusive aninhadas para os fluxos de trabalho usando essa API. Os fluxos de trabalho aparecem na ferramenta de análise para sua equipe usar. Os fluxos de trabalho também são usados por operações de trabalho da API de Revisão.

## <a name="prerequisites"></a>Pré-requisitos

1. Vá para a [ferramenta de análise](https://contentmoderator.cognitive.microsoft.com/). Inscreva-se, caso ainda não tenha feito isso. 
2. Na ferramenta de análise, em **Configurações**, selecione a guia **Fluxos de trabalho**, como mostra o [tutorial do fluxo de trabalho](Review-Tool-User-Guide/Workflows.md) da ferramenta de análise.

### <a name="browse-to-the-workflows-screen"></a>Navegue até a tela de fluxos de trabalho

No painel do Content Moderator, selecione **Análise** > **Configurações** > **Fluxos de trabalho**. Você verá um fluxo de trabalho padrão.

  ![Fluxo de trabalho padrão](images/default-workflow-listed.PNG)

### <a name="get-the-json-definition-of-the-default-workflow"></a>Obter a definição de JSON do fluxo de trabalho padrão

Selecione a opção **Editar** para seu fluxo de trabalho e, em seguida, selecione a guia **JSON**. Você verá a seguinte expressão de JSON:

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isAdult",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": []
        }
        ],
        "Type": "Actions"
        }
    }

## <a name="get-workflow-details"></a>Obter detalhes do fluxo de trabalho

Use a operação **Fluxo de trabalho - Get** para obter detalhes do fluxo de trabalho padrão existente.

Na ferramenta de análise, vá para a seção [Credenciais](Review-Tool-User-Guide/credentials.md#the-review-tool).

### <a name="browse-to-the-api-reference"></a>Navegar até a referência de API

1. Na exibição **Credenciais**, selecione [Referência de API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). 
2. Quando a página **Fluxo de trabalho - Criar ou atualizar** for aberta, vá para a referência [Fluxo de trabalho - Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58).

### <a name="select-your-region"></a>Selecione sua região

Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local.

  ![Seleção de região de Fluxo de trabalho - Get](images/test-drive-region.png)

  O console de API **Fluxo de trabalho - Get** será aberto.

### <a name="enter-parameters"></a>Inserir parâmetros

Insira valores para **equipe**, **workflowname**, e **Ocp-Apim-chave de assinatura** (sua chave de assinatura):

- **team**: A ID da equipe que você criou ao configurar sua [Conta da ferramenta de análise](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: O nome do fluxo de trabalho. Usar o `default`.
- **Ocp-Apim-Subscription-Key**: Localizado na guia **Configurações**. Para mais informações, confira [Visão Geral](overview.md).

  ![Obter cabeçalhos e parâmetros de consulta](images/workflow-get-default.PNG)

### <a name="submit-your-request"></a>Enviar a solicitação
  
Selecione **Enviar**. Se a operação for bem sucedida, o**Status da resposta** será `200 OK` e a caixa **Conteúdo da resposta** exibirá o seguinte fluxo de trabalho JSON:

    {
        "Name": "default",
        "Description": "Default",
        "Type": "Image",
        "Expression": {
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isadult",
            "Operator": "eq",
            "Value": "true",
            "AlternateInput": null,
            "Type": "Condition"
            },
        "Then": {
            "Perform": [{
                "Name": "createreview",
                "Subteam": null,
                "CallbackEndpoint": null,
                "Tags": []
            }],
            "Type": "Actions"
            },
            "Else": null,
            "Type": "Logic"
            }
    }


## <a name="create-a-workflow"></a>Criar um fluxo de trabalho

Na ferramenta de análise, vá para a seção [Credenciais](Review-Tool-User-Guide/credentials.md#the-review-tool).

### <a name="browse-to-the-api-reference"></a>Navegar até a referência de API

Na exibição **Credenciais**, selecione [Referência de API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). A página **Fluxo de trabalho - Criar ou atualizar** será aberta.

### <a name="select-your-region"></a>Selecione sua região

Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local.

  ![Seleção de região da página Fluxo de trabalho - Criar ou atualizar](images/test-drive-region.png)

  O console de API **Fluxo de trabalho - Criar ou atualizar** será aberto.

### <a name="enter-parameters"></a>Inserir parâmetros

Insira valores para **equipe**, **workflowname**, e **Ocp-Apim-chave de assinatura** (sua chave de assinatura):

- **team**: A ID da equipe que você criou ao configurar sua [Conta da ferramenta de análise](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: O nome do novo fluxo de trabalho.
- **Ocp-Apim-Subscription-Key**: Localizado na guia **Configurações**. Para mais informações, confira [Visão Geral](overview.md).

  ![Cabeçalhos e parâmetros de consulta do console Fluxo de trabalho - Criar ou atualizar](images/workflow-console-parameters.PNG)

### <a name="enter-the-workflow-definition"></a>Inserir a definição de fluxo de trabalho

1. Editar a caixa **Corpo da solicitação** para inserir a solicitação JSON com os detalhes para **Descrição** e **Tipo** (Imagem ou Texto). 
2. Para **Expressão**, copie a expressão de fluxo de trabalho padrão da seção anterior, conforme mostrado aqui:

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": 
                // Copy the default workflow expression from the preceding section
        }

    O corpo da solicitação é semelhante à seguinte solicitação JSON:

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": {
                "Type": "Logic",
                "If": {
                    "ConnectorName": "moderator",
                    "OutputName": "isAdult",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                    },
                "Then": {
                "Perform": [
                {
                    "Name": "createreview",
                    "CallbackEndpoint": null,
                    "Tags": [ ]
                }
                ],
                "Type": "Actions"
                }
            }
        }
 
### <a name="submit-your-request"></a>Enviar a solicitação
  
Selecione **Enviar**. Se a operação for bem sucedida, o**Status da resposta** será `200 OK` e a caixa **Conteúdo da resposta** exibirá `true`.

### <a name="check-out-the-new-workflow"></a>Confira o novo fluxo de trabalho

Na ferramenta de análise, selecione **Revisão** > **Configurações** > **Fluxos de trabalho**. O novo fluxo de trabalho é exibido e está pronto para uso.

  ![Revisar lista de ferramentas de fluxos de trabalho](images/workflow-console-new-workflow.PNG)
  
### <a name="review-your-new-workflow-details"></a>Revisar os detalhes do novo fluxo de trabalho

1. Selecione a opção **Editar** para seu fluxo de trabalho e, em seguida, selecione as guias **Designer** e **JSON**.

   ![Guia Designer para um fluxo de trabalho selecionado](images/workflow-console-new-workflow-designer.PNG)

2. Para ver a exibição de JSON do fluxo de trabalho, selecione a guia **JSON**.

## <a name="next-steps"></a>Próximas etapas

* Para obter exemplos mais complexos do fluxo de trabalho, consulte [Visão geral de fluxos de trabalho](workflow-api.md).
* Saiba como usar fluxos de trabalho com [trabalhos moderação de conteúdo](try-review-api-job.md).
