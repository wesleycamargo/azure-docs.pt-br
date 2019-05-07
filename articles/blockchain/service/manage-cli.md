---
title: Gerenciar o serviço de Blockchain do Azure usando a CLI do Azure
description: Como criar e gerenciar o serviço de Blockchain do Azure com CLI do Azure
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: d078ca181b2eed4b80d4f12f1c03b42f4e242194
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154450"
---
# <a name="manage-azure-blockchain-service-with-azure-cli"></a>Gerenciar o serviço de Blockchain do Azure com a CLI do Azure

Além do portal do Azure, você pode usar a CLI do Azure para criar rapidamente e gerenciar os membros do blockchain e nós de transação para o serviço de Blockchain do Azure.

Certifique-se de que você tenha instalado a versão mais recente [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e entrou em uma conta do Azure com `az login`.

Nos exemplos a seguir, substitua o exemplo `<parameter names>` com seus próprios valores.

## <a name="create-blockchain-member"></a>Criar um membro de blockchain

Exemplo cria um membro de blockchain no serviço de Blockchain do Azure que executa o protocolo de razão de Quorum em um consórcio de novo.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| Parâmetro | DESCRIÇÃO |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde os recursos do serviço de Blockchain do Azure são criados. |
| **name** | Um nome exclusivo que identifica o membro de blockchain do serviço de Blockchain do Azure. O nome é usado para o endereço do ponto de extremidade público. Por exemplo, `myblockchainmember.blockchain.azure.com`. |
| **local** | Região do Azure em que o membro de blockchain é criado. Por exemplo, `eastus`. Escolha o local mais próximo para os usuários ou para outros aplicativos do Azure. |
| **password** | A senha da conta de membro. A senha da conta de membro é usada para autenticar no ponto de extremidade público do blockchain membro usando a autenticação básica. A senha deve atender três dos quatro requisitos a seguir: comprimento precisa ter entre 12 e 72 caracteres, 1 caractere minúsculo, 1 caractere maiusculo, 1 número e 1 caractere especial que é o número não sign(#), percent(%), vírgula (,), star(*), aspa (\`), clique duas vezes quote("), quote(') único, dash(-) e semicolumn(;)|
| **protocol** | Visualização pública dá suporte ao Quorum. |
| **consortium** | Nome do consórcio para criar ou ingressar. |
| **consortiumManagementAccountPassword** | A senha de gerenciamento consortium. A senha é usada para ingressar em um consórcio. |
| **ruleName** | Nome da regra de lista de permissões de um intervalo de endereços IP. Parâmetro opcional para as regras de firewall.|
| **startIpAddress** | Início do intervalo de endereços IP para a lista de permissões. Parâmetro opcional para as regras de firewall. |
| **endIpAddress** | Fim do intervalo de endereços IP para a lista de permissões. Parâmetro opcional para as regras de firewall. |
| **skuName** | Tipo de camada. Use S0 para Standard e B0 para Basic. |

## <a name="change-blockchain-member-password"></a>Alteração de senha de membro de blockchain

Exemplo altera a senha de um membro do blockchain.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| Parâmetro | DESCRIÇÃO |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde os recursos do serviço de Blockchain do Azure são criados. |
| **name** | Nome que identifica o membro de serviço de Blockchain do Azure. |
| **password** | A senha da conta de membro. A senha deve atender três dos quatro requisitos a seguir: comprimento precisa ter entre 12 e 72 caracteres, 1 caractere minúsculo, 1 caractere maiusculo, 1 número e 1 caractere especial que é o número não sign(#), percent(%), vírgula (,), star(*), aspa (\`), clique duas vezes quote("), quote(') único, dash(-) e semicolon(;). |


## <a name="create-transaction-node"></a>Criar nó de transação

Crie um nó de transação dentro de um membro existente de blockchain. Adicionando nós de transação, você pode aumentar o isolamento de segurança e distribuir a carga. Por exemplo, você poderia ter um ponto de extremidade do nó de transação para aplicativos cliente diferentes.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| Parâmetro | DESCRIÇÃO |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde os recursos do serviço de Blockchain do Azure são criados. |
| **name** | Nome do membro de blockchain do serviço de Blockchain do Azure que também inclui o novo nome de nó da transação. |
| **local** | Região do Azure em que o membro de blockchain é criado. Por exemplo, `eastus`. Escolha o local mais próximo para os usuários ou para outros aplicativos do Azure. |
| **password** | A senha do nó de transação. A senha deve atender três dos quatro requisitos a seguir: comprimento precisa ter entre 12 e 72 caracteres, 1 caractere minúsculo, 1 caractere maiusculo, 1 número e 1 caractere especial que é o número não sign(#), percent(%), vírgula (,), star(*), aspa (\`), clique duas vezes quote("), quote(') único, dash(-) e semicolon(;). |
| **ruleName** | Nome da regra de lista de permissões de um intervalo de endereços IP. Parâmetro opcional para as regras de firewall. |
| **startIpAddress** | Início do intervalo de endereços IP para a lista de permissões. Parâmetro opcional para as regras de firewall. |
| **endIpAddress** | Fim do intervalo de endereços IP para a lista de permissões. Parâmetro opcional para as regras de firewall.|

## <a name="change-transaction-node-password"></a>Alterar senha do nó de transação

Exemplo altera uma senha de nó da transação.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| Parâmetro | DESCRIÇÃO |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos em que existem recursos de serviço de Blockchain do Azure. |
| **name** | Nome do membro de blockchain do serviço de Blockchain do Azure que também inclui o novo nome de nó da transação. |
| **password** | A senha do nó de transação. A senha deve atender três dos quatro requisitos a seguir: comprimento precisa ter entre 12 e 72 caracteres, 1 caractere minúsculo, 1 caractere maiusculo, 1 número e 1 caractere especial que é o número não sign(#), percent(%), vírgula (,), star(*), aspa (\`), clique duas vezes quote("), quote(') único, dash(-) e semicolon(;). |

## <a name="change-consortium-management-account-password"></a>Alterar senha de conta de gerenciamento de consortium

A conta de gerenciamento consortium é usada para gerenciamento de associação consortium. Cada membro é identificado exclusivamente por uma conta de gerenciamento consortium e você pode alterar a senha dessa conta com o comando a seguir.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| Parâmetro | DESCRIÇÃO |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde os recursos do serviço de Blockchain do Azure são criados. |
| **name** | Nome que identifica o membro de serviço de Blockchain do Azure. |
| **consortiumManagementAccountPassword** | A senha de conta de gerenciamento consortium. A senha deve atender três dos quatro requisitos a seguir: comprimento precisa ter entre 12 e 72 caracteres, 1 caractere minúsculo, 1 caractere maiusculo, 1 número e 1 caractere especial que é o número não sign(#), percent(%), vírgula (,), star(*), aspa (\`), clique duas vezes quote("), quote(') único, dash(-) e semicolon(;). |
  
## <a name="update-firewall-rules"></a>Atualizar regras de firewall

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| Parâmetro | DESCRIÇÃO |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos em que existem recursos de serviço de Blockchain do Azure. |
| **name** | Nome do membro de blockchain do serviço de Blockchain do Azure. |
| **ruleName** | Nome da regra de lista de permissões de um intervalo de endereços IP. Parâmetro opcional para as regras de firewall.|
| **startIpAddress** | Início do intervalo de endereços IP para a lista de permissões. Parâmetro opcional para as regras de firewall.|
| **endIpAddress** | Fim do intervalo de endereços IP para a lista de permissões. Parâmetro opcional para as regras de firewall.|

## <a name="list-api-keys"></a>Chaves de API de lista

Chaves de API podem ser usadas para acesso ao nó semelhante ao nome de usuário e senha. Há duas chaves de API para dar suporte a rotação de chaves. Use o seguinte comando para listar as chaves de API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | DESCRIÇÃO |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos em que existem recursos de serviço de Blockchain do Azure. |
| **name** | Nome do membro de blockchain do serviço de Blockchain do Azure que também inclui o novo nome de nó da transação. |

## <a name="regenerate-api-keys"></a>Regenerar chaves de API

Use o comando a seguir para regenerar as chaves de API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| Parâmetro | DESCRIÇÃO |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos em que existem recursos de serviço de Blockchain do Azure. |
| **name** | Nome do membro de blockchain do serviço de Blockchain do Azure que também inclui o novo nome de nó da transação. |
| **keyName** | Substitua \<keyValue\> com key1 ou key2. |

## <a name="delete-a-transaction-node"></a>Excluir um nó de transação

Exemplo exclui um nó de transação do membro de blockchain.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | DESCRIÇÃO |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos em que existem recursos de serviço de Blockchain do Azure. |
| **name** | Nome do membro de blockchain do serviço de Blockchain do Azure que também inclui o novo nome de nó de transação a ser excluído. |

## <a name="delete-a-blockchain-member"></a>Excluir um membro de blockchain

Exemplo exclui um membro de blockchain.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | DESCRIÇÃO |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos em que existem recursos de serviço de Blockchain do Azure. |
| **name** | Nome do serviço de Blockchain do Azure blockchain membro a ser excluído. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Conceder acesso de usuário do Azure AD

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parâmetro | DESCRIÇÃO |
|---------|-------------|
| **role** | Nome da função do Azure AD. |
| **assignee** | ID de usuário do Azure AD. Por exemplo, `user@contoso.com` |
| **scope** | Escopo da atribuição de função. Pode ser um membro de blockchain do nó de transação. |

**Exemplo:**

Conceder acesso de nó para o usuário do Azure AD para blockchain **membro**:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Exemplo:**

Conceder acesso de nó para o usuário do Azure AD para blockchain **nó de transação**:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Conceder acesso de nó para o grupo do Azure AD ou o aplicativo de função

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| Parâmetro | DESCRIÇÃO |
|---------|-------------|
| **role** | Nome da função do Azure AD. |
| **assignee-object-id** | A ID de grupo do Azure AD ou aplicativo ID. |
| **scope** | Escopo da atribuição de função. Pode ser um membro de blockchain do nó de transação. |

**Exemplo:**

Conceder acesso de nó para **função de aplicativo**

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Remover o acesso do nó do Azure AD

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parâmetro | DESCRIÇÃO |
|---------|-------------|
| **role** | Nome da função do Azure AD. |
| **assignee** | ID de usuário do Azure AD. Por exemplo, `user@contoso.com` |
| **scope** | Escopo da atribuição de função. Pode ser um membro de blockchain do nó de transação. |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar nós de transação de serviço do Azure Blockchain com o portal do Azure](configure-transaction-nodes.md)
