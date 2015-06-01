<properties 
	pageTitle="Controle de acesso com base em função no portal de Visualização do Azure" 
	description="Descreve como funciona o controle de acesso baseado em função e como configurá-lo" 
	services="" 
	documentationCenter="" 
	authors="Justinha" 
	manager="terrylan" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="Ibiza" 
	ms.workload="infrastructure-services" 
	ms.date="02/20/2015" 
	ms.author="justinha"/>

<!--Este é um modelo básico que mostra como usar a redução para criar um tópico que inclui um Sumário, seções com subtítulos, links para outros tópicos azure.microsoft.com, links para outros sites, negrito, itálico, listas numeradas e com marcadores, trechos de código, e imagens. Para redução mais sofisticada, localize um tópico publicado e copie a redução ou HTML que desejar. Para obter mais detalhes sobre o uso da redução, consulte http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->

<!--Properties section (above): this is required in all topics. Please fill it out!-->

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Controle de acesso com base em função no portal de visualização do Azure 

<p> Adicionamos suporte para o controle de acesso com base em função (RBAC) no portal de visualização do Azure para ajudar as organizações a atender seus requisitos de gerenciamento de acesso de forma simples e precisa. A <a href="http://go.microsoft.com/fwlink/?LinkId=511576" target="_blank">postagem no blog</a> fornecerá a você uma introdução rápida do recurso e ajudará você a começar. Este tópico descreve os conceitos em detalhes e abrange casos de uso adicionais. </p>

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## RBAC no Azure
                                                                   
Cada assinatura do Azure está associada com um Active Directory do Azure. Os usuários e serviços que acessam os recursos da assinatura usando o portal de Gerenciamento do Azure ou a API do Gerenciador de Recursos do Azure primeiro precisam se autenticar com aquele Azure Active Directory.

![][1] 

O controle de acesso com base em função permite que você conceda acesso apropriado aos usuários, grupos e serviços do Azure AD, atribuindo funções para eles em uma assinatura ou grupo de recursos ou nível de recurso individual. A função atribuída define o nível de acesso que os usuários, grupos ou serviços tem no recurso do Azure. 

### Função

