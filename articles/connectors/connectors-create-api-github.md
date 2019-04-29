---
title: Conectar ao GitHub – Aplicativos Lógicos do Azure | Microsoft Docs
description: Monitorar eventos do GitHub com as APIs REST do GitHub e os Aplicativos Lógicos do Azure
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/02/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 0d2ff9368bc244a5afd6fafc40cf476b90a80a52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61462509"
---
# <a name="connect-to-github"></a>Conectar ao GitHub

O GitHub é um serviço de hospedagem de repositório Git baseado na Web que oferece toda a funcionalidade de controle de revisão distribuída e SCM (gerenciamento código-fonte) do Git mais outros recursos.

Para começar com o conector do GitHub, [primeiro crie um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Criar uma conexão com o GitHub

Para usar o conector do GitHub em um aplicativo lógico, é necessário primeiro criar uma *conexão* e, em seguida, fornecer detalhes sobre essas propriedades: 

| Propriedade | Obrigatório | DESCRIÇÃO | 
| -------- | -------- | ----------- | 
| A criptografia do token | Sim | Forneça as credenciais do GitHub. |

Após criar a conexão, será possível executar as ações e ouvir os gatilhos descritos neste artigo.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para gatilhos e ações definidos em Swagger e quaisquer limites, revise os [detalhes do conector](/connectors/github/).

## <a name="find-more-connectors"></a>Localizar mais conectores

* Examine a [Lista de conectores](apis-list.md).