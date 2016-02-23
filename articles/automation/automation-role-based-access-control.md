<properties 
   pageTitle="Controle de acesso baseado em função na Automação do Azure | Microsoft Azure"
   description="O RBAC (controle de acesso baseado em função) permite o gerenciamento de acesso aos recursos do Azure. Esse artigo descreve como configurar o RBAC na Automação do Azure."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="sngun"/>

# Controle de acesso com base em função na Automação do Azure

## Controle de acesso baseado em função

O RBAC (controle de acesso baseado em função) permite o gerenciamento de acesso aos recursos do Azure. Com o [RBAC](../active-directory/role-based-access-control-configure.md), você pode separar as tarefas dentro de sua equipe e conceder somente a quantidade de acesso que os usuários, os grupos e os aplicativos precisam para realizar seus trabalhos. O acesso baseado em função pode ser concedido aos usuários que usam o portal do Azure, as ferramentas de Linha de Comando do Azure ou as APIs de Gerenciamento do Azure.

## RBAC em Contas de Automação

Na Automação do Azure, o acesso é concedido atribuindo a função apropriada de RBAC aos usuários, grupos e aplicativos no escopo da Conta de Automação. Veja a seguir as funções internas com suporte de uma Conta de Automação:

|**Função** | **Descrição** |
|:--- |:---|
| Proprietário | A função de proprietário permite acesso a todos os recursos e ações em uma Conta de Automação, incluindo o fornecimento de acesso a outros usuários, grupos e aplicativos para gerenciar a Conta de Automação. |
| Colaborador | A função de colaborador permite gerenciar tudo, exceto a modificação de permissões de acesso de outros usuários para uma Conta de Automação. |
| Leitor | A função de leitor permite que você veja todos os recursos em uma Conta de Automação, mas não permite realizar alterações. |
| Operador de Automação | A função de Operador de Automação permite realizar tarefas operacionais como iniciar, parar, suspender, continuar e agendar trabalhos. Essa função é útil se você deseja evitar que seus recursos da Conta de Automação, como runbooks e ativos de credenciais, sejam exibidos ou modificados, mas ainda permitir que os membros de sua organização executem esses runbooks. As [ações de um Operador de Automação](../active-directory/role-based-access-built-in-roles.md#automation-operator) listam as ações compatíveis com a função de Operador de Automação na Conta de Automação e seus recursos. |
| Administrador de acesso do usuário | A função de administrador de acesso de usuário permite que você gerencie o acesso dos usuários às Contas de Automação do Azure. |

Neste artigo, vamos orientar você pela configuração do RBAC na Automação do Azure.

## Configurar o RBAC para sua Conta de Automação usando o Portal do Azure

1.	Faça logon no [Portal do Azure](https://portal.azure.com/) e abra sua conta de Automação na folha Contas de Automação.  

2.	Clique no controle **Acesso** no canto superior direito. Isso abre a folha **Usuários** onde você pode adicionar novos usuários, grupos e aplicativos para gerenciar sua Conta de Automação e exibir as funções existentes que podem ser configuradas para ela.

    ![Botão de acesso](media/automation-role-based-access-control/automation-01-access-button.png)

>[AZURE.NOTE]  **Os administradores de assinatura** já existem como usuários padrão. O grupo de administradores de assinatura do Active Directory inclui os administradores de serviço e os coadministradores de sua assinatura do Azure. O administrador de serviços é o proprietário da assinatura do Azure e de seus recursos e também herdará a função de proprietário das Contas de Automação. Isso significa que o acesso é **Herdado** para **administradores e coadministradores de serviço** de uma assinatura e ele é **Atribuído** a todos os outros usuários. Clique em **Administradores de assinatura** para ver mais detalhes sobre suas permissões.

### Adicionar um novo usuário e atribuir uma função

1.	Na folha Usuários, clique em **Adicionar** para abrir a opção **Adicionar folha de acesso** onde você pode adicionar um usuário, grupo ou aplicativo e atribuir uma função a eles.  

    ![Adicionar usuário](media/automation-role-based-access-control/automation-02-add-user.png)

2.	Selecione uma função na lista de funções disponíveis. Escolheremos a função **Leitor**, mas você pode escolher qualquer uma das funções internas disponíveis compatíveis com a Conta de Automação ou função personalizada que você tenha definido.

    ![Escolher função](media/automation-role-based-access-control/automation-03-select-role.png)

3.	Clique em **Adicionar usuários** para abrir a folha **Adicionar usuários**. Se você estiver adicionado usuários, grupos ou aplicativos para gerenciar sua assinatura, eles estarão listados e você poderá selecioná-los para adicionar o acesso. Se não houver usuários listados, ou se o usuário no qual você estiver interessado em adicionar não estiver na lista, clique em **Convidar** para abrir a folha **Convidar uma pessoa** e convidar um usuário com um endereço de email válido da conta da Microsoft, como Outlook.com, OneDrive ou Xbox Live Ids. Após digitar o endereço de email do usuário, clique em **Selecionar** para adicionar o usuário e clique em **OK**.

    ![Adicionar usuários](media/automation-role-based-access-control/automation-04-add-users.png)
 
Agora você já deve ver o usuário adicionado à folha **Usuários** com a função **Leitor** atribuída.

![Listar usuários](media/automation-role-based-access-control/automation-05-list-users.png)

Você também pode atribuir uma função para o usuário na folha **Funções**. Clique em **Funções** na folha Usuários para abrir a **folha Funções**. Nessa folha, você pode exibir o nome da função além do número de usuários e grupos atribuídos a essa função.

![Atribuir função na folha de usuários](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)
   
>[AZURE.NOTE] O controle de acesso baseado em função só pode ser definido no nível da Conta de Automação e não em qualquer recurso abaixo dela.

Você pode atribuir mais de uma função a um usuário, grupo ou aplicativo. Por exemplo, se adicionarmos a função **Operador de Automação** com a **função Leitor** ao usuário, essas funções poderão exibir todos os recursos de automação e executar os trabalhos de runbook. Você pode expandir a lista suspensa para exibir uma lista de funções atribuídas ao usuário.

![Exibir várias funções](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)
 
### Remover um usuário

Você pode remover a permissão de acesso de um usuário que não está gerenciando a Conta de Automação ou que não trabalha mais para a organização. Veja abaixo as etapas para remover um usuário:

1.	Na folha **Usuários**, escolha a atribuição de função que você deseja remover.

2.	Clique no botão **Remover** na folha de detalhes de atribuição.

3.	Clique em **Sim** para confirmar a remoção.

    ![Remover usuários](media/automation-role-based-access-control/automation-08-remove-users.png)

## Usuário com função atribuída

Quando um usuário atribuído a uma função entra em sua Conta de Automação, ele pode ver a conta do proprietário exibida na lista de **Diretórios Padrão**. Para exibir a Conta de Automação à qual ele foi adicionado, é preciso alternar o diretório padrão para o diretório padrão do proprietário.

![Diretório padrão](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)

### Experiência do usuário para a função de Operador de Automação

Quando um usuário, atribuído à função de Operador de Automação, exibe a Conta de Automação atribuída, ele só consegue exibir a lista de runbooks, trabalhos de runbook e agendamentos criados na Conta de Automação, mas não exibe suas definições. Ele pode iniciar, parar, suspender, continuar ou agendar o trabalho de runbook. O usuário não terá acesso a outros recursos de automação, como configurações, grupos de trabalho híbridos ou nós DSC.

![Nenhum acesso aos recursos](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

Quando o usuário clica no runbook, os comandos para exibir a fonte ou editar o runbook não são fornecidos uma vez que a função de Operador de Automação não permite acesso a esses comandos.

![Sem acesso para editar o runbook](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)

O usuário terá acesso para exibir e criar agendamentos, mas não terá acesso a qualquer outro tipo de ativo.

![Nenhum acesso a ativos](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)

Esse usuário não tem acesso para exibir os Webhooks associados a um runbook

![Nenhum acesso ao Webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)

## Configurar o RBAC para sua Conta de Automação usando o Azure PowerShell

O acesso baseado em função também pode ser configurado para uma Conta de Automação usando os seguintes [cmdlets do Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) lista todas as funções que estão disponíveis no RBAC do Active Directory do Azure. Você pode usar esse comando juntamente com a propriedade **Nome** para listar todos os usuários com uma função específica. **Exemplo:** ![Obter a definição da função](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) lista as atribuições de função do RBAC do Azure no escopo especificado. Sem parâmetros, esse comando retorna todas as atribuições de função realizadas na assinatura. Use o parâmetro **ExpandPrincipalGroups** para listar as atribuições de acesso para o usuário especificado, bem como para os grupos dos quais o usuário é membro. **Exemplo:** use o comando a seguir para listar todos os usuários e suas funções em uma Conta de Automação.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Obter a atribuição da função](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) para conceder acesso a usuários, grupos e aplicativos em um determinado escopo. **Exemplo:** use o comando a seguir para criar uma nova função "Operador de Automação" para um usuário no escopo da Conta de Automação.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Nova atribuição de função](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• Use [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) para remover o acesso do usuário, grupo ou aplicativo especificado em um determinado escopo. **Exemplo:** use o comando a seguir para criar uma nova função "Operador de Automação" para um usuário no escopo da Conta de Automação.

    Remove-AzureRmRoleAssignment -SignInName "<sign-in Id of a user you wish to remove>" -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

Nos cmdlets acima, substitua o nome de login, ID de assinatura, nome do grupo de recursos e nome da Conta de Automação pelos detalhes de sua conta. Escolha **sim** quando for solicitado a continuar a excluir a atribuição de função.


## Próximas etapas
-  Para obter informações sobre as diversas maneiras de configurar o RBAC para a Automação do Azure, consulte [gerenciar o RBAC com o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
- Para obter detalhes sobre diferentes maneiras de iniciar um runbook, confira [Iniciando um runbook](automation-starting-a-runbook.md)
- Para obter informações sobre os diferentes tipos, consulte [Tipos de runbook da Automação do Azure](automation-runbook-types.md)

<!---HONumber=AcomDC_0218_2016-->