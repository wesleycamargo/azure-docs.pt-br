---
title: Interpretar o esquema do log de auditoria do Azure Active Directory no Azure Monitor (visualização) | Microsoft Docs
description: Descrever o esquema de log de auditoria do Azure AD para uso no Azure Monitor (visualização)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e1ae8e2a4dc9ef9c21300ebfc4df8c0f1c5819f2
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240313"
---
# <a name="interpret-the-azure-active-directory-audit-logs-schema-in-azure-monitor-preview"></a>Interpretar o esquema de logs de auditoria do Azure Active Directory no Azure Monitor (visualização)

Este artigo descreve o esquema de log de auditoria do Azure AD no Monitor do Azure. Cada entrada de log individual é armazenada como texto, formatada como um blob JSON, conforme mostrado nos dois exemplos abaixo. 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

| Nome do campo | DESCRIÇÃO |
|------------|-------------|
| tempo real       | Data e hora (UTC) |
| operationName | Nome da operação |
| operationVersion | Versão da API REST solicitada pelo cliente |
| categoria | Atualmente, *Auditoria* é o único valor suportado |
| tenantId | Guia de locatário associado aos logs |
| resultType | O resultado da operação pode ser *Sucesso* ou *Falha* |
| resultSignature |  Isso não é mapeado e você pode ignorar esse campo com segurança. | 
| resultDescription | Descrição adicional do resultado, quando disponível | 
| durationMs |  Isso não é mapeado e você pode ignorar esse campo com segurança. |
| callerIpAddress | Endereço IP do cliente que fez o pedido | 
| correlationId | Guia opcional passado pelo cliente. Isso pode ajudar a correlacionar operações do lado do cliente com operações do lado do servidor e é útil ao rastrear logs que se estendem pelos serviços. |
| identidade | Identidade do token que foi apresentado ao fazer a solicitação. Identidade do token que pode ser uma conta de usuário, conta de sistema ou serviço principal.presentada ao fazer a solicitação. |
| level | Tipo de mensagem. Para logs de auditoria, ela será sempre *informativo* |
| location | Local do datacenter |
| propriedades | Lista as propriedades suportadas relacionadas a um log de auditoria. Para mais informações, veja a tabela abaixo. | 


| Nome da propriedade | DESCRIÇÃO |
|---------------|-------------|
| AuditEventCategory | Tipo de evento de auditoria. Pode ser *Gerenciamento de Usuários*, *Gerenciamento de Aplicativos* etc.|
| Tipo de identidade | *Aplicativo* ou *usuário* |
| Tipo de operação | Pode ser *Adicionar*, *Atualizar*, *Excluir* ou *Outro* |
| Tipo de recurso de destino | Especifica o tipo de recurso de destino no qual a operação foi executada. Pode ser *Aplicação*, *Usuário*, *Função*, *Política* | 
| Nome do recurso de destino | Nome do recurso de destino. Por exemplo, isso pode ser um nome de aplicativo, um nome de função, um nome principal de usuário ou um nome principal de serviço |
| additionalTargets | Lista quaisquer propriedades adicionais para operações específicas. Por exemplo, para uma operação de atualização, os valores antigos e os novos valores são listados em *targetUpdatedProperties* | 

## <a name="next-steps"></a>Próximas etapas

* [Interpretar o esquema de logs de entrada no monitor do Azure](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Saiba mais sobre os Logs de Diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Perguntas frequentes e problemas conhecidos](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)