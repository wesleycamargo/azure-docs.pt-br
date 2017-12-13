---
title: "Exemplo de política de gerenciamento de API do Azure – Adicionar um cabeçalho Forwarded | Microsoft Docs"
description: "Exemplo de política de gerenciamento de API do Azure – Demonstra como adicionar um cabeçalho Forwarded na solicitação de entrada para permitir que a API de back-end crie URLs adequadas."
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
ms.openlocfilehash: cc2df914532b6cda37c951b65b243e90b63d57cb
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="add-a-forwarded-header"></a>Adicionar um cabeçalho Forwarded

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como adicionar um cabeçalho Forwarded na solicitação de entrada para permitir que a API de back-end crie URLs adequadas. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="code"></a>Código

Cole o código no bloco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)
