---
title: "Adicionar o Conector do Twilio aos seus Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Visão geral do Conector do Twilio com os parâmetros da API REST"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 50361a3342a0d14ae02b2cb478bbb0f74b61bba0
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-twilio-connector"></a>Introdução ao conector do Twilio
Conecte-se a Twilio para enviar e receber mensagens SMS, MMS e de IP globais. Com o Twilio, você pode:

* Compile seu fluxo de negócios baseado nos dados obtidos do Twilio. 
* Use ações para obter uma mensagem, listar mensagens e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando você recebe uma nova mensagem do Twilio, você pode obtê-la e usá-la como um fluxo de trabalho do Barramento de Serviço. 

Comece pela criação de um aplicativo lógico; veja [Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-twilio"></a>Criar uma conexão com o Twilio
Ao adicionar esse Conector aos seus aplicativos lógicos, insira os seguintes valores do Twilio:

| Propriedade | Obrigatório | DESCRIÇÃO |
| --- | --- | --- |
| ID da Conta |sim |Insira seu ID de conta do Twilio |
| Token de Acesso |sim |Insira seu token de acesso do Twilio |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Se você não tiver um token de acesso do Twilio, veja [Identidade de usuário e Tokens de acesso](https://www.twilio.com/docs/api/chat/guides/identity).

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/twilio/).

## <a name="more-connectors"></a>Mais conectores
Volte para a [Lista de APIs](apis-list.md).