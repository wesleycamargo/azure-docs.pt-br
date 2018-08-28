---
title: Configurar a CLI da Malha do Azure Service Fabric | Microsoft Docs
description: Saiba como configurar a CLI da Malha do Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/26/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: c0e2aefe1222263b169e21490da079b165a57321
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42108471"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Configurar CLI da Malha do Service Fabric
A CLI da Malha do Service Fabric é necessária para implantar e gerenciar recursos na Malha do Service Fabric. 

Para a versão prévia, a CLI da Malha do Azure Service Fabric é gravada como uma extensão da CLI do Azure. Você pode instalá-lo no Azure Cloud Shell ou uma instalação local da CLI do Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se você optar por instalar e usar a CLI localmente, será necessário instalar a CLI do Azure versão 2.0.43 ou posterior. Execute `az --version` para encontrar a versão. Para instalar ou atualizar para a versão mais recente da CLI, confira [Instalar a CLI do Azure 2.0][azure-cli-install].

Instale o módulo de extensão de CLI da Malha do Azure Service Fabric usando o comando a seguir. 

```azurecli-interactive
az extension add --name mesh
```

Para atualizar um módulo de CLI da Malha do Azure Service Fabric existente, execute o comando a seguir.

```azurecli-interactive
az extension update --name mesh
```

Você também pode configurar o seu [ambiente de desenvolvimento do Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

[azure-cli-install]: /cli/azure/install-azure-cli