---
title: "Arquivo de inclusão"
description: "Arquivo de inclusão"
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 463e8b0122339831d5d6a65b6e41d4f697a82013
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
No Cloud Shell, crie credenciais de implantação com o comando [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az_webapp_deployment_user_set). Esse usuário de implantação é necessário para a implantação do FTP e do Git local em um aplicativo Web. O nome de usuário e a senha estão no nível de conta. _Eles são diferentes das credenciais da sua assinatura do Azure._

No exemplo a seguir, substitua *\<nome de usuário>* e *\<senha>* (incluindo os colchetes) por um novo nome de usuário e senha. O nome do usuário deve ser exclusivo no Azure. A senha deve ter pelo menos oito caracteres, com dois destes três elementos: letras, números, símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Você deve obter uma saída JSON, com a senha mostrada como `null`. Se receber um erro `'Conflict'. Details: 409`, altere o nome de usuário. Se receber um erro ` 'Bad Request'. Details: 400`, use uma senha mais forte.

Você só cria esse usuário de implantação uma vez; é possível usá-lo para todas as implantações do Azure.

> [!NOTE]
> Registre um nome de usuário e uma senha. Você precisará delas para implantar o aplicativo Web mais tarde.
>
>
