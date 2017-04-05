---
title: "Amostra de script da CLI do Azure – Criar uma conta do Lote | Microsoft Docs"
description: "Amostra de script da CLI do Azure – Criar uma conta do Lote"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 4d37dfc6c7110aa32788bbd3c3f81cd84a1426d2
ms.lasthandoff: 03/24/2017

---

# <a name="create-a-batch-account-with-the-azure-cli"></a>Criar uma conta do Lote com a CLI do Azure

Esse script cria uma conta do Lote do Azure e mostra como várias propriedades da conta podem ser consultadas e atualizadas.

Se necessário, instale a CLI do Azure usando as instruções encontradas no [guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e, em seguida, execute `az login` para fazer logon no Azure.

## <a name="batch-account-sample-script"></a>Script de exemplo de conta do Lote

Quando você cria uma conta do Lote, os nós de computação dessa conta são atribuídos internamente, por padrão, pelo serviço de Lote. Nós de computação alocados estarão sujeitos a uma cota de núcleo separada e a conta poderá ser autenticada por meio de credenciais de Chave Compartilhada ou um token do Azure Active Directory.

[!code-azurecli[principal](../../../cli_scripts/batch/create-account/create-account.sh "Criar Conta")]

## <a name="batch-account-using-user-subscription-sample-script"></a>Conta do Lote usando o script de exemplo de assinatura de usuário

Você também pode optar por fazer com que o Lote crie seus nós de computação na sua própria assinatura do Azure.
Contas que alocam nós de computação em sua assinatura devem ser autenticadas por meio de um token do Azure Active Directory e os nós de computação distribuídos contarão para a sua cota de assinatura. Para criar uma conta nesse modo, uma pessoa deve especificar uma referência do Key Vault ao criar a conta.

[!code-azurecli[principal](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh  "Criar Conta Usando a Assinatura do Usuário")]

## <a name="clean-up-deployment"></a>Limpar implantação

Depois de executar qualquer um dos scripts de exemplo acima, execute o comando a seguir para remover o grupo de recursos e todos os recursos relacionados (incluindo contas do Lote, contas de Armazenamento do Azure e cofres de chaves do Azure).

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos para criar um grupo de recursos, uma conta do Lote e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az batch account create](https://docs.microsoft.com/cli/azure/batch/account#create) | Cria a conta do Lote.  |
| [az batch account set](https://docs.microsoft.com/cli/azure/batch/account#set) | Atualiza as propriedades da conta do Lote.  |
| [az batch account show](https://docs.microsoft.com/cli/azure/batch/account#show) | Recupera os detalhes da conta do Lote especificada.  |
| [az batch account keys list](https://docs.microsoft.com/cli/azure/batch/account/keys#list) | Recupera as chaves de acesso da conta do Lote especificada.  |
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | Autentica na conta do Lote especificada para interação adicional com a CLI.  |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Cria uma conta de armazenamento. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#create) | Cria um cofre de chave. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#set-policy) | Atualize a política de segurança do cofre de chaves especificado. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

As amostras de script da CLI do Lote adicionais podem ser encontrados na [documentação da CLI do Lote do Azure](../batch-cli-samples.md).

