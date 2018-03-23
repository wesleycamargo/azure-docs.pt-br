---
title: Restrições de IP do Serviço de Aplicativo do Azure | Microsoft Docs
description: Como usar as restrições de IP do Serviço de Aplicativo do Azure
author: btardif
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/23/2017
ms.author: byvinyal
ms.openlocfilehash: 72416cfcd05767b223cc92ac28bd0e736516ddf6
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-app-service-static-ip-restrictions"></a>Restrições de IP estático do Serviço de Aplicativo do Azure #

As restrições de IP permitem definir uma lista de endereços IP cujo acesso ao aplicativo é permitido. A lista de permissões pode incluir endereços IP individuais ou um intervalo de endereços IP definido por uma máscara de sub-rede.

Quando um cliente gera uma solicitação para o aplicativo, seu endereço IP é buscado na lista de permissões. Se o endereço IP não estiver na lista, o aplicativo responderá com um código de status [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

Restrições de IP são definidas no web.config que seu aplicativo consome em tempo de execução (mais exatamente, as restrições são inseridas em um conjunto de endereços IP permitidos no arquivo applicationHost.config, portanto, se você também adicionar um conjunto de endereços IP permitidos no arquivo web.config, eles terão precedência). Em determinadas circunstâncias, alguns módulos podem ser executados antes da lógica de restrições de IP no pipeline de HTTP. Quando isso acontece, a solicitação falha com um código de erro de HTTP diferente.

As restrições de IP são avaliadas nas mesmas instâncias do Plano do Serviço de Aplicativo atribuídas ao seu aplicativo.

Para adicionar uma regra de restrição de IP ao aplicativo, use o menu para abrir **Rede**>**Restrições de IP** e clique em **Configurar Restrições de IP**

![Restrições de IP](media/app-service-ip-restrictions/ip-restrictions.png)  

A partir deste ponto, é possível analisar a lista de regras de restrição de IP definida para o aplicativo.

![lista de restrições de IP](media/app-service-ip-restrictions/browse-ip-restrictions.png)

Clique em **[+] Adicionar** para adicionar uma nova regra de restrição de IP.

![adicionar restrições de IP](media/app-service-ip-restrictions/add-ip-restrictions.png)
