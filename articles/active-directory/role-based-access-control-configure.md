<properties
	pageTitle="Controle de acesso baseado em função do AD do Azure | Microsoft Azure"
	description="Introdução ao gerenciamento de acesso com o controle de acesso baseado em função do Azure no Portal do Azure. Use as atribuições de função para atribuir permissões em seu diretório."
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
	ms.date="03/22/2016"
	ms.author="kgremban"/>

# Controle de Acesso Baseado em Função do Azure

## Controle de Acesso Baseado em Função
O RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso refinado para o Azure. Com o RBAC, você pode separar as tarefas dentro de sua equipe de operação de desenvolvimento e conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos. Este artigo apresenta as noções básicas sobre gerenciamento de acesso e o ajuda a colocar o RBAC em funcionamento no portal do Azure.

### Noções básicas de gerenciamento de acesso no Azure
Cada assinatura do Azure está associada com um Active Directory do Azure. Usuários, grupos e aplicativos do diretório podem gerenciar recursos na assinatura do Azure. Esses direitos de acesso são concedidos usando o portal do Azure, ferramentas de linha de comando do Azure e APIs de gerenciamento do Azure.

O acesso é concedido atribuindo-se a função RBAC apropriada a usuários, grupos e aplicativos em determinado escopo. O escopo de uma atribuição de função pode ser uma assinatura, um grupo de recursos ou um único recurso. Uma função atribuída a um escopo pai também concede acesso aos filhos contidos nele. Por exemplo, um usuário com acesso a um grupo de recursos pode gerenciar todos os recursos que ele contém, como sites, máquinas virtuais e sub-redes.

![Relação entre os elementos do Azure Active Directory - diagrama](./media/role-based-access-control-configure/rbac_aad.png)

A função RBAC que você atribui a usuários, grupos e aplicativos determina quais recursos podem ser gerenciados naquele escopo.

### Funções internas
O RBAC do Azure tem três funções básicas que se aplicam a todos os tipos de recurso:

- O proprietário tem acesso total a todos os recursos, inclusive o direito de delegar acesso a outros usuários.
- O colaborador pode criar e gerenciar todos os tipos de recursos do Azure, mas não pode conceder acesso a outras pessoas.
- O leitor pode exibir os recursos existentes do Azure.

As demais funções RBAC no Azure permitem o gerenciamento de recursos específicos do Azure. Por exemplo, a função Colaborador de Máquina Virtual permite a criação e o gerenciamento de máquinas virtuais, mas não dá acesso à rede virtual ou à sub-rede com a qual a máquina virtual está conectada.

[Funções internas RBAC](role-based-access-built-in-roles.md) lista as funções disponíveis no Azure. Ela especifica as operações e o escopo que cada função interna concede aos usuários. Se você pretende definir suas próprias funções para ter ainda mais controle, confira como criar [Funções personalizadas no RBAC do Azure](role-based-access-control-custom-roles.md).

### Hierarquia de recursos e herança de acesso
- Cada assinatura do Azure pertence somente a um diretório.
- Cada grupo de recursos pertence somente a uma assinatura.
- Cada recurso pertence somente a um grupo de recursos.

O acesso concedido em escopos pai é herdado em escopos filho. Se você atribuir a função Leitor a um grupo do AD do Azure no escopo de assinatura, os membros desse grupo poderão exibir todos os grupos de recursos e todos os recursos na assinatura. Se você atribuir a função Colaborador a um aplicativo no escopo do grupo de recursos, ele poderá gerenciar recursos de todos os tipos naquele grupo de recursos, mas não em outros grupos de recursos na assinatura.

