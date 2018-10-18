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
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 3df0087a21547585883f791d4d385025f92580c7
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395030"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Interpretar o esquema de logs de auditoria do Microsoft Azure Active Directory no Azure Monitor (visualização)

Este artigo descreve o esquema de log de logon do Microsoft Azure Active Directory no Azure Monitor. Cada entrada de log individual é armazenada como texto e formatada como um blob JSON, conforme mostrado nos dois exemplos abaixo: 

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

## <a name="field-and-property-descriptions"></a>Descrições de campo e propriedade

| Nome do campo | DESCRIÇÃO |
|------------|-------------|
| tempo real       | Data e hora (UTC). |
| operationName | O nome da operação. |
| operationVersion | Versão da API REST solicitada pelo cliente. |
| categoria | Atualmente, *Auditoria* é o único valor suportado. |
| tenantId | O GUID de locatário associado aos logs. |
| resultType | Resultado da operação. O resultado pode ser *Sucesso* ou *Falha*. |
| resultSignature |  Esse campo não é mapeado e você pode ignorar esse campo com segurança. | 
| resultDescription | Uma descrição adicional do resultado, quando disponível. | 
| durationMs |  Esse campo não é mapeado e você pode ignorar esse campo com segurança. |
| callerIpAddress | Endereço IP do cliente que fez a solicitação. | 
| correlationId | GUID opcional passado pelo cliente. Isso pode ajudar a correlacionar operações do lado do cliente com operações do lado do servidor e é útil ao rastrear logs que abrangem os serviços. |
| identidade | A identidade do token que foi apresentada ao fazer a solicitação. A identidade pode ser uma conta de usuário, conta do sistema ou principal de serviço. |
| level | O tipo de mensagem. Para logs de auditoria, o nível será sempre *informativo*. |
| location | Local do datacenter. |
| propriedades | Lista as propriedades suportadas relacionadas a um log de auditoria. Para obter mais informações, confira a próxima seção. | 

<br>

| Nome da propriedade | DESCRIÇÃO |
|---------------|-------------|
| AuditEventCategory | Tipo de evento de auditoria. Pode ser *Gerenciamento de Usuários*, *Gerenciamento de Aplicativos* ou outro tipo.|
| Tipo de identidade | O tipo pode ser *Aplicativo* ou *Usuário*. |
| Tipo de operação | O tipo pode ser *Adicionar*, *Atualizar*, *Excluir*. ou *Outros*. |
| Tipo de recurso de destino | Especifica o tipo de recurso de destino no qual a operação foi executada. O tipo pode ser *Aplicativo*, *Usuário*, *Função*, *Política* | 
| Nome do recurso de destino | Nome do recurso de destino. Isso pode ser um nome de aplicativo, um nome de função, um nome principal de usuário ou um nome principal de serviço. |
| additionalTargets | Lista quaisquer propriedades adicionais para operações específicas. Por exemplo, para uma operação de atualização, os valores antigos e os novos valores são listados em *targetUpdatedProperties*. | 

## <a name="next-steps"></a>Próximas etapas

* [Interpretar o esquema de logs de entrada no Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Saiba mais sobre os Logs de Diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Perguntas frequentes e problemas conhecidos](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
