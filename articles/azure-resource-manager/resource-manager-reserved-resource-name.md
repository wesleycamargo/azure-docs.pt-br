---
title: Erros de nome de recurso reservado do Azure | Microsoft Docs
description: Descreve como resolver erros ao fornecer um nome de recurso que inclui uma palavra reservada.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 2f06b7bc65ea309bc5374b41f402f8e7bc4d2a38
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2017
---
# <a name="resolve-reserved-resource-name-errors"></a>Resolver erros de nome de recurso reservado

Este artigo descreve o erro encontrado durante a implantação de um recurso que inclui uma palavra reservada em seu nome.

## <a name="symptom"></a>Sintoma

Ao implantar um recurso que está disponível por meio de um ponto de extremidade público, você poderá receber o seguinte erro:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Causa

Recursos que têm um ponto de extremidade público não podem usar palavras reservadas nem marcas no nome.

As seguintes palavras são reservadas:

* ACCESS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

As seguintes palavras não podem ser usadas como uma palavra inteira ou uma subcadeia de caracteres no nome:

* LOGIN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Solução

Forneça um nome que não use uma das palavras reservadas.