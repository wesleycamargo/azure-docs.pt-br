<properties title="Role Based Access Control in Azure Preview Portal" pageTitle="Role Based Access Control in Azure Preview Portal" description="Describes how role based access control works and how to set it up" metaKeywords="" services="multiple" solutions="" documentationCenter="" authors="justinha" videoId="" scriptId="" />

<tags ms.service="multiple" ms.devlang="dotnet" ms.topic="article" ms.tgt_pltfrm="Ibiza" ms.workload="infrastructure-services" ms.date="09/12/2014" ms.author="justinha;Justinha@microsoft.com"></tags>


# Controle de acesso com base em função no portal de visualização do Azure

Adicionamos suporte para o controle de acesso com base em função (RBAC) no portal de visualização do Azure para ajudar as organizações a atender seus requisitos de gerenciamento de acesso de forma simples e precisa. A [postagem no blog][postagem no blog] fornecerá a você uma introdução rápida do recurso e ajudará você a começar. Este tópico descreve os conceitos em detalhes e abrange casos de uso adicionais.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

## Sumário

-   [RBAC no Azure][RBAC no Azure]
-   [Coexistência do RBAC com os coadministradores de assinatura][Coexistência do RBAC com os coadministradores de assinatura]
-   [Autorização para o gerenciamento versus operações de dados][Autorização para o gerenciamento versus operações de dados]
-   [Como adicionar e remover acesso][Como adicionar e remover acesso]
-   [Problemas conhecidos ao usar o controle de acesso com base em função][Problemas conhecidos ao usar o controle de acesso com base em função]
-   [Como fornecer comentários][Como fornecer comentários]
-   [Próximas etapas][Próximas etapas]

## <span id="whatisrbac"></span></a>RBAC no Azure

Cada assinatura do Azure está associada com um Azure Active Directory. Os usuários e serviços que acessam os recursos da assinatura usando o portal de Gerenciamento do Azure ou a API do Gerenciador de Recursos do Azure primeiro precisam se autenticar com aquele Azure Active Directory.

![][]

O controle de acesso com base em função permite que você conceda acesso apropriado aos usuários, grupos e serviços do Azure AD, atribuindo funções para eles em uma assinatura ou grupo de recursos ou nível de recurso individual. A função atribuída define o nível de acesso que os usuários, grupos ou serviços tem no recurso do Azure.

### Função

Uma função é uma coleção de ações que podem ser executadas nos recursos do Azure. Um usuário ou um serviço tem a permissão de executar uma ação em um recurso do Azure, se eles forem atribuídos uma função que contém aquela ação.

#### Funções internas

Na primeira visualização, o controle de acesso com base em função vem com três funções internas que podem ser atribuídas aos usuários, grupos e serviços.

-   **Proprietário**: tem controle total sobre os recursos do Azure. O proprietário pode realizar todas as operações de gerenciamento em um recurso incluindo o gerenciamento de acesso.
-   **Colaborador**: pode realizar todas as operações de gerenciamento exceto o gerenciamento de acesso. Portanto, um colaborador não pode conceder acesso aos outros.
-   **Leitor**: pode somente exibir os recursos. O leitor pode exibir os segredos associados a um recurso.

Você não pode modificar a definição das funções internas. Em uma versão futura do Azure RBAC, você poderá definir funções personalizadas pela composição de um conjunto de ações de uma lista de ações disponíveis que podem ser executadas nos recursos do Azure.

#### Ações e não-ações

A propriedade **ações** da definição de função especifica as ações permitidas nos recursos do Azure. As cadeias de caracteres da ação podem usar caracteres curingas. A propriedade **não-ações** da definição de função especifica as ações que devem ser excluídas das ações permitidas.

<table>
<thead>
<tr class="header">
<th align="left">
<strong>Função interna<strong />

</th>
<th align="left">
Ações

</th>
<th align="left">
Não-ações

</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">
Proprietário (permite todas as ações)

</td>
<td align="left">
*
<td>
</td>
</td>
</tr>
<tr class="even">
<td align="left">
Colaborador (permite todas as ações exceto a gravação ou a exclusão de atribuições de função)

</td>
<td align="left">
*

</td>
<td align="left">
Microsoft.Authorization/ * /Write, Microsoft.Authorization/ * /Delete

</td>
</tr>
<tr class="odd">
<td align="left">
Leitor (permite todas as ações de leitura)

</td>
<td align="left">
*/Read 
</td>
<td>
</td>
</tr>
</tbody>
</table>
### Atribuição de função

O acesso é concedido aos usuários e serviços do Azure AD atribuindo a função apropriada para eles em um recurso do Azure.

#### Entidades de segurança do Azure AD

As funções podem ser atribuídas para os seguintes tipos de entidades de segurança do Azure AD:

