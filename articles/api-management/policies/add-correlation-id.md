---
title: Exemplo de política de gerenciamento de API do Azure – Adicionar um cabeçalho que contém uma ID de correlação | Microsoft Docs
description: Exemplo de política de gerenciamento de API do Azure – Demonstra como adicionar um cabeçalho que contém uma ID de correlação à solicitação de entrada.
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
ms.openlocfilehash: 68f42124369194124ae1f8ebb93834a5be4e0128
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287364"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Adicionar um cabeçalho que contém uma ID de correlação

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como adicionar um cabeçalho que contém uma ID de correlação à solicitação de entrada. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

