---
title: Criar um cluster do Service Fabric no Azure | Microsoft Docs
description: Saiba como criar um cluster do Linux no Azure usando um modelo.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/16/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: aabf3a0767b2f6f96e357dc63f3e8ee737577a1f
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="deploy-a-secure-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Implantar um cluster seguro do Linux do Service Fabric em uma rede virtual do Azure
Este tutorial é a primeira parte de uma série. Você aprenderá como criar um cluster do Service Fabric (Linux) em execução no Azure e como implantá-lo em uma VNET (rede virtual) e sub-rede existentes. Ao terminar, você terá um cluster em execução na nuvem no qual você poderá implantar aplicativos. Para criar um cluster do Windows, consulte [Create a secure Windows cluster on Azure using a template](service-fabric-tutorial-create-vnet-and-windows-cluster.md) (Criar um cluster seguro do Windows no Azure usando um modelo).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma VNET no Azure usando um modelo
> * Criar um cluster seguro do Service Fabric no Azure usando um modelo
> * Proteger o cluster com um certificado X.509
> * Conectar-se ao cluster usando a CLI do Service Fabric
> * Remover um cluster

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Criar um cluster seguro no Azure usando um modelo
> * [Implantar o Gerenciamento de API com o Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instalar a [CLI do Service Fabric](service-fabric-cli.md)
- Instalar a [CLI 2.0 do Azure](/cli/azure/install-azure-cli)

Os procedimentos a seguir criam um cluster de cinco nós do Service Fabric. O cluster é protegido por um certificado autoassinado colocado em um cofre de chaves. 

Para calcular o custo incorrido ao executar um cluster do Service Fabric no Azure, use a [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/).
Para obter mais informações sobre a criação de clusters do Service Fabric, confira [Criar um cluster do Service Fabric usando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Entre no Azure e selecione sua assinatura
Este guia usa o a CLI do Azure. Ao iniciar uma nova sessão, entre em sua conta do Azure e selecione sua assinatura antes de executar comandos do Azure.
 
Execute o seguinte script para entrar em sua conta do Azure e selecione sua assinatura:

```azurecli
az login
az account set --subscription <guid>
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um novo grupo de recursos para sua implantação e dê um nome e um local a ela.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
az group create --name $ResourceGroupName --location $Location
```

## <a name="deploy-the-network-topology"></a>Implante a topologia da rede
Em seguida, configure a topologia de rede para a qual o Gerenciamento de API e o cluster do Service Fabric serão implantados. O modelo do Resource Manager [network.json][network-arm] foi configurado para criar uma VNET (rede virtual) e também um NSG (grupo de segurança de rede) e uma sub-rede para o Service Fabric e um NSG e uma sub-rede para o Gerenciamento de API. Saiba mais sobre VNETs, sub-redes e NSGs [aqui](../virtual-network/virtual-networks-overview.md).

O arquivo de parâmetros [network.parameters.json][network-parameters-arm] contém os nomes das sub-redes e NSGs em que o Service Fabric e o Gerenciamento de API são implantados.  O Gerenciamento de API é implantado no [seguinte tutorial](service-fabric-tutorial-deploy-api-management.md). Para este guia, os valores de parâmetros não precisam ser alterados. Os modelos do Resource Manager do Service Fabric usam esses valores.  Se os valores forem modificados aqui, será necessário modificá-los em outros modelos do Resource Manager usados neste tutorial e em [Implantar tutorial de Gerenciamento de API](service-fabric-tutorial-deploy-api-management.md). 

Baixe o seguinte modelo do Resource Manager e arquivo de parâmetros:
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

Use o seguinte script para implantar o modelo do Resource Manager e os arquivos de parâmetros para a configuração de rede:

```azurecli
az group deployment create \
    --name VnetDeployment \
    --resource-group $ResourceGroupName \
    --template-file network.json \
    --parameters @network.parameters.json
```
<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Implantar o cluster do Service Fabric
Uma vez que os recursos de rede concluíram a implantação, a próxima etapa é implantar um cluster do Service Fabric na VNET na sub-rede e o NSG designado para o cluster do Service Fabric. Para esta série de tutoriais, o modelo do Resource Manager do Service Fabric é pré-configurado para usar os nomes da VNET, da sub-rede e do NSG que você configurou em uma etapa anterior.

Implantar um cluster em uma VNET e em uma sub-rede existentes (implantada anteriormente neste artigo) requer um modelo do Resource Manager.  Baixe o seguinte modelo do Resource Manager e arquivo de parâmetros:
- [linuxcluster.json][cluster-arm]
- [linuxcluster.parameters.json][cluster-parameters-arm]

Preencha os parâmetros **clusterName**, **adminUserName** e **adminPassword** vazios no arquivo `linuxcluster.parameters.json` para sua implantação.  Deixe os parâmetros **certificateThumbprint**, **certificateUrlValue** e **sourceVaultValue** em branco se você deseja criar um certificado autoassinado.  Se você tiver um certificado existente carregado para um keyvault, preencha esses valores de parâmetro.

Use o script a seguir para implantar o cluster usando o modelo do Resource Manager e os arquivos de parâmetro.  Um certificado autoassinado é criado no cofre de chaves especificado e usado para proteger o cluster.  O certificado também é baixado localmente.

```azurecli
Password="q6D7nN%6ck@6"
Subject="aztestcluster.southcentralus.cloudapp.azure.com"
VaultName="linuxclusterkeyvault"
az group create --name $ResourceGroupName --location $Location

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file portalcluster.json --parameter-file portalcluster.parameters.json

```

## <a name="connect-to-the-secure-cluster"></a>Conectar-se ao cluster seguro
Conecte-se ao cluster usando o comando `sfctl cluster select` da CLI do Service Fabric por meio da sua chave.  Observação: use apenas a opção **--no-verify** para um certificado autoassinado.

```azurecli
sfctl cluster select --endpoint https://mysfcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Verifique se você está conectado e se o cluster está íntegro usando o comando `sfctl cluster health`.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Limpar recursos

Um cluster é composto por outros recursos do Azure, além do próprio recurso do cluster. A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos.

Faça logon no Azure e selecione a ID de assinatura com a qual você deseja remover o cluster.  Você pode encontrar sua ID de assinatura fazendo logon no [Portal do Azure](http://portal.azure.com). Exclua o grupo de recursos e todos os recursos do cluster usando o comando [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete).

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma VNET no Azure usando um modelo
> * Criar um cluster seguro do Service Fabric no Azure usando um modelo
> * Proteger o cluster com um certificado X.509
> * Conectar-se ao cluster usando a CLI do Service Fabric
> * Remover um cluster

Em seguida, avance para o próximo tutorial para saber como implantar um aplicativo existente.
> [!div class="nextstepaction"]
> [Implantar o Gerenciamento de API](service-fabric-tutorial-deploy-api-management.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.parameters.json