-   **Usuários**: as funções podem ser atribuídas aos usuários organizacionais que estão no Azure AD com os quais a assinatura do Azure está associada. As funções também podem ser atribuídas aos usuários da conta da Microsoft externa (como <joe@outlook.com>) usando a ação de Convite para atribuir ao usuário um função no portal de visualização do Azure. Ao atribuir uma função a um usuário de conta da Microsoft externa faz com que uma conta de convidado seja criada no Azure AD para ele. Se essa conta de convidado está desabilitada no diretório, o usuário externo não poderá acessar nenhum recurso do Azure para o qual o usuário tem concedido acesso.
-   **Grupos**: as funções podem ser atribuídas para os grupos de segurança do Azure AD. Um usuário é concedido acesso automaticamente a um recurso se ele se tornar um membro de um grupo que tem acesso. O usuário também perde o acesso automaticamente ao recurso após ser removido do grupo. Gerenciar o acesso através de grupos ao atribuir funções aos grupos e adicionar usuários a esses grupos é a prática recomendada, em vez de atribuir funções diretamente aos usuários. O Azure RBAC não permite a atribuição de funções às listas de distribuição.
    A capacidade de atribuir funções aos grupos permite que uma organização estenda seu modelo de controle de acesso existente de seu diretório local para a nuvem, para que os grupos de segurança que já estão estabelecidos para controlarem o acesso local possam ser reutilizados no portal de visualização do Azure. Para obter mais informações sobre as diferentes opções para sincronizar usuários e grupos de um diretório local, consulte [Integração de diretórios][Integração de diretórios]. O Azure AD Premium também oferece um [recurso de gerenciamento de grupos delegados][recurso de gerenciamento de grupos delegados] com o qual a capacidade de criar e gerenciar grupos pode ser delegada aos usuários não administradores do Azure AD.
-   **Entidades de serviço**: as identidades de serviço são representadas como entidades de serviço no diretório. Elas se autenticam com o Azure AD e se comunicam com segurança umas com as outras. Os serviços podem ser concedidos acesso aos recursos do Azure ao atribuir funções através do módulo do Azure para o Windows PowerShell à entidade de serviço do Azure AD representando aquele serviço.

#### Escopo do recurso

O acesso não precisa ser concedido para toda a assinatura. As funções podem ser atribuídas para grupos de recursos bem como para recursos individuais. No Azure RBAC, um recurso herda atribuições de função de seus recursos pai. Então se um usuário, grupo ou serviço é concedido acesso somente a um grupo de recursos dentro da assinatura, eles poderão somente acessar aquele grupo de recursos e os recursos dentro dele, e não outros grupos de recursos dentro da assinatura. Como outro exemplo, um grupo de segurança pode ser adicionado à função de Leitor para um grupo de recursos, mas ser adicionado a uma função de Colaborador para um banco de dados dentro daquele grupo de recursos.

![][1]

## <span id="coexist"></span></a>Coexistência do RBAC com os coadministradores de assinatura

Os administradores e coadministradores de assinatura continuarão a ter acesso completo aos portais do Azure e as APIs de gerenciamento. 

No modelo RBAC, eles são atribuídos a função de Proprietário em nível de assinatura. No entanto, o novo modelo RBAC tem suporte apenas no portal de visualização do Azure e nas APIs do Gerenciador de Recursos do Azure. Os usuários e serviços que são atribuídos às funções RBAC não podem acessar o portal de Gerenciamento do Azure e as APIs de Gerenciamento de serviços. Ao adicionar um usuário a função de Proprietário de uma assinatura no portal de visualização do Azure não torna aquele usuário um coadministrador da assinatura no portal completo do Azure.

Se você deseja conceder acesso a um usuário para um recurso do Azure que ainda não está disponível para ser gerenciado por meio do portal de visualização do Azure, você deve adicioná-los aos coadministradores de assinatura usando o portal completo de Gerenciamento do Azure. O Barramento de Serviço e os Serviços de Nuvem são exemplo de recursos que hoje não podem ser gerenciados usando o RBAC.

## <span id="authmgmt"></span></a>Autorização para o gerenciamento versus operações de dados

O controle de acesso com base em função tem suporte somente para as operações de gerenciamento dos recursos do Azure no portal de visualização do Azure e nas APIs do Gerenciador de Recursos do Azure. Nem todas as operações no nível de dados para os recursos do Azure podem ser autorizadas por meio do RBAC. Por exemplo, a criação/leitura/atualização/exclusão de contas de armazenamento pode ser controlada por meio do RBAC, mas a criação/leitura/atualização/exclusão de blobs ou tabelas dentro da conta de armazenamento não pode ser controlada por meio do RBAC. Semelhantemente, a criação/leitura/atualização/exclusão de um BD SQL pode ser controlada por meio do RBAC, mas a criação/leitura/atualização/exclusão de tabelas de SQL dentro do BD ainda não pode ser controlada por meio do RBAC.

