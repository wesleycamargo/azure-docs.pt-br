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
ms.openlocfilehash: 71e76b234b614f5935775d5c387c9897e1dcb968
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="implement-x-csrf-pattern"></a>Implementar padrão X-CSRF

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como implementar o padrão X-CSRF usado por muitas APIs. Este exemplo é específico do SAP Gateway. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get X-CSRF token from SAP gateway using send request policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

