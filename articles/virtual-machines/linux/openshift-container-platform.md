---
title: Implantar o OpenShift Container Platform no Azure | Microsoft Docs
description: Implante o OpenShift Container Platform no Azure.
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
ms.openlocfilehash: c91b7232b2f87e0b4b5e659126b96a6ef8b4202c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Implantar o OpenShift Container Platform no Azure

Há várias maneiras de implantar o OpenShift Container Platform no Azure. Você pode implantar manualmente todos os componentes de infraestrutura necessários do Azure e, em seguida, seguir a [documentação](https://docs.openshift.com/container-platform/3.6/welcome/index.html) do OpenShift Container Platform.
Você também pode usar um modelo do Resource Manager existente que simplifica a implantação do cluster do OpenShift Container Platform. Esse modelo está localizado [aqui](https://github.com/Microsoft/openshift-container-platform/).

Outra opção é usar a [Oferta do Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Para ambas as opções, é necessária uma assinatura do Red Hat. Durante a implantação, a instância RHEL é registrada na Assinatura do Red Hat e anexada à ID do Pool que contém os direitos para o OpenShift Container Platform.
Certifique-se de que você tenha uma senha, uma ID do Pool e um Nome de usuário de gerente de Assinatura do Red Hat (nome de usuário de RHSM, senha de RHSM e ID do Pool) válidos. Você pode verificar as informações fazendo logon no https://access.redhat.com.

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Implantar usando o modelo do Resource Manager de OpenShift Container Platform

Para implantar usando o modelo do Resource Manager, um arquivo de parâmetros é usado para fornecer todos os parâmetros de entrada. Se você quiser personalizar qualquer um dos itens de implantação que não são cobertos usando parâmetros de entrada, crie o fork do repositório github e altere os itens apropriados.

Algumas opções de personalização comuns incluem (mas não se limitam a):

- CIDR VNet [variável em azuredeploy.json]
- tamanho de VM de bastião [variável em azuredeploy.json]
- Convenções de nomenclatura [variáveis em azuredeploy.json]
- Especificações de cluster do OpenShift – modificadas por meio do arquivo de hosts [deployOpenShift.sh]

### <a name="configure-parameters-file"></a>Configurar o arquivo de parâmetros

Use o valor `appId` da entidade de serviço que você criou anteriormente para o parâmetro `aadClientId`. 

O exemplo a seguir cria um arquivo de parâmetros chamado **azuredeploy.parameters.json** com todas as entradas necessárias.

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
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "true"
        },
        "enableCockpit": {
            "value": "false"
        },
        "rhsmUsernamePasswordOrActivationKey": {
            "value": "usernamepassword"
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
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Substitua itens entre {} por suas informações pertinentes.

### <a name="deploy-using-azure-cli"></a>Implantar usando a CLI do Azure

> [!NOTE] 
> O comando a seguir requer a CLI do Azure 2.0.8 ou posterior. Você pode verificar a versão de az CLI com o comando `az --version`. Para atualizar a versão da CLI, confira [Instalar a CLI do Azure 2.0]( /cli/azure/install-azure-cli).

O exemplo a seguir implanta o cluster do OpenShift e todos os recursos relacionados em um grupo de recursos denominado myResourceGroup com um nome de implantação de myOpenShiftCluster. O modelo é referenciado diretamente do repositório github e um arquivo de parâmetros local chamado **azuredeploy.parameters.json** é usado.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A implantação leva pelo menos 30 minutos para ser concluída dependendo do número total de nós implantados. A URL do console openShift e o nome DNS do mestre OpenShift são impressas no terminal quando a implantação é concluída.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-using-openshift-container-platform-marketplace-offer"></a>Implantar usando a oferta do marketplace do OpenShift Container Platform

A maneira mais simples para implantar o OpenShift Container Platform no Azure é usar a [Oferta do Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Essa opção é a mais simples, mas também tem recursos de personalização limitados. A oferta inclui três opções de configuração:

- Pequena: implanta um cluster não HA com um Nó Mestre, um Nó de Infraestrutura, dois Nós de Aplicativo e um Nó de Bastião. Todos os nós são de tamanhos de VM DS2v2 padrão. Este cluster requer 10 núcleos no total e é ideal para testes de pequena escala.
- Média: implanta um cluster de HA com três Nós Mestres, dois Nós de Infraestrutura, quatro Nós de Aplicativo e um Nó de Bastião. Todos os nós, exceto os de Bastião, são de tamanho de VM DS3v2 padrão. O Nó de Bastião é um DS2v2 padrão. Este cluster exige 38 núcleos.
- Grande: implanta um cluster de HA com três Nós Mestres, dois Nós de Infraestrutura, seis Nós de Aplicativo e um Nó de Bastião. Os Nós Mestres e de Infraestrutura são de tamanhos de VM DS3v2 padrão, os Nós de Aplicativo são de tamanho de VM DS4v2 padrão e o Nó de Bastião é um DS2v2 padrão. Este cluster exige 70 núcleos.

A configuração do provedor de nuvem do Azure é opcional para tamanhos de cluster Médio e Grande. O tamanho de cluster Pequeno não oferece uma opção para configurar o Provedor de nuvem do Azure.

## <a name="connect-to-the-openshift-cluster"></a>Conectar-se ao cluster OpenShift

Quando a implantação for concluída, conecte-se ao console do OpenShift com o navegador usando o `OpenShift Console Uri`. Como alternativa, você pode se conectar ao mestre do OpenShift usando o comando a seguir:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, o cluster OpenShift e todos os recursos relacionados.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

- [Tarefas pós-implantação](./openshift-post-deployment.md)
- [Solução de problemas de implantação do OpenShift](./openshift-troubleshooting.md)
- [Introdução ao OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
