---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/12/2019
ms.author: glenga
ms.custom: include file, fasttrack-edit
ms.openlocfilehash: 3b0be31afbce78c0de8dc919245e92bee7af04b0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57964054"
---
## <a name="update-the-function"></a>Atualizar a função

Por padrão, o modelo cria uma função que requer uma tecla de função ao fazer solicitações. Para facilitar o teste da função no Azure, você precisará atualizar a função para permitir acesso anônimo. A maneira de fazer essa alteração depende da linguagem do projeto de funções.

### <a name="c"></a>C\#

Abra o arquivo de código MyHttpTrigger.cs que é sua nova função, atualize o atributo **AuthorizationLevel** na definição de função para o valor `Anonymous` e salve as alterações.

```csharp
[FunctionName("MyHttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    ILogger log)
```

### <a name="javascript"></a>JavaScript

Abra o arquivo function.json para a nova função, abra-o em um editor de texto, atualize a propriedade **authLevel** em **bindings.httpTrigger** para `anonymous`e salve as alterações.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

Agora você pode chamar a função no Azure sem ter que fornecer a tecla de função. A tecla de função nunca é necessária na execução local.
