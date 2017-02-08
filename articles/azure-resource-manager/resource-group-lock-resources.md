---
title: "Impedir alterações em recursos críticos do Azure | Microsoft Docs"
description: "Impeça que os usuários atualizem ou excluam determinados recursos ao aplicar uma restrição a todos os usuários e funções."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: de8137a69ccc2028a7dcbff491573f36640bdc50


---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Bloquear recursos para evitar alterações inesperadas 
Como administrador, talvez você precise bloquear uma assinatura, um recurso ou grupo de recursos para impedir que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos. É possível definir o nível de bloqueio como **CanNotDelete** ou **ReadOnly**. 

* **CanNotDelete** significa que os usuários autorizados ainda poderão ler e modificar um recurso, mas não poderão excluir o recurso. 
* **ReadOnly** significa que os usuários autorizados poderão ler um recurso, mas não poderão excluir ou atualizar o recurso. Aplicar esse bloqueio é semelhante ao restringir todos os usuários autorizados para as permissões concedidas pela função **Leitor**. 

Bloqueios do Resource Manager se aplicam apenas às operações que ocorrem no plano de gerenciamento, que consistem em operações enviadas para `https://management.azure.com`. Os bloqueios não restringem a maneira como os recursos executam suas próprias funções. Alterações de recursos são restritas, mas as operações de recursos não são restritas. Por exemplo, um bloqueio ReadOnly em um Banco de Dados SQL impede que você exclua ou modifique o banco de dados, mas ele não impede que você crie, atualize ou exclua dados no banco de dados. Transações de dados são permitidas porque essas operações não são enviadas para `https://management.azure.com`.

A aplicação de **ReadOnly** pode gerar resultados inesperados, pois algumas operações que parecem operações de leitura exigem ações adicionais. Por exemplo, aplicar um bloqueio **ReadOnly** em uma conta de armazenamento impedirá que todos os usuários listem as chaves. A operação de lista de chaves é tratada por meio de uma solicitação POST, pois as chaves retornadas estão disponíveis para operações de gravação. Em outro exemplo, a aplicação de um bloqueio **ReadOnly** em um recurso do Serviço de Aplicativo impedirá o Visual Studio Server Explorer de exibir os arquivos para o recurso, pois essa interação exige acesso de gravação.

Ao contrário do controle de acesso baseado em função, é possível usar bloqueios de gerenciamento para aplicar uma restrição a todos os usuários e a todas as funções. Para saber mais sobre como configurar permissões para usuários e funções, veja [Controle de Acesso Baseado em Função do Azure](../active-directory/role-based-access-control-configure.md).

Quando você aplica um bloqueio a um escopo pai, todos os recursos filho herdam o mesmo bloqueio. Até mesmo os recursos que você adiciona posteriormente herdam o bloqueio do pai. O bloqueio mais restritivo na herança terá precedência.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Quem pode criar ou excluir bloqueios na sua organização
Para criar ou excluir bloqueios de gerenciamento, você deve ter acesso às ações `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*`. Das funções internas, somente **Proprietário** e **Administrador do Acesso de Usuário** recebem essas ações.

## <a name="creating-a-lock-through-the-portal"></a>Criando um bloqueio por meio do portal
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="creating-a-lock-in-a-template"></a>Criando um bloqueio em um modelo
O exemplo a seguir mostra um modelo que cria um bloqueio em uma conta de armazenamento. A conta de armazenamento em que o bloqueio será aplicado é fornecida como um parâmetro. O nome do bloqueio é criado por meio da concatenação do nome do recurso com **/Microsoft.Authorization/** e do nome do bloqueio, que nesse caso é **myLock**.

O tipo fornecido é específico para o tipo de recurso. Para armazenamento, defina o tipo como "Microsoft.Storage/storageaccounts/providers/locks".

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="creating-a-lock-with-rest-api"></a>Criando um bloqueio com a API REST
Você pode bloquear os recursos implantados com a [API REST para bloqueios de gerenciamento](https://docs.microsoft.com/rest/api/resources/managementlocks). A API REST permite que você crie e exclua bloqueios e recupere informações sobre bloqueios existentes.

Para criar um bloqueio, execute:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

O escopo pode ser uma assinatura, grupo de recursos ou recurso. O nome do bloqueio é como você deseja chamar o bloqueio. Para a api-version, use **2015-01-01**.

Na solicitação, inclua um objeto JSON que especifica as propriedades do bloqueio.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 


## <a name="creating-a-lock-with-azure-powershell"></a>Criando um bloqueio com o Azure PowerShell
Você pode bloquear os recursos implantados com o Azure PowerShell usando o **New-AzureRmResourceLock** , conforme mostra o exemplo a seguir.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup

O Azure PowerShell fornece outros comandos para trabalhar com bloqueios, tais como **Set-AzureRmResourceLock** para atualizar um bloqueio e **Remove-AzureRmResourceLock** para excluir um bloqueio.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como trabalhar com bloqueios de recursos, confira [Bloquear os recursos do Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
* Para saber mais sobre a organização lógica de recursos, confira [Usando marcas para organizar os recursos](resource-group-using-tags.md)
* Para alterar o grupo de recursos em que um recurso reside, confira [Mover recursos para um novo grupo de recursos](resource-group-move-resources.md)
* É possível aplicar restrições e convenções em sua assinatura com políticas personalizadas. Para saber mais, confira [Usar a Política para gerenciar recursos e controlar o acesso](resource-manager-policy.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).




<!--HONumber=Jan17_HO4-->


