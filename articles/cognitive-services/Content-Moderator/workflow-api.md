---
title: Fluxos de trabalho de moderação – Content Moderator
titlesuffix: Azure Cognitive Services
description: Use os fluxos de trabalho usados com as operações de Trabalho da API de Análise para automatizar análises por interação humana com base nas políticas e limites de conteúdo.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 0a13d86afe3d395cb34f592b03c1eb9daa18076b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454844"
---
# <a name="automate-moderation-reviews-with-workflows"></a>Automatizar as análises de moderação com fluxos de trabalho

O Content Moderator inclui ferramentas e APIs para gerenciar fluxos de trabalho. Os fluxos de trabalho são usados com as [operações de Trabalho da API de Análise](review-api.md) para automatizar a criação de análise por interação humana com base nas políticas e limites de conteúdo.

A API de Análise oferece as seguintes maneiras de incluir a supervisão humana no processo de moderação de conteúdo:

1. As operações do **Trabalho** para iniciar a moderação assistida por computador e a criação de análise humana como uma etapa.
1. As operações de **Análise** para criação de análise humana, fora da etapa de moderação.
1. As operações de **Fluxo de trabalho** para gerenciar fluxos de trabalho que automatizam a verificação com limites para a criação de análise.

Este artigo aborda as operações do **Fluxo de trabalho**. Leia a visão geral de [Trabalhos e Análises](review-api.md) para saber mais sobre os trabalhos e as análises de moderação de conteúdo.

A verificação do fluxo de trabalho **padrão** é a melhor maneira de começar a reconhecer os fluxos de trabalho no Content Moderator.

## <a name="your-first-workflow"></a>Seu primeiro fluxo de trabalho