### RBAC do Azure versus administrador de assinatura clássico
Os administradores de assinatura clássicos têm acesso completo à assinatura do Azure. Eles podem gerenciar recursos usando o [Portal do Azure](https://portal.azure.com) com APIs do Azure Resource Manager ou o [Portal Clássico do Azure](https://manage.windowsazure.com) com APIs de Gerenciamento de Serviços do Azure. No modelo RBAC, aos administradores clássicos é atribuída a função de proprietário no nível de assinatura.

O RBAC do Azure tem suporte somente no Portal do Azure e nas novas APIs do Azure Resource Manager. Os usuários e aplicativos aos quais são atribuídas funções RBAC não podem usar o portal de gerenciamento clássico ou as APIs de Gerenciamento de Serviços do Azure.

### Autorização para o gerenciamento versus operações de dados
O RBAC do Azure tem suporte somente para as operações de gerenciamento dos recursos do Azure no Portal do Azure e nas APIs do Azure Resource Manager. Nem todas as operações no nível de dados para os recursos do Azure podem ser autorizadas por meio do RBAC. Por exemplo, a criação/leitura/atualização/exclusão de contas de armazenamento pode ser controlada por meio do RBAC, mas a criação/leitura/atualização/exclusão de blobs ou tabelas dentro da conta de armazenamento não pode ser controlada por meio do RBAC. Semelhantemente, a criação/leitura/atualização/exclusão de um BD SQL pode ser controlada por meio do RBAC, mas a criação/leitura/atualização/exclusão de tabelas de SQL dentro do BD ainda não pode ser controlada por meio do RBAC.

## Gerenciar o acesso usando o portal do Azure
### Exibir o acesso
Você pode ver quem tem acesso a um recurso, grupo de recursos ou assinatura em sua folha principal no [portal do Azure](https://portal.azure.com). Por exemplo, queremos ver quem tem acesso a um dos nossos grupos de recursos:

1. Selecione o ícone **Grupo de recursos** na barra de navegação à esquerda.
2. Selecione o nome do grupo de recursos que você deseja examinar na folha **Grupos de recursos**.
3. Selecione o ícone **Usuários** no canto superior direito da folha do grupo de recursos.
4. A folha **Usuários** lista todos os usuários, grupos e aplicativos que receberam acesso ao grupo de recursos.

![Folha Usuários - acesso herdado versus atribuído - captura de tela](./media/role-based-access-control-configure/view-access.png)

Observe que alguns usuários tiveram o acesso **Atribuído**, ao passo que outros o **Herdaram**. O acesso é atribuído especificamente ao grupo de recursos ou herdado de uma atribuição à assinatura pai.

> [AZURE.NOTE] Os administradores e coadministradores de assinatura clássicos são, na realidade, os proprietários da assinatura no novo modelo de RBAC.


### Adicionar acesso
Conceda acesso de dentro do recurso, do grupo de recursos ou da assinatura. Esse será o escopo da atribuição de função.

1. Selecione o ícone **Adicionar** na folha **Usuários**. ![Folha Adicionar acesso - selecione uma função - captura de tela](./media/role-based-access-control-configure/grant-access1.png)
2. Selecione a função que você deseja atribuir.
3. Selecione o usuário, o grupo ou o aplicativo ao qual você deseja conceder acesso. Você pode pesquisar o diretório por nomes para exibição, endereços de email e identificadores de objeto. ![Folha Adicionar usuários - pesquisar - captura de tela](./media/role-based-access-control-configure/grant-access2.png)

### Remover acesso

1. Na folha **Usuários**, selecione a atribuição de função que você deseja remover.
2. Clique no ícone **Remover** na folha de detalhes de atribuição.
3. Clique em **sim** para confirmar a remoção.

![Folha Usuários - remover da função - captura de tela](./media/role-based-access-control-configure/remove-access1.png)

Atribuições herdadas não podem ser removidas de escopos filho. Você precisa remover a atribuição de função do escopo pai.

![Folha Usuários - botão remover desativa o acesso herdado - captura de tela](./media/role-based-access-control-configure/remove-access2.png)

## Outras ferramentas para gerenciar o acesso
Você pode atribuir funções e gerenciar o acesso com comandos do RBAC do Azure em ferramentas que não sejam o portal do Azure. Siga os links para saber mais sobre os pré-requisitos e começar a usar os comandos do RBAC do Azure.

- [PowerShell do Azure](role-based-access-control-manage-access-powershell.md)
- [Interface de linha de comando do Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)

## Próximas etapas
- [Criar relatório de histórico de alterações de acesso](role-based-access-control-access-change-history-report.md)
- Confira as [Funções internas do RBAC do Azure](role-based-access-built-in-roles.md)
- Defina suas próprias [Funções personalizadas no RBAC do Azure](role-based-access-control-custom-roles.md)

<!---HONumber=AcomDC_0330_2016-->