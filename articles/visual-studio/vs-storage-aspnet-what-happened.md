---
title: O que aconteceu ao meu projeto do ASP.NET? | Microsoft Docs
description: Descreve o que acontece após a adição do Armazenamento do Azure a um projeto do ASP.NET usando os serviços conectados do Visual Studio
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 8f858dc5e3b28f5ea83af295e3dffe2c12fafc53
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2018
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>O que aconteceu ao meu projeto do ASP.NET (serviço conectado do Armazenamento do Azure do Visual Studio)?
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
No arquivo web.config do seu projeto, foi criado um elemento com a cadeia de conexão e chave da conta de armazenamento selecionada.

Para obter mais informações, consulte [ASP.NET](http://www.asp.net).

