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
ms.openlocfilehash: 82de8eab089e5f666e1a2ce4eab09bfd2895185b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47020074"
---
1. Inicie sessão na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela. Para obter mais informações sobre como conectar-se, confira [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).

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
