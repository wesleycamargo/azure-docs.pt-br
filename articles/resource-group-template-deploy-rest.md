<properties
   pageTitle="Implantar recursos com a API REST e o modelo | Microsoft Azure"
   description="Use o Azure Resource Manager e a API REST do Resource Manager para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="tomfitz"/>

# Implantar recursos com modelos do Resource Manager e a API REST do Resource Manager

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI do Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)
- [Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Nó](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)

Este artigo explica como usar a API REST do Resource Manager com modelos do Resource Manager para implantar seus recursos no Azure.

> [AZURE.TIP] Para obter ajuda com a depuração de erros durante a implantação, consulte:
>
> - [Exibir operações de implantação com a API REST](resource-manager-troubleshoot-deployments-rest.md) para saber mais sobre como obter informações que o ajudarão a solucionar o erro
> - [Solucionar erros comuns ao implantar recursos no Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md) para saber como resolver problemas comuns de implantação

Seu modelo pode ser um arquivo local ou um arquivo externo que está disponível por meio de um URI. Quando seu modelo reside em uma conta de armazenamento, você pode restringir o acesso a ele e fornecer um token de SAS (Assinatura de Acesso Compartilhado) durante a implantação.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## Implantar com a API REST
1. Definir [Parâmetros e cabeçalhos comuns](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), incluindo tokens de autenticação.
2. Se você não tiver um grupo de recursos existente, crie um novo grupo de recursos. Forneça sua id de assinatura, o nome do novo grupo de recursos e local que você precisa para sua solução. Para obter mais informações, consulte [Criar um grupo de recursos](https://msdn.microsoft.com/library/azure/dn790525.aspx).

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. Valide sua implantação antes de executá-la usando a operação [Validar uma implantação de modelo](https://msdn.microsoft.com/library/azure/dn790547.aspx). Ao testar a implantação, forneça parâmetros exatamente como faria ao executar a implantação (mostrado na próxima etapa).

3. Criar uma nova implantação. Forneça sua ID da assinatura, o nome do grupo de recursos a ser implantado, o nome da implantação e um link para seu modelo. Para obter informações sobre o arquivo de modelo, consulte [Arquivo de parâmetro](./#parameter-file). Para obter mais informações sobre a API REST para criar um grupo de recursos, consulte [Criar uma implantação de modelo](https://msdn.microsoft.com/library/azure/dn790564.aspx). Observe que **mode** está definido como **Incremental**. Para executar uma implantação completa, defina **mode** como **Complete**. Tenha cuidado ao usar o modo completo, pois você pode excluir acidentalmente recursos que não estão em seu modelo.
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
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
              }
            }
          }
   
      Se você desejar registrar o conteúdo da resposta, o conteúdo da solicitação ou ambos, inclua **debugSetting** na solicitação.

        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }

      Você pode configurar sua conta de armazenamento para usar um token de SAS (Assinatura de Acesso Compartilhado). Para obter mais informações, consulte [Delegando acesso com uma Assinatura de Acesso Compartilhado](https://msdn.microsoft.com/library/ee395415.aspx).

4. Obtém o status da implantação do modelo. Para obter mais informações, consulte [obter informações sobre uma implantação de modelo](https://msdn.microsoft.com/library/azure/dn790565.aspx).

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## Próximas etapas
- Para obter um exemplo de como implantar recursos por meio da biblioteca de cliente do .NET, veja [Implantar recursos usando bibliotecas do .NET e um modelo](virtual-machines/virtual-machines-windows-csharp-template.md).
- Para definir parâmetros no modelo, consulte [Criando modelos](resource-group-authoring-templates.md#parameters).
- Para obter orientação sobre como implantar a solução em ambientes diferentes, confira [Ambientes de desenvolvimento e de teste no Microsoft Azure](solution-dev-test-environments.md).
- Para obter detalhes sobre como usar uma referência do KeyVault para transmitir valores seguros, consulte [Transmitir valores seguros durante a implantação](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0713_2016-->