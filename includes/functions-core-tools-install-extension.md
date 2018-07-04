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
ms.openlocfilehash: d166a77a0636efea3b63660fde2187e3f2ec15c0
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063741"
---
Quando você desenvolve funções localmente, pode instalar as extensões necessárias usando as ferramentas do Azure Functions Core do Terminal ou de um prompt de comando. 

Depois de atualizar seu arquivo *function.json* para incluir todas as associações de sua função precisa, execute o `func extensions install` comando na pasta do projeto. O comando lê o arquivo *function.json* arquivo para ver os pacotes que você precisa e, em seguida, instala-os.

Se você deseja instalar uma versão específica de um pacote ou se você deseja instalar pacotes antes de editar o arquivo *function.json*, use o comando `func extensions install` com o nome do pacote, conforme mostrado no exemplo a seguir:

```
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Substitua `<target_version>`por uma versão específica do pacote, como`3.0.0-beta5`. Versões válidas são listadas nas páginas de pacotes individuais em [NuGet.org](https://nuget.org).
