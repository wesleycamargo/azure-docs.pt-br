---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 2808264b4641bda49a53677ebe216a3b53b7d0d9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293630"
---
O código para todas as funções em um aplicativo de funções específico está localizado em uma pasta do projeto raiz que contém um arquivo de configuração do host e uma ou mais subpastas. Cada subpasta contém o código para uma função separada, como na seguinte representação:

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

Na versão 2.x do tempo de execução do Functions, todas as funções no aplicativo de funções devem compartilhar o mesmo trabalhado de idioma.  

O arquivo [host.json](../articles/azure-functions/functions-host-json.md), que contém algumas configurações específicas de tempo de execução, está na pasta raiz do aplicativo de funções. Uma pasta `bin` contém pacotes e outros arquivos de biblioteca exigidos pelo aplicativo de funções. Consulte os requisitos específicos a um idioma para um projeto de aplicativo de função:

* [Biblioteca de classes C# (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Script do C# (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Script em F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



