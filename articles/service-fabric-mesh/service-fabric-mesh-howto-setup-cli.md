---
title: Configurar a CLI da Malha do Azure Service Fabric | Microsoft Docs
description: Saiba como configurar a CLI da Malha do Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: f56bcf0c844545e8883175da2bd3f22afdcd19ea
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089520"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Configurar CLI da Malha do Service Fabric
A CLI da Malha do Service Fabric é necessária para implantar e gerenciar recursos na Malha do Service Fabric. 

Para a versão prévia, a CLI da Malha do Azure Service Fabric é gravada como uma extensão da CLI do Azure. Você pode instalá-lo no Azure Cloud Shell ou uma instalação local da CLI do Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se você optar por instalar e usar a CLI localmente, será necessário instalar a CLI do Azure versão 2.0.35 ou posterior. Execute `az --version` para encontrar a versão. Para instalar ou atualizar para a versão mais recente da CLI, confira [Instalar a CLI do Azure 2.0][azure-cli-install].

Remova qualquer instalação anterior do módulo da CLI da Malha do Azure Service Fabric.

```azurecli-interactive
az extension remove --name mesh
```

Instale o módulo de extensão de CLI da Malha do Azure Service Fabric usando o comando a seguir. 

```azurecli-interactive
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

Você também pode definir até o seu [ambiente de desenvolvimento do Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

[azure-cli-install]: /cli/azure/install-azure-cli