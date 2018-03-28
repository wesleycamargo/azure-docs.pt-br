---
title: "Criar um conjunto de dimensionamento de máquinas virtuais Linux com um modelo do Azure | Microsoft Docs"
description: "Saiba como criar rapidamente um dimensionamento de máquinas virtuais do Linux com um modelo do Azure Resource Manager que implante um aplicativo de exemplo e configure regras de dimensionamento automático"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 16e9c0b30710d711ef2789f7781b17e72889d4da
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-linux-virtual-machine-scale-set-with-an-azure-template"></a>Criar um conjunto de dimensionamento de máquinas virtuais Linux com um modelo do Azure
Um conjunto de dimensionamento de máquinas virtuais permite implantar e gerenciar um conjunto de máquinas virtuais idênticas de dimensionamento automático. Dimensione o número de VMs no conjunto de dimensionamento manualmente ou defina regras para o dimensionamento automático com base no uso de recursos, como CPU, demanda de memória ou tráfego de rede. Neste artigo de introdução, você criará um conjunto de dimensionamento de máquinas virtuais Linux com um modelo do Azure Resource Manager. Também é possível criar um conjunto de dimensionamento com a [CLI do Azure 2.0](virtual-machine-scale-sets-create-cli.md), com o [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) ou com o [Portal do Azure](virtual-machine-scale-sets-create-portal.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.20 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 


## <a name="define-a-scale-set-in-a-template"></a>Definir um conjunto de dimensionamento em um modelo
Os modelos do Azure Resource Manager permitem a implantação de grupos de recursos relacionados. Os modelos são escritos em JSON (JavaScript Object Notation) e definem todo o ambiente de infraestrutura do Azure para seu aplicativo. Em um único modelo, é possível criar o conjunto de dimensionamento de máquinas virtuais, instalar aplicativos e configurar regras de dimensionamento automático. Com o uso de variáveis e parâmetros, esse modelo pode ser reutilizado para atualizar conjuntos de dimensionamento existentes ou criar conjuntos adicionais. É possível implantar modelos por meio do portal do Azure, da CLI do Azure 2.0 ou do Azure PowerShell ou de pipelines CI/CD (integração contínua/entrega contínua).

Para obter mais informações sobre modelos, consulte [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)

Para criar uma escala com um modelo, defina os recursos apropriados. As partes principais do tipo de recurso de conjunto de dimensionamento de máquinas virtuais são:

| Propriedade                     | Descrição da propriedade                                  | Exemplo de valor do modelo                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| Tipo                         | Tipo de recurso do Azure a ser criado                            | Microsoft.Compute/virtualMachineScaleSets |
| Nome                         | O nome do conjunto de dimensionamento                                       | myScaleSet                                |
| location                     | O local para criar o conjunto de dimensionamento                     | Leste dos EUA                                   |
| sku.name                     | O tamanho da VM para cada instância do conjunto de dimensionamento                  | Standard_A1                               |
| sku.capacity                 | O número de instâncias de VM a serem criadas inicialmente           | 2                                         |
| upgradePolicy.mode           | Modo de atualização de instância de VM quando ocorrem alterações              | Automático                                 |
| imageReference               | A plataforma ou a imagem personalizada a ser usada nas instâncias de VM | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | O prefixo do nome de cada instância de VM                     | myvmss                                    |
| osProfile.adminUsername      | O nome de usuário de cada instância de VM                        | azureuser                                 |
| osProfile.adminPassword      | A senha de cada instância de VM                        | P@ssw0rd!                                 |

 O exemplo a seguir mostra a definição de recurso do conjunto de dimensionamento principal. Para personalizar um modelo de conjunto de dimensionamento, altere o tamanho da VM ou a capacidade inicial, ou use uma plataforma diferente ou uma imagem personalizada.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 Para manter o exemplo curto, a configuração da NIC (placa de interface de rede) virtual não é mostrada. Os componentes adicionais, como um balanceador de carga, também não são mostrados. Um modelo completo de conjunto de dimensionamento é mostrado [no fim deste artigo](#deploy-the-template).


## <a name="install-an-application"></a>Instalar um aplicativo
Quando você implanta um conjunto de dimensionamento, as extensões da VM podem fornecer tarefas de configuração e de automação pós-implantação, tais como instalar um aplicativo. Os scripts podem ser baixados do armazenamento do Azure ou do GitHub, ou fornecidos ao Portal do Azure no tempo de execução da extensão. Para aplicar uma extensão ao conjunto de dimensionamento, adicione a seção *extensionProfile* ao exemplo anterior do recurso. O perfil da extensão normalmente define as propriedades a seguir:

- Tipo de extensão
- Editor de extensão
- Versão da extensão
- Local dos scripts de configuração ou de instalação
- Comandos a serem executados nas instâncias da VM

O modelo [servidor HTTP Python no Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) usa a extensão de Script personalizado para instalar o [Bottle](http://bottlepy.org/docs/dev/), uma estrutura de Web Python e um servidor HTTP simples. 

Dois scripts são definidos em **fileUris** - *installserver.sh* e *workserver.py*. Esses arquivos são baixados do GitHub e o *commandToExecute* executa `bash installserver.sh` para instalar e configurar o aplicativo:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
          ],
          "commandToExecute": "bash installserver.sh"
        }
      }
    }
  ]
}
```


## <a name="deploy-the-template"></a>Implantar o modelo
É possível implantar o modelo [servidor HTTP Python no Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) com o seguinte botão **Implantar no Azure**. Esse botão abre o Portal do Azure, carrega o modelo completo e solicita alguns parâmetros, como o nome de um conjunto de dimensionamento, a contagem de instâncias e as credenciais de administrador.

[![Implantar o modelo no Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

Também é possível usar a CLI do Azure 2.0 para instalar o servidor HTTP Python no Linux com [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create) da seguinte maneira:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Responda os prompts para fornecer nome do conjunto de dimensionamento, uma contagem de instâncias e credenciais de administrador para as instâncias de VM. Levará alguns minutos para o conjunto de dimensionamento e os recursos de suporte serem criados.


## <a name="test-your-sample-application"></a>Testar o aplicativo de exemplo
Para ver seu aplicativo em ação, obtenha o endereço IP público do balanceador de carga com a [az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_show) da seguinte maneira:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Insira o endereço IP público do balanceador de carga em um navegador da Web no formato *http://publicIpAddress:9000/do_work*. O balanceador de carga distribui o tráfego para uma de suas instâncias de VM, conforme mostrado no exemplo a seguir:

![Página da Web padrão em NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)


## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, use [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados, como demonstrado a seguir:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Próximas etapas
Neste artigo de introdução, você criou um conjunto de dimensionamento do Linux com um modelo do Azure e usou uma Extensão de Script Personalizado para instalar um servidor Web Python básico nas instâncias de VM. Para maior escalabilidade e a automação, expanda seu conjunto de dimensionamento com os seguintes artigos de instruções:

- [Implantar o aplicativo em conjuntos de dimensionamento de máquinas virtuais](virtual-machine-scale-sets-deploy-app.md)
- Dimensione automaticamente com a [CLI do Azure](virtual-machine-scale-sets-autoscale-cli.md), [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) ou o [Portal do Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Usar atualizações automáticas de sistema operacional para suas instâncias de VM de conjunto de dimensionamento](virtual-machine-scale-sets-automatic-upgrade.md)
