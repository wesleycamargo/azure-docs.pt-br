---
title: Exportar e excluir dados
titleSuffix: Azure Cognitive Services
description: O LUIS (Serviço Inteligente de Reconhecimento Vocal) preserva o conteúdo do cliente para operar o serviço, mas o usuário do LUIS tem controle total sobre a exibição, a exportação e a exclusão dos próprios dados. Isso pode ser feito por meio do portal da Web do LUIS ou de APIs Programáticas do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 62d6dce4ccb5d4badbcf9b02cbbcda576ee63212
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869238"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportar e excluir dados do cliente no LUIS (Serviço Inteligente de Reconhecimento Vocal) nos Serviços Cognitivos

## <a name="summary-of-customer-data-request-features"></a>Resumo dos recursos de solicitação de dados do cliente
O LUIS (Serviço Inteligente de Reconhecimento Vocal) preserva o conteúdo do cliente para operar o serviço, mas o usuário do LUIS tem controle total sobre a exibição, a exportação e a exclusão dos próprios dados. Isso pode ser feito por meio do [portal](luis-reference-regions.md) da Web do LUIS ou de [APIs Programáticas do LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

O conteúdo do cliente foi armazenado criptografado no armazenamento do Azure regional da Microsoft e inclui:

- Conteúdo da conta de usuário coletado no momento do registro
- Dados de treinamento necessários para compilar modelos (ou seja, intenção e entidades)
- Consultas do usuário registradas em log em tempo de execução para ajudar a melhorar os modelos do usuário
  - Os usuários podem desligar o registro em log de consulta anexando `&log=false` à solicitação; detalhes [aqui](luis-resources-faq.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Excluindo os dados do cliente
Os usuários do LUIS têm controle total para excluir qualquer conteúdo de usuário, seja por meio do portal da Web do LUIS, seja pelas APIs Programáticas do LUIS. A tabela a seguir exibe links para ajudar a ajudar com ambos:

| | **Conta do Usuário** | **Aplicativo** | **Enunciado(s)** | **Consultas do usuário final** |
| --- | --- | --- | --- | --- |
| **Portal** | [Link](luis-how-to-account-settings.md) | [Link](luis-how-to-start-new-app.md#delete-app) | [Link](luis-how-to-start-new-app.md#delete-app) | [Link](luis-how-to-start-new-app.md#delete-app) |
| **APIs** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportando os dados do cliente
Os usuários do LUIS têm controle total para exibir os dados no portal, no entanto, eles devem ser exportados por meio das APIs Programáticas do LUIS. A tabela a seguir exibe links para ajudar com exportações de dados por meio das APIs Programáticas do LUIS:

| | **Conta do Usuário** | **Aplicativo** | **Enunciado(s)** | **Consultas do usuário final** |
| --- | --- | --- | --- | --- |
| **APIs** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência de regiões do LUIS](./luis-reference-regions.md)
