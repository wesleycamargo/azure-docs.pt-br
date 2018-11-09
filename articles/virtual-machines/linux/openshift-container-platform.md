---
title: Implantar o OpenShift Container Platform no Azure | Microsoft Docs
description: Implante o OpenShift Container Platform no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 21eebb6c27a83b939f321d38026da7d4c39b7071
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085824"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Implantar o OpenShift Container Platform no Azure

Você pode usar um dos vários métodos para implantar a plataforma de contêiner OpenShift no Azure:

- Você pode implantar manualmente todos os componentes de infraestrutura necessários do Azure e, em seguida, seguir a [documentação](https://docs.openshift.com/container-platform) do OpenShift Container Platform.
- Você também pode usar um [modelo do Gerenciador de Recursos](https://github.com/Microsoft/openshift-container-platform/) existente que simplifica a implantação do cluster do OpenShift Container Platform.
- Outra opção é usar a [Oferta do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Para ambas as opções, é necessária uma assinatura do Red Hat. Durante a implantação, a instância Red Hat Enterprise Linux é registrada na Assinatura do Red Hat e anexada à ID do Pool que contém os direitos para o OpenShift Container Platform.
Certifique-se de que você tenha uma senha, uma ID do Pool e um Nome de usuário de gerente de Assinatura do Red Hat (RHSM) válidos. Você pode usar uma Chave de Ativação, ID da Organização e ID do Pool. Você pode verificar essas informações entrando em https://access.redhat.com.

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Implantar usando o modelo do Resource Manager de OpenShift Container Platform

Para implantar usando o modelo do Gerenciador de Recursos, um arquivo de parâmetros é usado para fornecer todos os parâmetros de entrada. Para personalizar ainda mais a implantação, crie um fork do repositório do GitHub e altere os itens apropriados.

Algumas opções de personalização comuns incluem, mas não se limitam a:

- Tamanho de VM de bastião (variável em azuredeploy.json)
- Convenções de nomenclatura (variáveis em azuredeploy.json)
- Especificações de cluster do OpenShift – modificadas por meio do arquivo de hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Configurar o arquivo de parâmetros

O [modelo do OpenShift Container Platform](https://github.com/Microsoft/openshift-container-platform) tem várias ramificações disponíveis para diferentes versões do OpenShift Container Platform.  Com base em suas necessidades, você pode implantar diretamente do repositório ou você pode criar fork do repositório e fazer alterações personalizadas para os modelos ou scripts antes de implantar.

Use o valor `appId` da entidade de serviço que você criou anteriormente para o parâmetro `aadClientId`.

O exemplo a seguir mostra um arquivo de parâmetros chamado azuredeploy.parameters.json com todas as entradas necessárias.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
        },
        "rhsmBrokerPoolId": {
            "value": "{Pool ID}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "routing.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "openshiftvnet"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "mastersubnet"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "infrasubnet"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "nodesubnet"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/masterinfrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.201"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        },
        "proxySettings": {
            "value": "none"
        },
        "httpProxyEntry": {
            "value": "none"
        },
        "httpsProxyEntry": {
            "value": "none"
        },
        "noProxyEntry": {
            "value": "none"
        }
    }
}
```

Substitua os parâmetros com as informações específicas.

Versões diferentes podem ter parâmetros diferentes. Portanto, verifique os parâmetros necessários para a ramificação que você usar.

### <a name="deploy-using-azure-cli"></a>Implantar usando a CLI do Azure

> [!NOTE] 
> O comando a seguir requer a CLI do Azure 2.0.8 ou posterior. Você pode verificar a versão CLI com o comando `az --version`. Para atualizar a versão da CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

O exemplo a seguir implanta o cluster do OpenShift e todos os recursos relacionados em um grupo de recursos denominado openshiftrg, com um nome de implantação de myOpenShiftCluster. O modelo é referenciado diretamente do repositório GitHub e um arquivo de parâmetros local chamado azuredeploy.parameters.json é usado.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A implantação leva pelo menos 30 minutos para ser concluída, com base no número total de nós implantados e as opções configuradas. O FQDN do DNS de bastiões e a URL do console do OpenShift imprime no terminal quando a implantação for concluída.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Se você não deseja obstruir a linha de comando, aguardando a implantação seja concluída, adicione `--no-wait` como uma das opções para a implantação de grupo. A saída da implantação pode ser recuperada do portal do Azure, na seção de implantação para o grupo de recursos.
 
## <a name="deploy-using-the-openshift-container-platform-azure-marketplace-offer"></a>Implantar usando a oferta de OpenShift Container Platform Azure Marketplace

A maneira mais simples para implantar o OpenShift Container Platform no Azure é usar a [Oferta Marketplace do Azure](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Essa opção é a mais simples, mas também tem recursos de personalização limitados. A oferta do Marketplace inclui as seguintes opções de configuração:

- **Nós mestres**: tipo de instância de nós mestres três (3) com configurável.
- **Nós mestres**: tipo de instância de nós mestres três (3) com configurável.
- **Nós**: O número de nós é configurável (entre 2 e 9), bem como o tipo de instância.
- **Tipo de Disco**: Discos gerenciados são usados.
- **Rede**: suporte para rede nova ou existente, bem como o intervalo CIDR personalizado.
- **CNS**: CNS pode ser habilitado.
- **Métricas**: as métricas podem ser habilitadas.
- **Registro em log**: registro em log pode ser habilitado.
- **O provedor de nuvem do Azure**: pode ser habilitado.

## <a name="connect-to-the-openshift-cluster"></a>Conectar-se ao cluster OpenShift

Quando a implantação for concluída, recupere a conexão da seção de saída da implantação. Conecte-se ao console do OpenShift com o navegador usando o `OpenShift Console URL`. Como alternativa, você pode SSH para o host de bastiões. A seguir está um exemplo em que o nome de usuário do administrador é clusteradmin e o IP público de bastiões FQDN do DNS é bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos, o cluster OpenShift e todos os recursos relacionados.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Próximas etapas

- [Tarefas de pós-implantação](./openshift-post-deployment.md)
- [Solução de problemas de implantação do OpenShift no Azure](./openshift-troubleshooting.md)
- [Introdução ao OpenShift Container Platform](https://docs.openshift.com/container-platform)

### <a name="documentation-contributors"></a>Colaboradores de documentação

Meus agradecimentos aos Vincent Power (vincepower) e Alfred Sin (asinn826) por suas contribuições para manter esta documentação-se atualizado!