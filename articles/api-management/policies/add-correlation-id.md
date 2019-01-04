---
title: 'Exemplo de política de gerenciamento de API do Azure: adicionar um cabeçalho que contém uma ID de correlação | Microsoft Docs'
description: 'Exemplo de política de gerenciamento de API do Azure: demonstra como adicionar um cabeçalho que contém uma ID de correlação à solicitação de entrada.'
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
ms.openlocfilehash: 126701674b6dc529404f5a7854cda9b31c336170
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868422"
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

