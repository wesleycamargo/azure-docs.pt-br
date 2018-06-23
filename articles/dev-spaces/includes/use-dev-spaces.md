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
ms.openlocfilehash: 44ce986fcfdf079d3077c007a378f3467073d00d
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823173"
---
### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Configurar o cluster AKS para usar Azure Dev Spaces

Abra uma janela Comando e insira os comandos da CLI do Azure a seguir, usando o grupo de recursos que contém o cluster do AKS e o nome do cluster do AKS. O comando configura o cluster com suporte para o Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

