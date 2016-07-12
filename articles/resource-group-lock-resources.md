<properties 
	pageTitle="Bloquear recursos com o Gerenciador de Recursos | Microsoft Azure" 
	description="Impeça que os usuários atualizem ou excluam determinados recursos ao aplicar uma restrição a todos os usuários e funções." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/26/2016" 
	ms.author="tomfitz"/>

# Bloquear recursos com o Gerenciador de Recursos do Azure

Como administrador, talvez você precise bloquear uma assinatura, um recurso ou grupo de recursos para impedir que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos. É possível definir o nível de bloqueio como **CanNotDelete** ou **ReadOnly**.

- **CanNotDelete** significa que os usuários autorizados ainda poderão ler e modificar um recurso, mas não poderão excluí-lo.
- **ReadOnly** significa que os usuários autorizados poderão ler um recurso, mas não poderão excluí-lo ou executar ações nele. A permissão no recurso é restrita à função **Leitor**. A aplicação de **ReadOnly** pode gerar resultados inesperados, pois algumas operações que parecem operações de leitura exigem ações adicionais. Por exemplo, aplicar um bloqueio **ReadOnly** em uma conta de armazenamento impedirá que todos os usuários listem as chaves. A operação de lista de chaves é tratada por meio de uma solicitação POST, pois as chaves retornadas estão disponíveis para operações de gravação. Em outro exemplo, a aplicação de um bloqueio **ReadOnly** em um recurso do Serviço de Aplicativo impedirá que Visual Studio Server Explorer seja capaz de exibir os arquivos para o recurso, pois essa interação exige acesso de gravação.

Ao contrário do controle de acesso baseado em função, é possível usar bloqueios de gerenciamento para aplicar uma restrição a todos os usuários e a todas as funções. Para saber mais sobre como configurar permissões para usuários e funções, confira [Controle de Acesso Baseado em Função do Azure](./active-directory/role-based-access-control-configure.md).

Quando você aplica um bloqueio a um escopo pai, todos os recursos filho herdam o mesmo bloqueio. O bloqueio mais restritivo na herança terá precedência.

## Quem pode criar ou excluir bloqueios na sua organização

Para criar ou excluir os bloqueios de gerenciamento, você deverá ter acesso às ações **Microsoft.Authorization/*** ou **Microsoft.Authorization/locks/***. Das funções internas, somente **Proprietário** e **Administrador do Acesso de Usuário** recebem permissão para realizar essas ações.

## Criando um bloqueio por meio do portal

1. Na folha Configurações do recurso, do grupo de recursos ou da assinatura que você deseja bloquear, selecione **Bloqueios**.

      ![selecionar bloqueio](./media/resource-group-lock-resources/select-lock.png)

2. Para adicionar um bloqueio, selecione **Adicionar**. Se, em vez disso, você desejar criar um bloqueio em um nível pai que será herdado pelo recurso atualmente selecionado, selecione o pai (como a assinatura mostrada abaixo).

      ![adicionar bloqueio](./media/resource-group-lock-resources/add-lock.png)

3. Forneça um nome e um nível para o bloqueio. Opcionalmente, é possível adicionar anotações que descrevem por que o bloqueio é necessário.

      ![definir bloqueio](./media/resource-group-lock-resources/set-lock.png)

4. Para excluir o bloqueio, selecione as reticências e **Excluir** nas opções disponíveis.

      ![excluir bloqueio](./media/resource-group-lock-resources/delete-lock.png)

## Criando um bloqueio em um modelo

O exemplo a seguir mostra um modelo que cria um bloqueio em uma conta de armazenamento. A conta de armazenamento em que o bloqueio será aplicado é fornecida como um parâmetro. O nome do bloqueio é criado por meio da concatenação do nome do recurso com **/Microsoft.Authorization/** e do nome do bloqueio, que nesse caso é **myLock**.

O tipo fornecido é específico para o tipo de recurso. Para armazenamento, esse tipo é "Microsoft.Storage/storageaccounts/providers/locks".

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

## Criando um bloqueio com a API REST

Você pode bloquear os recursos implantados com a [API REST para bloqueios de gerenciamento](https://msdn.microsoft.com/library/azure/mt204563.aspx). A API REST permite que você crie e exclua bloqueios e recupere informações sobre bloqueios existentes.

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

Para obter exemplos, confira [API REST para bloqueios de gerenciamento](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## Criando um bloqueio com o Azure PowerShell

Você pode bloquear os recursos implantados com o Azure PowerShell usando o **New-AzureRmResourceLock** como mostrado abaixo.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup

O Azure PowerShell fornece outros comandos para trabalhar com bloqueios, tais como **Set-AzureRmResourceLock** para atualizar um bloqueio e **Remove-AzureRmResourceLock** para excluir um bloqueio.

## Próximas etapas

- Para saber mais sobre como trabalhar com bloqueios de recursos, confira [Bloquear os recursos do Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- Para saber mais sobre a organização lógica de recursos, confira [Usando marcas para organizar os recursos](resource-group-using-tags.md)
- Para alterar o grupo de recursos em que um recurso reside, confira [Mover recursos para um novo grupo de recursos](resource-group-move-resources.md)
- É possível aplicar restrições e convenções em sua assinatura com políticas personalizadas. Para saber mais, confira [Usar a Política para gerenciar recursos e controlar o acesso](resource-manager-policy.md).

<!---HONumber=AcomDC_0629_2016-->