## <span id="addremoveaccess"></span></a>Como adicionar e remover acesso

Vamos olhar um exemplo de como o proprietário do recurso em uma organização pode gerenciar o acesso. Nesse cenário, você tem várias pessoas trabalhando em uma variedade de testes e projetos de produção que são desenvolvidos usando os recursos do Azure. Você deseja seguir as práticas recomendadas para conceder acesso. Os usuários devem ter acesso a todos os recursos que eles precisam, porém não acesso adicional. Você deseja reutilizar todos os investimentos que você fez nos processos e ferramentas para usar grupos de segurança que são controlados em um Active Directory local. Estas seções cobrem como configurar o acesso para estes recursos:

-   [Adicionar acesso][Adicionar acesso]
-   [Remover acesso][Remover acesso]
-   [Adicionar ou remover o acesso para o usuário externo][Adicionar ou remover o acesso para o usuário externo]

### 

<p>
<h3><a id="add"></a>AAdicionar acesso</h2>
</h2>
</p>
Aqui está um resumo dos requisitos de acesso e como eles são configurados no Azure.

| Usuário/Grupo                   | Requisitos de acesso                                        | função e escopo para o acesso                                                                                         |
|---------------------------------|-------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| Todas as equipes de Jill Santos | Leia todos os recursos do Azure                             | Adicione o grupo AD que representa a equipe de Jill Santos para a função de Leitor para a assinatura do Azure.        |
| Todas as equipes de Jill Santos | Crie e gerencie todos os recursos do grupo de recursos Test | Adicione o grupo AD que representa a equipe de Jill Santos para a função de Colaborador para o grupo de recursos Test |
| Brock                           | Crie e gerencie todos os recursos do grupo de recursos Prod | Adicione Brock à função de Colaborador para o grupo de recursos Prod                                                  |

Primeiro, vamos adicionar o acesso à Leitura para todos os recursos da assinatura. Clique em **Procurar \> Todos \> Assinaturas**.

![][2]

Clique no *nome de sua assinatura* \*\* \> Leitor \> Adicionar\*\*. Na lista de usuários e grupos, selecione ou digite o nome do grupo do Active Directory.

![][3]

Depois adicione a mesma equipe à função de colaborador do grupo de recursos Test. Clique no grupo de recursos para abrir sua lâmina de propriedade. Em **Funções**, clique em **Colaborador \> Adicionar** e digite o nome da equipe.

![][4]

Para adicionar Brock à função de Colaborador do grupo de recursos Prod, clique no grupo de recursos, clique em **Colaborador \> Adicionar** e digite o nome do Brock.

![][5]

As atribuições de função podem também ser gerenciadas usando o módulo Microsoft Azure para o Windows PowerShell. Aqui está um exemplo de como adicionar a conta de Brock usando o cmdlet New-AzureRoleAssignment em vez do portal:

    PS C:PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDBgt; New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Para obter mais informações sobre como usar o Windows PowerShell para adicionar e remover o acesso, consulte [Gerenciar o controle de acesso com base em função com o Windows PowerShell][Gerenciar o controle de acesso com base em função com o Windows PowerShell].

### 

<p>
<h3><a id="remove"></a>Remover acesso</h2>
</h2>
</p>
Você também pode remover atribuições facilmente. Digamos que você deseja remover um usuário chamado de Brad Adams da função de Leitor para um grupo de recursos chamado TestDB. Abra a lâmina do grupo de recursos, clique em **Leitor \> Brad Adams \> Remover**.

![][6]

Aqui está um exemplo de como remover Brad Adams usando o cmdlet New-AzureRoleAssignment:

    PS C:PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDBgt; Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

### 

<p>
<h3><a id="addremoveext"></a>AAdicionar ou remover o acesso para o usuário externo</h2>
</h2>
</p>
A guia **Configurar** do diretório inclui opções para controlar o acesso para usuários externos. Essas opções podem ser alteradas somente na interface do usuário (não há método de API ou Windows PowerShell) no portal completo do Azure por um administrador global do diretório.
Para abrir a guia **Configurar** no portal completo do Azure, clique em **Active Directory** e, em seguida, clique no nome do diretório.

![][7]

Então você pode editar as opções para controlar o acesso para os usuários externos.

![][8]

Por padrão, os convidados não podem enumerar o conteúdo do diretório, assim eles não podem ver nenhum usuário ou grupo na **Lista de membros**. Eles podem pesquisar por um usuário digitando o endereço de email completo do usuário e depois conceder acesso. O conjunto de restrições padrão para os convidados são:

