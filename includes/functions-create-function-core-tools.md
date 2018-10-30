---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: fa1046e8d752e133813924957b439b63fc2acfbd
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987946"
---
## <a name="create-a-function"></a>Criar uma função

O comando a seguir cria uma função disparada por HTTP denominada `MyHtpTrigger`.

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

Quando o comando for executado, você verá algo parecido com a seguinte saída:

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```