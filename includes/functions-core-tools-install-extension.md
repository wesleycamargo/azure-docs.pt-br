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
ms.openlocfilehash: 6fb497a5b6da00dece43c7f41ea3c411f385a2ba
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726880"
---
Quando você desenvolve funções localmente, pode instalar as extensões necessárias usando as ferramentas do Azure Functions Core do Terminal ou de um prompt de comando. 

Depois de atualizar seu arquivo *function.json* para incluir todas as associações de sua função precisa, execute o `func extensions install` comando na pasta do projeto. O comando lê o arquivo *function.json* arquivo para ver os pacotes que você precisa e, em seguida, instala-os.

Se você deseja instalar uma versão específica de um pacote ou se você deseja instalar pacotes antes de editar o arquivo *function.json*, use o comando `func extensions install` com o nome do pacote, conforme mostrado no exemplo a seguir:

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Substituir `<target_version>` por uma versão específica do pacote. Versões válidas são listadas nas páginas de pacotes individuais em [NuGet.org](https://nuget.org).
