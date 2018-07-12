---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/01/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88c01e8e57d4a92478b8b1ca0689ff0f8e499b39
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38739275"
---
Quando o host Funções executa localmente, ele grava logs no caminho a seguir:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

No Windows, `<DefaultTempDirectory>` é o primeiro valor encontrado das variáveis de ambiente TMP, TEMP, USERPROFILE ou o diretório do Windows.
No MacOS ou Linux, `<DefaultTempDirectory>` é a variável de ambiente TMPDIR.

> [!NOTE]
> Quando o host Funções for iniciado, ele substituirá a estrutura de arquivos existente no diretório.