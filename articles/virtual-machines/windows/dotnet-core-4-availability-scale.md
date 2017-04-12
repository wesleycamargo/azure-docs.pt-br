---
title: Disponibilidade e Escala em modelos do Azure Resource Manager | Microsoft Docs
description: "Tutorial principal de DotNet da máquina virtual do Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 494724b5-06af-4dea-a774-ba580cf27527
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 09f556e107058aa55f59254b29b119efbaa6700b
ms.lasthandoff: 03/31/2017


---
# <a name="availability-and-scale-in-azure-resource-manager-templates-for-windows-vms"></a>Disponibilidade e escala em modelos do Azure Resource Manager para VMs Windows

Disponibilidade e escala referem-se ao tempo de atividade e à capacidade de atender à demanda. Se um aplicativo deve estar ativo 99,9% do tempo, ele precisa ter uma arquitetura que permite vários recursos de computação simultâneos. Por exemplo, em vez de ter um único site, uma configuração com um nível mais alto de disponibilidade inclui várias instâncias do mesmo site, com tecnologia de balanceamento na frente delas. Nessa configuração, uma instância do aplicativo pode ser desativada para manutenção, enquanto o restante continua a funcionar. Por outro lado, a escala se refere à capacidade de aplicativos para atender à demanda. Com um aplicativo de balanceamento de carga, adicionar ou remover instâncias do pool permite que um aplicativo seja dimensionado para atender à demanda.

Este documento detalha como a implantação de exemplo de Loja de Música é configurada para disponibilidade e escala. Todas as dependências e configurações exclusivas são realçadas. Para obter a melhor experiência, pré-implante uma instância da solução em sua assinatura do Azure e trabalhe com o modelo do Azure Resource Manager. O modelo completo pode ser encontrado aqui – [Implantação de Loja de Música no Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="availability-set"></a>Conjunto de disponibilidade
Logicamente, um conjunto de disponibilidade abrange as máquinas virtuais do Azure em hosts físicos e outros componentes de infraestrutura, como fontes de alimentação e hardware de rede física. Conjuntos de disponibilidade garantem que, durante a manutenção, a falha de dispositivo ou outro tempo de inatividade, nem todas as máquinas virtuais sejam afetadas. Um conjunto de disponibilidade pode ser adicionado a um modelo do Azure Resource Manager usando o Assistente para Adicionar Novos Recursos do Visual Studio ou inserindo JSON válido em um modelo.

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Conjunto de disponibilidade](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L368).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "availability-set"
  },
  "properties": {
  }
}
```

Um conjunto de disponibilidade é declarado como uma propriedade de um recurso de máquina virtual. 

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Associação de conjunto de disponibilidade com máquina virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L302).

```json
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  }
```
O conjunto de disponibilidade como visto no portal do Azure. Cada máquina virtual e detalhes sobre a configuração são descritos aqui.

![Conjunto de disponibilidade](./media/dotnet-core-4-availability-scale/ase-win.png)

Para obter informações detalhadas sobre conjuntos de disponibilidade, consulte [Gerenciar a disponibilidade das máquinas virtuais](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

## <a name="network-load-balancer"></a>Balanceador de carga de rede
Enquanto um conjunto de disponibilidade fornece tolerância a falhas do aplicativo, um balanceador de carga disponibiliza várias instâncias do aplicativo em um único endereço de rede. Várias instâncias de um aplicativo podem ser hospedadas em várias máquinas virtuais, cada uma conectada a um balanceador de carga. Quando o aplicativo é acessado, o balanceador de carga encaminha a solicitação de entrada entre os membros anexados. Um balanceador de carga pode ser adicionado usando o Assistente para Adicionar Novos Recursos do Visual Studio ou inserindo recurso JSON formatado adequadamente no modelo do Azure Resource Manager.

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Balanceador de carga de rede](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L198).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer"
  },
  ........<truncated>
}
```

Como o aplicativo de exemplo é exposto à Internet com um endereço IP público, esse endereço é associado com o balanceador de carga. 

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Associação de balanceador de carga de rede com endereço IP público](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211).

