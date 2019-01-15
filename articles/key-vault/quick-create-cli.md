---
title: Início Rápido do Azure – Definir e recuperar um segredo do Key Vault usando a CLI do Azure | Microsoft Docs
description: Início Rápido que mostra como definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 4acc894f-fee0-4c2f-988e-bc0eceea5eda
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/08/2019
ms.author: barclayn
ms.openlocfilehash: 9b57dfcd6ecd00f9f1bb8ec752e0996ee52305db
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159036"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure

O Azure Key Vault é um serviço de nuvem que funciona como um repositório de segredos seguro. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Para saber mais sobre o Key Vault, reveja a [Visão geral](key-vault-overview.md). A CLI do Azure é usada para criar e gerenciar recursos do Azure usando comandos ou scripts. Neste guia de início rápido, você criará um cofre de chaves. Depois de concluído, você armazenará um segredo.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

Para entrar no Azure usando a CLI, você pode digitar:

```azurecli
az login
```

Para saber mais sobre as opções de logon por meio da CLI, veja [Entrar com a CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Em seguida, você criará um Key Vault no grupo de recursos criado na etapa anterior. Você precisará fornecer algumas informações:

- Neste início rápido, usamos **Contoso-vault2**. Você deve fornecer um nome exclusivo para o teste.
- Nome do grupo de recursos **ContosoResourceGroup**.
- O local **Leste dos EUA**.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

A saída desse cmdlet mostra as propriedades do cofre de chaves criado recentemente. Anote as duas propriedades listadas abaixo:

- **Nome do cofre**: no exemplo, é **Contoso-Vault2**. Você usará esse nome para outros comandos do Key Vault.
- **URI do cofre**: no exemplo, é https://contoso-vault2.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Nesse ponto, sua conta do Azure é a única autorizada a executar qualquer operação nesse novo cofre.

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, basta executar algumas etapas adicionais. Essa senha pode ser usada por um aplicativo. A senha será chamada **ExamplePassword** e armazenará o valor **Pa$$w0rd** nele.

Digite os comandos a seguir para criar um segredo no Key Vault chamado **ExamplePassword** que armazenará o valor **Pa$$w0rd**:

```azurecli
az keyvault secret set --vault-name "Contoso-Vault2" --name "ExamplePassword" --value "Pa$$w0rd"
```

Agora, você pode fazer referência a essa senha que foi adicionada ao Azure Key Vault usando seu URI. Use **https://ContosoVault.vault.azure.net/secrets/ExamplePassword**  para obter a versão atual. 

Para exibir o valor contido no segredo como texto sem formatação:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "Contoso-Vault2"
```

Agora, você criou um Key Vault, armazenou um segredo e o recuperou.

## <a name="clean-up-resources"></a>Limpar recursos

Outros guias de início rápido e tutoriais da coleção aproveitam esse guia de início rápido. Se você planeja continuar a trabalhar com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.
Quando não forem mais necessários, você poderá usar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos e todos os recursos relacionados. Você pode excluir os recursos da seguinte maneira:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault e armazenou um segredo nele. Para saber mais sobre o Key Vault e como você pode usá-lo com seus aplicativos, prossiga para o tutorial de aplicativos Web que trabalham com o Key Vault.

> [!div class="nextstepaction"]
> Para saber como ler um segredo do Key Vault com um aplicativo Web usando identidades gerenciadas para recursos do Azure, continue com o tutorial a seguir, [Configurar um aplicativo Web do Azure para ler um segredo do Key Vault](quick-create-net.md)
