---
title: "Exemplo de política de gerenciamento de API do Azure – Gerar Assinatura de Acesso Compartilhado | Microsoft Docs"
description: "Exemplo de política de gerenciamento de API do Azure – Demonstra como gerar uma Assinatura de Acesso Compartilhado usando expressões e encaminhar a solicitação para o armazenamento do Azure com a política rewrite-uri."
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
ms.openlocfilehash: 9b0d37e4f7930389d3399e51de905db2b2ce8c27
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="generate-shared-access-signature"></a>Gerar a Assinatura de Acesso Compartilhado

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como gerar uma [Assinatura de Acesso Compartilhado](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) usando expressões e encaminhar a solicitação para o armazenamento do Azure com a política rewrite-uri. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

