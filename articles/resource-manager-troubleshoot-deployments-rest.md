<properties
   pageTitle="Exibir operações de implantação com a API REST | Microsoft Azure"
   description="Descreve como usar a API REST do Azure Resource Manager para detectar problemas de implantação do Resource Manager."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="05/19/2016"
   ms.author="tomfitz"/>

# Exibir operações de implantação com a API REST do Azure Resource Manager

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI do Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Se você recebeu um erro durante a implantação de recursos do Azure, você talvez queira ver mais detalhes sobre as operações de implantação que foram executadas. A API REST fornece operações que permitem encontrar os erros e determinar as possíveis correções.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## Solucionar problemas com a API REST

1. Implante seus recursos com a operação [Criar uma implantação de modelo](https://msdn.microsoft.com/library/azure/dn790564.aspx). Para reter informações que podem ser úteis para depuração, defina a propriedade **debugSetting** na solicitação JSON como **requestContent** e/ou **responseContent**. 

        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",      
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }

    Por padrão, o valor de **debugSetting** é definido como **none**. Ao especificar o valor de **debugSetting**, considere cuidadosamente o tipo de informação que está passando durante a implantação. Ao registrar em log as informações sobre a solicitação ou resposta, você pode potencialmente expor dados confidenciais que são recuperados por meio de operações de implantação.

2. Obtenha informações sobre uma implantação com a operação [Obter informações sobre uma implantação de modelo](https://msdn.microsoft.com/library/azure/dn790565.aspx).

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}

    Na resposta, observe, em particular, os elementos **provisioningState**, **correlationId** e **error**. **correlationId** é usado para acompanhar eventos relacionados e pode ser útil ao trabalhar com o suporte técnico na solução de um problema.
    
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  "error": {\r\n    "message": "Conflict",\r\n    "code": "Conflict"\r\n  }\r\n}"}]
            }  
          }
        }

3. Obtenha informações sobre operações de implantação com a operação [Listar todas as operações de implantação de modelo](https://msdn.microsoft.com/library/azure/dn790518.aspx).

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}

    A resposta incluirá informações de solicitação e/ou resposta com base no que foi especificado na propriedade **debugSetting** durante a implantação.
    
        {
          ...
          "properties": 
          {
            ...
            "request":{
              "content":{
                "location":"West US",
                "properties":{
                  "accountType": "Standard_LRS"
                }
              }
            },
            "response":{
              "content":{
                "error":{
                  "message":"Conflict","code":"Conflict"
                }
              }
            }
          }
        }

4. Obtenha eventos dos logs de auditoria para a implantação com a operação [Listar os eventos de gerenciamento em uma assinatura](https://msdn.microsoft.com/library/azure/dn931934.aspx).

        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}


## Próximas etapas

- Para obter ajuda com a resolução de erros de implantação específicos, veja [Resolver erros comuns ao implantar recursos no Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Para saber mais sobre como usar os logs de auditoria para monitorar outros tipos de ações, veja [Operações de auditoria com o Gerenciador de Recursos](resource-group-audit.md).
- Para validar sua implantação antes de executá-la, confira [Implantar um grupo de recursos com o modelo do Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0525_2016-->