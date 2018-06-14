---
title: Exemplo de política de gerenciamento de API do Azure – Gerar Assinatura de Acesso Compartilhado | Microsoft Docs
description: Exemplo de política de gerenciamento de API do Azure – Demonstra como gerar uma Assinatura de Acesso Compartilhado usando expressões e encaminhar a solicitação para o armazenamento do Azure com a política rewrite-uri.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: fadad6f83f1a855f3ca1509a6a5e02c822db512d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33933950"
---
# <a name="generate-shared-access-signature"></a>Gerar a Assinatura de Acesso Compartilhado

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como gerar uma [Assinatura de Acesso Compartilhado](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) usando expressões e encaminhar a solicitação para o armazenamento do Azure com a política rewrite-uri. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

