---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3cbe634d862682a5f6b06c2cfc77a4d3b03954f9
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809673"
---
O código para todas as funções em um aplicativo de função específica está localizado em uma pasta raiz (`wwwroot`) que contém um arquivo de configuração de host e uma ou mais subpastas. Cada subpasta contém o código para uma função distinta, como no exemplo a seguir:

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
 | - bin
 | | - mycompiledcsharp.dll
```

O arquivo host.json contém algumas configurações específicas do tempo de execução e encontra-se na pasta raiz do aplicativo de função. Para obter informações sobre as configurações que estão disponíveis, consulte a [referência do host.json](../articles/azure-functions/functions-host-json.md).

Cada função tem uma pasta que contém um ou mais arquivos de código, a configuração function.json e outras dependências. Para um projeto de biblioteca de classes C#, o arquivo de biblioteca de classes compilado (.dll) é implantado na subpasta `bin`.

