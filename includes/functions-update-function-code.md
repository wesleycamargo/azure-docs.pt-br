---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/16/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d264477693458ff4132c1f69704a480eed2756e0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987950"
---
## <a name="update-the-function"></a>Atualizar a função

Por padrão, o modelo cria uma função que requer uma tecla de função ao fazer solicitações. Para facilitar o teste da função no Azure, você precisará atualizar a função para permitir acesso anônimo. A maneira de fazer essa alteração depende da linguagem do projeto de funções.

### <a name="c"></a>C\#

Abra o arquivo de código MyHttpTrigger.cs que é sua nova função, atualize o atributo **AuthorizationLevel** na definição de função para o valor `anonymous` e salve as alterações.

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
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
