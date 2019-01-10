---
title: Proteger seu Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Gerencie permissões de acesso para Azure Key Vault, chaves e segredos. Abrange o modelo de autenticação e autorização do Key Vault e descreve como proteger o cofre de chaves.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 9877698c8c6af68c5ffd88dab37150274ce87b37
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077327"
---
# <a name="secure-your-key-vault"></a>Proteger seu cofre de chaves

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia (como certificados, cadeias de conexão e senhas). Como esses dados são confidenciais e comercialmente críticos, é necessário proteger o acesso aos seus cofres de chaves permitindo apenas aplicativos e usuários autorizados. Este artigo fornece uma visão geral do modelo de acesso do Key Vault. Explica a autenticação e autorização e descreve como proteger o acesso.

## <a name="overview"></a>Visão geral

O acesso a um cofre de chaves é controlado por meio de duas interfaces separadas: plano de gerenciamento e plano de dados. 
O **Plano de gerenciamento plane** lida com o gerenciamento do cofre, por exemplo- criando um cofre, atualizando um cofre, excluindo um cofre. 
O **Plano de dados** lida com segredos dentro de um cofre, ou seja, criar, atualizar, excluir e ler um segredo dentro do cofre. Para ambos os planos, a autorização e a autenticação adequadas são necessárias para que um chamador (um usuário ou um aplicativo) possa obter acesso ao cofre de chaves. A autenticação estabelece a identidade do chamador, enquanto a autorização determina as operações que o chamador tem permissão para executar.

Para a autenticação, o plano de gerenciamento e o plano de dados usam o Azure Active Directory. Para a autorização, o plano de gerenciamento usa RBAC (controle de acesso baseado em função), enquanto o plano de dados usa a política de acesso do cofre de chaves.

Aqui está uma breve visão geral dos tópicos abordados:

