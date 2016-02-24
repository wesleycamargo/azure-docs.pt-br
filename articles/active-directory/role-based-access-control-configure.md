<properties
	pageTitle="Controle de acesso baseado em função do AD do Azure | Microsoft Azure"
	description="Este artigo descreve o controle de acesso baseado em função do Azure."
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
	ms.date="02/10/2016"
	ms.author="kgremban"/>

# Controle de Acesso Baseado em Função do Azure

## Controle de Acesso Baseado em Função
O RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso refinado para o Azure. Com o RBAC, você pode separar as tarefas dentro de sua equipe de operação de desenvolvimento e conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos.

### Noções básicas de gerenciamento de acesso no Azure
Cada assinatura do Azure está associada com um Active Directory do Azure. Somente usuários, grupos e aplicativos desse diretório podem receber acesso para gerenciar recursos na assinatura do Azure, usando o portal do Azure, as ferramentas de Linha de Comando do Azure e as APIs de Gerenciamento do Azure.

O acesso é concedido atribuindo-se a função RBAC apropriada a usuários, grupos e aplicativos no escopo correto. Para conceder acesso para toda a assinatura, atribua uma função no escopo de assinatura. Para conceder acesso a um grupo de recursos específico em uma assinatura, atribua uma função no escopo do grupo de recursos. Você também pode atribuir funções a recursos específicos, como sites, máquinas virtuais e sub-redes, para conceder acesso apenas a um recurso.

![Recursos e ferramentas de gerenciamento de recursos do Active Directory do Azure - diagrama](./media/role-based-access-control-configure/overview.png)

A função RBAC que você atribui a usuários, grupos e aplicativos determina quais recursos podem ser gerenciados dentro do escopo.

### Funções internas do RBAC do Azure
O RBAC do Azure tem três funções básicas que se aplicam a todos os tipos de recurso: proprietário, colaborador e leitor. O proprietário tem acesso total a todos os recursos, inclusive o direito de delegar acesso a outros usuários. O colaborador pode criar e gerenciar todos os tipos de recursos do Azure, mas não pode conceder acesso a outras pessoas. O leitor pode apenas exibir os recursos existentes do Azure. As demais funções RBAC no Azure permitem o gerenciamento de recursos específicos do Azure. Por exemplo, a função Colaborador de Máquina Virtual permite a criação e o gerenciamento de máquinas virtuais, mas não permite o gerenciamento de rede virtual ou da sub-rede à qual a máquina virtual está conectada.

[Funções internas RBAC](role-based-access-built-in-roles.md) lista as funções internas RBAC disponíveis no Azure. Para cada função, especifica-se as operações para a qual uma função interna concede acesso.

### Hierarquia de recursos do Azure e herança de acesso
Cada assinatura do Azure pertence a somente um diretório, cada grupo de recursos pertence a somente uma assinatura e cada recurso pertence a somente um grupo de recursos. O acesso concedido em escopos pai é herdado em escopos filho. Se você conceder a função de leitor a um grupo do AD do Azure no escopo de assinatura, os membros desse grupo poderão exibir todos os grupos de recursos e todos os recursos na assinatura. Se você conceder a função de colaborador para um aplicativo no escopo do grupo de recursos, será possível gerenciar recursos de todos os tipos naquele grupo de recursos, mas não em outros grupos de recursos na assinatura.