-   Eles não podem enumerar os usuários e grupos no diretório.
-   Eles podem ver detalhes limitados de um usuário se sabem o endereço de email do usuário.
-   Eles podem ver detalhes limitados de um grupo quando eles sabem o nome do grupo.

A capacidade dos convidados verem detalhes limitados de um usuário ou grupo permite que eles convidem outras pessoas e vejam alguns detalhes das pessoas com quem eles estão colaborando.

Vamos passar pelo processo para adicionar o acesso para um usuário externo. Adicionaremos um usuário externo a mesma função de Leitor para grupo de recursos TestDB para que o usuário possa ajudar a depurar um erro. Abra a lâmina do grupo de recursos, clique em **Leitor \> Adicionar \> Convidar** e digite o endereço de email do usuário que deseja adicionar.

![][9]

Quando você adiciona um usuário externo, um convidado é criado no diretório. Posteriormente, aquele convidado pode ser adicionado a um grupo ou removido de um grupo, ou você pode adicioná-lo ou removê-lo individualmente de uma função simplesmente como faria para qualquer outro usuário do diretório.

Você pode também remover um convidado de qualquer função, simplesmente como você removeria qualquer usuário. Remover o convidado de uma função em um recurso não remove o convidado do diretório.

## <span id="knownissues"></span></a>Problemas conhecidos ao usar o controle de acesso com base em função

Se você encontrar um problema ao usar o recurso do controle de acesso com base em função enquanto estiver na visualização, consulte [Solucionar problemas do controle de acesso com base em função][Solucionar problemas do controle de acesso com base em função] para qualquer problema conhecido que possa estar relacionado ao problema.

## <span id="feedback"></span></a>Como fornecer comentários

Tente o Azure RBAC e nos envie [comentários][comentários].

## <span id="next"></span></a>Próximas etapas

Veja alguns recursos adicionais para ajudar você a usar o controle de acesso com base em função:

-   [Gerenciar o controle de acesso com base em função com o Windows PowerShell][Gerenciar o controle de acesso com base em função com o Windows PowerShell]
-   [Gerenciar o controle de acesso com base em função com o XPLAT CLI][Gerenciar o controle de acesso com base em função com o XPLAT CLI]
-   [Solucionar problemas do controle de acesso com base em função][Solucionar problemas do controle de acesso com base em função]
-   [Azure Active Directory][Azure Active Directory]
-   [Azure Active Directory Premium e Basic][Azure Active Directory Premium e Basic]
-   [Como as assinaturas do Azure estão associadas ao Azure AD][Como as assinaturas do Azure estão associadas ao Azure AD]
-   Para uma introdução para o gerenciamento de grupo de autoatendimento para os grupos de segurança, consulte o [Blog da equipe do Active Directory][Blog da equipe do Active Directory]

<!--Image references-->

  [postagem no blog]: http://go.microsoft.com/fwlink/?LinkId=511576
  [RBAC no Azure]: #whatisrbac
  [Coexistência do RBAC com os coadministradores de assinatura]: #coexist
  [Autorização para o gerenciamento versus operações de dados]: #authmgmt
  [Como adicionar e remover acesso]: #addremoveaccess
  [Problemas conhecidos ao usar o controle de acesso com base em função]: #knownissues
  [Como fornecer comentários]: #feedback
  [Próximas etapas]: #next
  []: ./media/role-based-access-control-configure/RBACSubAuthDir.png
  [Integração de diretórios]: http://technet.microsoft.com/library/jj573653.aspx
  [recurso de gerenciamento de grupos delegados]: http://msdn.microsoft.com/library/azure/dn641267.aspx
  [1]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
  [Adicionar acesso]: #add
  [Remover acesso]: #remove
  [Adicionar ou remover o acesso para o usuário externo]: #addremoveext
  [2]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
  [3]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
  [4]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
  [5]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
  [Gerenciar o controle de acesso com base em função com o Windows PowerShell]: http://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-powershell/
  [6]: ./media/role-based-access-control-configure/RBACRemoveRole.png
  [7]: ./media/role-based-access-control-configure/RBACDirConfigTab.png
  [8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
  [9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
  [Solucionar problemas do controle de acesso com base em função]: http://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-troubleshooting/
  [comentários]: http://aka.ms/azurerbacfeedback
  [Gerenciar o controle de acesso com base em função com o XPLAT CLI]: http://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-xplat-cli/
  [Azure Active Directory]: http://msdn.microsoft.com/library/azure/jj673460.aspx
  [Azure Active Directory Premium e Basic]: http://msdn.microsoft.com/en-us/library/azure/dn532272.aspx
  [Como as assinaturas do Azure estão associadas ao Azure AD]: http://msdn.microsoft.com/en-us/library/azure/dn629581.aspx
  [Blog da equipe do Active Directory]: http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx
