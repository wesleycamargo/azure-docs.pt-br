---
title: Exemplo de política de gerenciamento API do Azure – Implementar o padrão X-CSRF | Microsoft Docs
description: Exemplo de política de gerenciamento de API do Azure – Demonstra como implementar o padrão X-CSRF usado por muitas APIs. Este exemplo é específico do SAP Gateway.
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
ms.openlocfilehash: 3a2067836a1488d117dced96f3935f2d1f8b1b48
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2018
ms.locfileid: "36946016"
---
# <a name="implement-x-csrf-pattern"></a>Implementar padrão X-CSRF

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como implementar o padrão X-CSRF usado por muitas APIs. Este exemplo é específico do SAP Gateway. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

