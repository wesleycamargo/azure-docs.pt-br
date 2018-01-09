---
title: "Criar um conjunto de dimensionamento de máquinas virtuais com a CLI do Azure 2.0 | Microsoft Docs"
description: "Saiba como criar um rapidamente uma escala de máquina virtual com o Azure PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 222bfa1c3070fa4634cf5c48d934a6387c48a4b0
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Criar um conjunto de dimensionamento de máquinas virtuais com o CLI do Azure 2.0
Um conjunto de dimensionamento de máquinas virtuais permite implantar e gerenciar um conjunto de máquinas virtuais idênticas de dimensionamento automático. Dimensione o número de VMs no conjunto de dimensionamento manualmente ou defina regras para o dimensionamento automático com base no uso de recursos, como CPU, demanda de memória ou tráfego de rede. Neste artigo de introdução, você cria um conjunto de dimensionamento de máquinas virtuais com a CLI do Azure 2.0. Também é possível criar um conjunto de dimensionamento com o [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) ou com o [Portal do Azure](virtual-machine-scale-sets-create-portal.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.20 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Criar um conjunto de escala
Antes de criar uma máquina virtual, crie um grupo de recursos com o [az group create](/cli/azure/group#create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Crie um conjunto de dimensionamento de máquinas virtuais com [az vmss create](/cli/azure/vmss#create). O exemplo a seguir criará um conjunto de dimensionamento chamado *myScaleSet* e gerará chaves SSH se elas não existirem:

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.


## <a name="install-nginx-webserver"></a>Instalar servidor Web NGINX
Para testar seu conjunto de dimensionamento, use a Extensão de Script Personalizado para baixar e executar um script que instala o NGINX nas instâncias de VM. Essa extensão é útil para a configuração de implantação de postagem, instalação de software ou qualquer outra configuração/tarefa de gerenciamento. Para obter mais informações, consulte a [Visão geral da Extensão de Script Personalizado](../virtual-machines/linux/extensions-customscript.md).

Aplique a Extensão do Script Personalizado que instala o NGINX da seguinte maneira:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-web-traffic"></a>Permitir o tráfego da web
Para permitir o tráfego acessar o servidor Web, crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule#create). O exemplo a seguir cria uma regra chamada *myLoadBalancerRuleWeb*:

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## <a name="test-your-web-server"></a>Testar seu servidor Web
Para ver seu servidor Web em ação, obtenha o endereço IP público de seu balanceador de carga com [az network public-ip show](/cli/azure/network/public-ip#show). O seguinte exemplo obtém o endereço IP de *myScaleSetLBPublicIP*, criado como parte do conjunto de dimensionamento:

```azurecli-interactive 
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Insira o endereço IP público do balanceador de carga em um navegador da Web. O balanceador de carga distribui o tráfego para uma de suas instâncias de VM, conforme mostrado no exemplo a seguir:

![Página da Web padrão em NGINX](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, use [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados, como demonstrado a seguir:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Próximas etapas
Neste artigo de introdução, você criou um conjunto de dimensionamento básico e usou uma Extensão de Script Personalizado para instalar um servidor Web NGINX básico nas instâncias de VM. Para maior escalabilidade e a automação, expanda seu conjunto de dimensionamento com os seguintes artigos de instruções:

- [Implantar o aplicativo em conjuntos de dimensionamento de máquinas virtuais](virtual-machine-scale-sets-deploy-app.md)
- Dimensione automaticamente com a [CLI do Azure](virtual-machine-scale-sets-autoscale-cli.md), [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) ou o [Portal do Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Usar atualizações automáticas de sistema operacional para suas instâncias de VM de conjunto de dimensionamento](virtual-machine-scale-sets-automatic-upgrade.md)