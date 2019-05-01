---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: craigshoemaker
ms.service: functions
ms.topic: include
ms.date: 09/25/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fc5b43dcdee394fea023124171fb42c1a18224dc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733261"
---
Pacotes de extensão fazer todas as associações, publicadas pela equipe do Azure Functions disponível por meio de uma configuração na *host. JSON* arquivo. Para o desenvolvimento local, verifique se você tem a versão mais recente do [as ferramentas básicas do Azure Functions](../articles/azure-functions/functions-run-local.md#install-the-azure-functions-core-tools).

Para usar pacotes de extensão, atualize o *host. JSON* arquivo para incluir a seguinte entrada para `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

- O `id` propriedade faz referência ao namespace para pacotes de extensão do Microsoft Azure Functions.
- O `version` faz referência a versão do pacote.

Incremento de versões do pacote como pacotes in das alterações do pacote. Alterações de versão principal acontecem somente quando os pacotes no pacote movem uma versão principal. O `version` usos de propriedade de [notação de intervalo para especificar os intervalos de versão](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). O tempo de execução de funções sempre escolhe a versão permitida máximo definida pelo intervalo de versão ou intervalo.

Depois de fazer referência os pacotes de extensão em seu projeto, em seguida, todas as associações padrão estão disponíveis para suas funções. As associações disponíveis na [pacote de extensão](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) são:

|Pacote  |Version  |
|---------|---------|
|Microsoft.Azure.WebJobs.Extensions.CosmosDB|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.DurableTask|1.8.0|
|Microsoft.Azure.WebJobs.Extensions.EventGrid|2.0.0|
|Microsoft.Azure.WebJobs.Extensions.EventHubs|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SendGrid|3.0.0|
|Microsoft.Azure.WebJobs.Extensions.ServiceBus|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SignalRService|1.0.0|
|Microsoft.Azure.WebJobs.Extensions.Storage|3.0.4|
|Microsoft.Azure.WebJobs.Extensions.Twilio|3.0.0|