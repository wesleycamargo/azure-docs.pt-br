<properties
	pageTitle="Funções personalizadas no RBAC do Azure | Microsoft Azure"
	description="Saiba como definir funções personalizadas com o Controle de Acesso Baseado em Função para um gerenciamento de identidade mais preciso na sua assinatura do Azure."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="04/18/2016"
	ms.author="kgremban"/>


# Funções personalizadas no RBAC do Azure


Crie uma função personalizada no RBAC (Controle de Acesso Baseado em Função) do Azure se nenhuma das funções internas atende às suas necessidades de acesso específicas. Funções personalizadas podem ser criadas usando o [Azure PowerShell](role-based-access-control-manage-access-powershell.md), a [CLI](role-based-access-control-manage-access-azure-cli.md) (Interface de linha de comando) do Azure e a [API REST](role-based-access-control-manage-access-rest.md). Assim como as funções internas, as funções personalizadas podem ser atribuídas a usuários, grupos e aplicativos na assinatura, no grupo de recursos e nos escopos de recurso.

Este é um exemplo de uma função personalizada para monitorar e reiniciar as máquinas virtuais:

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## Ações
A propriedade **Actions** de uma função personalizada especifica as operações do Azure às quais a função concede acesso. É uma coleção de cadeias de operação que identificam as operações protegíveis dos provedores de recursos do Azure. As cadeias de operação que contêm caracteres curinga (*) concedem acesso a todas as operações que correspondem à cadeia de operação. Por exemplo:

-	`*/read` concede acesso a operações de leitura a todos os tipos de recursos de todos os provedores de recursos do Azure.
-	`Microsoft.Network/*/read` concede acesso a operações de leitura a todos os tipos de recursos no provedor de recursos Microsoft.Network do Azure.
-	`Microsoft.Compute/virtualMachines/*` concede acesso a todas as operações de máquinas virtuais e seus tipos de recursos filhos.
-	`Microsoft.Web/sites/restart/Action` concede acesso para reinicialização de sites.

Use `Get-AzureRmProviderOperation` (no PowerShell) ou `azure provider operations show` (na CLI do Azure) para listar as operações dos provedores de recursos do Azure. Você também pode usar esses comandos para verificar se uma cadeia de operação é válida e para expandir as cadeias de operação curinga.

![Captura de tela PowerShell - Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

![Captura de tela CLI do Azure - azure provider operations show "Microsoft.Compute/virtualMachines/*/action"](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## NotActions
Use a propriedade **NotActions** se o conjunto de operações que você deseja permitir fica definido mais facilmente pela exclusão de operações restritas. O acesso concedido por uma função personalizada é computado por meio da subtração de operações **NotActions** das operações **Actions**.

> [AZURE.NOTE] Se um usuário recebe uma função que exclui uma operação em **NotActions** e recebe uma segunda função que concede acesso à mesma operação, ele tem permissão para executar essa operação. **NotActions** não é uma regra de negação, é simplesmente uma maneira conveniente de criar um conjunto de operações permitidas quando for necessário excluir operações específicas.

## AssignableScopes
A propriedade **AssignableScopes** da função personalizada especifica os escopos (assinaturas, grupos de recursos ou recursos) nos quais a função personalizada ficará disponível para atribuição. Você pode disponibilizar a função personalizada para atribuição apenas nas assinaturas ou grupos de recursos que a exijam e não sobrecarregar a experiência do usuário nas assinaturas ou grupos de recursos restantes.

Exemplos de escopos válidos que podem ser atribuídos incluem:

-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”, “/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624” – disponibiliza a função para atribuição em duas assinaturas.
-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e” – disponibiliza a função para atribuição em uma única assinatura.
-  “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network” – disponibiliza a função para atribuição apenas no Grupo de recursos de rede.

> [AZURE.NOTE] Você deve usar pelo menos uma assinatura, grupo de recursos ou ID de recurso.

## Controle de acesso de funções personalizadas
A propriedade **AssignableScopes** da função personalizada também controla quem pode exibir, modificar e excluir a função.

- Quem pode criar uma função personalizada? Os Proprietários (e os Administradores de acesso do usuário) de assinaturas, grupos de recursos e recursos podem criar funções personalizadas para uso nesses escopos. O usuário que está criando a função precisa ser capaz de executar a operação `Microsoft.Authorization/roleDefinition/write` em todos os **AssignableScopes** da função.

- Quem pode modificar uma função personalizada? Os Proprietários (e os Administradores de acesso do usuário) de assinaturas, grupos de recursos e recursos podem modificar funções personalizadas nesses escopos. Os usuários precisam poder executar a operação `Microsoft.Authorization/roleDefinition/write` em todos os **AssignableScopes** de uma função personalizada.

- Quem pode exibir funções personalizadas? Todas as funções internas no RBAC do Azure permitem a visualização de funções disponíveis para atribuição. Os usuários que podem executar a operação `Microsoft.Authorization/roleDefinition/read` em um escopo podem exibir as funções de RBAC disponíveis para atribuição nesse escopo.

## Consulte também
- [Controle de Acesso Baseado em Função](role-based-access-control-configure.md): introdução ao RBAC no portal do Azure.
- Saiba como gerenciar o acesso com:
	- [PowerShell](role-based-access-control-manage-access-powershell.md)
	- [CLI do Azure](role-based-access-control-manage-access-azure-cli.md)
	- [API REST](role-based-access-control-manage-access-rest.md)
- [Funções internas](role-based-access-built-in-roles.md): obter detalhes sobre as funções que estão incluídas por padrão no RBAC.

<!---HONumber=AcomDC_0420_2016-->