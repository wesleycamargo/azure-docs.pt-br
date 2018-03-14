---
title: "Conectar ao GitHub com Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Automatizar os fluxos de trabalho para GitHub com Aplicativos Lógicos do Azure"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/02/2018
ms.author: mandia; ladocs
ms.openlocfilehash: cd7cd3babbfb7efc5917d3a7ec5b9d10112ba791
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2018
---
# <a name="connect-to-github"></a>Conectar ao GitHub

O GitHub é um serviço de hospedagem de repositório Git baseado na Web que oferece toda a funcionalidade de controle de revisão distribuída e SCM (gerenciamento código-fonte) do Git mais outros recursos.

Para começar com o conector do GitHub, [primeiro crie um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Criar uma conexão com o GitHub

Para usar o conector do GitHub em um aplicativo lógico, é necessário primeiro criar uma *conexão* e, em seguida, fornecer detalhes sobre essas propriedades: 

| Propriedade | Obrigatório | DESCRIÇÃO | 
| -------- | -------- | ----------- | 
| A criptografia do token | sim | Forneça as credenciais do GitHub. |

Após criar a conexão, será possível executar as ações e ouvir os gatilhos descritos neste artigo.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para gatilhos e ações definidos em Swagger e quaisquer limites, revise os [detalhes do conector](/connectors/github/).

## <a name="find-more-connectors"></a>Localizar mais conectores

* Examine a [Lista de conectores](apis-list.md).