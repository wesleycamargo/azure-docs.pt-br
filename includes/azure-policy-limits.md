---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/16/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 0e5d1214a8e1af8299cb40d1a3b31ff6eafc8c5c
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40185144"
---
Há uma contagem máxima para cada tipo de objeto do Azure Policy. Uma entrada do _Escopo_ significa a assinatura ou o [grupo de gerenciamento](../articles/azure-resource-manager/management-groups-overview.md).

| Where | O que | Contagem máxima |
|---|---|---|
| Escopo | Definições de Política | 250 |
| Escopo | Definições de iniciativa | 100 |
| Locatário | Definições de iniciativa | 1000 |
| Escopo | Atribuições de Política/Iniciativa | 100 |
| Definição de política | parâmetros | 20 |
| Definição de iniciativa | Políticas | 100 |
| Definição de iniciativa | parâmetros | 100 |
| Atribuições de Política/Iniciativa | Exclusões (notScopes) | 100 |
| Regra de política | Condicionais aninhadas | 512 |
