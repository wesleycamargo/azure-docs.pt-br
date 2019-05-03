---
title: Gerenciamento de consortium Blockchain Service do Azure usando o PowerShell
description: Como gerenciar o serviço do Azure Blockchain consortium membros usando o PowerShell
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: bef0c5d776e8ab6424b8604a49782088c45b0538
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028224"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Gerenciar membros consortium no serviço de Blockchain do Azure usando o PowerShell

Você pode usar o PowerShell para gerenciar membros do blockchain consortium para o serviço de Blockchain do Azure. Os membros com um privilégio de administrador podem convidar, adicionar, remover e alterações de funções para todos os participantes de consórcio blockchain. Membros com privilégio de usuário podem exibir todos os participantes de consórcio blockchain e podem alterar seu nome de exibição de membro.

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um membro de blockchain usando o portal do Azure](create-member.md)
* Para obter mais informações sobre consórcios, membros e nós, consulte [consortium de serviço do Azure Blockchain](consortium.md)

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

## <a name="install-powershell-module"></a>Instalar o módulo do PowerShell

Instale o pacote de Microsoft.AzureBlockchainService.ConsortiumManagement.PS da Galeria do PowerShell.

```powershell
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="establish-a-web3-connection"></a>Estabelecer uma conexão Web3

Para gerenciar membros consortium, é necessário estabelecer uma conexão Web3 para seu ponto de extremidade do serviço do Azure Blockchain membro. Você pode usar esse script para definir variáveis globais que podem ser usadas ao chamar os cmdlets de gerenciamento consortium.

```powershell
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Substitua \<senha da conta do membro\> com a senha da conta membro usou quando criou o membro.

Localize os outros valores no portal do Azure:

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Navegue até seu membro de serviço do Azure Blockchain padrão **visão geral** página.

    ![Visão geral de membros](./media/manage-consortium-powershell/member-overview.png)

    Substitua \<conta do membro\>, e \<RootContract endereço\> com os valores do portal.

1. Para o endereço do ponto de extremidade, selecione **nós de transação** e selecione um nó de transação.
1. Selecione **cadeias de caracteres de Conexão**.

    ![Cadeias de conexão](./media/manage-consortium-powershell/connection-strings.png)

    Substitua \<endereço do ponto de extremidade\> com o valor de **HTTPS (chave de acesso 1)** ou **HTTPS (chave de acesso 2)**.

## <a name="network-and-smart-contract-management"></a>Gerenciamento de contratos inteligentes e de rede

Use os cmdlets de contrato inteligente e de rede para estabelecer uma conexão ao blockchain ponto de extremidade contratos inteligentes responsáveis pelo gerenciamento consortium.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Conecta-se para os consortium gerenciamento contratos inteligentes, que são usados para gerenciar e aplicar os membros dentro do consortium.

```powershell
Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>
```

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| RootContractAddress | O endereço do contrato raiz dos contratos inteligentes de gerenciamento consortium | Sim |
| Web3Client | Objeto Web3Client obtido de New-Web3Connection | Sim |

**Exemplo**

```powershell
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Importação Web3Account

Use este cmdlet para criar um objeto para manter a conta de gerenciamento de nó remoto de informações.

```powershell
Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>
```

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Endereço de conta de membro do Blockchain | Sim |
| ManagedAccountPassword | Senha da conta de endereço | Sim |

**Exemplo**

```powershell
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Estabelece uma conexão para o ponto de extremidade RPC de um nó de transação.

```powershell
New-Web3Connection [-RemoteRPCEndpoint <String>]
```

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Endereço de ponto de extremidade de membro de Blockchain | Sim |


**Exemplo**

