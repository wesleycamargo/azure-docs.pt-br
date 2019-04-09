---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4f061dc66829c49fba6722215dc36ed2ce6464ee
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58919151"
---
No Azure Cloud Shell, configure credenciais de implantação com o comando [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Esse usuário de implantação é necessário para a implantação do FTP e do Git local em um aplicativo Web. O nome de usuário e senha são o nível de conta. _São diferentes das credenciais da sua assinatura do Azure._

No exemplo a seguir, substitua *\<nome de usuário>* e *\<senha>* (incluindo os colchetes) por um novo nome de usuário e senha. O nome do usuário deve ser exclusivo no Azure. A senha deve ter pelo menos oito caracteres, com dois destes três elementos: letras, números, símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Você obterá uma saída JSON com a senha mostrada como `null`. Se receber um erro `'Conflict'. Details: 409`, altere o nome de usuário. Se receber um erro ` 'Bad Request'. Details: 400`, use uma senha mais forte. O nome de usuário da implantação não deve conter o símbolo “@” para pushes locais do Git.

É necessário configurar esse usuário de implantação somente uma vez. Você pode usá-lo para todas as implantações do Azure.

> [!NOTE]
> Registre o nome de usuário e a senha. Você precisará delas para implantar o aplicativo Web mais tarde.
>
>
