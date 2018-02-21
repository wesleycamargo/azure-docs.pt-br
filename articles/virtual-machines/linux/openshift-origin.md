---
title: Implantar a origem do OpenShift no Azure | Microsoft Docs
description: Implante a origem do OpenShift no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: f7a668f30d7acb1ea14fe9fd8921066d40a6669b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-openshift-origin-in-azure"></a>Implantar a origem do OpenShift no Azure

Você pode usar uma de duas maneiras para implantar a Origem do OpenShift no Azure:

- Você pode implantar manualmente todos os componentes de infraestrutura necessários do Azure e, em seguida, seguir a [documentação](https://docs.openshift.org/3.6/welcome/index.html) da Origem do OpenShift.
- Você também pode usar um [modelo do Gerenciador de Recursos](https://github.com/Microsoft/openshift-origin) existente que simplifica a implantação do cluster da Origem do OpenShift.

## <a name="deploy-by-using-the-openshift-origin-template"></a>Implantar usando o modelo de Origem do OpenShift

Use o valor `appId` da entidade de serviço que você criou anteriormente para o parâmetro `aadClientId`.

O exemplo a seguir cria um arquivo de parâmetros chamado azuredeploy.parameters.json com todas as entradas necessárias.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
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
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

### <a name="deploy-by-using-azure-cli"></a>Implantar usando a CLI do Azure


> [!NOTE] 
> O comando a seguir requer a CLI do Azure 2.0.8 ou posterior. Você pode verificar a versão CLI com o comando `az --version`. Para atualizar a versão da CLI, confira [Instalar a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

O exemplo a seguir implanta o cluster do OpenShift e todos os recursos relacionados em um grupo de recursos denominado myResourceGroup com um nome de implantação de myOpenShiftCluster. O modelo é referenciado diretamente do repositório GitHub por um arquivo de parâmetros local chamado azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A implantação leva pelo menos 25 minutos para ser concluída dependendo do número total de nós implantados. A URL do console openShift e o nome DNS do mestre OpenShift são impressas no terminal quando a implantação é concluída.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-openshift-cluster"></a>Conectar-se ao cluster OpenShift

Quando a implantação for concluída, conecte-se ao console do OpenShift com o navegador usando o `OpenShift Console Uri`. Como alternativa, você pode se conectar ao mestre do OpenShift usando o comando a seguir:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos, o cluster OpenShift e todos os recursos relacionados.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

- [Tarefas de pós-implantação](./openshift-post-deployment.md)
- [Solução de problemas de implantação do OpenShift](./openshift-troubleshooting.md)
- [Introdução ao OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)