Uma função é uma coleção de ações que podem ser executadas nos recursos do Azure. Um usuário ou um serviço tem a permissão de executar uma ação em um recurso do Azure, se eles forem atribuídos uma função que contém aquela ação. Para obter uma lista de funções internas e **suas** ações e não as propriedades das **ações**, consulte [Funções internas](#builtinroles).

### Atribuição de função

O acesso é concedido aos usuários e serviços do Azure AD atribuindo a função apropriada a eles em um recurso do Azure. 

#### Entidades de segurança do Azure AD

As funções podem ser atribuídas para os seguintes tipos de entidades de segurança do Azure AD:

+ **Usuários**: As funções podem ser atribuídas aos usuários organizacionais que estão no Azure AD com os quais a assinatura do Azure está associada. As funções também podem ser atribuídas aos usuários da conta da Microsoft externa (como joe@outlook.com) usando a ação de Convite para atribuir ao usuário um função no portal de visualização do Azure. Ao atribuir uma função a um usuário de conta da Microsoft externa faz com que uma conta de convidado seja criada no Azure AD para ele. Se essa conta de convidado está desabilitada no diretório, o usuário externo não poderá acessar nenhum recurso do Azure para o qual o usuário tem concedido acesso.
+ **Grupos**: as funções podem ser atribuídas para os grupos de segurança do Azure AD. Um usuário é concedido acesso automaticamente a um recurso se ele se tornar um membro de um grupo que tem acesso. O usuário também perde o acesso automaticamente ao recurso após ser removido do grupo. Gerenciar o acesso através de grupos ao atribuir funções aos grupos e adicionar usuários a esses grupos é a prática recomendada, em vez de atribuir funções diretamente aos usuários. O RBAC do Azure não permite atribuir funções a listas de distribuição.
	A capacidade de atribuir funções a grupos permite que uma organização estenda seu modelo de controle de acesso existente de seu diretório local para a nuvem, para que os grupos de segurança que já estejam estabelecidos para controlar o acesso local possam ser reutilizados no portal de Visualização do Azure. Para obter mais informações sobre as diferentes opções para sincronizar usuários e grupos de um diretório local, consulte [Integração de diretórios](http://technet.microsoft.com/library/jj573653.aspx). O Azure AD Premium também oferece um [recurso de gerenciamento de grupos delegados](http://msdn.microsoft.com/library/azure/dn641267.aspx) com o qual a capacidade de criar e gerenciar grupos pode ser delegada aos usuários não administradores do Azure AD.
+ **Entidades de serviço**: as identidades de serviço são representadas como entidades de serviço no diretório. Elas se autenticam com o Azure AD e se comunicam com segurança umas com as outras. Os serviços podem ser concedidos acesso aos recursos do Azure ao atribuir funções através do módulo do Azure para o Windows PowerShell à entidade de serviço do Azure AD representando aquele serviço. 

#### Escopo do recurso

O acesso não precisa ser concedido para toda a assinatura. As funções podem ser atribuídas para grupos de recursos bem como para recursos individuais. No Azure RBAC, um recurso herda atribuições de função de seus recursos pai. Então se um usuário, grupo ou serviço é concedido acesso somente a um grupo de recursos dentro da assinatura, eles poderão somente acessar aquele grupo de recursos e os recursos dentro dele, e não outros grupos de recursos dentro da assinatura. Como outro exemplo, um grupo de segurança pode ser adicionado à função de Leitor para um grupo de recursos, mas ser adicionado a uma função de Colaborador para um banco de dados dentro daquele grupo de recursos.

![][2]

## Coexistência do RBAC com os coadministradores de assinatura

Os administradores e coadministradores de assinatura continuarão a ter acesso completo aos portais do Azure e as APIs de gerenciamento. No modelo RBAC, eles recebem a função de Proprietário no nível de assinatura.  
No entanto, há suporte para o novo modelo RBAC apenas no portal de visualização do Azure e nas APIs do Gerenciador de Recursos do Azure. Os usuários e serviços que são atribuídos às funções RBAC não podem acessar o portal de Gerenciamento do Azure e as APIs de Gerenciamento de serviços. Ao adicionar um usuário a função de Proprietário de uma assinatura no portal de visualização do Azure não torna aquele usuário um coadministrador da assinatura no portal completo do Azure.

Se você deseja permitir acesso a um usuário para um recurso do Azure que ainda não está disponível para ser gerenciado por meio do portal de Visualização do Azure, você deve adicioná-los aos coadministradores de assinatura usando o portal completo de Gerenciamento do Azure. O Barramento de Serviço e os Serviços de Nuvem são exemplo de recursos que hoje não podem ser gerenciados usando o RBAC.

## Autorização para o gerenciamento versus operações de dados

O controle de acesso com base em função tem suporte somente para as operações de gerenciamento dos recursos do Azure no portal de Visualização do Azure e nas APIs do Gerenciador de Recursos do Azure. Nem todas as operações no nível de dados para os recursos do Azure podem ser autorizadas por meio do RBAC. Por exemplo, a criação/leitura/atualização/exclusão de contas de armazenamento pode ser controlada por meio do RBAC, mas a criação/leitura/atualização/exclusão de blobs ou tabelas dentro da conta de armazenamento não pode ser controlada por meio do RBAC. Semelhantemente, a criação/leitura/atualização/exclusão de um BD SQL pode ser controlada por meio do RBAC, mas a criação/leitura/atualização/exclusão de tabelas de SQL dentro do BD ainda não pode ser controlada por meio do RBAC.

## Como adicionar e remover acesso

Vamos olhar um exemplo de como o proprietário do recurso em uma organização pode gerenciar o acesso. Nesse cenário, você tem várias pessoas trabalhando em uma variedade de testes e projetos de produção que são desenvolvidos usando os recursos do Azure. Você deseja seguir as práticas recomendadas para conceder acesso. Os usuários devem ter acesso a todos os recursos que eles precisam, porém não acesso adicional. Você deseja reutilizar todos os investimentos que você fez nos processos e ferramentas para usar grupos de segurança que são controlados em um Active Directory local. Estas seções cobrem como configurar o acesso para estes recursos:

* [Adicionar acesso](#add)
* [Remover acesso](#remove)
* [Adicionar ou remover o acesso para o usuário externo](#addremoveext)

<h3><a id="add"></a>Adicionar acesso</h3>

Aqui está um resumo dos requisitos de acesso e como eles são configurados no Azure.

Usuário/grupo | Requisito de acesso | função e o escopo para acesso	
------------- | -------------  | ------------
Toda a equipe de Jill Santos | Ler todos os recursos do Azure | Adicionar o grupo do AD que representa a equipe de Jill Santos à função de Leitor para a assinatura do Azure
Toda a equipe de Jill Santos | Criar e gerenciar todos os recursos no grupo de recursos de teste | Adicionar o grupo do AD que representa a equipe de Jill Santos à função de Colaborador para o grupo de recursos de teste
Brock | Criar e gerenciar todos os recursos no grupo de recursos Prod | Adicionar Brock à função de Colaborador para o grupo de recursos Prod


Primeiro, vamos adicionar o acesso de Leitura para todos os recursos da assinatura. Clique em **Procurar > Tudo > Assinaturas**.

![][3] 

Clique em  *name of your subscription*** > Leitor > Adicionar**. Na lista de usuários e grupos, selecione ou digite o nome do grupo do Active Directory.

![][4]

Depois adicione a mesma equipe à função de colaborador do grupo de recursos de teste. Clique no grupo de recursos para abrir sua lâmina de propriedade. Em **Funções**, clique em **Colaborador > Adicionar** e digite o nome da equipe.

![][5]

Para adicionar Brock à função de Colaborador do grupo de recursos Prod, clique no grupo de recursos, clique em **Colaborador > Adicionar** e digite o nome do Brock. 

![][6]

As atribuições de função podem também ser gerenciadas usando o módulo Microsoft Azure para o Windows PowerShell. Aqui está um exemplo de como adicionar a conta de Brock usando o cmdlet New-AzureRoleAssignment em vez do portal:

	PS C:> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Para obter mais informações sobre como usar o Windows PowerShell para adicionar e remover o acesso, consulte [Gerenciar o controle de acesso com base em função com o Windows PowerShell](http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/). 

<h3><a id="remove"></a>Remover acesso</h3>

Você também pode remover atribuições facilmente. Digamos que você deseja remover um usuário chamado de Brad Adams da função de Leitor para um grupo de recursos chamado TestDB. Abra a lâmina do grupo de recursos, clique em **Leitor > Brad Adams > Remover**.

![][7]

Aqui está um exemplo de como remover Brad Adams usando o cmdlet New-AzureRoleAssignment:

	PS C:> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

<h3><a id="addremoveext"></a>Adicionar ou remover o acesso para o usuário externo</h3>

A guia **Configurar** do diretório inclui opções para controlar o acesso para usuários externos. Essas opções podem ser alteradas apenas na interface do usuário (não há nenhum método de API do Windows PowerShell) no portal do Azure completo por um administrador global do diretório. 
Para abrir a guia **Configurar** no portal do Azure completo, clique em **Active Directory** e clique no nome do diretório.

![][10]

Então você pode editar as opções para controlar o acesso para os usuários externos. 

![][8]

Por padrão, os convidados não podem enumerar o conteúdo do diretório, assim eles não podem ver nenhum usuário ou grupo na **Lista de membros**. Eles podem pesquisar por um usuário digitando o endereço de email completo do usuário e depois conceder acesso. O conjunto de restrições padrão para os convidados são:

- Eles não podem enumerar os usuários e grupos no diretório.
- Eles podem ver detalhes limitados de um usuário se sabem o endereço de email do usuário.
- Eles podem ver detalhes limitados de um grupo quando eles sabem o nome do grupo.

A capacidade dos convidados verem detalhes limitados de um usuário ou grupo permite que eles convidem outras pessoas e vejam alguns detalhes das pessoas com quem eles estão colaborando.  

Vamos passar pelo processo para adicionar o acesso para um usuário externo. Adicionaremos um usuário externo a mesma função de Leitor para grupo de recursos TestDB para que o usuário possa ajudar a depurar um erro. Abra a lâmina do grupo de recursos, clique em **Leitor > Adicionar > Convidar** e digite o endereço de email do usuário que deseja adicionar. 

![][9]

Quando você adiciona um usuário externo, um convidado é criado no diretório. Posteriormente, aquele convidado pode ser adicionado a um grupo ou removido de um grupo, ou você pode adicioná-lo ou removê-lo individualmente de uma função simplesmente como faria para qualquer outro usuário do diretório. 

Você pode também remover um convidado de qualquer função, simplesmente como você removeria qualquer usuário. Remover o convidado de uma função em um recurso não remove o convidado do diretório. 
 
## Problemas conhecidos ao usar o controle de acesso com base em função

Se você encontrar um problema ao usar o recurso do controle de acesso com base em função enquanto estiver na visualização, consulte [Solucionar problemas do controle de acesso com base em função](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/) para qualquer problema conhecido que possa estar relacionado ao problema.


## Funções internas

O controle de acesso baseado em função do Azure é fornecido com as seguintes funções internas que podem ser atribuídas a usuários, grupos e serviços. Você não pode modificar a definição das funções integradas. Em uma versão futura do RBAC do Azure, você poderá definir funções personalizadas pela composição de um conjunto de ações de uma lista de ações disponíveis que podem ser executadas nos recursos do Azure.

Clique no link correspondente para ver as **ações** e não as propriedades das **ações** de uma definição de função. A propriedade das **ações** especifica as ações permitidas em recursos do Azure. As cadeias de caracteres da ação podem usar caracteres curingas. A propriedade **não-ações** da definição de função especifica as ações que devem ser excluídas das ações permitidas. 


Nome da função | Descrição  	
------------- | -------------  
[Colaborador de serviço de gerenciamento de API](#APIMgmt) | Permite gerenciar o serviço de gerenciamento de API, mas não acessá-lo.
[Colaborador de componente do Application Insights](#AppInsights) | Permite gerenciar componentes do Application Insights, mas não acessá-los.
[Colaborador do BizTalk](#BizTalk) | Permite gerenciar os serviços do BizTalk, mas não acessá-los.
[Colaborador do DB MySQL ClearDB](#ClearDB) | Permite que você gerencie bancos de dados MySQL ClearDB, mas não acessá-los.
[Colaborador](#Contributor) | Colaboradores podem gerenciar tudo, exceto o acesso.
[Colaborador da fábrica de dados](#DataFactory) | Permite que você gerencie fábricas de dados, mas não acessá-las.
[Colaborador de conta do banco de dados de documento](#DocDBContrib) | Permite gerenciar contas do DocumentDB, mas não acessá-las.
[Colaborador de conta de sistemas inteligentes](#IntelliSysContrib) | Permite gerenciar contas de sistemas inteligentes, mas não acessá-las.
[Colaborador de conta do APM NewRelic](#NewRelicContrib) | Permite gerenciar novas contas e aplicativos de gerenciamento de desempenho de aplicativo Relic, mas não acessá-las.
[Proprietário](#Owner) | O proprietário pode gerenciar tudo, incluindo o acesso.
[Leitor](#Reader) | Os leitores podem ver tudo, mas não podem fazer alterações.
[Colaborador do Cache Redis](#Redis) | Permite gerenciar caches Redis, mas não acessá-los.
[Colaborador do banco de dados SQL](#SQLDBContrib) | Permite que você gerencie bancos de dados SQL, mas não acessá-los. Além disso, você não pode gerenciar suas políticas de segurança ou seus SQL servers principais.
[Gerenciador de Segurança do SQL](#SQLSecMgr) | Permite que você gerencie políticas de segurança de SQL servers e bancos de dados, mas não acessá-las.
[Colaborador do SQL Server](#SQLSrvContrib) | Permite que você gerencie SQL servers e bancos de dados, mas não acessá-los nem as políticas de segurança.
[Colaborador de Coleções de Trabalho do Agendador](#SchedContrib) | Permite que você gerencie coleções de trabalho do Agendador, mas não acessá-las.
[Colaborador do Serviço de Pesquisa](#SearchContrib) | Permite que você gerencie Serviços de pesquisa, mas não acessá-los.
[Colaborador da Conta de Armazenamento](#StorageContrib) | Permite que você gerencie contas de armazenamento, mas não acessá-las.
[Administrador de Acesso do Usuário](#UserAccessAdmin) | Permite que você gerencie o acesso do usuário aos recursos do Azure.
[Colaborador de Máquina Virtual](#VMContrib) | Permite que você gerencie máquinas virtuais, mas não acessá-las e não a rede virtual ou conta de armazenamento que estão conectadas.
[Colaborador de Rede Virtual](#VNetContrib) | Permite gerenciar redes virtuais, mas não acessá-las.
[Colaborador do Plano de Web](#WebPlanContrib) | Permite que você gerencie os planos da Web para sites, mas não acessá-los.
[Colaborador do Site](#WebsiteContrib) | Permite que você gerencie sites (não os planos da Web), mas não acessá-los.


<h3><a id="APIMgmt"></a>Colaborador de serviço de gerenciamento de API</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>Criar e gerenciar os serviços de gerenciamento de API</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="AppInsights"></a>Colaborador de componente do Application Insights</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.Insights/components/*</td>
<td>Criar e gerenciar componentes do Insights</td>
</tr>
<tr>
<td>Microsoft.Insights/webtests/*</td>
<td>Criar e gerenciar testes da Web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="BizTalk"></a>Colaborador do BizTalk</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.BizTalkServices/BizTalk/*</td>
<td>Criar e gerenciar os serviços do BizTalk</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="ClearDB"></a>Colaborador do DB MySQL ClearDB</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>successbricks.cleardb/databases/*</td>
<td>Criar e gerenciar bancos de dados MySQL ClearDB</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="Contributor"></a>Colaborador</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>*</td>
<td>Criar e gerenciar recursos de todos os tipos</td>
</tr>
<tr>
<th colspan="2">Não-ações</th>
</tr>
<tr>
<td>Microsoft.Authorization/*/Write</td>
<td>Não é possível criar funções e atribuições de função </td>
</tr>
<tr>
<td>Microsoft.Authorization/*/Delete</td>
<td>Não é possível excluir funções e atribuições de função</td>
</tr>
</table>

<h3><a id="DataFactory"></a>Colaborador da fábrica de dados</h3>

<table style=width:100%">
<tr>
<td>Microsoft.DataFactory/dataFactories/*</td>
<td>Criar e gerenciar fábricas de dados</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="DocDBContrib"></a>Colaborador de conta do banco de dados de documento</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.DocumentDb/databaseAccounts/*</td>
<td>Criar e gerenciar contas do DocumentDB</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="IntelliSysContrib"></a>Colaborador de conta do sistemas inteligentes</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.IntelligentSystems/accounts/*</td>
<td>Criar e gerenciar contas do Intelligent Systems</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="NewRelicContrib"></a>Colaborador de conta do APM NewRelic</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>NewRelic.APM/accounts/*</td>
<td>Criar e gerenciar contas de gerenciamento de desempenho do aplicativo NewRelic</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="Owner"></a>Proprietário</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>*</td>
<td>Criar e gerenciar recursos de todos os tipos</td>
</tr>
</table>

<h3><a id="Reader"></a>Leitor</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>*/leitura</td>
<td>Ler recursos de todos os tipos. No entanto, você não pode ler senhas.</td>
</tr>
</table>

<h3><a id="Redis"></a>Colaborador do Cache Redis</h3>

<table style=width:100%">
<tr>
<td>Microsoft.Cache/redis/*</td>
<td>Criar e gerenciar Caches Redis</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="SQLDBContrib"></a>Colaborador do banco de dados SQL</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>Leia os SQL Servers</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/*</td>
<td>Criar e gerenciar bancos de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
<tr>
<th colspan="2">Não-ações</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Não é possível gerenciar políticas de auditoria do banco de dados do SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Não é possível gerenciar políticas de conexão do banco de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Não é possível gerenciar políticas de mascaramento de dados do banco de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Não é possível gerenciar métricas de segurança do banco de dados SQL</td>
</tr>
</table>

<h3><a id="SQLSecMgr"></a>Gerenciador de segurança do SQL</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>Leia os SQL Servers</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>Criar e gerenciar políticas de auditoria do SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/read</td>
<td>Ler bancos de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Criar e gerenciar políticas de auditoria do banco de dados do SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Criar e gerenciar políticas de conexão do banco de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Criar e gerenciar políticas de mascaramento de dados do banco de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Criar e gerenciar métricas de segurança do banco de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="SQLSrvContrib"></a>Colaborador do SQL Server</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>Criar e gerenciar os serviços de gerenciamento de API</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
<tr>
<th colspan="2">Não-ações</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>Não é possível gerenciar políticas de auditoria do SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Não é possível gerenciar políticas de auditoria do banco de dados do SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Não é possível gerenciar políticas de conexão do banco de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Não é possível gerenciar políticas de mascaramento de dados do banco de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Não é possível gerenciar métricas de segurança do banco de dados SQL</td>
</tr>
</table>

<h3><a id="SchedContrib"></a>Colaborador de coleções de trabalho do Agendador</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.Scheduler/jobcollections/*</td>
<td>Criar e gerenciar coleções de trabalhos do Agendador</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="SearchContrib"></a>Colaborador do serviço de pesquisa</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.Search/searchServices/*</td>
<td>Criar e gerenciar serviços de pesquisa</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="StorageContrib"></a>Colaborador da conta de armazenamento</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/*</td>
<td>Criar e gerenciar contas de armazenamento</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="UserAccessAdmin"></a>Administrador de Acesso do Usuário</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>*/leitura</td>
<td>Ler recursos de todos os tipos</td>
</tr>
<tr>
<td>Microsoft.Authorization/*</td>
<td>Criar e gerenciar funções e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="VMContrib"></a>Colaborador de Máquina Virtual</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/read</td>
<td>Ler contas de armazenamento</td>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/listKeys/action</td>
<td>Recuperar chaves da conta de armazenamento</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/read</td>
<td>Ler redes virtuais</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/join/action</td>
<td>Ingressar em redes virtuais</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/read</td>
<td>Ler endereços IP reservados</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/link/action</td>
<td>Vincular para endereços IP reservados</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/domainNames/*</td>
<td>Criar e gerenciar serviços em nuvem</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/virtualMachines/*</td>
<td>Criar e gerenciar máquinas virtuais</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="VNetContrib"></a>Colaborador de Rede Virtual</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/*</td>
<td>Criar e gerenciar redes virtuais</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="WebPlanContrib"></a>Colaborador do Plano da Web</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/*</td>
<td>Criar e gerenciar planos da Web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

<h3><a id="WebsiteContrib"></a>Colaborador do Site</h3>

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/read</td>
<td>Ler planos da Web</td>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/join/action</td>
<td>Ingressar em planos da Web</td>
</tr>
<tr>
<td>Microsoft.Web/sites/*</td>
<td>Criar e gerenciar sites da Web</td>
</tr>
<tr>
<td>Microsoft.Web/certificates/*</td>
<td>Criar e gerenciar certificados de site da Web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>


## Como fornecer comentários

Tente o Azure RBAC e nos envie [comentários](http://aka.ms/azurerbacfeedback). 


## Próximas etapas

Veja alguns recursos adicionais para ajudar você a usar o controle de acesso com base em função: 

+ [Gerenciar o controle de acesso com base em função com o Windows PowerShell](http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/)
+ [Gerenciar o controle de acesso com base em função com o XPLAT CLI](http://azure.microsoft.com/documentation/articles/role-based-access-control-xplat-cli/)
+ [Solucionar problemas do controle de acesso com base em função](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/)
+ [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx)
+ [Azure Active Directory Premium e Basic](http://msdn.microsoft.com/library/azure/dn532272.aspx)
+ [Como as assinaturas do Azure estão associadas ao Azure AD](http://msdn.microsoft.com/library/azure/dn629581.aspx)
+ Para uma introdução para o gerenciamento de grupo de autoatendimento para os grupos de segurança, consulte o [Blog da equipe do Active Directory](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx)



<!--Image references-->
[1]: ./media/role-based-access-control-configure/RBACSubAuthDir.png
[2]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
[3]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
[4]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
[5]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
[6]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
[7]: ./media/role-based-access-control-configure/RBACRemoveRole.png
[8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
[9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
[10]: ./media/role-based-access-control-configure/RBACDirConfigTab.png



<!--HONumber=47-->
