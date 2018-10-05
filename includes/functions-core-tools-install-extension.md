---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 09/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f1b53c53b1e5fb089eb9b8a9b816b11a1eea126d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044502"
---
Quando você desenvolve funções localmente, pode instalar as extensões necessárias usando as ferramentas do Azure Functions Core do Terminal ou de um prompt de comando.

Depois de atualizar o arquivo *function.json* para incluir todas as associações que a função precisa, execute o comando a seguir na pasta do projeto.

```bash
func extensions install
```

O comando lê o arquivo *function.json* para saber quais são os pacotes necessários, instala-os e recria o projeto de extensão. Ele adiciona as novas associações na versão atual, mas não atualiza as associações existentes. Use a opção `--force` para atualizar as associações existentes para a versão mais recente nas novas instalações.

Se você deseja instalar uma versão específica de um pacote ou se você deseja instalar pacotes antes de editar o arquivo *function.json*, use o comando `func extensions install` com o nome do pacote, conforme mostrado no exemplo a seguir:

```bash
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Substitua `<target_version>`por uma versão específica do pacote, como`3.0.0-beta5`. Versões válidas são listadas nas páginas de pacotes individuais em [NuGet.org](https://nuget.org).
