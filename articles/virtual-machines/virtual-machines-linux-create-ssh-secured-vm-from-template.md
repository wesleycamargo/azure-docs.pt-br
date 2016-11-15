---
title: Criar uma VM do Linux usando um modelo do Azure | Microsoft Docs
description: Crie uma VM do Linux no Azure usando um modelo do Azure Resource Manager.
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/24/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ff7ea65c329a37acf8b2febb52fd140954d81e97


---
# <a name="create-a-linux-vm-using-an-azure-template"></a>Criar uma VM do Linux usando um modelo do Azure
Este artigo mostra como implantar rapidamente uma nova Máquina Virtual do Linux no Azure usando um Modelo do Azure.  O artigo exige:

* uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).
* a [CLI do Azure](../xplat-cli-install.md) conectada com o `azure login`.
* A CLI do Azure *deve estar no* modo Azure Resource Manager `azure config mode arm`.

Você também pode implantar rapidamente um modelo de VM do Linux usando o [portal do Azure](virtual-machines-linux-quick-create-portal.md).

## <a name="quick-command-summary"></a>Resumo rápido do comando
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado
Os modelos permitem criar VMs no Azure com as configurações que você deseja personalizar durante a inicialização, como nomes de usuário e nomes de host. Neste artigo, estamos iniciando um modelo do Azure utilizando uma VM do Ubuntu junto com um grupo de segurança de rede (NSG) com a porta 22 aberta para o SSH.

Os modelos do Azure Resource Manager são arquivos JSON que podem ser usados para tarefas únicas simples, como iniciar uma VM Ubuntu, como feito neste artigo.  Os Modelos do Azure também podem ser usados para construir configurações complexas do Azure de ambientes inteiros como uma pilha de implantação de teste, desenvolvimento ou produção.

## <a name="create-the-linux-vm"></a>Criar a VM Linux
O exemplo de código a seguir mostra como chamar o `azure group create` para criar um grupo de recursos e implantar uma VM do Linux protegida por SSH ao mesmo tempo usando [este modelo do Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Lembre-se de que, em seu exemplo, você precisará usar nomes que sejam exclusivos para seu ambiente. Este exemplo usa `myResourceGroup` como o nome do grupo de recursos, e `myVM` como o nome da VM.

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

A saída deve ser semelhante ao bloco de saída a seguir:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Este exemplo implantou uma VM usando o parâmetro `--template-uri` .  Você também pode baixar ou criar um modelo localmente e passar o modelo usando o parâmetro `--template-file` com um caminho para o arquivo de modelo como um argumento. A CLI do Azure solicita os parâmetros necessários ao modelo.

## <a name="next-steps"></a>Próximas etapas
Pesquise a [galeria de modelos](https://azure.microsoft.com/documentation/templates/) para descobrir quais estruturas de aplicativos implantar em seguida.




<!--HONumber=Nov16_HO2-->


