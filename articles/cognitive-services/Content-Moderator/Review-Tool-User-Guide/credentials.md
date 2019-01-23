---
title: Gerenciar credenciais no Azure Content Moderator – Content Moderator
titlesuffix: Azure Cognitive Services
description: Gerencie credenciais do Content Moderator, que serão necessárias para usar as APIs.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 3d183870938f44cd71f1dab4105eec390ba12d94
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266138"
---
# <a name="manage-content-moderator-service-credentials"></a>Gerenciar credenciais de serviço do Content Moderator

Suas credenciais do Content Moderator são criadas nos seguintes locais:

- [O portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [A ferramenta de análise do Content Moderator](https://contentmoderator.cognitive.microsoft.com/)

Este artigo explica onde encontrá-las e como elas se relacionam entre si.

## <a name="the-azure-portal"></a>O Portal do Azure

No painel do portal do Azure, selecione sua conta de Content Moderator. No **Gerenciamento de Recursos**, selecione **Chaves**. Para copiar a chave, selecione o ícone à direita da chave.

![Chaves do Content Moderator no portal do Azure](images/credentials-azure-portal-keys.PNG)

### <a name="use-the-azure-account-with-the-review-tool-and-review-api"></a>Usar a conta do Azure com a ferramenta de análise e a API de análise
Para usar sua chave do Azure com as APIs de análise, copie a ID de Recurso listada na tela **Propriedades** na seguinte captura de tela e insira-a na tela credenciais da ferramenta de análise nos campos **IDs de Recurso na lista de permissões** conforme mostrado na seguinte seção **ID de Recurso**. 

> [!NOTE]
> A sua região de assinatura do Content Moderator deve corresponder com a região da equipe de análise para que ele reconheça a sua equipe e acesse os dados de equipe. Por exemplo, nas imagens nessa página, a região **Oeste dos EUA** **(4)** contém a assinatura do Azure do Content Moderator e sua equipe de análise.
>
> Depois que você substituir os dois locais na ferramenta de análise com a chave e a ID de recurso de sua assinatura do Azure, sua **Ocp-Apim-Subscription-Key de avaliação** exibida na tela de Credenciais não será mais usada, mas estará sempre disponível.
> A chave de avaliação está limitada a um máximo de 5.000 transações por mês em 1 solicitações por segundo (RPS).

![ID de Recurso do Content Moderator no portal do Azure](images/credentials-azure-portal-resourceid.PNG)

### <a name="use-the-azure-account-with-the-workflows-in-the-review-tool"></a>Usar a conta do Azure com os fluxos de trabalho na ferramenta de análise

Para usar sua chave do Azure para os fluxos de trabalho disponíveis no Content Moderator, insira-a no campo **Ocp-Apim-Subscription-Key** na seção **Configurações de fluxo de trabalho** conforme mostrado na seguinte seção **Fluxos de trabalho**. Clique no **'+'** para salvar sua ID de recurso.

![Credenciais de fluxo de trabalho do Content Moderator na ferramenta de análise](images/credentials-workflow.PNG)

## <a name="the-review-tool"></a>A ferramenta de análise

No painel da ferramenta de análise, na guia **Configurações**, selecione **Credenciais**.

![Credenciais do Content Moderator na ferramenta de análise](images/credentials-trial-resource-workflow.PNG)

A seção a seguir examina a imagem anterior em mais detalhes:

### <a name="api"></a>API

A primeira parte lista seu **ponto de extremidade da API de análise**, **ID da equipe**e o **Ocp-Apim-Subscription-Key (chave de avaliação do Content Moderator)** gerados como parte da criação da sua equipe de análise. Use-os para chamar todas as APIs do Content Moderator, incluindo a API de análise.

Observe também o identificador de região para seu ponto de extremidade de API. Por exemplo, **westus** é a região de "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0"

![Chave do Content Moderator na ferramenta de análise](images/credentials-trialkey.PNG)

### <a name="resource-id"></a>ID de Recurso

Esse conjunto de campos é abordado na seção anterior, [usar a conta do Azure com a ferramenta de revisão e a API](credentials.md#use-the-azure-account-with-the-review-tool-and-review-api). Este campo geralmente fica em branco, a menos que você adicione sua ID de Recurso do Azure a ele conforme explicado na seção anterior.

### <a name="workflows"></a>Fluxos de trabalho

Esse conjunto de campos é abordado na seção anterior, [usar a conta do Azure com os fluxos de trabalho na ferramenta de revisão](credentials.md#use-the-azure-account-with-the-workflows-in-the-review-tool). Por padrão, a ferramenta de análise usa sua chave de avaliação gerada automaticamente para a execução de fluxos de trabalho, e é isso que é exibido a princípio. Os dois outros campos permitem usar listas de termos e imagens nas operações de texto de tela e imagem de avaliação respectivamente.

## <a name="next-steps"></a>Próximas etapas

* Saiba como usar as credenciais do Content Moderator nos seus [fluxos de trabalho](workflows.md).
