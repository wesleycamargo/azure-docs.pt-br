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
ms.openlocfilehash: 5b009fafc818a06bdda309b3e025251cc0997e47
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59736848"
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

Abra o arquivo function.json para a nova função, abra-o em um editor de texto, atualize a propriedade **authLevel** em **bindings** para `anonymous`e salve as alterações.

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
