---
title: Noções básicas sobre o armazenamento de dados no LUIS – Azure | Microsoft Docs
description: Saiba como os dados são armazenados no LUIS (Reconhecimento vocal)
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2018
ms.author: diberry
ms.openlocfilehash: e4e289db0803a127bdbff5122095ae2125cdbb38
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226185"
---
# <a name="data-storage-and-removal"></a>Armazenamento e remoção de dados
O LUIS armazena dados criptografados em um armazenamento de dados do Azure correspondente à região especificada pela chave. Esses dados são armazenados por 30 dias. 

## <a name="export-and-delete-app"></a>Exportar e excluir o aplicativo
Os usuários têm controle completo sobre a [exportação](luis-how-to-start-new-app.md#export-app) e [exclusão](luis-how-to-start-new-app.md#delete-app) do aplicativo. 

## <a name="utterances-in-an-intent"></a>Declarações em uma intenção
Exclua enunciados de exemplo usados para treinar o [LUIS](luis-reference-regions.md). Se você excluir um enunciado de exemplo do seu aplicativo de LUIS, ele será removido do serviço Web de LUIS e não estará disponível para exportação.

## <a name="utterances-in-review"></a>Revisão de declarações
É possível excluir declarações da lista de declarações do usuário sugeridas pelo LUIS na **[página Examinar declarações de ponto de extremidade](luis-how-to-review-endoint-utt.md)**. Excluir enunciados dessa lista impede que eles sejam sugeridos, mas não os exclui dos logs.

## <a name="accounts"></a>Contas
Se você excluir uma conta, todos os aplicativos serão excluídos, junto com os respectivos logs e enunciados de exemplo. Os dados são mantidos por 60 dias antes da exclusão permanente da conta e dos dados.

A exclusão da conta está disponível na página **Configurações**. Selecione o nome da sua conta na barra de navegação superior direita para chegar à página **Configurações**.

## <a name="data-inactivity-as-an-expired-subscription"></a>Inatividade de dados como uma assinatura expirada
Para os fins da retenção e da exclusão de dados, um aplicativo LUIS inativo pode, _a critério da Microsoft_, ser tratado como uma assinatura expirada. Um aplicativo será considerado inativo se ele atender aos seguintes critérios para os últimos 90 dias: 

* **Não** teve chamadas feitas a ele.
* Não foi modificado.
* Não tem uma chave atual atribuída a ele.
* Não tem uma credencial de usuário para ele.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre como exportar e excluir um aplicativo](luis-how-to-start-new-app.md)