```json
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

No portal do Azure, a visão de geral do balanceador de carga de rede mostra a associação com o endereço IP público.

![Balanceador de carga de rede](./media/dotnet-core-4-availability-scale/nlb-win.png)

## <a name="load-balancer-rule"></a>Regra do balanceador de carga
Ao usar um balanceador de carga, regras são configuradas que controlam como o tráfego é balanceado entre recursos planejados. Com o aplicativo de Loja de Música de exemplo, o tráfego chega à porta 80 do endereço IP público e é distribuído pela porta 80 de todas as máquinas virtuais. 

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Regra do balanceador de carga](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L226).

```json
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
]
```

Uma exibição da regra do balanceador de carga de rede do portal.

![Regra do balanceador de carga de rede](./media/dotnet-core-4-availability-scale/lbrule-win.png)

## <a name="load-balancer-probe"></a>Investigação do balanceador de carga
O balanceador de carga também precisa monitorar cada máquina virtual para que as solicitações sejam atendidas apenas a sistemas em execução. Esse monitoramento é feito por meio da investigação constante de uma porta predefinida. A implantação da Loja de Música está configurada para a porta de investigação 80 em todas as máquinas virtuais incluídas. 

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Investigação do balanceador de carga](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L247).

```json
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

A investigação do balanceador de carga vista no portal do Azure.

![Investigação do balanceador de carga de rede](./media/dotnet-core-4-availability-scale/lbprobe-win.png)

## <a name="inbound-nat-rules"></a>Regras NAT de Entrada
Ao usar um Balanceador de Carga, regras precisam ser colocadas em prática que fornecem acesso sem balanceamento de carga a cada Máquina Virtual. Por exemplo, ao criar uma conexão RDP com cada máquina virtual, esse tráfego não deve ter o balanceamento de carga, em vez disso, deve ser configurado um caminho predeterminado. Caminhos predeterminados são configurados usando um recurso de Regra NAT de entrada. Usando esse recurso, a comunicação de entrada pode ser mapeada para máquinas virtuais individuais. 

Com o aplicativo de Loja de Música, uma porta começando em 5000 é mapeada para a porta 3389 em cada máquina virtual para acesso do RDP. A função `copyindex()` é usada para incrementar a porta de entrada, de modo que a segunda máquina virtual recebe uma porta de entrada de 5001, a terceiro 5002 e assim por diante.

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Regras NAT de entrada](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L260). 

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'RDP-VM', copyIndex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 3389,
    "enableFloatingIP": false
  }
}
```

Um exemplo de regra NAT de entrada como visto no portal do Azure. Uma regra NAT RDP é criada para cada máquina virtual na implantação.

![Regra NAT de entrada](./media/dotnet-core-4-availability-scale/natrule-win.png)

Para obter informações detalhadas sobre o balanceador de carga de rede do Azure, consulte [Balanceamento de carga para serviços de infraestrutura do Azure](load-balance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="deploy-multiple-vms"></a>Implantar várias VMs
Por fim, para que um conjunto de disponibilidade ou balanceador de carga funcione com eficácia, várias máquinas virtuais são necessárias. Várias VMs podem ser implantadas usando a função de cópia de modelo do Azure Resource Manager. Usando a função de cópia, não é necessário definir um número finito de máquinas virtuais, em vez disso, esse valor pode ser fornecido dinamicamente no momento da implantação. A função de cópia consome o número de instâncias a serem criadas e lida com a implantação do número adequado de máquinas virtuais e recursos associados.

No modelo de exemplo de Loja de Música, um parâmetro definido usa uma contagem de instância. Esse número é usado em todo o modelo ao criar máquinas virtuais e recursos relacionados.

```json
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 2,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
},
```

No recurso de máquina virtual, o loop de cópia recebe um nome e o número do parâmetro de instâncias usado para controlar o número de cópias resultantes.

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Função de cópia de máquina virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L290). 

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  }
```

A iteração atual da função de cópia pode ser acessada com a função `copyIndex()` . O valor da função de índice de cópia pode ser usado para nomear as máquinas virtuais e outros recursos. Por exemplo, se duas instâncias de uma máquina virtual forem implantadas, elas terão nomes diferentes. A função `copyIndex()` pode ser usada como parte do nome da máquina virtual para criar um nome exclusivo. Um exemplo da função `copyindex()` usada para fins de nomenclatura pode ser vista no recurso de máquina virtual. Aqui, o nome do computador é uma concatenação do parâmetro `vmName` e da função `copyIndex()`. 

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Função de índice de cópia](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L309). 

```json
"osProfile": {
  "computerName": "[concat(variables('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "adminPassword": "[parameters('adminPassword')]"
}
```

A função `copyIndex` é usada várias vezes no modelo de exemplo da Loja de Música. Recursos e funções utilizando `copyIndex` incluem qualquer coisa específica a uma única instância da máquina virtual como adaptador de rede, regras de balanceador de carga e qualquer uma depende de funções. 

Para obter mais informações sobre a função de cópia, consulte [Criar várias instâncias de recursos no Azure Resource Manager](../../resource-group-create-multiple.md).

## <a name="next-step"></a>Próxima etapa
<hr>

[Etapa 4 – Implantação de aplicativos com modelos do Azure Resource Manager](dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


