---
title: Conectar-se a outros serviços ao moderar conteúdo – Content Moderator
titlesuffix: Azure Cognitive Services
description: Saiba como acessar outras APIs para seus fluxos de trabalho do Content Moderator usando conectores.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 5e56579a856f7b6259acddcbe34f2e5361505cb5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217604"
---
# <a name="connect-to-other-cognitive-services"></a>Conectar-se a outros serviços cognitivos

Fluxos de trabalho do Content Moderator do Azure podem usar outras APIs, além das APIs de Content Moderator. Você pode acessar outras APIs usando um conector no Content Moderator. O conector fornece um link para as outras APIs.

O Content Moderator inclui esses conectores padrão:

* API de Detecção de Emoções
* API de Detecção Facial
* Serviço de nuvem PhotoDNA

![Conectores disponíveis do Content Moderator](images/connectors-1.png)

## <a name="verify-your-credentials"></a>Verificar suas credenciais 

Antes de definir um fluxo de trabalho, verifique se você tem credenciais válidas para a API do conector que você deseja usar:

1.  No painel da ferramenta de análise, selecione **Configurações** > **Conectores**.

  ![No Content Moderator, selecione Conectores](images/connectors-2.png)

2.  Selecione o símbolo **Editar** ao lado do conector para o qual você deseja verificar as credenciais.

  ![No Content Moderator, selecione o símbolo Editar](images/connectors-3.png)

3.  A chave de assinatura é exibida. Se você fizer qualquer edição, selecione **Salvar** quando tiver terminado.

  ![Página Editar conectores do Content Moderator](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>Adicionar um conexão

1.  Antes de adicionar um conector, você precisa de uma chave de assinatura. No painel da ferramenta de análise, selecione **Configurações** > **Credenciais**. Selecione e copie o valor da caixa **Ocp-Admin-Subscription-Key**.

2.  Selecione **Conectores**. Selecione um dos conectores disponíveis que são exibidos no painel da ferramenta de análise. Em seguida, selecione **Conectar**. 

  ![Página Adicionar conector do Content Moderator](images/connectors-5.png)

3.  Na caixa **Ocp-Admin-Subscription-Key**, cole a chave que você copiou. Em seguida, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

* Saiba como usar conectores para [definir fluxos de trabalho personalizados](workflows.md).