### RBAC do Azure versus administrador e coadministradores de assinatura clássicos
O administrador e os coadministradores de assinatura clássicos têm acesso completo à assinatura do Azure. Eles podem gerenciar recursos usando o [Portal do Azure](https://portal.azure.com) com as APIs do Gerenciador de Serviços do Azure, bem como o [Portal Clássico do Azure](https://manage.windowsazure.com) e as APIs do Gerenciador de Recursos do Azure. No modelo RBAC, aos administradores clássicos é atribuída a função de proprietário no nível de assinatura.

O modelo de autorização refinado (RBAC do Azure) tem suporte somente por meio do Portal do Azure e das novas APIs do Gerenciador de Recursos do Azure. Os usuários e aplicativos aos quais são atribuídas funções RBAC (no escopo de recurso da assinatura/grupo de recursos/recurso) não podem usar o portal de gerenciamento clássico nem as APIs de Gerenciamento de Serviço do Azure.

### Autorização para o gerenciamento versus operações de dados
O modelo de autorização refinado (RBAC do Azure) tem suporte somente para as operações de gerenciamento dos recursos do Azure no portal do Azure e nas APIs do Gerenciador de Recursos do Azure. Nem todas as operações no nível de dados para os recursos do Azure podem ser autorizadas por meio do RBAC. Por exemplo, a criação/leitura/atualização/exclusão de contas de armazenamento pode ser controlada por meio do RBAC, mas a criação/leitura/atualização/exclusão de blobs ou tabelas dentro da conta de armazenamento não pode ser controlada por meio do RBAC. Semelhantemente, a criação/leitura/atualização/exclusão de um BD SQL pode ser controlada por meio do RBAC, mas a criação/leitura/atualização/exclusão de tabelas de SQL dentro do BD ainda não pode ser controlada por meio do RBAC.

## Gerenciar o acesso usando o portal do Azure
### Exibir o acesso
Selecione as configurações de acesso na seção de fundamentos da folha do **Grupo de recursos**. A folha **Usuários** lista todos os usuários, grupos e aplicativos aos quais tenha sido concedido acesso ao grupo de recursos. O acesso é ainda atribuído no grupo de recursos ou herdado de uma atribuição na assinatura pai.

![Folha Usuários - acesso herdado versus atribuído - captura de tela](./media/role-based-access-control-configure/view-access.png)

> [AZURE.NOTE] Os administradores e coadministradores de assinatura clássicos são, na realidade, os proprietários da assinatura no novo modelo de RBAC.


### Adicionar acesso
1. Clique no ícone **Adicionar** na folha **Usuários**. ![Folha Adicionar acesso - selecione uma função - captura de tela](./media/role-based-access-control-configure/grant-access1.png)
2. Selecione a função que você deseja atribuir.
3. Pesquise e selecione o usuário, o grupo ou o aplicativo ao qual você deseja conceder acesso.
4. Pesquise no diretório usuários, grupos e aplicativos que usam nomes de exibição, endereços de email e identificadores de objeto.![Folha Adicionar usuários - pesquisar - captura de tela](./media/role-based-access-control-configure/grant-access2.png)

### Remover acesso
1. Na folha **Usuários**, selecione a atribuição de função que você deseja remover.
2. Clique no ícone **Remover** na folha de detalhes de atribuição.
3. Clique em **sim** para confirmar a remoção.

![Folha Usuários - remover da função - captura de tela](./media/role-based-access-control-configure/remove-access1.png)


> [AZURE.NOTE] Atribuições herdadas não podem ser removidas de escopos filho. Navegue até o escopo pai e remova essas atribuições.

![Folha Usuários - botão remover desativa o acesso herdado - captura de tela](./media/role-based-access-control-configure/remove-access2.png)

## Gerenciar o acesso usando o Azure PowerShell
O acesso pode ser gerenciado usando comandos do RBAC nas ferramentas do Azure PowerShell.

-	Use `Get-AzureRmRoleDefinition` para listar as funções de RBAC disponíveis para atribuição e para inspecionar as operações às quais elas concedem acesso.

-	Use `Get-AzureRmRoleAssignment` para listar as atribuições de acesso do RBAC como válidas na assinatura, no recurso ou no grupo de recursos especificados. Use o parâmetro `ExpandPrincipalGroups` para listar as atribuições de acesso para o usuário especificado, bem como para os grupos dos quais o usuário é membro. Use o parâmetro `IncludeClassicAdministrators` para listar também o Administrador e os Coadministradores de Assinatura clássicos.

-	Use `New-AzureRmRoleAssignment` para conceder acesso a usuários, grupos e aplicativos.

-	Use `Remove-AzureRmRoleAssignment` para remover o acesso.

Consulte [Gerenciar o acesso usando o Azure PowerShell](role-based-access-control-manage-access-powershell.md) para obter exemplos mais detalhados de gerenciamento de acesso usando o Azure PowerShell.

## Gerenciar o acesso usando a interface de linha de comando do Azure
O acesso pode ser gerenciado usando comandos de RBAC na interface da linha de comando do Azure.

-	Use `azure role list` para listar as funções RBAC disponíveis para atribuição. Use `azure role show` para inspecionar as operações para as quais foi concedido acesso.

-	Use `azure role assignment list` para listar as atribuições de acesso do RBAC como válidas na assinatura, no recurso ou no grupo de recursos especificados. Use a opção `expandPrincipalGroups` para listar as atribuições de acesso para o usuário especificado, bem como para os grupos dos quais o usuário é membro. Use o parâmetro `includeClassicAdministrators` para listar também o Administrador e os Coadministradores de Assinatura clássicos.

-	Use `azure role assignment create` para conceder acesso a usuários, grupos e aplicativos.

-	Use `azure role assignment delete` para remover o acesso.

Consulte [Gerenciar o acesso usando a CLI do Azure](role-based-access-control-manage-access-azure-cli.md) para obter exemplos mais detalhados de gerenciamento de acesso usando a CLI do Azure.

## Gerenciar o acesso usando a API REST
Confira [Gerenciando o controle de acesso baseado em funções com a API REST](role-based-access-control-manage-access-rest.md) para obter mais exemplos detalhados de gerenciamento de acesso com a API REST.

## Usando o relatório de histórico de alterações de acesso
Todas as alterações de acesso que ocorrem em suas assinaturas do Azure ficam registradas em eventos do Azure.

### Criar um relatório com o Azure PowerShell
Para criar um relatório de quem concedeu/revogou qual tipo de acesso para/de quem e em qual escopo de suas assinaturas do Azure, use o seguinte comando do PowerShell:

    `Get-AzureRMAuthorizationChangeLog`

### Criar um relatório com a CLI do Azure
Para criar um relatório de quem concedeu/revogou qual tipo de acesso para/de quem e em qual escopo de suas assinaturas do Azure, use o seguinte comando da interface de linha de comando (CLI) do Azure:

    `azure authorization changelog`

> [AZURE.NOTE] As alterações de acesso podem ser consultadas para os últimos 90 dias (em lotes de 15 dias).

O exemplo a seguir lista todas as alterações de acesso na assinatura nos últimos sete dias.

![PowerShell Get-AzureRMAuthorizationChangeLog - captura de tela](./media/role-based-access-control-configure/access-change-history.png)

### Exportar a alteração de acesso para uma planilha
É conveniente exportar as alterações de acesso para uma planilha para análise.

![Log de alteração exibido como planilha - captura de tela](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## Funções personalizadas no RBAC do Azure
Crie uma função personalizada no RBAC do Azure se nenhuma das funções internas atender às suas necessidades de acesso específicas. As funções personalizadas podem ser criadas com as ferramentas de linha de comando de RBAC no Azure PowerShell e na Interface de Linha de Comando do Azure. Assim como as funções internas, as funções personalizadas podem ser atribuídas a usuários, grupos e aplicativos na assinatura, no grupo de recursos e no escopo do recurso.

Veja a seguir um exemplo de definição de função personalizada que permite o monitoramento e reinicialização de máquinas virtuais:

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
### Ações
A propriedade actions de uma função personalizada especifica as operações do Azure às quais a função concede acesso. É uma coleção de cadeias de operação que identificam as operações protegíveis dos provedores de recursos do Azure. As cadeias de operação que contêm caracteres curinga (*) concedem acesso a todas as operações que correspondem à cadeia de operação. Por exemplo:

-	`*/read` concede acesso a operações de leitura a todos os tipos de recursos de todos os provedores de recursos do Azure.
-	`Microsoft.Network/*/read` concede acesso a operações de leitura a todos os tipos de recursos no provedor de recursos Microsoft.Network do Azure.
-	`Microsoft.Compute/virtualMachines/*` concede acesso a todas as operações de máquinas virtuais e seus tipos de recursos filhos.
-	`Microsoft.Web/sites/restart/Action` concede acesso para reinicialização de sites.

Use os comandos `Get-AzureRmProviderOperation` ou `azure provider operations show` para listar operações de provedores de recursos do Azure. Você também pode usar esses comandos para verificar se uma cadeia de operação é válida e para expandir as cadeias de operação curinga.

![PowerShell Get-AzureRMProviderOperation - captura de tela](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

![Exibição de operações do provedor do Azure da CLI do Azure - captura de tela](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

### Não-ações
Se o conjunto de operações que você deseja permitir for expresso facilmente excluindo operações específicas, em vez de incluir todas as operações exceto aquelas que você deseja excluir, use a propriedade **NotActions** de uma função personalizada. O acesso efetivo concedido por uma função personalizada é computado por meio da exclusão de operações **NotActions** das operações **Actions**.

> [AZURE.NOTE] Se um usuário recebe uma função que exclui uma operação em **NotActions**, e recebe uma segunda função que concede acesso à mesma operação, o usuário terá permissão para executar essa operação. **NotActions** não é uma regra de negação, é simplesmente uma maneira conveniente de criar um conjunto de operações permitidas quando for necessário excluir operações específicas.

### AssignableScopes
A propriedade **AssignableScopes** da função personalizada especifica os escopos (assinaturas, grupos de recursos ou recursos) dentro dos quais a função personalizada ficará disponível para atribuição a usuários, grupos e aplicativos. Com **AssignableScopes** você pode disponibilizar a função personalizada para atribuição apenas nas assinaturas ou grupos de recursos que a exijam, e não sobrecarregar a experiência do usuário nas assinaturas ou grupos de recursos restantes.

> [AZURE.NOTE] Você deve usar pelo menos uma assinatura, grupo de recursos ou ID de recurso.

**AssignableScopes** de uma função personalizada também controla quem tem permissão para exibir, atualizar e excluir a função. Veja a seguir alguns escopos válidos que podem ser atribuídos:

-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”, “/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624”: disponibiliza a função para atribuição em duas assinaturas.
-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”: disponibiliza a função para atribuição em uma única assinatura.
-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network”: disponibiliza a função para atribuição apenas no grupo de recursos de Rede.

### Controle de acesso de funções personalizadas
A propriedade **AssignableScopes** da função personalizada determina quem pode exibir, modificar e excluir a função.

**Quem pode criar uma função personalizada?** - A criação da função personalizada é realizada somente se o usuário que está criando a função tiver permissão para criar uma função personalizada para todos os **AssignableScopes** especificados. A criação da função personalizada terá êxito somente se o usuário que está criando a função puder executar `Microsoft.Authorization/roleDefinition/write operation` em todos os **AssignableScopes** da função. Portanto, os Proprietários (e os Administradores de acesso do usuário) de assinaturas, grupos de recursos e recursos podem criar funções personalizadas para uso nesses escopos.

**Quem pode modificar uma função personalizada?** - Os usuários que têm permissão para atualizar funções personalizadas para todos os **AssignableScopes** de uma função podem modificar essa função personalizada. Os usuários que podem executar a operação `Microsoft.Authorization/roleDefinition/write` em todos os **AssignableScopes** de uma função personalizada podem modificar essa função personalizada. Por exemplo, se uma função personalizada puder ser atribuída em duas assinaturas do Azure (ou seja, se houver duas assinaturas na propriedade **AssignableScopes**), o usuário deverá ser o Proprietário (ou Administrador de acesso do usuário) das duas assinaturas para poder modificar a função personalizada.

**Quem pode ver as funções personalizadas disponíveis para atribuição em um escopo?** -Os usuários que podem executar a operação `Microsoft.Authorization/roleDefinition/read` em um escopo podem exibir as funções RBAC disponíveis para atribuição nesse escopo. Todas as funções internas no RBAC do Azure permitem a visualização de funções disponíveis para atribuição.

<!---HONumber=AcomDC_0211_2016-->