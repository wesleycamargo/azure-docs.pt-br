---
title: O que aconteceu com meu projeto do ASP.NET 5 (serviços conectados do Visual Studio) | Microsoft Docs
description: Descreve o que acontece após a conexão a uma conta de armazenamento do Azure em um projeto do ASP.NET 5 do Visual Studio usando os serviços conectados do Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: f99ba4b6c954ae9faa87b9604c06e94c56e4f631
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>O que aconteceu com meu projeto do ASP.NET 5 (serviços conectados do Armazenamento do Azure do Visual Studio)?
## <a name="references-added"></a>Referências adicionadas
O pacote do NuGet do armazenamento do Azure foi adicionado ao seu projeto do Visual Studio.  
Esse pacote adiciona as referências de .NET a seguir:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

Além disso, o pacote NuGet **Microsoft.Framework.Configuration.Json** foi adicionado.

## <a name="connection-string-for-azure-storage-added"></a>Cadeia de conexão do Armazenamento do Azure adicionada
No arquivo config.json do seu projeto, foi criado um elemento com a cadeia de conexão e chave da conta de armazenamento selecionada.

Para obter mais informações, consulte [ASP.NET 5](http://www.asp.net/vnext).

