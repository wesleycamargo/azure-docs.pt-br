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
ms.openlocfilehash: a8f5adc8e6745fdbfd31d1a9cb9407672d61da07
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129198"
---
### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Configurar o cluster AKS para usar Azure Dev Spaces

Abra uma janela Comando e insira os comandos da CLI do Azure a seguir, usando o grupo de recursos que contém o cluster do AKS e o nome do cluster do AKS. O comando configura o cluster com suporte para o Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

