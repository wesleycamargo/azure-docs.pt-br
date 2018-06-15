---
title: O que aconteceu ao meu projeto do serviço de nuvem? | Microsoft Docs
description: Descreve o que acontece em um projeto de serviços de nuvem após a conexão a uma conta de armazenamento do Azure usando os serviços conectados do Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: e88e41edbd062f89e24915889f5b74660ec45513
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790671"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>O que aconteceu ao meu projeto dos serviços de nuvem (serviço conectado do Armazenamento do Azure do Visual Studio)?
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

## <a name="connection-string-for-azure-storage-added"></a>Cadeia de conexão do Armazenamento do Azure adicionada
Elementos foram criados com a cadeia de conexão e chave da conta de armazenamento selecionada. Foram realizadas modificações nos seguintes arquivos:

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**

