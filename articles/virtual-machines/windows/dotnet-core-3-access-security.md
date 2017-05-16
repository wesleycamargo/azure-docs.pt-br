---
title: "Acesso e segurança em modelos do Azure para VMs do Windows | Microsoft Docs"
description: "Tutorial principal de DotNet da máquina virtual do Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: e671fc45-5e4d-40fd-aac5-290892713cc0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: db267e4374d8786e65078843e8bcc4225085d5c8
ms.contentlocale: pt-br
ms.lasthandoff: 03/31/2017

---
# <a name="access-and-security-in-azure-resource-manager-templates-for-windows-vms"></a>Acesso e segurança em modelos do Azure Resource Manager para VMs Windows

Aplicativos hospedados no Azure provavelmente precisam ser acessados pela Internet ou por uma VPN/conexão de rota expressa com o Azure. Com o exemplo de aplicativo de Loja de Música, o site da Web é disponibilizado na Internet com um endereço IP público. Com acesso estabelecido, as conexões com o aplicativo e o acessa aos recursos de máquina virtual devem ser protegidos. Essa segurança de acesso é fornecida com um Grupo de Segurança de Rede. 

Este documento fornece detalhes sobre como o aplicativo de Loja de Música é protegido no modelo do Azure Resource Manager de exemplo. Todas as dependências e configurações exclusivas são realçadas. Para obter a melhor experiência, pré-implante uma instância da solução em sua assinatura do Azure e trabalhe com o modelo do Azure Resource Manager. O modelo completo pode ser encontrado aqui – [Implantação de Loja de Música no Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="public-ip-address"></a>Endereço IP público
Para fornecer acesso público a um recurso do Azure, um recurso de endereço IP público pode ser usado. O endereço IP público pode ser configurado com um endereço IP estático ou dinâmico. Se um endereço dinâmico for usado e a máquina virtual for interrompida e desalocada, os endereços serão removidos. Quando o computador for iniciado novamente, ele poderá ser atribuído um endereço IP público diferente. Para impedir a alteração de um endereço IP, um endereço IP reservado pode ser usado. 

Um endereço IP público pode ser adicionado a um modelo do Azure Resource Manager usando o Assistente para Adicionar Novos Recursos do Visual Studio ou inserindo JSON válido no modelo. 

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Endereço IP público](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L110).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicIpAddressName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "public-ip"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

Um endereço IP público pode ser associado a um adaptador de rede virtual ou a um balanceador de carga. Neste exemplo, como o site de Loja de Música tem carga balanceada entre várias máquinas virtuais, o endereço IP público está conectado ao balanceador de carga.

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Associação de endereço IP público com balanceador de carga](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211).

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

O endereço IP público como visto no portal do Azure. Observe que o endereço IP público está associado a um balanceador de carga, e não a uma máquina virtual. Balanceadores de carga de rede são detalhados no seguinte documento desta série.

![Endereço IP público](./media/dotnet-core-3-access-security/pubip-win.png)

Para obter mais informações sobre os endereços IP públicos do Azure, consulte [Endereços IP no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

## <a name="network-security-group"></a>Grupo de Segurança de Rede
Depois de estabelecer acesso aos recursos do Azure, esse acesso deve ser limitado. Para máquinas virtuais do Azure, proteger o acesso é feito usando um grupo de segurança de rede. Com o exemplo do aplicativo de Loja de Música, todo o acesso à máquina virtual é restrito exceto pela porta 80 para o acesso http e a porta 3389 para acesso do RDP. Um grupo de segurança de rede pode ser adicionado a um modelo do Azure Resource Manager usando o Assistente para Adicionar Novos Recursos do Visual Studio ou inserindo JSON válido no modelo.

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Grupo de segurança de rede](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L57).

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('networkSecurityGroup')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-security-group"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
},
```

Neste exemplo, o grupo de segurança de rede é associado com o objeto de sub-rede declarado no recurso de Rede Virtual. 

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Associação de grupo de segurança de rede com Rede Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L143).

```json
"subnets": [
  {
    "name": "[variables('subnetName')]",
    "properties": {
      "addressPrefix": "10.0.0.0/24",
      "networkSecurityGroup": {
        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
      }
    }
  }
]
```

Aqui está a aparência do grupo de segurança de rede no portal do Azure. Observe que um NSG pode associar-se a um adaptador de sub-rede e/ou rede. Nesse caso, o NSG é associado a uma sub-rede. Nessa configuração, as regras de entrada se aplicam a todas as máquinas virtuais conectadas à sub-rede.

![Grupo de Segurança de Rede](./media/dotnet-core-3-access-security/nsg-win.png)

Para obter informações detalhadas sobre grupos de segurança de rede, leia [O que é um Grupo de Segurança de Rede](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/).

## <a name="next-step"></a>Próxima etapa
<hr>

[Etapa 3 – Disponibilidade e escala em modelos do Azure Resource Manager](dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


