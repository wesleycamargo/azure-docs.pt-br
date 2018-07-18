---
title: Exemplo de política de gerenciamento de API do Azure – Autorizar o acesso com base em declarações JWT | Microsoft Docs
description: Exemplo de política de gerenciamento de API do Azure – Demonstra como autorizar o acesso aos métodos HTTP específicos em uma API com base em declarações JWT.
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
ms.openlocfilehash: 78ea1b51dea0a65aede0e75e3a8c1d424689854e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285357"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autorizar o acesso com base em declarações JWT

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como autorizar o acesso aos métodos HTTP específicos em uma API com base em declarações JWT. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

