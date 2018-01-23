---
title: "Implantar do Registro de Contêiner do Azure em Instâncias de Contêiner do Azure"
description: "Implantar Instâncias de Contêiner do Azure do 	Registro de Contêiner do Azure"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 4205b47dc67920021812c1e573a98de64ad198ec
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Implantar do Registro de Contêiner do Azure em Instâncias de Contêiner do Azure

O Registro de Contêiner do Azure é um registro privado baseado no Azure para imagens de contêiner do Docker. Este artigo aborda como implantar imagens de contêiner armazenadas no Registro de Contêiner do Azure em Instâncias de Contêiner do Azure.

## <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

A CLI do Azure inclui comandos para criar e gerenciar contêineres nas Instâncias de Contêiner do Azure. Se especificar uma imagem privada no comando [az container create][az-container-create], você também poderá especificar a senha do registro da imagem, necessária para autenticar com o registro de contêiner.

```azurecli-interactive
az container create --resource-group myResourceGroup --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword
```

O comando [az container create][az-container-create] também é compatível com a especificação de `--registry-login-server` e `--registry-username`. No entanto, o servidor de logon do Registro de Contêiner do Azure é sempre *registryname*.azurecr.io e o nome de usuário é *registryname*, portanto, esses valores são inferidos do nome da imagem, caso não sejam explicitamente fornecidos.

## <a name="deploy-with-azure-resource-manager-template"></a>Implantar com o modelo do Azure Resource Manager

Você pode especificar as propriedades do Registro de Contêiner do Azure em um modelo do Azure Resource Manager incluindo a propriedade `imageRegistryCredentials` na definição de grupos do contêiner:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Para evitar o armazenamento da senha do registro de contêiner diretamente no modelo, é recomendável que você armazene-a como um segredo no [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) e referencie-a no modelo, usando a [integração nativa entre o Azure Resource Manager e o Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Implantar com o portal do Azure

Se você mantiver as imagens de contêiner no Registro de Contêiner do Azure, você poderá facilmente criar um contêiner em Instâncias de Contêiner do Azure usando o Portal do Azure.

1. No Portal do Azure, navegue até o registro de contêiner.

2. Selecione **Repositórios** e, em seguida, selecione o repositório do qual deseja implantar, clique com o botão direito do mouse na marca da imagem de contêiner que deseja implantar e selecione **Executar instância**.

    !["Executar instância" no Registro de Contêiner do Azure no portal do Azure][acr-runinstance-contextmenu]

3. Insira um nome para o contêiner e um nome para o grupo de recursos. Você também poderá alterar os valores padrão se desejar.

    ![Criar menu para Instâncias de Contêiner do Azure][acr-create-deeplink]

4. Quando a implantação for concluída, você poderá navegar para o grupo de contêineres do painel de notificações para localizar o endereço IP e outras propriedades do contêiner.

    ![Exibição de detalhes de grupo de contêineres das Instâncias de Contêiner do Azure][aci-detailsview]

## <a name="next-steps"></a>Próximas etapas

Saiba como criar contêineres, enviá-los por push a um registro de contêiner privado e implantá-los em Instâncias de Contêiner do Azure ao [concluir o tutorial](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create