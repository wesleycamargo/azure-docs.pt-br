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
ms.openlocfilehash: c30f4b9de279f8c02b7f6bc7fa7d9765972899b1
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2018
ms.locfileid: "50977424"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Configurar CLI da Malha do Service Fabric
A interface de linha de comando (CLI) da Malha do Service Fabric é necessária para implantar e gerenciar recursos na Malha do Service Fabric. 

Para a versão prévia, a CLI da Malha do Azure Service Fabric é gravada como uma extensão da CLI do Azure. Você pode instalá-lo no Azure Cloud Shell ou uma instalação local da CLI do Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-service-fabric-mesh-cli-locally"></a>Instalar a CLI da Malha do Service Fabric localmente
Se você optar por instalar e usar a CLI localmente, será necessário instalar a CLI do Azure versão 2.0.43 ou posterior. Execute `az --version` para encontrar a versão. Para instalar ou atualizar para a versão mais recente da CLI, confira [Instalar a CLI do Azure][azure-cli-install].

Instale o módulo de extensão de CLI da Malha do Azure Service Fabric usando o comando a seguir. 

```azurecli-interactive
az extension add --name mesh
```

Para atualizar um módulo de CLI da Malha do Azure Service Fabric existente, execute o comando a seguir.

```azurecli-interactive
az extension update --name mesh
```
## <a name="next-steps"></a>Próximas etapas

Você também pode configurar o seu [ambiente de desenvolvimento do Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Encontre respostas para [dúvidas e problemas comuns](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
