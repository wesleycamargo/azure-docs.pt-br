---
title: Gerenciar contas e chaves
titleSuffix: Language Understanding - Azure Cognitive Services
description: As duas partes principais de informação para uma conta LUIS são a conta de usuário e a chave de criador. As informações de logon são gerenciadas em account.microsoft.com. A chave de criação é gerenciada na página Configurações do portal LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: d7d63ad642ab2d3b336e15dcca606077762ceb9d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60195804"
---
# <a name="manage-account-and-authoring-key"></a>Gerenciar conta e chave de criação

As duas partes principais de informação para uma conta LUIS são a conta de usuário e a chave de criador. Suas informações de logon são gerenciadas em [account.microsoft.com](https://account.microsoft.com). Sua chave de criação é gerenciado a partir de [LUIS](luis-reference-regions.md) portal **configurações** página.

## <a name="authoring-key"></a>Chave de criação

Essa única e específica da região de criação chave na **configurações** página, permite que você crie todos os seus aplicativos da [LUIS](luis-reference-regions.md) portal, bem como a [APIs de criação](https://aka.ms/luis-authoring-api). Para sua comodidade, a chave de criador é permitida para fazer um número [limitado](luis-boundaries.md) de consultas de ponto de extremidade a cada mês.

[![Página Configurações de LUIS](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

A chave de criador é usada para todos os seus aplicativos, bem como quaisquer aplicativos que você listou como colaborador.

## <a name="authoring-key-regions"></a>Regiões da chave de criador

A chave de criador é específica para a [região de autoria](luis-reference-regions.md#publishing-regions). A chave não funciona em uma região diferente.

## <a name="reset-authoring-key"></a>Redefinir a chave de criador

Se a chave de criador estiver comprometida, redefina a chave. A chave é redefinida em todos os seus aplicativos na [LUIS](luis-reference-regions.md) portal. Se você criar seus aplicativos por meio de APIs de criador, você precisa alterar o valor de `Ocp-Apim-Subscription-Key` para a nova chave.

## <a name="delete-account"></a>Exclui a conta

Consulte [Armazenamento e remoção de dados](luis-concept-data-storage.md#accounts) para obter informações sobre quais dados são excluídos quando você exclui sua conta.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre sua [chave de criador](luis-concept-keys.md#authoring-key).

