<properties 
	pageTitle="Bloquear recursos com o Gerenciador de Recursos | Microsoft Azure" 
	description="Impeça que os usuários atualizem ou excluam determinados recursos ao aplicar uma restrição a todos os usuários e funções." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/01/2015" 
	ms.author="tomfitz"/>

# Bloquear recursos com o Gerenciador de Recursos do Azure

Como administrador, há situações em que você deseja colocar um bloqueio em uma assinatura, em um grupo de recursos ou em um recurso para impedir que outros usuários na sua organização confirmem ações de gravação ou acidentalmente excluam um recurso essencial.

O Gerenciador de Recursos do Azure permite restringir operações nos recursos por meio de bloqueios de gerenciamento de recursos. Os bloqueios de recursos são políticas que impõem um nível de bloqueio em um escopo específico. O escopo pode ser uma assinatura, um grupo de recursos ou um recurso. O nível de bloqueio identifica o tipo de imposição da política, que atualmente tem dois valores –**CanNotDelete** e **ReadOnly**. **CanNotDelete** significa que os usuários autorizados ainda poderão ler e modificar os recursos, mas não poderão excluir qualquer um dos recursos restritos. **ReadOnly** significa que os usuários autorizados ainda poderão ler os recursos, mas não poderão modificar ou excluir qualquer um dos recursos restritos.

Os bloqueios são diferentes do uso de controle de acesso baseado em função para atribuir permissões de usuário para executar determinadas ações. Para saber mais sobre como configurar permissões para usuários e funções, consulte [Controle de acesso baseado em função no portal de visualização](role-based-access-control-configure.md) e [Gerenciar e auditar o acesso a recursos](resource-group-rbac.md). Ao contrário do controle de acesso baseado em função, você utiliza os bloqueios de gerenciamento para aplicar uma restrição a todos os usuários e funções, e normalmente aplica os bloqueios por apenas uma duração limitada.

## Cenários comuns

Um cenário comum é quando você tem um grupo de recursos com alguns recursos que são usados esporadicamente. Os recursos de VM são ligados periodicamente para processar dados por um determinado intervalo de tempo e, em seguida, desligados. Nesse cenário, convém habilitar o desligamento das VMs, mas é fundamental que uma conta de armazenamento não seja excluída. Nesse cenário, você usaria um bloqueio de recurso com um nível de bloqueio **CanNotDelete** na conta de armazenamento.

Em outro cenário, sua empresa pode ter períodos em que não deseja que as atualizações entrem em produção. O nível de bloqueio **ReadOnly** para a criação ou as atualizações. Se você é uma empresa de varejo, talvez não queira permitir atualizações durante períodos de compras em feriados. Se você é uma empresa de serviços financeiros, você pode ter restrições relacionadas às implantações durante determinadas horas do mercado. Um bloqueio de recurso pode fornecer uma política para bloquear os recursos conforme apropriado. Isso pode ser aplicado a apenas determinados recursos ou a todo o grupo de recursos.

## Quem pode criar ou excluir bloqueios na sua organização

Para criar ou excluir os bloqueios de gerenciamento, você deverá ter acesso às ações **Microsoft.Authorization/*** ou **Microsoft.Authorization/locks/***. Das funções internas, somente **Proprietário** e **Administrador do Acesso de Usuário** obtêm essas ações. Para saber mais sobre a atribuição do controle de acesso, consulte [Gerenciando o acesso a recursos](resource-group-rbac.md).

## Bloquear herança

Quando você aplica um bloqueio a um escopo pai, todos os recursos filho herdam o mesmo bloqueio.

Se você aplicar mais de um bloqueio a um recurso, o bloqueio mais restritivo terá precedência. Por exemplo, se você aplicar **ReadOnly** no nível pai (como no grupo de recursos) e **CanNotDelete** em um recurso dentro desse grupo, o bloqueio mais restritivo (ReadOnly) do pai terá precedência.

## Criando um bloqueio em um modelo

O exemplo a seguir mostra um modelo que cria um bloqueio em uma conta de armazenamento. A conta de armazenamento à qual o bloqueio será aplicado é fornecida como um parâmetro, que é usado em conjunto com a função concat(). O resultado é o nome do recurso anexado a 'Microsoft.Authorization' e então um nome do bloqueio (neste caso, **myLock**).

O tipo fornecido é específico para o tipo de recurso. Para armazenamento, esse tipo é "Microsoft.Storage/storageaccounts/providers/locks".

O nível de escopo é definido usando a propriedade **level** do recurso. Como o exemplo se concentra em ajudar a evitar a exclusão acidental, o nível será definido como **CannotDelete**.

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
            "level": {lock-level},
            "notes": "Optional text notes."
        }
    } 

Para nível de bloqueio, especifique **CanNotDelete** ou **ReadOnly**.

Para obter exemplos, confira [API REST para bloqueios de gerenciamento](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## Criando um bloqueio com o Azure PowerShell

Você pode bloquear os recursos implantados com o Azure PowerShell usando o **New-AzureResourceLock** como mostrado abaixo. Por meio do PowerShell, você só poderá definir o **LockLevel** como **CanNotDelete**.

    PS C:\> New-AzureResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName ExampleGroup

O PowerShell fornece outros comandos para trabalhar com bloqueios, tais como **Set-AzureResourceLock** para atualizar um bloqueio e **Remove-AzureResourceLock** para excluir um bloqueio.

## Próximas etapas

- Para saber mais sobre como trabalhar com bloqueios de recursos, confira [Bloquear os recursos do Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- Para saber mais sobre a organização lógica de recursos, confira [Usando marcas para organizar os recursos](resource-group-using-tags.md)
- Para alterar o grupo de recursos em que um recurso reside, confira [Mover recursos para um novo grupo de recursos](resource-group-move-resources.md)

<!---HONumber=Oct15_HO2-->