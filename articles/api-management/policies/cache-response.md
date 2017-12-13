---
title: "Política de gerenciamento de API do Azure de exemplo – adicionar recursos a um serviço de back-end | Microsoft Docs"
description: "Política de gerenciamento de API do Azure de exemplo – demonstra como adicionar recursos a um serviço de back-end. Por exemplo, aceitar um nome do local em vez de latitude e longitude em uma API de previsão do tempo."
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
ms.openlocfilehash: d500df0f0e48134ac9c1397795d65706d2e56ff9
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="add-capabilities-to-a-backend-service"></a>Adicionar recursos a um serviço de back-end

Este artigo mostra uma política de gerenciamento de API do Azure que demonstra como adicionar recursos a um serviço de back-end. Por exemplo, aceitar um nome do local em vez de latitude e longitude em uma API de previsão do tempo. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

