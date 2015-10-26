<properties
	pageTitle="Controle de acesso baseado em função do AD do Azure | Microsoft Azure"
	description="Este artigo descreve o controle de acesso baseado em função do Azure."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="10/12/2015"
	ms.author="inhenk"/>

# Controle de acesso baseado em função do Active Directory do Azure

## Controle de acesso baseado em função
O controle de acesso baseado em função do Azure (RBAC) permite o gerenciamento de acesso refinado para o Azure. Usando o RBAC, você pode separar as tarefas dentro de sua equipe de operação de desenvolvimento e conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos.

### Noções básicas de gerenciamento de acesso no Azure
Cada assinatura do Azure está hospedada em um Active Directory do Azure. Somente usuários, grupos e aplicativos desse diretório podem receber acesso para gerenciar recursos na assinatura do Azure, usando o Portal de Gerenciamento do Azure, as ferramentas de Linha de Comando do Azure e as APIs de Gerenciamento do Azure.

O acesso é concedido atribuindo-se a função RBAC apropriada a usuários, grupos e aplicativos no escopo correto. Para conceder acesso para toda a assinatura, atribua uma função no escopo de assinatura. Para conceder acesso a um grupo de recursos específico em uma assinatura, atribua uma função no escopo do grupo de recursos. Você também pode atribuir funções a recursos específicos, como sites, máquinas virtuais e sub-redes, para conceder acesso apenas a um recurso.

![](./media/role-based-access-control-configure/overview.png)

A função RBAC que você atribui a usuários, grupos e aplicativos determina quais recursos o usuário (ou aplicativo) pode gerenciar dentro do escopo.

### Funções internas do RBAC do Azure
O RBAC do Azure tem três funções básicas que se aplicam a todos os tipos de recurso: proprietário, colaborador e leitor. O proprietário tem acesso total a todos os recursos, inclusive o direito de delegar acesso a outros usuários. O colaborador pode criar e gerenciar todos os tipos de recursos do Azure, mas não pode conceder acesso a outras pessoas. O leitor pode apenas exibir os recursos existentes do Azure. As demais funções RBAC no Azure permitem o gerenciamento de recursos específicos do Azure. Por exemplo, a função Colaborador de Máquina Virtual permite a criação e o gerenciamento de máquinas virtuais, mas não permite o gerenciamento de rede virtual ou da sub-rede à qual a máquina virtual está conectada.

[Funções internas RBAC](role-based-access-built-in-roles.md) lista as funções internas RBAC disponíveis no Azure. Para cada função, especifica-se as operações para a qual uma função interna concede acesso.

### Hierarquia de recursos do Azure e herança de acesso
Cada assinatura do Azure pertence a somente um diretório, cada grupo de recursos pertence a somente uma assinatura e cada recurso pertence a somente um grupo de recursos. O acesso concedido em escopos pai é herdado em escopos filho. Se você conceder a função de leitor a um grupo do AD do Azure no escopo de assinatura, os membros desse grupo poderão exibir todos os grupos de recursos e todos os recursos na assinatura. Se você conceder a função de colaborador para um aplicativo no escopo do grupo de recursos, será possível gerenciar recursos de todos os tipos naquele grupo de recursos, mas não em outros grupos de recursos na assinatura.