Seu primeiro fluxo de trabalho vem com a [equipe de ferramenta de análise](https://contentmoderator.cognitive.microsoft.com/). Inscreva-se, caso ainda não tenha feito isso.

Navegue até a tela [Fluxos de trabalho da ferramenta de análise](Review-Tool-User-Guide/Workflows.md) na guia Configurações. Você vê um fluxo de trabalho **padrão**, conforme mostrado na imagem a seguir:

![Fluxos de trabalho do Content Moderator](Review-Tool-User-Guide/images/2-workflows-1.png)

### <a name="open-the-default-workflow"></a>Abrir o fluxo de trabalho padrão

Use a opção **editar** para abrir a página de edição do fluxo de trabalho, conforme mostrado na imagem a seguir: ![Fluxo de trabalho padrão do Content Moderator](images/default-workflow-listed.PNG)

### <a name="the-designer-view"></a>A exibição do designer

Você vê a guia **Designer** para o fluxo de trabalho. A exibição de designer mostra as seguintes etapas:

1. A **condição** para o fluxo de trabalho a ser avaliado. Nesse caso, o fluxo de trabalho chama a API de imagem do Content Moderator e verifica se a `isAdult` saída é igual a `true`.
1. A **ação** a ser executada se a condição for atendida. Nesse caso, o fluxo de trabalho cria uma análise na ferramenta de análise se a `isAdult` saída for `true`.

![Fluxo de trabalho padrão do Content Moderator - designer](images/default-workflow-designer.png)

### <a name="the-json-view"></a>A exibição JSON

Selecione a guia **JSON** para ver a definição JSON do fluxo de trabalho.

    {
        "Type": "Logic",
        If": {
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

### <a name="key-learning"></a>Aprendizado chave

Os fluxos de trabalho no Content Moderator são fáceis de configurar e flexíveis. Se o designer interno não atender aos requisitos, grave a definição do fluxo de trabalho no formato **JSON**. Em seguida, use a definição JSON com a [API de Fluxo de Trabalho](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) para criar e gerenciar o fluxo de trabalho a partir do aplicativo.

## <a name="define-a-custom-workflow"></a>Definir um fluxo de trabalho personalizado

Os recursos de fluxo de trabalho do Content Moderator permitem definir e usar fluxos de trabalho personalizados. Use o artigo sobre [como fazer fluxos de trabalho de ferramenta de análise](Review-Tool-User-Guide/Workflows.md) para definir um fluxo de trabalho personalizado. Esse fluxo de trabalho usa o recurso OCR do Content Moderator para extrair texto de uma imagem de exemplo. Em seguida, cria uma análise na ferramenta de análise.

### <a name="the-sample-image"></a>A imagem de exemplo

Salve a [imagem de exemplo](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) na unidade local. Essa imagem será necessária para o exercício.

### <a name="the-designer-view"></a>A exibição do designer

Selecione a guia **Designer** e o [tutorial de criação de fluxo de trabalho](Review-Tool-User-Guide/Workflows.md) para definir um fluxo de trabalho personalizado. A imagem a seguir mostra a exibição da **Condition** do designer. Consulte o tutorial para ver o restante das etapas.

![Content Moderator - Condição de fluxo de trabalho](Review-Tool-User-Guide/images/ocr-workflow-step-2-condition.PNG)

### <a name="the-json-view"></a>A exibição JSON

Selecione a guia **JSON** para ver a seguinte definição JSON do fluxo de trabalho personalizado. Observe como as instruções **If-Then** na definição JSON correspondem às etapas que você definiu usando a exibição de designer.

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="workflow-result"></a>Resultado do fluxo de trabalho

Após testar o fluxo de trabalho na tela de fluxos de trabalho, a seguinte análise será criada. Navegue até a guia **Imagem** em **Análise** para ver a análise.
O fluxo de trabalho criou a análise porque a condição principal foi positiva para a presença de texto. A análise também realçou a marca **`a`** na análise de imagem.

![Content Moderator - saída simples de fluxo de trabalho](images/ocr-sample-image-workflow1.PNG)


## <a name="advanced-workflow-with-combination"></a>Fluxo de trabalho avançado com combinação

### <a name="the-sample-image"></a>A imagem de exemplo

Use a mesma [imagem de exemplo](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) que foi usada na seção anterior.

No entanto, desta vez, altere a condição primária para uma combinação de duas verificações. Além de verificar o texto, verifique se o texto tem algum conteúdo ofensivo. O fluxo de trabalho criará uma análise, se encontrar texto **e** detectar conteúdo ofensivo.

### <a name="the-designer-view"></a>A exibição do designer

Para alterar a **Condição** para uma **Combinação**, modifique o fluxo de trabalho. A imagem a seguir mostra a nova exibição que você visualiza no designer.

![Content Moderator - Condição de fluxo de trabalho modificada](images/ocr-workflow-2-designer.PNG)

### <a name="the-json-view"></a>A exibição JSON

Selecione a guia **JSON** para ver a seguinte definição JSON do fluxo de trabalho personalizado modificado. Observe como as instruções **If-Then** na definição JSON correspondem às novas etapas adicionadas ao fluxo de trabalho.

    {
        "Type": "Logic",
        "If": {
        "Left": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Right": {
            "ConnectorName": "moderator",
            "OutputName": "text.HasProfanity",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition",
            "AlternateInput": "moderator.ocrText"
            },
        "Combine": "AND",
        "Type": "Combine"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

    
### <a name="workflow-result"></a>Resultado do fluxo de trabalho

Depois de testar novamente o fluxo de trabalho, você achar que nenhuma análise é criado. Para confirmar a ausência de qualquer análise, navegue até a guia **Imagem** em **Análise**.
O fluxo de trabalho não criou a análise porque não detectou conteúdo ofensivo no texto extraído.

![Content Moderator - saída do fluxo de trabalho modificado](images/ocr-workflow-2-result.PNG)


## <a name="the-workflow-api"></a>A API de Fluxo de Trabalho

As [operações de Fluxo de Trabalho](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) fornecem a interface de programação para os recursos de fluxo de trabalho. Você cria fluxos de trabalho, obtém detalhes do fluxo de trabalho e atualiza as definições de fluxo de trabalho usando a API de Fluxo de Trabalho.

### <a name="get-all-workflow-details"></a>Obter [Todos] detalhes do fluxo de trabalho

A operação **Workflow-Get** aceita as seguintes entradas:

- **team**: a ID da equipe que você criou ao configurar sua [conta da ferramenta de análise](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: o nome do fluxo de trabalho. Use `default` para começar.
- **Ocp-Apim-Subscription-Key**: localizada na guia **Configurações**. Para mais informações, confira [Visão Geral](overview.md).

Se a operação for com êxito, o **Status da resposta** será `200 OK` e a caixa **Conteúdo da resposta** exibirá a definição do fluxo de trabalho no formato JSON.
Para saber mais, leia o [Início rápido do console de API de Fluxo de Trabalho](try-review-api-job.md).

### <a name="create-or-update-workflow"></a>Criar ou atualizar o fluxo de trabalho

A operação de criação e atualização permite criar fluxo de trabalho a partir da API.

A operação **Fluxo de Trabalho - Criar ou Atualizar** aceita as seguintes entradas:

- **team**: a ID da equipe que você criou ao configurar sua [conta da ferramenta de análise](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: o nome do fluxo de trabalho. Use `default` para começar.
- **Ocp-Apim-Subscription-Key**: localizada na guia **Configurações**. Para mais informações, confira [Visão Geral](overview.md).

Se a operação for com êxito, o**Status da resposta** será `200 OK` e a caixa **Conteúdo da resposta** exibirá `true`. Para saber mais, faça o [test drive da `Create` operação](try-review-api-job.md).

## <a name="next-steps"></a>Próximas etapas

Para saber como criar fluxos de trabalho personalizados, confira o [tutorial do fluxo de trabalho da ferramenta de análise](Review-Tool-User-Guide/Workflows.md). 

Faça test drive do [Console de API de Fluxo de Trabalho](try-review-api-job.md) e use os exemplos de código de API REST. 

Por fim, use seus fluxos de trabalho personalizados com o **trabalho** operações, conforme mostrado na [console API Job](try-review-api-job.md) e o [início rápido do .NET de trabalhos](moderation-jobs-quickstart-dotnet.md).
