---
title: "Adicionar o Conector do Yammer ao seu Aplicativo Lógico do Azure | Microsoft Docs"
description: "Visão geral do Conector do Yammer com os parâmetros da API REST"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 37f72d829fc50a0f967f08e068c553f5026f35eb
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-yammer-connector"></a>Introdução ao conector do Yammer
Conecte-se ao Yammer para acessar conversas em sua rede corporativa. Com o Yammer, você pode:

* Crie seu fluxo de negócios baseado nos dados obtidos do Yammer. 
* Use gatilhos para quando há uma nova mensagem em um grupo ou um feed a seguir.
* Use as ações para postar uma mensagem, obter todas as mensagens e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando uma nova mensagem for exibida, você poderá enviar um email usando o Office 365.

Comece pela criação de um aplicativo lógico; veja [Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-yammer"></a>Criar uma conexão com o Yammer
Para usar o conector do Yammer, você primeiro cria uma **conexão**, em seguida, fornece os detalhes dessas propriedades: 

| Propriedade | Obrigatório | DESCRIÇÃO |
| --- | --- | --- |
| A criptografia do token |sim |Fornecer suas credenciais do Yammer |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/yammer/).

## <a name="more-connectors"></a>Mais conectores
Volte para a [Lista de APIs](apis-list.md).