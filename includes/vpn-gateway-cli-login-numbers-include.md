---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a72f5f5a1d8c64b347185827906f15d3cb9f55ad
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
1. Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela. Para obter mais informações sobre registro em log, consulte [Introdução à CLI do Azure 2.0](/cli/azure/get-started-with-azure-cli).

  ```azurecli
  az login
  ```
2. Se tiver mais de uma assinatura do Azure, liste as assinaturas para a conta.

  ```azurecli
  az account list --all
  ```
3. Especifique a assinatura que você deseja usar.

  ```azurecli
  az account set --subscription <replace_with_your_subscription_id>
  ```