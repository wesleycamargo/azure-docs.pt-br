---
title: Exportar as informações de nível superior da assinatura do Azure | Microsoft Docs
description: Descreve como é possível exibir todas as IDs da assinatura do Azure associadas à conta.
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/9/2017
ms.author: adpick
ms.openlocfilehash: b995b0c7154faab66a44bef1a7d74eeb8404e5c1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658812"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exportar e exibir as informações da assinatura de nível superior
Caso seja necessário exibir o conjunto de IDs da assinatura associadas às credenciais do usuário, [baixe um arquivo .json com as informações da assinatura no Centro de Contas do Azure](http://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

O arquivo .json baixado fornece as informações a seguir:
- Email: o endereço de email associado à conta.
- Puid: o identificador exclusivo associado à conta de cobrança.
- SubscriptionIds: uma lista de assinaturas que pertencem à conta, enumeradas pela ID da assinatura.

### <a name="subscriptionsjson-sample"></a>exemplo de subscriptions.json
~~~~
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
~~~~
