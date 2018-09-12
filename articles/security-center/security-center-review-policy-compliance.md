---
title: Revisando a conformidade com a política da Central de Segurança com a API REST do Azure | Microsoft Docs
description: Saiba como usar APIs REST do Azure para revisar a conformidade atual com as políticas da Central de Segurança.
services: security-center
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: alleonar
ms.openlocfilehash: 1443486590859aac5591aff2ab0551bed9228d7b
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301742"
---
# <a name="review-security-center-policy-compliance-using-rest-apis"></a>Revisão da conformidade com a política da Central de Segurança usando APIs REST

A Central de Segurança periodicamente valida os recursos do Azure em relação às suas políticas de segurança definidas. A Central de Segurança também fornece uma API REST que lhe permite revisar a conformidade de seus próprios aplicativos; você pode consultar o serviço diretamente ou importar resultados de JSON e outros aplicativos. 

Aqui, você aprenderá a recuperar o conjunto atual de recomendações de todos os recursos do Azure associados a uma assinatura.

Para recuperar o conjunto atual de recomendações:
``` http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/tasks?api-version={api-version}
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Criar a solicitação  

O parâmetro `{subscription-id}` é obrigatório e deve conter a ID de assinatura referente à assinatura do Azure que define as políticas. Se você tiver várias assinaturas, consulte [Trabalhando com várias assinaturas](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions).  

O `api-version` parâmetro é obrigatório. Neste momento, esses pontos de extremidade são suportados apenas para `api-version=2015-06-01-preview`. 

Os cabeçalhos a seguir são necessários: 

|Cabeçalho da solicitação|DESCRIÇÃO|  
|--------------------|-----------------|  
|*Tipo de Conteúdo:*|Obrigatório. Defina como `application/json`.|  
|*Autorização:*|Obrigatório. Defina como um `Bearer` [token de acesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) válido. |  

## <a name="response"></a>Response  

O código de status 200 (OK) é retornado para uma resposta bem-sucedida, que contém uma lista de tarefas recomendadas para proteger os recursos do Azure.

``` json
{  
  "value": [  
    {  
       "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
       "name": "{task_id}",
       "type": "Microsoft.Security/locations/{region}/tasks",
       "properties": {
       "state": "Active",
       "subState": "NA",
       "creationTimeUtc": "{create-time}",
       "lastStateChangeTimeUtc": "{last-state-change}",
       "securityTaskParameters": "{security-task-properties}"
    } 
  ]  
}  
```  

Cada item em **valor** representa uma recomendação:

|Propriedade de resposta|DESCRIÇÃO|
|----------------|----------|
|**state** | Indica se a recomendação é `active` ou `resolved`. |
|**creationTimeUtc** | Data e hora, em UTC, que mostram quando a recomendação foi criada. |
|**lastStateChangeUtc** | Data e hora, em UTV, da última alteração de estado, se houver. |
|**securityTaskParameters** | Fornece detalhes sobre a recomendação; as propriedades variam de acordo com a recomendação subjacente. |
||
  
Para as recomendações atualmente com suporte, consulte [Implementar as recomendações de segurança](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Outros códigos de status indicam condições de erro. Nesses casos, o objeto de resposta inclui uma descrição que explica por que a solicitação falhou.

``` json
{  
  "value": [  
    {  
      "description": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="example-response"></a>Exemplo de resposta  

``` json
{  
  "value": [  
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
            "name": "{task_id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "vmId": "/subscriptions/{subscription-id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "vmName": "{vm_name}",
                    "severity": "{severity}",
                    "isOsDiskEncrypted": {is_os_disk_encrypted},
                    "isDataDiskEncrypted": {is_data_disk_encrypted},
                    "name": "EncryptionOnVm",
                    "uniqueKey": "EncryptionOnVmTaskParameters_/subscriptions/{subscription-id}/resourceGroups/{resoource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "resourceId": "/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}"
                }
            }
        },
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{location}/tasks/{task-id}",
            "name": "{task-id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "serverName": "{sql-server-name}",
                    "serverId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}",
                    "name": "Enable auditing for the SQL server",
                    "uniqueKey": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}/auditingPolicies/Default",
                    "resourceId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}"
                }
            }
        }  ]  
}  
```  

Essa resposta mostra duas recomendações; cada item na lista corresponde a uma recomendação específica. O primeiro recomenda a criptografia de armazenamento em uma máquina virtual Linux e o segundo sugere que você habilite a auditoria para um SQL Server.

As recomendações variam de acordo com as políticas que estão ativadas. Para saber mais, incluindo as recomendações atualmente disponíveis, consulte [Gerenciando recomendações de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations).


## <a name="see-also"></a>Consulte também  
- [Definir políticas de segurança](https://docs.microsoft.com/azure/security-center/security-center-policies-overview)
- [API REST do Provedor de Recursos de Segurança do Azure](https://msdn.microsoft.com/library/azure/mt704034.aspx)   
- [Iniciar com a API REST do Azure](https://docs.microsoft.com/rest/api/azure/)   
- [Módulo do PowerShell da Central de Segurança do Azure](https://www.powershellgallery.com/packages/Azure-Security-Center/0.0.22)
