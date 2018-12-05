---
title: Gerenciar suas configurações de conta em LUIS | Microsoft Docs
description: Use o website do LUIS para gerenciar suas configurações de conta.
titleSuffix: Azure
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: bb41331228e700c55da21c627d617d16faa2dcb9
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52335393"
---
# <a name="manage-account-and-authoring-key"></a>Gerenciar conta e chave de criação
As duas partes principais de informação para uma conta LUIS são a conta de usuário e a chave de criador. Suas informações de logon são gerenciadas em [account.microsoft.com](https://account.microsoft.com). A chave de criação é gerenciada no site do [LUIS](luis-reference-regions.md) na página **Configurações**. 

## <a name="authoring-key"></a>Chave de criação

Essa chave única de criação específica à região, na página **Configurações**, permite que você crie todos os aplicativos no site do [LUIS](luis-reference-regions.md) assim como nas [APIs de criação](https://aka.ms/luis-authoring-api). Para sua comodidade, a chave de criador é permitida para fazer um número [limitado](luis-boundaries.md) de consultas de ponto de extremidade a cada mês. 

[![Página Configurações de LUIS](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

A chave de criador é usada para todos os seus aplicativos, bem como quaisquer aplicativos que você listou como colaborador.

## <a name="authoring-key-regions"></a>Regiões da chave de criador
A chave de criador é específica para a [região de autoria](luis-reference-regions.md#publishing-regions). A chave não funciona em uma região diferente. 

## <a name="reset-authoring-key"></a>Redefinir a chave de criador
Se a chave de criador estiver comprometida, redefina a chave. A chave é redefinida em todos os seus aplicativos no website de [LUIS](luis-reference-regions.md). Se você criar seus aplicativos por meio de APIs de criador, você precisa alterar o valor de `Ocp-Apim-Subscription-Key` para a nova chave. 

## <a name="delete-account"></a>Exclui a conta
Consulte [Armazenamento e remoção de dados](luis-concept-data-storage.md#accounts) para obter informações sobre quais dados são excluídos quando você exclui sua conta. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre sua [chave de criador](luis-concept-keys.md#authoring-key). 

