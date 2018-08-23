---
title: Início Rápido - Implantar Olá, Mundo na malha do Service Fabric | Microsoft Docs
description: Este início rápido mostra como implantar um aplicativo da Malha do Service Fabric na Malha do Azure Service Fabric.
services: service-fabric-mesh
keywords: Não adicione ou edite palavras-chave sem consultar seu especialista em SEO.
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: quickstart
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: a740672ea948bd86efce92c534e0f95f65563438
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2018
ms.locfileid: "42023717"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>Início Rápido: Implantar Olá, Mundo na malha do Service Fabric

A [Malha do Service Fabric](service-fabric-mesh-overview.md) torna mais fácil criar e gerenciar aplicativos de microsserviços no Azure, sem a necessidade de provisionar máquinas virtuais. Neste início rápido, você criará um aplicativo Olá, Mundo no Azure e o exporá à internet. Essa operação é concluída com um único comando. Em apenas alguns minutos, você verá este modo de exibição em seu navegador:

![Aplicativo Olá, Mundo no navegador][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Se você ainda não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="set-up-service-fabric-mesh-cli"></a>Configurar CLI da Malha do Service Fabric 
Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir esse guia de início rápido. Instale o módulo de extensão de CLI da Malha do Azure Service Fabric seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no Azure e defina sua assinatura.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Criar grupo de recursos
Crie um grupo de recursos para implantar o aplicativo. Você pode usar um grupo de recursos existente e ignorar esta etapa. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Implantar o aplicativo
Criar seu aplicativo no grupo de recursos usando o comando `az mesh deployment create`:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}" 
```
O comando anterior implanta um aplicativo Linux usando o [modelo mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.linux.json). Se você desejar implantar um aplicativo do Windows, use o [modelo mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.windows.json). Imagens de contêiner do Windows são maiores do que as imagens de contêiner do Linux e podem levar mais tempo para implantar.

Em alguns minutos, o comando retorna:

`helloWorldApp has been deployed successfully on helloWorldNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Abrir o aplicativo
Depois que o aplicativo é implantado com êxito, copie o endereço IP público para o ponto de extremidade de serviço da saída da CLI. Abra o endereço IP em um navegador da Web. Uma página da Web com o logotipo da Malha do Azure Service Fabric é exibida.

## <a name="check-the-application-details"></a>Verifique os detalhes do aplicativo
Você pode verificar o status do aplicativo usando o comando `az mesh app show`. Esse comando fornece informações úteis que você pode acompanhar.

O nome do aplicativo para este início rápido é `helloWorldApp`, para coletar os detalhes sobre o aplicativo execute o seguinte comando:

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>Ver os logs de aplicativo

Examine os logs para o aplicativo implantado usando o comando `az mesh code-package-log get`:

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando você estiver pronto para excluir o aplicativo, execute o comando [az group delete][az-group-delete] para remover o grupo de recursos e os recursos de rede e aplicativos que ele contém.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como criar e implantar aplicativos da Malha do Service Fabric, continue no tutorial.
> [!div class="nextstepaction"]
> [Criar e implantar um aplicativo Web com vários serviços](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
