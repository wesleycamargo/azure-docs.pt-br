---
title: Implantar um aplicativo de pilha dupla de IPv6 na rede virtual do Azure - modelo do Gerenciador de recursos (visualização)
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implanta um aplicativo de pilha dupla de IPv6 na rede virtual do Azure usando modelos de VM do Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: ae90bc4a12763803f38224d917c4644a68ae7d6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130832"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---template-preview"></a>Implantar um aplicativo de pilha dupla do IPv6 no Azure - modelo (visualização)

Este artigo fornece uma lista de tarefas de configuração de IPv6 com a parte do modelo de VM do Azure Resource Manager que se aplica a. Use o modelo descrito neste artigo para implantar um aplicativo de pilha dual (IPv4 + IPv6) no Azure que inclua uma pilha dupla rede virtual com sub-redes IPv4 e IPv6, um balanceador de carga com configurações de front-end dual (IPv4 + IPv6), VMs com NICs que têm uma dupla de IP configuração do grupo de segurança de rede e IPs públicos. 

## <a name="required-configurations"></a>Configurações necessárias

Procure as seções de modelo no modelo para ver onde eles devem ocorrer.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>AddressSpace de IPv6 para a rede virtual

Seção de modelo para adicionar:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Subrede IPv6 dentro o addressSpace da rede virtual de IPv6

Seção de modelo para adicionar:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Configuração de IPv6 para o NIC

Seção de modelo para adicionar:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>Regras NSG (grupo) de segurança de rede IPv6

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Configuração condicional

Se você estiver usando um dispositivo de rede virtual, adicione rotas IPv6 na tabela de rotas. Caso contrário, essa configuração é opcional.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Configuração opcional

### <a name="ipv6-internet-access-for-the-virtual-network"></a>Acesso à Internet IPv6 para a rede virtual

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Endereços IP públicos do IPv6

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6 de Front-end de Balanceador de carga

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Pool de endereços de Back-end do IPv6 para o balanceador de carga

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>Regras para associar as portas de entrada e saídas do balanceador de carga do IPv6

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Exemplo de JSON do modelo VM
Clique em [aqui](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/) para implantar um aplicativo de pilha dupla de IPv6 na rede virtual do Azure usando o modelo do Resource Manager.

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar detalhes sobre os preços para [endereços IP públicos](https://azure.microsoft.com/pricing/details/ip-addresses/), [largura de banda de rede](https://azure.microsoft.com/pricing/details/bandwidth/), ou [balanceador de carga](https://azure.microsoft.com/pricing/details/load-balancer/).
