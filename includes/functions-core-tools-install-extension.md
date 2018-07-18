---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4c9b579534d9a7f2c55e9c589b1738fe060b1cf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
Quando você desenvolve funções localmente, pode instalar as extensões necessárias usando as ferramentas do Azure Functions Core do Terminal ou de um prompt de comando. O comando `func extensions install` a seguir instala a extensão de associação do Azure Cosmos DB:

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Substituir `<taget_version>` por uma versão específica do pacote. Versões válidas são listadas nas páginas de pacotes individuais em [NuGet.org](https://nuget.org).
