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
ms.openlocfilehash: 8dbc90c3888a9c53db7d2ebeea2dd5f3ee5746a0
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939563"
---
## <a name="clean-up"></a>Limpar
Para excluir completamente uma instância do Azure Dev Spaces em um cluster, incluindo todos os espaços de desenvolvimento e execução de serviços dentro dele, use o comando `az aks remove-dev-spaces`. Tenha em mente que essa ação é irreversível. Você pode adicionar suporte para Azure Dev Spaces novamente no cluster, mas isso será como se você estiver iniciando novamente. Os serviços e os espaços antigos não serão restaurados.

O exemplo a seguir lista os controladores de Azure Dev Spaces em sua assinatura ativa e então exclui o controlador de Azure Dev Spaces associado ao cluster AKS ‘myaks’ do grupo de recursos 'myaks-rg'.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```

