---
title: Acesso e segurança em modelos do Azure Resource Manager | Microsoft Docs
description: Tutorial principal de DotNet da máquina virtual do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/21/2016
ms.author: nepeters

---
# <a name="access-and-security-in-azure-resource-manager-templates"></a>Acesso e segurança em modelos do Azure Resource Manager
Aplicativos hospedados no Azure provavelmente precisam ser acessados pela Internet ou por uma VPN/conexão de rota expressa com o Azure. Com o exemplo de aplicativo de Loja de Música, o site da Web é disponibilizado na Internet com um endereço IP público. Com acesso estabelecido, as conexões com o aplicativo e o acessa aos recursos de máquina virtual devem ser protegidos. Essa segurança de acesso é fornecida com um Grupo de Segurança de Rede. 

Este documento fornece detalhes sobre como o aplicativo de Loja de Música é protegido no modelo do Azure Resource Manager de exemplo. Todas as dependências e configurações exclusivas são realçadas. Para obter a melhor experiência, pré-implante uma instância da solução em sua assinatura do Azure e trabalhe com o modelo do Azure Resource Manager. O modelo completo pode ser encontrado aqui – [Implantação de Loja de Música no Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="public-ip-address"></a>Endereço IP público
Para fornecer acesso público a um recurso do Azure, um recurso de endereço IP público pode ser usado. O endereço IP público pode ser configurado com um endereço IP estático ou dinâmico. Se um endereço dinâmico for usado e a máquina virtual for interrompida e desalocada, os endereços serão removidos. Quando o computador for iniciado novamente, ele poderá ser atribuído um endereço IP público diferente. Para impedir a alteração de um endereço IP, um endereço IP reservado pode ser usado. 

Um endereço IP público pode ser adicionado a um modelo do Azure Resource Manager usando o Assistente para Adicionar Novos Recursos do Visual Studio ou inserindo JSON válido no modelo. 

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Endereço IP público](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L121).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicipaddressName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "public-ip-front"
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

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Associação de endereço IP público com balanceador de carga](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208).

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicipaddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

O endereço IP público como visto no portal do Azure. Observe que o endereço IP público está associado a um balanceador de carga, e não a uma máquina virtual. Balanceadores de carga de rede são detalhados no seguinte documento desta série.

![Endereço IP público](./media/virtual-machines-linux-dotnet-core/pubip.png)

Para obter mais informações sobre os endereços IP públicos do Azure, consulte [Endereços IP no Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

## <a name="network-security-group"></a>Grupo de Segurança de Rede
Depois de estabelecer acesso aos recursos do Azure, esse acesso deve ser limitado. Para máquinas virtuais do Azure, proteger o acesso é feito usando um grupo de segurança de rede. Com o exemplo de aplicativo de Loja de Música, todo o acesso à máquina virtual é restrito exceto pela porta 80 para o acesso http e a porta 22 para acesso do SSH. Um grupo de segurança de rede pode ser adicionado a um modelo do Azure Resource Manager usando o Assistente para Adicionar Novos Recursos do Visual Studio ou inserindo JSON válido no modelo.

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Grupo de segurança de rede](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L68).

```none
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('nsgfront')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "nsg-front"
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
}
```

Neste exemplo, o grupo de segurança de rede é associado com o objeto de sub-rede declarado no recurso de Rede Virtual. 

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Associação de grupo de segurança de rede com Rede Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L158).

```none
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
```

Aqui está a aparência do grupo de segurança de rede no portal do Azure. Observe que um NSG pode associar-se a um adaptador de sub-rede e/ou rede. Nesse caso, o NSG é associado a uma sub-rede. Nessa configuração, as regras de entrada se aplicam a todas as máquinas virtuais conectadas à sub-rede.

![Grupo de Segurança de Rede](./media/virtual-machines-linux-dotnet-core/nsg.png)

Para obter informações detalhadas sobre grupos de segurança de rede, leia [O que é um Grupo de Segurança de Rede](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/).

## <a name="next-step"></a>Próxima etapa
<hr>

[Etapa 3 – Disponibilidade e escala em modelos do Azure Resource Manager](virtual-machines-linux-dotnet-core-4-avalibility-scale.md)

<!--HONumber=Oct16_HO2-->


