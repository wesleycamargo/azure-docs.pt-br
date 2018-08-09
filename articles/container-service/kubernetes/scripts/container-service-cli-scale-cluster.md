---
title: Exemplo de script da CLI do Azure – ajustar a escala de um cluster do ACS | Microsoft Docs
description: Exemplo de script CLI do Azure – ajustar a escala de um cluster do ACS
services: container-service
documentationcenter: ''
author: neilpeterson
manager: jeconnoc
editor: ''
tags: acs, azure-container-service
keywords: Docker, Contêineres, Microsserviços, Kubernetes, DC/SO, Azure
ms.assetid: ''
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: nepeters
ms.openlocfilehash: 1e5ca9fb44ea3ad15206f36a16e61f2865d79f5f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427764"
---
# <a name="scale-an-azure-container-service-cluster"></a>Ajustar a escala de um cluster do Serviço de Contêiner do Azure

Este exemplo ajusta a escala do Serviço de Contêiner do Azure. 

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

```azurecli
az acs scale --resource-group myResourceGroup --name myK8SCluster --new-agent-count 5
```

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar a implantação. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az acs scale](/cli/azure/acs#az-acs-scale) | Dimensionar um cluster do ACS. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de script CLI do Serviço de Contêiner do Azure podem ser encontrados na [documentação do Serviço de Contêiner do Azure](../cli-samples.md).