```powershell
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="consortium-member-management"></a>Gerenciamento de membros Consortium

Use cmdlets de gerenciamento membro consortium para gerenciar membros dentro do consortium. Ações disponíveis dependem da sua função consortium.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Obtém detalhes de membro ou membros da lista do consórcio.

```powershell
Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>
```

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| NOME | O nome do membro de serviço de Blockchain do Azure que você deseja recuperar os detalhes. Se você fornecer um nome de membro, detalhes do membro são retornados. Se o nome for omitido, uma lista de todos os membros de consortium será retornada. | Não  |
| Membros | Objeto de membros obtido de importação ConsortiumManagementContracts | Sim |
| Web3Client | Objeto Web3Client obtido de New-Web3Connection | Sim |

**Exemplo**

```powershell
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

**Saída de exemplo**

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Remove um membro de blockchain.

```powershell
Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| NOME | Nome do membro a ser removido | Sim |
| Membros | Objeto de membros obtido de importação ConsortiumManagementContracts | Sim |
| Web3Account | Objeto Web3Account obtido de importação Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido de New-Web3Connection | Sim |

**Exemplo**

```powershell
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Define os atributos de membro, incluindo função consortium e de nome de exibição de blockchain.

Os administradores do consórcio podem definir **DisplayName** e **função** para todos os membros. Membro de consórcio com a função de usuário só pode alterar o nome de exibição do seu próprio membro.

```powershell
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| NOME | Nome do membro blockchain | Sim |
| DisplayName | Novo nome de exibição | Não  |
| AccountAddress | Endereço da conta | Não  |
| Membros | Objeto de membros obtido de importação ConsortiumManagementContracts | Sim |
| Web3Account | Objeto Web3Account obtido de importação Web3Account | Sim |
| Web3Client |  Objeto Web3Client obtido de New-Web3Connection| Sim |

**Exemplo**

```powershell
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="consortium-member-invitation-management"></a>Gerenciamento de convite de membros Consortium

Use cmdlets de gerenciamento de convite de membro consortium para gerenciar convites de membro consortium. Ações disponíveis dependem da sua função consortium.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Convide novos membros para o consortium.

```powershell
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | ID de assinatura do Azure de membros convidados | Sim |
| Função | Função Consortium. Valores podem ser administrador ou usuário. ADMINISTRADOR é a função de administrador consortium. USUÁRIO é a função de membro consortium. | Sim |
| Membros | Objeto de membros obtido de importação ConsortiumManagementContracts | Sim |
| Web3Account | Objeto Web3Account obtido de importação Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido de New-Web3Connection | Sim |

**Exemplo**

```powershell
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Recupera ou listar o status do convite membro consortium.

```powershell
Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>
```

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | ID de assinatura do Azure de membros convidados. Se a SubscriptionID for fornecido, os detalhes do convite a ID da assinatura serão retornados. Se for omitido SubscriptionID, uma lista de todos os convites de membro são retornados. | Não  |
| Membros | Objeto de membros obtido de importação ConsortiumManagementContracts | Sim |
| Web3Client | Objeto Web3Client obtido de New-Web3Connection | Sim |

**Exemplo**

```powershell
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

**Saída de exemplo**

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Revoga um convite de membro consortium.

```powershell
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | ID de assinatura do Azure do membro revogar | Sim |
| Membros | Objeto de membros obtido de importação ConsortiumManagementContracts | Sim |
| Web3Account | Objeto Web3Account obtido de importação Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido de New-Web3Connection | Sim |

**Exemplo**

```powershell
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Define o **função** para um convite existente. Somente os administradores de consortium podem alterar os convites.

```powershell
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | ID de assinatura do Azure de membros convidados | Sim |
| Função | Nova função de consortium para o convite. Os valores podem ser **usuário** ou **ADMIN** | Sim |
| Membros |  Objeto de membros obtido de importação ConsortiumManagementContracts | Sim |
| Web3Account | Objeto Web3Account obtido de importação Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido de New-Web3Connection | Sim |

**Exemplo**

```powershell
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre consórcios, membros e nós, consulte:

> [!div class="nextstepaction"]
> [Azure consortium de Blockchain Service](consortium.md)