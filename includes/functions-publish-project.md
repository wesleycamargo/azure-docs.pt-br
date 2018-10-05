---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/27/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 1b553cbd720fcb76899844712ce5053af46f7ccb
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452948"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Implantar o projeto de aplicativo de funções no Azure

Depois que o aplicativo de funções for criado no Azure, você poderá usar o comando [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) para implantar o código do projeto no Azure.

```bash
func azure functionapp publish <FunctionAppName>
```

Você verá algo semelhante à saída a seguir, que foi truncada para fins de legibilidade.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

Agora é possível testar suas funções no Azure.