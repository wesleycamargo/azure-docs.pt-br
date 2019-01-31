---
title: Gerenciar configurações
titleSuffix: Language Understanding - Azure Cognitive Services
description: Use o site do LUIS para gerenciar as configurações de conta do usuário e a chave de criação usada em todos os seus aplicativos.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 51b0d3f753ab89e5809e610f5754355d1c5090b0
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228620"
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