[Autenticação usando o Azure Active Directory](#authentication-using-azure-active-directory) - esta seção explica como um chamador autenticado com o Azure Active Directory acessa um cofre de chaves por meio do plano de gerenciamento e do plano de dados. 

Para autenticação, ambos os planos usam o Azure AD (Azure Active Directory). Para autorização, o plano de gerenciamento usa o RBAC (controle de acesso baseado em função), enquanto o plano de dados usa a política de acesso ao cofre de chaves.

## <a name="authenticate-by-using-azure-active-directory"></a>Autenticar usando o Azure Active Directory

Ao criar um cofre de chaves em uma assinatura do Azure, ele é automaticamente associado ao locatário do Azure AD da assinatura. Todos os chamadores devem estar registrados nesse locatário e deverão ser autenticados para acessar o cofre da chave. Esse requisito se aplica a ambos os acesso do plano de dados e o plano de gerenciamento. Em ambos os casos, um aplicativo poderá acessar o Key Vault de duas maneiras:

* **usuário+acesso de aplicativo**: Usado com aplicativos que acessam o Key Vault em nome de um usuário conectado. O Azure PowerShell e o portal do Azure são exemplos desse tipo de acesso. Há duas maneiras de conceder acesso a usuários:

  - Acesse o Key Vault de qualquer aplicativo.
  - Acesse o Key Vault somente ao usarem um aplicativo específico (referido como identidade composta).

* **acesso somente do aplicativo**: Usado com aplicativos que são executados como serviços do daemon ou trabalhos em segundo plano. A identidade do aplicativo recebe acesso ao cofre de chaves.

Em ambos os tipos de aplicativos, o aplicativo é autenticado com o Azure AD usando qualquer um dos [métodos de autenticação com suporte](../active-directory/develop/authentication-scenarios.md) e adquire um token. O método de autenticação usado depende do tipo de aplicativo. Em seguida, o aplicativo usará esse token e enviará uma solicitação de API REST ao Key Vault. Solicitações de plano de gerenciamento são roteadas por meio de um ponto de extremidade do Azure Resource Manager. Ao acessar o plano de dados, o aplicativo comunica-se diretamente com um ponto de extremidade do Key Vault. Para obter mais informações, consulte o [fluxo de autenticação inteiro](../active-directory/develop/v1-protocols-oauth-code.md). 

O nome do recurso para o qual o aplicativo solicita um token depende de qual plano o aplicativo está acessando. O nome do recurso é um ponto de extremidade do plano de gerenciamento ou um ponto de extremidade do plano de dados, dependendo do ambiente do Azure. Para obter mais detalhes, consulte a tabela mais adiante neste artigo.

Ter um único mecanismo para autenticação em ambos os planos tem alguns benefícios:

* As organizações podem controlar centralmente o acesso a todos os cofres de chaves em sua organização.
* Se um usuário sair, ele perderá instantaneamente o acesso a todos os cofres de chaves da organização.
* As organizações podem personalizar a autenticação por meio das opções no Azure AD (por exemplo, habilitar a autenticação multifator para obter maior segurança).

## <a name="the-management-plane-and-the-data-plane"></a>O plano de gerenciamento e o plano de dados

Use o plano de gerenciamento para gerenciar o próprio Key Vault. Isso inclui operações como atributos de gerenciamento e políticas de acesso do plano de dados de configuração. Use o plano de dados para adicionar, excluir, modificar e usar as chaves, segredos e certificados armazenados no Key Vault.

Acesse o plano de gerenciamento e as interfaces do plano de dados por meio dos diferentes pontos de extremidade listados na tabela a seguir. A segunda coluna da tabela descreve os nomes DNS para esses pontos de extremidade em diferentes ambientes do Azure. A terceira coluna descreve as operações que você pode executar em cada plano de acesso. Cada plano de acesso também tem seu próprio mecanismo de controle de acesso. O controle de acesso do plano de gerenciamento é definido usando o RBAC (controle de acesso baseado em função) do Azure Resource Manager. O controle de acesso do plano de dados é definido usando a política de acesso ao cofre de chaves.

| Plano de acesso | Pontos de extremidade de acesso | Operações | Mecanismo de controle de acesso |
| --- | --- | --- | --- |
| Plano de gerenciamento |**Global:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure Governo dos EUA:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br>  management.microsoftazure.de:443 |Criar/Ler/Atualizar/Excluir cofre de chaves <br> Definir políticas de acesso para o cofre de chaves<br>Definir marcas para o cofre de chaves |RBAC do Azure Resource Manager |
| Plano de dados |**Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure Governo dos EUA:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |Para chaves: Descriptografar, Criptografar, UnwrapKey, WrapKey, Verificar, Assinar, Obter, Listar, Atualizar, Criar, Importar, Excluir, Backup, Restaurar<br><br> Para segredos: Get, Listar, Definir, Excluir |Política de acesso ao cofre de chaves |

Os controles de acesso do plano de gerenciamento e do plano e dados funcionam de forma independente. Por exemplo, se você quiser conceder acesso de aplicativo para usar as chaves em um cofre de chaves, você apenas precisa conceder acesso ao plano de dados. Você concede o acesso por meio das políticas de acesso ao cofre de chaves. Por outro lado, um usuário que precisar ler propriedades e marcas do cofre de chaves, mas não acessar dados (chaves, segredos ou certificados), precisará apenas de acesso ao plano de gerenciamento. Você concede acesso atribuindo acesso de leitura ao usuário com RBAC.

## <a name="management-plane-access-control"></a>Controle de acesso do plano de gerenciamento

O plano de gerenciamento consiste em operações que afetam o cofre de chaves em si, como:

- Criar ou excluir um cofre de chaves.
- Obter uma lista de cofres em uma assinatura.
- Recuperar propriedades do cofre de chaves (como SKU e marcas).
- Definir políticas de acesso ao cofre de chaves que controlam o acesso do usuário e do aplicativo a chaves e segredos.

O controle de acesso do plano de gerenciamento usa RBAC.  

### <a name="role-based-access-control-rbac"></a>RBAC (Controle de Acesso Baseado em Função)

Cada assinatura do Azure tem uma instância do Azure AD. Você concede acesso a usuários, grupos e aplicativos desse diretório para gerenciar recursos na assinatura do Azure que usa o modelo de implantação do Azure Resource Manager. Esse tipo de controle de acesso é chamado de RBAC. Para gerenciar esse acesso, é possível usar o [Portal do Azure](https://portal.azure.com/), as [ferramentas da CLI do Azure](../cli-install-nodejs.md), o [PowerShell](/powershell/azureps-cmdlets-docs) ou as [APIs REST do Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Você cria um cofre de chaves em um grupo de recursos e controla o acesso ao plano de gerenciamento usando o Azure AD. Por exemplo, você pode conceder a usuários ou a um grupo a capacidade de gerenciar cofres de chaves em um grupo de recursos.

Você pode conceder acesso a usuários, grupos e aplicativos em um escopo específico atribuindo funções RBAC apropriadas. Por exemplo, para conceder acesso a um usuário para gerenciar cofres de chaves, você atribui uma função Colaborador do Key Vault predefinida a esse usuário, em um escopo específico. O escopo nesse caso, seria uma assinatura, um grupo de recursos ou um cofre de chaves específico. Uma função atribuída no nível da assinatura aplica-se a todos os grupos de recursos e recursos dentro dessa assinatura. Uma função atribuída no nível do grupo de recursos aplica-se a todos os recursos nesse grupo de recursos. Uma função atribuída para um recurso específico se aplica a esse recurso. Há várias funções predefinidas (consulte [RBAC: Funções internas](../role-based-access-control/built-in-roles.md)). Se uma função predefinida não atender às suas necessidades, você poderá definir sua própria função.

> [!IMPORTANT]
> Observe que, se um usuário tiver permissões de Colaborador para um plano de gerenciamento do Key Vault, ele poderá conceder acesso ao plano de dados definindo a política de acesso ao cofre de chaves. Portanto, você deve controlar rigorosamente quem tem acesso de Colaborador a seus cofres de chaves. Certifique-se de que apenas pessoas autorizadas possam acessar e gerenciar seus cofres de chaves, chaves, segredos e certificados.
>

## <a name="data-plane-access-control"></a>Controle de acesso do plano de dados

As operações do plano de dados do Key Vault aplicam-se a objetos armazenados como chaves, segredos e certificados. Operações de chave incluem criar, importar, atualizar, listar, fazer backup e restaurar as chaves. As operações criptográficas incluem assinar, verificar, criptografar, descriptografar, encapsular, desencapsular, definir marcas e outros atributos para chaves. Da mesma forma, as operações de segredos incluem obter, definir, listar, excluir.

Você concede acesso ao plano de dados, definindo políticas de acesso para um cofre de chaves. Um usuário, grupo ou aplicativo deve ter permissões de Colaborador para o plano de gerenciamento de um cofre de chaves para poder definir políticas de acesso para esse cofre de chaves. Você pode conceder acesso a um usuário, grupo ou aplicativo para executar operações específicas para chaves ou segredos em um cofre de chaves. O Key Vault dá suporte a até 1024 entradas de política de acesso para um cofre de chaves. Para conceder acesso ao plano de dados a vários usuários, crie um grupo de segurança do Azure AD e adicione usuários a esse grupo.

### <a name="key-vault-access-policies"></a>Políticas de acesso ao cofre de chaves

As políticas de acesso ao cofre de chaves concedem permissões a chaves, segredos e certificados separadamente. Por exemplo, você pode dar a um usuário acesso apenas a chaves, e nenhuma permissão para segredos. As permissões para acessar chaves, segredos ou certificados estão no nível de cofre. A política de acesso ao cofre de chaves não dá suporte a permissões no nível de objeto granulares como uma chave específica, segredo ou certificado. Você pode usar o [portal do Azure](https://portal.azure.com/), as [ferramentas CLI do Azure](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), ou as [APIs REST de gerenciamento do Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx) para acessar as políticas de acesso ao cofre de chaves.

> [!IMPORTANT]
> As políticas de acesso ao cofre de chaves aplicam-se no nível do cofre. Por exemplo, quando uma usuária tem permissão para criar e excluir chaves, ela pode executar essas operações em todas as chaves no cofre de chaves.

Além de usar as políticas de acesso, também é possível restringir o acesso ao plano de dados usando [pontos de extremidade de serviço de rede virtual para Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Você configura [Firewalls e regras de rede virtual](key-vault-network-security.md) para uma camada adicional de segurança.

## <a name="example"></a>Exemplo

Digamos que você esteja desenvolvendo um aplicativo que usa um certificado para SSL, o 	Armazenamento do Microsoft Azure para armazenamento de dados e uma chave RSA de 2048 bits para operações de assinatura. Vamos supor que esse aplicativo esteja executando em uma máquina virtual do Azure (ou em um conjunto de dimensionamento de máquinas virtuais). Você poderá usar um cofre de chaves para armazenar todos os segredos do aplicativo e armazenar o certificado de inicialização usado pelo aplicativo para autenticar com Azure AD.

Aqui está um resumo dos tipos de chaves e segredos armazenados:

* **Certificado SSL**: Usado para SSL.
* **Chave de Armazenamento**: Usado para obter acesso à conta de Armazenamento.
* **Chave RSA de 2048 bits**: Usado para operações de sinalização.
* **Certificado de inicialização**: Usado para autenticar com Azure AD. Após o acesso ser concedido, você poderá buscar a chave de armazenamento e usar a chave RSA para assinatura.

Agora, vamos conhecer as pessoas que estão gerenciando, implantando e auditando esse aplicativo. Vamos usar três funções neste exemplo.

* **Equipe de segurança**: Normalmente, a equipe de TI do escritório do CSO (Chief Security Officer), ou equivalente. Esta equipe é responsável pela segurança adequada de segredos. Os exemplos incluem certificados SSL, chaves RSA usadas para assinatura, cadeias de conexão e chaves da conta de armazenamento.
* **Desenvolvedores/operadores**: Pessoas que desenvolvem o aplicativo e implantam-no no Azure. Normalmente, eles não fazem parte da equipe de segurança, e, portanto, não deveriam ter acesso a dados confidenciais, como certificados SSL e chaves RSA. Somente o aplicativo que implantam deve ter acesso a esses objetos.
* **Auditores**: Normalmente, um conjunto diferente de pessoas, isoladas dos desenvolvedores e da equipe de TI geral. Sua responsabilidade é analisar o uso e a manutenção de certificados, chaves e segredos para garantir a conformidade com padrões de segurança. 

Há mais uma função que está fora do escopo deste aplicativo, mas relevante aqui para ser mencionado. Essa função é o administrador de assinatura (ou grupo de recursos). O administrador de assinatura define permissões de acesso iniciais para a equipe de segurança. O administrador da assinatura concede acesso à equipe de segurança, usando um grupo de recursos que contém os recursos requeridos por esse aplicativo.

Agora, vamos ver as ações que cada função executa no contexto do aplicativo.

* **Equipe de segurança**
  * Cria cofres de chaves.
  * Ativa o registro em log do cofre de chaves.
  * Adiciona chaves/segredos.
  * Cria backup de chaves para recuperação de desastre.
  * Define a política de acesso ao cofre de chaves para conceder permissões a usuários e aplicativos para executar operações específicas.
  * Distribui periodicamente chaves/segredos.
* **Desenvolvedores/operadores**
  * Obtém referências para inicialização e certificados SSL (impressões digitais), chave de armazenamento (URI de segredo) e chave de assinatura (URI de chave) da equipe de segurança.
  * Desenvolve e implanta o aplicativo que acessa chaves e segredos de forma programática.
* **Auditores**
  * Examina logs de uso para confirmar o uso adequado de chaves/segredos e a conformidade com padrões de segurança de dados.

Agora vamos ver quais permissões de acesso são necessárias para cada função e o aplicativo, para realizar suas tarefas atribuídas. 

| Função de usuário | Permissões do plano de gerenciamento | Permissões do plano de dados |
| --- | --- | --- |
| Equipe de Segurança |Colaborador do Key Vault |Chaves: fazer backup, criar, excluir, obter, importar, listar, restaurar <br> Segredos: todos |
| Desenvolvedores/operadores |A permissão de implantação do Key Vault permite que as máquinas virtuais implementadas possam extrair segredos do cofre de chaves. |Nenhum |
| Auditores |Nenhum |Chaves: lista<br>Segredos: lista |
| Aplicativo |Nenhum |Chaves: assinar<br>Segredos: obter |

> [!NOTE]
> Os auditores precisam de permissão de lista para chaves e segredos para que possam inspecionar os atributos de chaves e segredos que não são emitidos nos logs. Esses atributos incluem marcas, ativação e datas de validade.
> 
> 

Além das permissões do cofre de chaves, todas as três funções também precisam de acesso a outros recursos. Por exemplo, para poder implantar VMs (ou o recurso de Aplicativos Web do Serviço de Aplicativo do Azure), os desenvolvedores e operadores também precisam do acesso de Colaborador a esses tipos de recursos. Os auditores precisam de acesso de Leitura para a conta de armazenamento em que os logs do Key Vault estão armazenados.

Como o foco deste artigo é proteger o acesso ao cofre de chaves, apenas ilustramos conceitos relativos a esse tópico. Detalhes sobre como implantar certificados e acessar chaves e segredos programaticamente são abordados em outros locais. Por exemplo:

- Implantar certificados armazenados no cofre de chaves para VMs é abordado em uma [ Implantar certificados em VMs gerenciados pelo cliente do cofre de chaves](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (postagem no blog).
- O [download de exemplos de cliente do Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) ilustra como usar um certificado de inicialização para autenticar no Azure AD para acessar um cofre de chaves.

É possível conceder a maioria das permissões de acesso usando o portal do Azure. Para conceder permissões granulares, talvez seja necessário usar o Azure PowerShell ou a CLI para obter o resultado desejado. 

Os seguintes snippets de código do PowerShell pressupõem que:

* O administrador do Azure AD criou grupos de segurança que representam as três funções (Equipe de Segurança da Contoso, Desenvolvedores de Aplicativos da Contoso e Auditores de Aplicativos da Contoso). O administrador também adicionou usuários aos grupos aos quais pertencem.
* **ContosoAppRG** é o grupo de recursos em que residem todos os recursos. **contosologstorage** é onde os logs são armazenados. 
* O cofre de chaves **ContosoKeyVault** e a conta de armazenamento usada para logs do Key Vault **contosologstorage** devem estar no mesmo local no Azure.

Primeiro, o administrador da assinatura atribui funções `key vault Contributor` e `User Access Administrator` para a equipe de segurança. Essas funções permitem que a equipe de segurança gerencie o acesso a outros recursos e gerencie os cofres de chaves no grupo de recursos ContosoAppRG.

```PowerShell
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

O script a seguir mostra como a equipe de segurança pode criar um cofre de chaves e configurar as permissões de acesso e registro em log. Para obter detalhes sobre as permissões da política de acesso ao cofre de chaves, consulte [Sobre as chaves do Azure Key Vault, segredos e certificados](about-keys-secrets-and-certificates.md).

```PowerShell
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

A função personalizada definida é apenas atribuível à assinatura em que o grupo de recursos `ContosoAppRG` é criado. Se as mesmas funções personalizadas forem usadas para outros projetos em outras assinaturas, seu escopo poderá ter mais assinaturas adicionadas.

A atribuição de função personalizada para a permissão "implantar/ação" para desenvolvedores e operadores está no escopo do grupo de recursos. Isso permite que apenas as VMs criadas no grupo de recursos `ContosoAppRG` tenham acesso aos segredos (o certificado SSL e certificado de inicialização). As VMs criadas em outro grupo de recursos por um membro da equipe de desenvolvedores e operadores não terão acesso a esses segredos, mesmo se tiverem os URIs secretos.

Este exemplo mostra um cenário simples. Os cenários da vida real podem ser mais complexos e você pode ajustar as permissões para o cofre de chaves com base em suas necessidades. Em nosso exemplo, supomos que a equipe de segurança forneça as referências chave e secreta (URIs e impressões digitais) que os desenvolvedores e operadores precisam fazer referência nos aplicativos. Desenvolvedores e operadores não precisam de nenhum acesso ao plano de dados. Este exemplo se concentra sobre como proteger seu cofre de chaves. Considere maneiras semelhantes de proteger [as VMs](https://azure.microsoft.com/services/virtual-machines/security/), [contas de armazenamentos](../storage/common/storage-security-guide.md) e outros recursos do Azure.

> [!NOTE]
> Esse exemplo mostra como o acesso ao Key Vault será bloqueado na produção. Os desenvolvedores devem ter sua própria assinatura ou grupo de recursos em que tenham permissões totais para gerenciar seus cofres, VMs e conta de armazenamento onde desenvolvem o aplicativo.

É altamente recomendável proteger ainda mais o acesso ao seu cofre de chaves [configurando os firewalls e redes virtuais do Key Vault](key-vault-network-security.md).

## <a name="resources"></a>Recursos

* [Controle de acesso baseado em função do Azure Active Directory](../role-based-access-control/role-assignments-portal.md)
  
* [RBAC: Funções internas](../role-based-access-control/built-in-roles.md)
  
* [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md)
  
* [Gerenciar o controle de acesso baseado em função com o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
  
* [Gerenciar o controle de acesso baseado em função com a API REST](../role-based-access-control/role-assignments-rest.md)
  
* [Controle de acesso baseado em função para Microsoft Azure do Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Esta vídeo conferência 2015 Microsoft Ignite discute o gerenciamento de acesso e capacidades de relatório no Azure. Ele também explora as melhores práticas para proteger o acesso a assinaturas do Azure usando Azure AD.
* [Autorizar acesso aos aplicativos Web usando OAuth 2.0 e Azure AD](../active-directory/develop/v1-protocols-oauth-code.md)
  
* [APIs REST de gerenciamento do Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Este documento é a referência para as APIs REST para gerenciar o cofre de chaves de forma programática, incluindo a configuração de política de acesso ao cofre de chaves.
* [APIs REST do Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
* [Controle de acesso a chave](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Controle de acesso a segredo](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* [Definir](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy) e [Remover](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Remove-AzureRmKeyVaultAccessPolicy) política de acesso ao cofre de chaves usando PowerShell
  
## <a name="next-steps"></a>Próximas etapas

[Configurar redes virtuais e firewalls do Key Vault](key-vault-network-security.md)

Para ver um tutorial de introdução para um administrador, consulte [Introdução ao Cofre da Chave do Azure](key-vault-get-started.md).

Para saber mais sobre o log de uso do Key Vault, confira [Log do Azure Key Vault](key-vault-logging.md).

Para saber mais sobre o uso de chaves e segredos com o Azure Key Vault, consulte [Sobre chaves e segredos](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Se você tiver dúvidas sobre o Key Vault, visite os [fóruns](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

