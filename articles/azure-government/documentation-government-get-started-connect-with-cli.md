---
title: Conectar-se ao Azure Governamental com a CLI do Azure | Microsoft Docs
description: "Informações sobre como gerenciar sua assinatura no Azure Governamental conectando-se à CLI do Azure"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: c7cbe993-375e-4aed-9aa5-2f4b2111f71c
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 6e070870a93d2636f076b339dd401358735a9bd0
ms.lasthandoff: 02/21/2017


---


# <a name="connect-to-azure-government-with-azure-cli"></a>Conectar-se ao Azure Governamental com a CLI do Azure

Para usar a CLI do Azure, você precisa se conectar ao Azure Governamental, em vez do Azure público. A CLI do Azure pode ser usada para gerenciar uma assinatura de grande porte por meio de script, ou para acessar os recursos que não estão disponíveis atualmente no Portal do Azure. Se você usou a CLI do Azure no Público do Azure, é basicamente a mesma coisa.  As diferenças do Azure Governamental são:

Existem algumas maneiras de [instalar a CLI do Azure](https://docs.microsoft.com/en-us/azure/xplat-cli-install). Se você já tiver instalado o Node, a maneira mais fácil é instalar o pacote npm:

Para instalar a CLI de um pacote npm, você precisa baixar e instalar os [Node.js e o npm mais recentes](https://nodejs.org/en/download/package-manager/). Em seguida, execute **npm install** para instalar o pacote azure-cli:

```bash
npm install -g azure-cli
```

Em distribuições Linux, talvez você precise usar **sudo** para executar o comando **npm** com êxito, como segue:

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Se precisar instalar ou atualizar o Node.js e o npm em sua distribuição ou SO do Linux, é recomendável que você instale a versão mais recente do Node.js LTS (4. x). Se você usar uma versão mais antiga, poderá obter erros de instalação.


Após a instalação da CLI do Azure CLI, você precisa fazer logon no Azure Governamental:

```
azure login --username your-user-name@your-gov-tenant.onmicrosoft.com  --environment AzureUSGovernment
```

Quando você estiver conectado, poderá executar normalmente comandos da CLI do Azure:

```
azure webapp list my-resource-group
```
