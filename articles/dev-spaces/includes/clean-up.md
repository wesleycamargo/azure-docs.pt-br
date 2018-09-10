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
ms.openlocfilehash: b8b8b1a593328197c61f8edba9f8f849518dd2e5
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129071"
---
## <a name="clean-up"></a>Limpar
Para excluir completamente uma instância do Azure Dev Spaces em um cluster, incluindo todos os espaços de desenvolvimento e execução de serviços dentro dele, use o comando `az aks remove-dev-spaces`. Tenha em mente que essa ação é irreversível. Você pode adicionar suporte para Azure Dev Spaces novamente no cluster, mas isso será como se você estiver iniciando novamente. Os serviços e os espaços antigos não serão restaurados.

O exemplo a seguir lista os controladores de Azure Dev Spaces em sua assinatura ativa e então exclui o controlador de Azure Dev Spaces associado ao cluster AKS ‘myaks’ do grupo de recursos 'myaks-rg'.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```

