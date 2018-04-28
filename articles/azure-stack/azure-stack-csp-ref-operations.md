---
title: Registrar locatários pelo uso de rastreamento de pilha do Azure | Microsoft Docs
description: Detalhes sobre operações usadas para gerenciar registros de Inquilino e como o uso de locatário é rastreado na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: fbdf4023bc70f1ad05dd52ac1eabe95b12be9be2
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Gerenciar o registro do inquilino na pilha do Azure

*Aplica-se a: sistemas integrados de pilha do Azure*

Este artigo contém detalhes sobre as operações que você pode usar para gerenciar seus registros de Inquilino e como o uso de locatário é controlado. Você pode encontrar detalhes sobre como adicionar, lista, ou remova os mapeamentos de locatário. Você pode usar o PowerShell ou os pontos de extremidade de API de cobrança para gerenciar o uso de controle.

## <a name="add-tenant-to-registration"></a>Adicionar locatário para registro

Use esta operação quando você deseja adicionar um novo locatário em seu registro, para que seu uso é relatado em uma assinatura do Azure conectada ao seu locatário do Azure Active Directory (AD do Azure).

Você também pode usar essa operação, se você quiser alterar a assinatura associada a um locatário, você pode chamar PUT/New-AzureRMResource novamente. O mapeamento antigo será substituído.

Observe que apenas uma assinatura do Azure pode ser associada um locatário. Se você tentar adicionar uma segunda assinatura para um locatário existente, a primeira assinatura é substituída. 


| Parâmetro                  | DESCRIÇÃO |
|---                         | --- |
| registrationSubscriptionID | A assinatura do Azure que foi usada para o registro inicial. |
| customerSubscriptionID     | A assinatura do Azure (não pilha do Azure) que pertencem ao cliente a ser registrado. Deve ser criado na oferta do provedor de serviços de nuvem (CSP). Na prática, isso significa, por meio do Centro de parceiros. Se um cliente tiver mais de um locatário, essa assinatura deve ser criada no locatário que será usado para fazer logon na pilha do Azure. |
| resourceGroup              | O grupo de recursos no Azure no qual o registro está armazenado. |
| registrationName           | O nome do registro de pilha do Azure. É um objeto armazenado no Azure. O nome geralmente está no CloudID-azurestack em formulário, onde CloudID é a ID de nuvem de sua implantação de pilha do Azure. |

> [!Note]  
> Os locatários precisam ser registrados com cada pilha do Azure que eles usam. Se um locatário usa mais de uma pilha do Azure, você precisa atualizar os registros inicias de cada implantação com a assinatura de locatário.

### <a name="powershell"></a>PowerShell

Use o cmdlet New-AzureRmResource para atualizar o recurso de registro. Faça logon no Azure (`Connect-AzureRmAccount`) usando a conta usada para o registro inicial. Aqui está um exemplo de como adicionar um locatário:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Chamada de API

**Operação**: colocar  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Resposta**: 201 criado  
**Corpo da resposta**: vazio  

## <a name="list-all-registered-tenants"></a>Lista todos os locatários

Obter uma lista de todos os locatários que foram adicionados a um registro.

 > [!Note]  
 > Se nenhum locatários tiveram sido registrados, você não receberá uma resposta.

### <a name="parameters"></a>parâmetros

| Parâmetro                  | DESCRIÇÃO          |
|---                         | ---                  |
| registrationSubscriptionId | A assinatura do Azure que foi usada para o registro inicial.   |
| resourceGroup              | O grupo de recursos no Azure no qual o registro está armazenado.    |
| registrationName           | O nome do registro de pilha do Azure. É um objeto armazenado no Azure. O nome é normalmente na forma de **azurestack**-***CloudID***, onde ***CloudID*** é a ID de nuvem de sua implantação de pilha do Azure.   |

### <a name="powershell"></a>PowerShell

Use o cmdlet Get-AzureRmResovurce para listar todos os locatários. Faça logon no Azure (`Connect-AzureRmAccount`) usando a conta usada para o registro inicial. Aqui está um exemplo de como adicionar um locatário:

```powershell
  Get-AzureRmResovurce -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Chamada de API

Você pode obter uma lista de todos os mapeamentos de locatário usando a operação GET

**Operação**: GET  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**Resposta**: 200  
**Corpo da resposta**: 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}”,
            "name": " cspSubscriptionId 1",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}”,
            "name": " cspSubscriptionId2 ",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Remover um mapeamento de locatário

Você pode remover um locatário que tenha sido adicionado a um registro. Se esse locatário ainda está usando recursos na pilha do Azure, seu uso é cobrado para a assinatura usada no registro do Azure pilha inicial.

### <a name="parameters"></a>parâmetros

| Parâmetro                  | DESCRIÇÃO          |
|---                         | ---                  |
| registrationSubscriptionId | ID de assinatura para o registro.   |
| resourceGroup              | O grupo de recursos para o registro.   |
| registrationName           | O nome do registro.  |
| customerSubscriptionId     | A ID de assinatura de cliente.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Chamada de API

Você pode remover mapeamentos de locatário usando a operação de exclusão.

**Operação**: excluir  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Resposta**: 204 sem conteúdo  
**Corpo da resposta**: vazio

## <a name="next-steps"></a>Próximas etapas

 - Para saber mais sobre como recuperar informações de uso do recurso da pilha do Azure, consulte [uso e cobrança no Azure pilha](/azure-stack-billing-and-chargeback.md).