### RBAC do Azure versus Administrador e coadministradores de assinatura clássicos
O administrador e os coadministradores de assinatura clássicos têm acesso completo à assinatura do Azure. Eles podem gerenciar recursos usando o portal clássico (https://manage.windowsazure.com)) e APIs do Gerenciador de Serviços do Azure, bem como o novo portal de gerenciamento (https://portal.azure.com)) e novas APIs do Gerenciador de Recursos do Azure. No modelo RBAC, aos administradores clássicos é atribuída a função de proprietário no nível de assinatura.

O modelo de autorização refinado (RBAC do Azure) tem suporte somente por meio do Portal de Gerenciamento (https://portal.azure.com) e das APIs do Gerenciador de Recursos do Azure. Os usuários e aplicativos aos quais são atribuídas funções RBAC (no escopo de recurso da assinatura/grupo de recursos/recurso) não podem usar o portal de gerenciamento clássico (http://manage.windowsazure.com) nem as APIs de Gerenciamento de Serviço do Azure.

### Autorização para o gerenciamento versus operações de dados
O modelo de autorização refinado (RBAC do Azure) tem suporte somente para as operações de gerenciamento dos recursos do Azure no Portal do Azure e nas APIs do Gerenciador de Recursos do Azure. Nem todas as operações no nível de dados para os recursos do Azure podem ser autorizadas por meio do RBAC. Por exemplo, a criação/leitura/atualização/exclusão de contas de armazenamento pode ser controlada por meio do RBAC, mas a criação/leitura/atualização/exclusão de blobs ou tabelas dentro da conta de armazenamento não pode ser controlada por meio do RBAC. Semelhantemente, a criação/leitura/atualização/exclusão de um BD SQL pode ser controlada por meio do RBAC, mas a criação/leitura/atualização/exclusão de tabelas de SQL dentro do BD ainda não pode ser controlada por meio do RBAC.

## Gerenciar o acesso usando o Portal de Gerenciamento do Azure
### Exibir o acesso
Selecione as configurações de acesso na seção de fundamentos da folha do grupo de recursos. A folha **Usuários** lista todos os usuários, grupos e aplicativos aos quais tenha sido concedido acesso ao grupo de recursos. O acesso é ainda atribuído no grupo de recursos ou herdado de uma atribuição na assinatura pai.

![](./media/role-based-access-control-configure/view-access.png)

> [AZURE.NOTE]Os administradores e coadministradores de assinatura clássicos são, na realidade, os proprietários da assinatura no novo modelo de RBAC.

### Adicionar acesso
1. Clique no ícone **Adicionar** na folha **Usuários**. ![](./media/role-based-access-control-configure/grant-access1.png)
2. Selecione a função que você deseja atribuir.
3. Pesquise e selecione o usuário, o grupo ou o aplicativo ao qual você deseja conceder acesso.
4. Pesquise no diretório usuários, grupos e aplicativos que usam nomes de exibição, endereços de email e identificadores de objeto.![](./media/role-based-access-control-configure/grant-access2.png)

### Remover acesso
1. Na folha **Usuários**, selecione a atribuição de função que você deseja remover.
2. Clique no ícone **Remover** na folha de detalhes de atribuição.
3. Clique em **sim** para confirmar a remoção.

![](./media/role-based-access-control-configure/remove-access1.png)


> [AZURE.NOTE]Atribuições herdadas não podem ser removidas de escopos filho. Navegue até o escopo pai e remova essas atribuições.


![](./media/role-based-access-control-configure/remove-access2.png)

## Gerenciar o acesso usando o Azure PowerShell
O acesso pode ser gerenciado usando comandos do RBAC nas ferramentas do Azure PowerShell.

-	Use `Get-AzureRoleDefinition` para listar as funções RBAC disponíveis para atribuição e para inspecionar as operações às quais elas concedem acesso:

-	Use `Get-AzureRoleAssignment` para listar as atribuições de acesso do RBAC como válidas na assinatura, no recurso ou no grupo de recursos especificados. Use o parâmetro `ExpandPrincipalGroups` para listar as atribuições de acesso para o usuário especificado, bem como para os grupos dos quais o usuário é membro. Use o parâmetro `IncludeClassicAdministrators` para listar também o Administrador e os Coadministradores de Assinatura clássicos.

-	Use `New-AzureRoleAssignment` para conceder acesso a usuários, grupos e aplicativos.

-	Use `Remove-AzureRoleAssignment` para remover acesso.

Consulte [Gerenciar o acesso usando o Azure PowerShell](role-based-access-control-manage-access-powershell.md) para obter exemplos mais detalhados de gerenciamento de acesso usando o Azure PowerShell.

## Gerenciar o acesso usando a interface de linha de comando do Azure
O acesso pode ser gerenciado usando comandos de RBAC na interface da linha de comando do Azure.

-	Use `azure role list` para listar as funções RBAC disponíveis para atribuição. Use a exibição de função do Azure para inspecionar as operações para as quais foi concedido acesso.

-	Use `azure role assignment list` para listar as atribuições de acesso do RBAC como válidas na assinatura, no recurso ou no grupo de recursos especificados. Use a opção `expandPrincipalGroups` para listar as atribuições de acesso para o usuário especificado, bem como para os grupos dos quais o usuário é membro. Use o parâmetro `includeClassicAdministrators` para listar também o Administrador e os Coadministradores de Assinatura clássicos.

-	Use `azure role assignment create` para conceder acesso a usuários, grupos e aplicativos.

-	Use `azure role assignment delete` para remover acesso.

Consulte [Gerenciar o acesso usando a CLI do Azure](role-based-access-control-manage-access-azure-cli.md) para obter exemplos mais detalhados de gerenciamento de acesso usando a CLI do Azure.

## Usando o relatório de histórico de alterações de acesso
Todas as alterações de acesso que ocorrem em suas assinaturas do Azure ficam registradas em eventos do Azure.

### Criar um relatório com o Azure PowerShell
Para criar um relatório de quem concedeu/revogou qual tipo de acesso para/de quem e em qual escopo de suas assinaturas do Azure, use o seguinte comando do PowerShell:

    Get-AzureAuthorizationChangeLog

### Criar um relatório com a CLI do Azure
Para criar um relatório de quem concedeu/revogou qual tipo de acesso para/de quem e em qual escopo de suas assinaturas do Azure, use o seguinte comando da interface de linha de comando (CLI) do Azure:

    azure authorization changelog

> [AZURE.NOTE]As alterações de acesso podem ser consultadas para os últimos 90 dias (em lotes de 15 dias).

O exemplo a seguir lista todas as alterações de acesso na assinatura para os últimos sete dias.

![](./media/role-based-access-control-configure/access-change-history.png)

### Exportar a alteração de acesso para uma planilha
É conveniente exportar as alterações de acesso para uma planilha para análise.

![](./media/role-based-access-control-configure/change-history-spreadsheet.png)

<!---HONumber=Oct15_HO3-->