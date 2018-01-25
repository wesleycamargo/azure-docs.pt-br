---
title: "Conector do Wunderlist no Aplicativo Lógico do Azure | Microsoft Docs"
description: "Criar uma conexão com o Wunderlist e usar essa conexão para compilar seu fluxo de trabalho nos aplicativos lógicos."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 3657955ca4280fecd3a0fb1ea64b90e0a5c5c765
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-wunderlist-connector"></a>Introdução ao conector do Wunderlist
O Wunderlist fornece um gerenciador de tarefas e de lista de tarefas pendentes para ajudar as pessoas a realizar seu trabalho.  Se você estiver compartilhando uma lista de compras com alguém da família, trabalhando em um projeto ou planejando suas férias, o Wunderlist facilitará a captura, o compartilhamento e a realização de suas listas de tarefas pendentes. O Wunderlist é sincronizado instantaneamente com seu telefone, tablet e computador, para que você possa acessar todas as tarefas de qualquer lugar.

Comece pela criação de um aplicativo lógico; veja [Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-wunderlist"></a>Criar uma conexão com o Wunderlist
Para criar Aplicativos lógicos com o Wunderlist, primeiro você deve criar uma **conexão**, em seguida, forneça os detalhes para as seguintes propriedades:

| Propriedade | Obrigatório | DESCRIÇÃO |
| --- | --- | --- |
| A criptografia do token |sim |Fornecer credenciais do Wunderlist |

Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos.

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/wunderlist/).

## <a name="more-connectors"></a>Mais conectores
Volte para a [Lista de APIs](apis-list.md).