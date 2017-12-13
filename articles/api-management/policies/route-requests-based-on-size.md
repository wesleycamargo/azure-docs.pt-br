---
title: "Exemplo de política de gerenciamento de API do Azure – Rotear a solicitação com base no tamanho do corpo | Microsoft Docs"
description: "Exemplo de política de gerenciamento de API do Azure – Demonstra como rotear solicitações com base no tamanho do corpo."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 0558f82a31015af91df135b6a0d4c4050049d11d
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Rotear a solicitação com base no tamanho do corpo

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como rotear solicitações com base no tamanho do corpo. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

