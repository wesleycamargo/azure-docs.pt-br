---
title: Exemplo de política de gerenciamento de API do Azure – Enviar erros para o Stackify para registro em log | Microsoft Docs
description: Exemplo de política de gerenciamento de API do Azure – Demonstra como adicionar uma política de registro em log de erros para enviar erros para o Stackify para registro em log.
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
ms.openlocfilehash: e47a3736e814229e881a314c7cb05054369db1f0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="send-errors-to-stackify-for-logging"></a>Enviar erros para o Stackify para registro em log

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como adicionar uma política de registro em log de erros para enviar erros para o Stackify para registro em log. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco **on-error**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Log errors to Stackify.policy.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

