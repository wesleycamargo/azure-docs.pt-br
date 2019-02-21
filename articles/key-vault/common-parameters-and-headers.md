---
title: Parâmetros e cabeçalhos comuns
description: Os parâmetros e cabeçalhos comuns a todas as operações que você pode executar relacionadas aos recursos do Key Vault.
services: key-vault
documentationcenter: ''
author: bryanla
manager: barbkess
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: bryanla
ms.openlocfilehash: 1ac0f54aa4dfdc9db4724629c4dbfe7a4982838f
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301223"
---
# <a name="common-parameters-and-headers"></a>Parâmetros e cabeçalhos comuns

As informações a seguir são comuns a todas as operações que você pode executar relacionadas aos recursos do Key Vault:

- Substitua `{api-version}` pela versão da api no URI.
- Substitua `{subscription-id}` pelo identificador de assinatura no URI
- Substitua `{resource-group-name}` pelo grupo de recursos. Para saber mais, consulte Usar os grupos de recursos para gerenciar seus recursos do Azure.
- Substitua `{vault-name}` pelo nome de seu cofre de chaves no URI.
- Defina o cabeçalho Content-Type como application/json.
- Defina o cabeçalho de autorização para um token Web JSON obtido do Azure Active Directory (AAD). Para saber mais, confira [Autenticação de solicitações do Azure Resource Manager](authentication-requests-and-responses.md).

## <a name="common-error-response"></a>Resposta de erro comum
O serviço usará códigos de status HTTP para indicar êxito ou falha. Além disso, as falhas contêm uma resposta no seguinte formato:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Nome do elemento | Type | DESCRIÇÃO |
|---|---|---|
| código | string | O tipo de erro que ocorreu.|
| Message | string | Uma descrição do que causou o erro. |



## <a name="see-also"></a>Veja também
 [Referência da API REST do Azure Key Vault](/rest/api/keyvault/)
