---
title: Exemplo de política de gerenciamento de API do Azure – Rotear a solicitação com base no tamanho do corpo | Microsoft Docs
description: Exemplo de política de gerenciamento de API do Azure – Demonstra como rotear solicitações com base no tamanho do corpo.
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
ms.openlocfilehash: a93e1d9fecea59ebb68c512b96c8381b5b1a9346
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284753"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Rotear a solicitação com base no tamanho do corpo

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como rotear solicitações com base no tamanho do corpo. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

