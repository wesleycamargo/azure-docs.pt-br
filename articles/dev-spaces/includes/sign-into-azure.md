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
ms.openlocfilehash: d8ef59b157dc01c50d96561df31bbca4a8505018
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38728863"
---
### <a name="sign-in-to-azure-cli"></a>Entrar na CLI do Azure
Entre no Azure. Digite o seguinte comando em uma janela de terminal:

```cmd
az login
```

> [!Note]
> Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita](https://azure.microsoft.com/free).

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Caso tenha várias assinaturas do Azure...
Você pode exibir suas assinaturas, executando: 

```cmd
az account list
```
Localize a assinatura que tem `isDefault: true`na saída do JSON.
Se não for a assinatura que você deseja usar, é possível alterar a assinatura padrão:

```cmd
az account set --subscription <subscription ID>
```
