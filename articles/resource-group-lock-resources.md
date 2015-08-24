<properties 
	pageTitle="Bloquear recursos com o Gerenciador de Recursos do Azure" 
	description="Bloqueie recursos para impedir que usuários atualizem ou excluam determinados recursos." 
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
	ms.date="08/10/2015" 
	ms.author="tomfitz"/>

# Bloquear recursos com o Gerenciador de Recursos do Azure

Como administrador, há situações em que você deseja colocar um bloqueio em um recurso ou grupo de recursos para impedir que outros usuários na sua organização confirmem ações de gravação ou acidentalmente excluam um recurso essencial.

O Gerenciador de Recursos do Azure permite restringir operações nos recursos por meio de bloqueios de gerenciamento de recursos. Bloqueios de recursos são políticas que impõem um nível de bloqueio em um escopo específico. O nível de bloqueio identifica o tipo de imposição da política, que atualmente tem dois valores –**CanNotDelete** e **ReadOnly**. O escopo é expresso como um URI e pode ser um recurso ou um grupo de recursos.

Usar bloqueios é diferente de atribuir permissões de usuário para executar determinadas ações. Para obter informações sobre como configurar permissões para usuários e funções, consulte [Controle de acesso baseado em função no portal de visualização](role-based-access-control-configure.md) e [Gerenciar e auditar o acesso a recursos](resource-group-rbac.md).

## Cenários comuns

Um cenário comum é quando você tem um grupo de recursos com alguns recursos que são usados esporadicamente. Os recursos de VM são ligados periodicamente para processar dados por um determinado intervalo de tempo e, em seguida, desligados. Nesse cenário, convém habilitar o desligamento das VMs, mas é fundamental que uma conta de armazenamento não seja excluída. Nesse cenário, você usaria um bloqueio de recurso com um nível de bloqueio de **CanNotDelete** na conta de armazenamento.

Em outro cenário, sua empresa pode ter períodos em que não deseja que as atualizações entrem em produção. O nível de bloqueio **ReadOnly** impede criação ou atualizações. Se você é uma empresa de varejo, talvez não queira permitir atualizações durante períodos de compras em feriados. Se você é uma empresa de serviços financeiros, você pode ter restrições relacionadas às implantações durante determinadas horas do mercado. Um bloqueio de recurso pode fornecer uma política para bloquear os recursos conforme apropriado. Isso pode ser aplicado a apenas determinados recursos ou a todo o grupo de recursos.

## Criando um bloqueio em um modelo

O exemplo a seguir mostra um modelo que cria um bloqueio em uma conta de armazenamento. A conta de armazenamento à qual o bloqueio será aplicado é fornecida como um parâmetro, que é usado em conjunto com a função concat(). O resultado é o nome do recurso anexado a 'Microsoft.Authorization' e, em seguida, um nome do bloqueio (neste caso, **myLock**).

O tipo fornecido é específico para o tipo de recurso. Para armazenamento, esse tipo é "Microsoft.Storage/storageaccounts/providers/locks".

O nível de escopo é definido usando a propriedade **level** do recurso. Como o exemplo se concentra em ajudar a evitar a exclusão acidental, o nível é definido como **CannotDelete**.

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

Você pode bloquear recursos implantados com a [API REST para bloqueios de gerenciamento](https://msdn.microsoft.com/library/azure/mt204563.aspx). A API REST permite que você crie e exclua bloqueios e recupere informações sobre bloqueios existentes.

Para criar um bloqueio, execute:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

O escopo pode ser uma assinatura, grupo de recursos ou recurso. O nome do bloqueio é como você deseja chamar o bloqueio. Para a versão de API, use **2015-01-01**.

Na solicitação, inclua um objeto JSON que especifica as propriedades do bloqueio.

    {
        "properties": {
            "level": {lock-level},
            "notes": "Optional text notes."
        }
    } 

Para o nível de bloqueio, especifique **CanNotDelete** ou **ReadOnly**.

Para obter exemplos, consulte [API REST para bloqueios de gerenciamento](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## Criando um bloqueio com o Azure PowerShell

Você pode bloquear recursos implantados com o Azure PowerShell usando o **New-AzureResourceLock** conforme mostrado abaixo. Por meio do PowerShell, você só pode definir o **LockLevel** como **CanNotDelete**.

    PS C:\> New-AzureResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName ExampleGroup

O PowerShell fornece outros comandos para trabalhar com bloqueios, tais como **Set-AzureResourceLock** para atualizar um bloqueio e **Remove-AzureResourceLock** para excluir um bloqueio.

## Próximas etapas

- Para obter mais informações sobre como trabalhar com bloqueios de recursos, consulte [Bloquear os recursos do Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- Para saber mais sobre a organização lógica de recursos, consulte [Usando marcas para organizar os recursos](resource-group-using-tags.md)
- Para alterar o grupo de recursos no qual um recurso está alocado, consulte [Mover recursos para um novo grupo de recursos](resource-group-move-resources.md)

<!---HONumber=August15_HO7-->