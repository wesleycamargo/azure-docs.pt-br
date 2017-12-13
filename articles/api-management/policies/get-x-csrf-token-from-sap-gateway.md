---
title: "Exemplo de política de gerenciamento API do Azure – Implementar o padrão X-CSRF | Microsoft Docs"
description: "Exemplo de política de gerenciamento de API do Azure – Demonstra como implementar o padrão X-CSRF usado por muitas APIs. Este exemplo é específico do SAP Gateway."
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
ms.openlocfilehash: a99ff25f7f8a3851936ba8d28e76eff98b1b0458
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="implement-x-csrf-pattern"></a>Implementar padrão X-CSRF

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como implementar o padrão X-CSRF usado por muitas APIs. Este exemplo é específico do SAP Gateway. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get X-CSRF token from SAP gateway using send request policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

