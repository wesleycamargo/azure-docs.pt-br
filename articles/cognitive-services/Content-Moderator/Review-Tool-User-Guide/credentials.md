---
title: Credenciais no Azure Content Moderator | Microsoft Docs
description: Gerencie credenciais do Content Moderator para usar com as APIs.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 4531fa4c8bbb7bb9c1daeaaac6f9e7078dae250a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363397"
---
# <a name="manage-credentials"></a>Gerenciar credenciais

Suas credenciais do Content Moderator são criadas nos seguintes locais:

- [O portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [A ferramenta de análise do Content Moderator](http://contentmoderator.cognitive.microsoft.com/)

Este artigo explica onde encontrá-las e como elas se relacionam entre si.

## <a name="the-azure-portal"></a>O Portal do Azure

No painel do portal do Azure, selecione sua conta de Content Moderator. No **Gerenciamento de Recursos**, selecione **Chaves**. Para copiar a chave, selecione o ícone à direita da chave.

![Chaves do Content Moderator no portal do Azure](images/credentials-azure-portal-keys.PNG)

### <a name="how-to-use-your-azure-account-with-the-review-tool"></a>Como usar sua conta do Azure com a ferramenta de análise
Para usar sua chave do Azure com as APIs de análise, copie a ID de Recurso listada na tela **Propriedades** na seguinte captura de tela e insira-a na tela credenciais da ferramenta de análise nos campos **IDs de Recurso na lista de permissões** conforme mostrado na seguinte seção **ID de Recurso**. 

Para usar sua chave do Azure para os fluxos de trabalho disponíveis no Content Moderator, insira-a no campo **Ocp-Apim-Subscription-Key** na seção **Configurações de fluxo de trabalho** conforme mostrado na seguinte seção **Fluxos de trabalho**.

> [!NOTE]
> Depois que você substituir os dois locais na ferramenta de análise com a chave e a ID de recurso de sua assinatura do Azure, sua **Ocp-Apim-Subscription-Key de avaliação** exibida na tela de Credenciais não será mais usada, mas estará sempre disponível.
> A chave de avaliação está limitada a um máximo de 5.000 transações por mês em 1 solicitações por segundo (RPS).

![ID de Recurso do Content Moderator no portal do Azure](images/credentials-azure-portal-resourceid.PNG)


## <a name="the-review-tool"></a>A ferramenta de análise

No painel da ferramenta de análise, na guia **Configurações**, selecione **Credenciais**.

![Credenciais do Content Moderator na ferramenta de análise](images/credentials-trial-resource-workflow.PNG)

A seção a seguir examina a imagem anterior em mais detalhes:


### <a name="api"></a>API

A primeira parte lista seu **ponto de extremidade da API de análise**, **ID da equipe**e o **Ocp-Apim-Subscription-Key (chave de avaliação do Content Moderator)** gerados como parte da criação da sua equipe de análise. Use-os para chamar todas as APIs do Content Moderator, incluindo a API de análise.

Observe também o identificador de região para seu ponto de extremidade de API. Por exemplo, **westus** é a região de "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0"

![Chave do Content Moderator na ferramenta de análise](images/credentials-trialkey.PNG)


### <a name="resource-id"></a>ID de Recurso

A segunda parte inicia como vazia com nenhuma ID de recurso. **Para usar sua chave de assinatura do Azure com a API de análise, navegue até a tela de ID de recurso, como mostrado anteriormente e copie-a para o campo mostrado**. Clique no **'+'** para salvar sua ID de recurso.

> [!NOTE]
> A sua região de assinatura do Content Moderator deve corresponder com a região da equipe de análise para que ele reconheça a sua equipe e acesse os dados de equipe. Por exemplo, nas imagens nessa página, a região **Oeste dos EUA** **(4)** contém a assinatura do Azure do Content Moderator e sua equipe de análise.

![ID de Recurso do Content Moderator na ferramenta de análise](images/credentials-resourceids.PNG)


### <a name="workflows"></a>Fluxos de trabalho

A terceira parte mostra as informações usadas para executar os fluxos de trabalho. Ela começa mostrando a chave de avaliação gerada automaticamente por padrão. 

**Atualize-a com sua chave do Azure ao obter uma assinatura do Azure**. Os dois outros campos permitem usar listas de termos e imagens nas operações de texto de tela e imagem de avaliação respectivamente.

![Credenciais de fluxo de trabalho do Content Moderator na ferramenta de análise](images/credentials-workflow.PNG)


## <a name="next-steps"></a>Próximas etapas

* Saiba como usar as credenciais do Content Moderator nos seus [fluxos de trabalho](workflows.md).
