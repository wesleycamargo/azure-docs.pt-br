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
ms.openlocfilehash: 95d89da66935ce933fee082a5f53ee2e36ea953f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344679"
---
No Cloud Shell, configure credenciais de implantação com o comando [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Esse usuário de implantação é necessário para a implantação do FTP e do Git local em um aplicativo Web. O nome de usuário e a senha estão no nível de conta. _Eles são diferentes das credenciais da sua assinatura do Azure._

No exemplo a seguir, substitua *\<nome de usuário>* e *\<senha>* (incluindo os colchetes) por um novo nome de usuário e senha. O nome do usuário deve ser exclusivo no Azure. A senha deve ter pelo menos oito caracteres, com dois destes três elementos: letras, números, símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Você deve obter uma saída JSON, com a senha mostrada como `null`. Se receber um erro `'Conflict'. Details: 409`, altere o nome de usuário. Se receber um erro ` 'Bad Request'. Details: 400`, use uma senha mais forte.

Você precisa configurar esse usuário de implantação uma única vez; é possível usá-lo para todas as implantações do Azure.

> [!NOTE]
> Registre um nome de usuário e uma senha. Você precisará delas para implantar o aplicativo Web mais tarde.
>
>
