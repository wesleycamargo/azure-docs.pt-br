---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 97c56b07833f7d93541bb0b3747889f5a50a8203
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34675268"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Versão do SDK de Armazenamento do Microsoft Azure em Funções 1. x

Em Funções de 1. x, os gatilhos de armazenamento e associações usam a versão 7.2.1 do SDK de Armazenamento do Microsoft Azure ([windowsazure](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) pacote NuGet). Se você referenciar uma versão diferente do SDK do armazenamento e associar a um tipo de SDK de armazenamento na sua assinatura de função, o tempo de execução de funções pode relatar se não é possível associar a esse tipo. A solução é verificar as referências do projeto [windowsazure 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
