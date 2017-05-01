---
title: "Criar um aplicativo altamente disponível no Azure usando conjuntos de dimensionamento de máquinas virtuais | Microsoft Docs"
description: "Crie e implante um aplicativo altamente disponível em VMs Linux usando um conjunto de dimensionamento de máquinas virtuais e a CLI do Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 4c76fb202f501f671504646395b800aeb90d8e69
ms.lasthandoff: 04/07/2017

---

# <a name="create-a-highly-available-application-on-linux-with-virtual-machine-scale-sets"></a>Criar um aplicativo altamente disponível no Linux com Conjunto de Dimensionamento de Máquinas Virtuais
Este tutorial mostra como criar um aplicativo altamente disponível em um conjunto de dimensionamento de máquinas virtuais. Você também aprenderá a automatizar a configuração das máquinas virtuais no conjunto de dimensionamento. 


## <a name="step-1---create-a-resource-group"></a>Etapa 1 – Criar um grupo de recursos
Para concluir este tutorial, certifique-se de que a versão mais recente da [CLI 2.0 do Azure](/cli/azure/install-azure-cli) esteja instalada. Se você ainda não estiver conectado à sua assinatura do Azure, faça logon com [az login](/cli/azure/#login) e siga as instruções na tela.

Crie um grupo de recursos com [az group create](/cli/azure/group#create). O exemplo a seguir cria um grupo de recursos denominado `myResourceGroupVMSS` no local `westus`:

```azurecli
az group create --name myResourceGroupVMSS --location westus
```


## <a name="step-2---define-your-app"></a>Etapa 2 – Definir o aplicativo
Use a mesma configuração **cloud-init** do tutorial em que criou um aplicativo altamente disponível com balanceamento de carga. Para saber mais sobre como usar **cloud_init**, consulte [Usar cloud-init para personalizar uma VM Linux durante a criação](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Crie um arquivo chamado `cloud-init.txt` e cole a seguinte configuração:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - nginx -s reload
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="step-3---create-scale-set"></a>Etapa 3 – Criar um conjunto de dimensionamento
Um conjunto de dimensionamento de máquinas virtuais permite implantar e gerenciar um conjunto de máquinas virtuais idênticas de dimensionamento automático. Os conjuntos de dimensionamento usam os mesmos componentes sobre os quais você aprendeu no tutorial para [Criar um aplicativo altamente disponível no Azure](tutorial-load-balance-nodejs.md). Esses componentes incluem conjuntos de disponibilidade, domínios de falha e atualização e balanceadores de carga.

Com um conjunto de dimensionamento, esses recursos são criados e gerenciados para você. O número de VMs no conjunto de dimensionamento pode aumentar ou diminuir automaticamente de acordo com as regras definidas. É possível [usar uma imagem personalizada](capture-image.md) como a origem da máquina virtual ou configurar as VMs durante a implantação com **cloud-init** como neste tutorial.

Crie um conjunto de dimensionamento de máquinas virtuais com [az vmss create](/cli/azure/vmss#create). O seguinte exemplo cria um conjunto de dimensionamento chamado `myScaleSet`:

```azurecli
az vmss create \
  --resource-group myResourceGroupVMSS \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.


## <a name="step-4---configure-firewall"></a>Etapa 4 – Configurar o firewall
Um balanceador de carga foi criado automaticamente como parte do conjunto de dimensionamento de máquinas virtuais. O balanceador de carga distribui o tráfego entre um conjunto de VMs definidas usando regras do balanceador de carga. Para permitir que o tráfego acesse o aplicativo Web, crie uma regra com [az network lb probe create](/cli/azure/network/lb/probe#create). O exemplo a seguir cria uma regra chamada `myLoadBalancerRuleWeb`:

```azurecli
az network lb rule create \
  --resource-group myResourceGroupVMSS \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="step-5---test-your-app"></a>Etapa 5 – Testar o aplicativo
Obtenha o endereço IP público de seu balanceador de carga com [az network public-ip show](/cli/azure/network/public-ip#show). O seguinte exemplo obtém o endereço IP de `myScaleSetLBPublicIP`, criado como parte do conjunto de dimensionamento:

```azurecli
az network public-ip show \
    --resource-group myResourceGroupVMSS \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Insira o endereço IP público em um navegador da Web. O aplicativo é exibido, incluindo o nome do host da VM para a qual o balanceador de carga distribui o tráfego:

![Executar o aplicativo Node.js](./media/tutorial-load-balance-nodejs/running-nodejs-app.png)

Force a atualização do navegador da Web para ver o balanceador de carga distribuir o tráfego entre todas as VMs no conjunto de dimensionamento que executa o aplicativo.


## <a name="step-6---management-tasks"></a>Etapa 6 – Tarefas de gerenciamento
Durante todo o ciclo de vida do conjunto de dimensionamento, você poderá precisar executar uma ou mais tarefas de gerenciamento. Além disso, talvez você deseje criar scripts que automatizam várias tarefas do ciclo de vida. A CLI 2.0 do Azure fornece uma maneira rápida de realizar essas tarefas. Estas são algumas tarefas comuns.

### <a name="increase-or-decrease-vm-instances"></a>Aumentar ou diminuir as instâncias de VM
Manualmente, é possível aumentar ou diminuir o número de máquinas virtuais no conjunto de dimensionamento com [az vmss scale](/cli/azure/vmss#scale). O seguinte exemplo aumenta o número de VMs no conjunto de dimensionamento para `5`:

```azurecli
az vmss scale --resource-group myResourceGroupVMSS --name myScaleSet --new-capacity 5
```

As regras de dimensionamento automático permitem definir como escalar e reduzir verticalmente o número de VMs no conjunto de dimensionamento em resposta à demanda, como tráfego de rede ou uso da CPU. Atualmente, essas regras não podem ser definidas na CLI 2.0 do Azure. Use o [portal do Azure](https://portal.azure.com) para configurar o dimensionamento automático.

### <a name="get-connection-info"></a>Obter informações de conexão
Para obter informações de conexão sobre as VMs nos conjuntos de dimensionamento, use [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info). Esse comando gera o endereço IP público e as portas para cada VM que permite a conexão com o SSH:

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroupVMSS --name myScaleSet
```

### <a name="delete-resource-group"></a>Excluir grupo de recursos
Excluir um grupo de recursos exclui todos os recursos contidos nele.

```azurecli
az group delete --name myResourceGroupVMSS
```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, definimos o aplicativo Web com **cloud-init** e configuramos cada VM durante a implantação. Para obter informações sobre como capturar uma VM para usar como a imagem de origem no conjunto de dimensionamento, consulte [Como generalizar e capturar uma máquina virtual Linux](capture-image.md).

Para saber mais sobre alguns dos recursos do conjunto de dimensionamento de máquinas virtuais apresentados neste tutorial, consulte as seguintes informações:

- [Visão geral dos conjuntos de dimensionamento de máquinas virtuais do Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Visão geral do Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
- [Controlar o fluxo de tráfego de rede com grupos de segurança de rede](../../virtual-network/virtual-networks-nsg.md)
