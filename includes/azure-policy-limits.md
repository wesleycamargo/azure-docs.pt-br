---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 0a54dfdb810ea578c1e7c8fcc7ca0343e72164ae
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50964526"
---
Há uma contagem máxima para cada tipo de objeto do Azure Policy. Uma entrada do _Escopo_ significa a assinatura ou o [grupo de gerenciamento](../articles/governance/management-groups/overview.md).

| Where | O que | Contagem máxima |
|---|---|---|
| Escopo | Definições de Política | 250 |
| Escopo | Definições de iniciativa | 100 |
| Locatário | Definições de iniciativa | 1000 |
| Escopo | Atribuições de Política/Iniciativa | 100 |
| Definição de política | parâmetros | 20 |
| Definição de iniciativa | Políticas | 100 |
| Definição de iniciativa | parâmetros | 100 |
| Atribuições de Política/Iniciativa | Exclusões (notScopes) | 250 |
| Regra de política | Condicionais aninhadas | 512 |
