---
title: Arquivo de inclusão
description: Arquivo de inclusão
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 2563f7c36283521541562bcd88f973d86a6f672a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198979"
---
## <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Configurar o cluster AKS para usar Azure Dev Spaces

Abra uma janela de comando e insira os comandos da CLI do Azure a seguir, usando o grupo de recursos que contém o cluster AKS e o nome do cluster AKS:

   ```cmd
   az extension add --name dev-spaces-preview 
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```
O primeiro comando instala uma extensão da CLI do Azure para adicionar suporte aos Azure Dev Spaces e o segundo configura o cluster com suporte para o Azure Dev Spaces.
