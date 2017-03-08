---
title: Criar um ambiente Linux com a CLI do Azure 2.0 | Microsoft Docs
description: "Crie um armazenamento, uma VM Linux, uma rede virtual e uma sub-rede, um balanceador de carga, uma NIC, um IP público e um grupo de segurança de rede, tudo do zero usando a CLI do Azure 2.0."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/07/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: d4cff286de1abd492ce7276c300b50d71f06345b
ms.openlocfilehash: f07a326aa2fcd659f69265001293c9ed332bb842
ms.lasthandoff: 02/27/2017


---
# <a name="create-a-complete-linux-environment-with-the-azure-cli-20"></a>Criar um ambiente Linux completo com a CLI do Azure 2.0
Neste artigo, vamos criar uma rede simples com um balanceador de carga e com um par de VMs úteis para desenvolvimento e computação simples. Percorreremos o processo, comando por comando, até que você tenha duas VMs do Linux funcionais e seguras às quais possa se conectar de qualquer lugar na Internet. Em seguida, você poderá passar para redes e ambientes mais complexos. Este artigo fornece detalhes sobre como criar o ambiente com a CLI do Azure 2.0. Você também pode executar estas etapas com a [CLI do Azure 1.0](virtual-machines-linux-create-cli-complete-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Durante o processo, você entenderá sobre a hierarquia de dependência que o modelo de implantação do Gerenciador de Recursos lhe oferece e todos os recursos que proporciona. Após ver como o sistema é criado, você poderá recriá-lo muito mais rapidamente usando [modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Além disso, após aprender como as partes de seu ambiente se encaixam, fica mais fácil criar modelos para automatizá-las.

O ambiente contém:

* Duas VMs dentro de um conjunto de disponibilidade.
* Um balanceador de carga com uma regra de balanceamento de carga na porta 80.
* Regras de NSG (grupo de segurança de rede) para proteger sua VM contra tráfego indesejado.

![Visão geral do ambiente básico](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

## <a name="quick-commands"></a>Comandos rápidos
Se você precisar realizar rapidamente a tarefa, a seção a seguir detalha a base de dados de comandos para carregar uma VM no Azure. Mais informações detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, começando [aqui](#detailed-walkthrough).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem `myResourceGroup`, `mystorageaccount` e `myVM`.

Para criar esse ambiente personalizado, é necessário ter a [CLI do Azure 2.0](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando [az login](/cli/azure/#login).

Primeiro, crie o grupo de recursos com [az group create](/cli/azure/group#create). O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westeurope`:

```azurecli
az group create --name myResourceGroup --location westeurope
```

Esta próxima etapa é opcional. A ação padrão ao criar uma VM com a CLI do Azure 2.0 é usar o Azure Managed Disks. Para saber mais sobre Azure Managed Disks, veja [Visão geral dos Azure Managed Disks](../storage/storage-managed-disks-overview.md). Se, em vez disso, você quiser usar discos não gerenciados, crie uma conta de armazenamento com [az storage account create](/cli/azure/storage/account#create). O exemplo a seguir cria uma conta de armazenamento chamada `mystorageaccount`. (O nome da conta de armazenamento deve ser exclusivo; portanto, forneça seu próprio nome exclusivo.)

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet#create). O exemplo a seguir cria uma rede virtual chamada `myVnet` e uma sub-rede chamada `mySubnet`:

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

Crie um IP público com [az network public-ip create](/cli/azure/network/public-ip#create). O exemplo a seguir cria um IP público chamado `myPublicIP` com o nome DNS de `mypublicdns`. (O nome DNS deve ser exclusivo; portanto, forneça seu próprio nome exclusivo.)

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

Crie o balanceador de carga com [az network lb create](/cli/azure/network/lb#create). O exemplo a seguir:

- cria um balanceador de carga chamado `myLoadBalancer`
- associa o IP público `myPublicIP`
- cria um pool de IPs de front-end chamado `mySubnetPool`
- cria um pool de IPs de back-end chamado `myBackEndPool`

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool
```

Crie regras de NAT (conversão de endereço de rede) de entrada de SSH para o balanceador de carga com [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create). O exemplo a seguir cria duas regras de balanceador de carga, `myLoadBalancerRuleSSH1` e `myLoadBalancerRuleSSH2`:

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

Cria a investigação de integridade do balanceador de carga com [az network lb probe create](/cli/azure/network/lb/probe#create). O exemplo a seguir cria uma investigação de TCP chamada `myHealthProbe`:

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

Crie as regras de NAT de entrada da Web para o balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule#create). O exemplo a seguir cria uma regra do balanceador de carga chamada `myLoadBalancerRuleWeb` e a associa com a investigação `myHealthProbe`:

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

Verifique o balanceador de carga, os pools de IPs e as regras de NAT com [az network lb show](/cli/azure/network/lb#show):

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

Crie o grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg#create). O exemplo a seguir cria um grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Adicione duas regras de entrada para o grupo de segurança de rede com [az network nsg rule create](/cli/azure/network/nsg/rule#create). O exemplo a seguir cria duas regras, `myNetworkSecurityGroupRuleSSH` e `myNetworkSecurityGroupRuleHTTP`:

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 22 \
  --access allow
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 80 \
  --access allow
```

Crie a primeira NIC (placa de adaptador de rede) com [az network nic create](/cli/azure/network/nic#create). O exemplo a seguir cria uma NIC chamada `myNic1` e a anexa ao balanceador de carga `myLoadBalancer` e aos pools apropriados, além de anexá-la ao `myNetworkSecurityGroup`:

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

Crie a segunda NIC, novamente com **az network nic create**. O exemplo a seguir cria uma NIC chamada `myNic2`:

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```

Crie a conjunto de disponibilidade com [az vm availability-set create](/cli/azure/vm/availability-set#create). O exemplo a seguir cria um conjunto de disponibilidade chamado `myAvailabilitySet`:

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet \
  --platform-fault-domain-count 3 --platform-update-domain-count 2
```

Crie a primeira VM do Linux com [az vm create](/cli/azure/vm#create). O exemplo a seguir cria uma VM chamada `myVM1` usando o Azure Managed Disks. Se você quiser usar discos não gerenciados, consulte a observação adicional abaixo.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Se você usar Azure Managed Disks, ignore esta etapa. Se você quiser usar discos não gerenciados e tiver criado uma conta de armazenamento nas etapas anteriores, adicione outros parâmetros ao comando de continuação. Adicione os seguintes parâmetros ao comando de continuação para criar os discos não gerenciados na conta de armazenamento denominada `mystorageaccount`: 

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
```

Crie a segunda VM do Linux, novamente com **az vm create**. O exemplo a seguir cria uma VM chamada `myVM2`:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Novamente, se você não usar o Azure Managed Disks padrão, adicione os seguintes parâmetros ao comando de continuação para criar os discos não gerenciados na conta de armazenamento denominada `mystorageaccount`:

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
``` 

Verifique se tudo foi criado corretamente com [az vm show](/cli/azure/vm#show):

```azurecli
az vm show --resource-group myResourceGroup --name myVM1
az vm show --resource-group myResourceGroup --name myVM2
```

Exporte seu novo ambiente para um modelo com [az group export](/cli/azure/group#export) para recriar novas instâncias rapidamente:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado
As etapas detalhadas a seguir explicam o que cada comando faz enquanto você cria seu ambiente. Esses conceitos são úteis durante a criação de seus próprios ambientes personalizados para desenvolvimento ou produção.

Certifique-se de que você instalou a versão mais recente da [CLI do Azure 2.0](/cli/azure/install-az-cli2) e entrou em uma conta do Azure com [az login](/cli/azure/#login).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem `myResourceGroup`, `mystorageaccount` e `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Criar grupos de recursos e escolher locais de implantação
Os grupos de recursos do Azure são entidades de implantação lógica que contêm metadados e informações de configuração para habilitar o gerenciamento lógico de implantações de recursos. Crie o grupo de recursos com [az group create](/cli/azure/group#create). O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westeurope`:

```azurecli
az group create --name myResourceGroup --location westeurope
```

Por padrão, a saída é em JSON (JavaScript Object Notation). Para gerar a saída como uma lista ou tabela, por exemplo, use [az configure --output](/cli/azure/#configure). Você também pode adicionar `--output` a qualquer comando para realizar uma alteração uma única vez no formato da saída. O seguinte exemplo mostra a saída JSON do comando **az group create**:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Esta próxima etapa é opcional. A ação padrão ao criar uma VM com a CLI do Azure 2.0 é usar o Azure Managed Disks. Esses discos são tratados pela plataforma do Azure e não exigem nenhuma preparação ou local para armazenamento. Para saber mais sobre Azure Managed Disks, veja [Visão geral dos Azure Managed Disks](../storage/storage-managed-disks-overview.md). Acesse [Criar uma rede e sub-rede virtuais](#create-a-virtual-network-and-subnet) se você quiser usar o Azure Managed Disks. 

Você precisa de contas de armazenamento para seus discos de VM e quaisquer discos de dados adicionais que deseje adicionar.

Aqui, usamos [az storage account create](/cli/azure/storage/account#create) e passamos a localização da conta, o grupo de recursos que a controla e o tipo de suporte de armazenamento que você deseja. O exemplo a seguir cria uma conta de armazenamento chamada `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Saída:

```json
{
  "accessTier": null,
  "creationTime": "2016-12-07T17:59:50.090092+00:00",
  "customDomain": null,
  "encryption": null,
  "id": "/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "westeurope",
  "name": "mystorageaccount",
  "primaryEndpoints": {
    "blob": "https://mystorageaccount.blob.core.windows.net/",
    "file": "https://mystorageaccount.file.core.windows.net/",
    "queue": "https://mystorageaccount.queue.core.windows.net/",
    "table": "https://mystorageaccount.table.core.windows.net/"
  },
  "primaryLocation": "westeurope",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "secondaryEndpoints": null,
  "secondaryLocation": null,
  "sku": {
    "name": "Standard_LRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "Available",
  "statusOfSecondary": null,
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

Para investigar a conta de armazenamento usando a CLI, primeiro, você precisa definir os nomes e as chaves da conta. Use [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Substitua o nome da conta de armazenamento no exemplo a seguir pelo nome que você escolher:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(az storage account show-connection-string --resource-group myResourceGroup --name mystorageaccount --query connectionString)"
```

Em seguida, você pode exibir suas informações de armazenamento com [az storage container list](/cli/azure/storage/container#list):

```azurecli
az storage container list
```

Saída:

```azurecli
[
  {
    "metadata": null,
    "name": "vhds",
    "properties": {
      "etag": "\"0x8D41F912D472F94\"",
      "lastModified": "2016-12-08T17:39:35+00:00",
      "lease": {
        "duration": null,
        "state": null,
        "status": null
      },
      "leaseDuration": "infinite",
      "leaseState": "leased",
      "leaseStatus": "locked"
    }
  }
]
```

## <a name="create-a-virtual-network-and-subnet"></a>Criar a rede virtual e a sub-rede
Em seguida, você precisará criar uma rede virtual em execução no Azure e uma sub-rede na qual possa criar suas VMs. O exemplo a seguir usa [az network vnet create](/cli/azure/network/vnet#create) para criar uma rede virtual chamada `myVnet` com o prefixo de endereço `192.168.0.0/16`, e uma sub-rede chamada `mySubnet` com o prefixo de endereço de sub-rede `192.168.1.0/24`:

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefix 192.168.0.0/16 \
  --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

A saída mostra a sub-rede logicamente criada dentro da rede virtual:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "westeurope",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Criar um endereço IP público
Agora, vamos criar o PIP (endereço IP público) que atribuiremos ao seu balanceador de carga. Ele permite que você se conecte às suas VMs a partir da Internet usando o comando [az network public-ip create](/cli/azure/network/public-ip#create). Como o endereço padrão é dinâmico, criaremos uma entrada DNS nomeada no domínio **cloudapp.azure.com** usando a opção `--domain-name-label`. O exemplo a seguir cria um IP público chamado `myPublicIP` com o nome DNS de `mypublicdns`. Como o nome DNS deve ser exclusivo, forneça seu próprio nome DNS exclusivo:

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

Saída:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.westeurope.cloudapp.azure.com",
      "reverseFqdn": ""
    },
    "idleTimeoutInMinutes": 4,
    "ipAddress": "52.174.48.109",
    "provisioningState": "Succeeded",
    "publicIPAllocationMethod": "Static",
    "resourceGuid": "78218510-ea54-42d7-b2c2-44773fc14af5"
  }
}
```

O recurso de endereço IP público foi alocado logicamente, mas ainda não há um endereço específico atribuído. Para obter um endereço IP, você precisará de um balanceador de carga, que nós ainda não criamos.

## <a name="create-a-load-balancer-and-ip-pools"></a>Criar um balanceador de carga e pools de IPs
Quando você cria um balanceador de carga, ele permite que você distribua o tráfego entre várias VMs. Ele também fornece redundância para seu aplicativo ao executar várias VMs que respondem às solicitações dos usuários em caso de manutenção ou cargas pesadas. O exemplo a seguir usa [az network lb create](/cli/azure/network/lb#create) para criar um balanceador de carga chamado `myLoadBalancer`, um pool de IPs front-end denominado `myFrontEndPool` e conecta o recurso `myPublicIP`:

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool
```

Saída:

```json
{
  "loadBalancer": {
    "backendAddressPools": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
        "name": "myLoadBalancerbepool",
        "properties": {
          "provisioningState": "Succeeded"
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "frontendIPConfigurations": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
        "name": "myFrontEndPool",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "publicIPAddress": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "inboundNatPools": [],
    "inboundNatRules": [],
    "loadBalancingRules": [],
    "outboundNatRules": [],
    "probes": [],
    "provisioningState": "Succeeded",
    "resourceGuid": "f4879d84-5ae8-4e06-8b9b-1419baa875d9"
  }
}
```

Observe como usamos a opção `--public-ip-address` para passar o `myPublicIP` que criamos anteriormente. A atribuição do endereço IP público ao balanceador de carga permite que você acesse suas VMs pela Internet.

Usaremos o pool de back-end como uma localização à qual nossas VMs se conectarão. Dessa forma, o tráfego pode fluir por meio do balanceador de carga para as VMs. Vamos criar o pool de IPs para nosso tráfego de back-end com [az network lb address-pool create](/cli/azure/network/lb/address-pool#create). O exemplo a seguir cria um pool de back-end chamado `myBackEndPool`:

```azurecli
az network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Saída em trechos:

```json
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
```


## <a name="create-load-balancer-nat-rules"></a>Criar regras NAT do balanceador de carga
Para fazer com que o tráfego flua pelo balanceador de carga, precisamos criar regras de conversão de endereço de rede (NAT) que especifiquem as ações de entrada ou de saída. Você pode especificar o protocolo a ser usado e mapear portas externas para portas internas conforme desejado. Para nosso ambiente, vamos criar algumas regras que permitem que o SSH passe pelo balanceador de carga e chegue às VMs com [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create). Vamos configurar as portas TCP 4222 e 4223 para direcionar para a porta TCP 22 em nossas VMs (que criaremos posteriormente). O exemplo a seguir cria uma regra chamada `myLoadBalancerRuleSSH1` para mapear a porta TCP 4222 para a porta 22:

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
```

Saída:

```json
{
  "backendIpConfiguration": null,
  "backendPort": 22,
  "enableFloatingIp": false,
  "etag": "W/\"72843cf8-b5fb-4655-9ac8-9cbbbbf1205a\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 4222,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
  "idleTimeoutInMinutes": 4,
  "name": "myLoadBalancerRuleSSH1",
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

Repita o procedimento para sua segunda regra de NAT para o SSH. O exemplo a seguir cria uma regra chamada `myLoadBalancerRuleSSH2` para mapear a porta TCP 4223 para a porta 22:

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

## <a name="create-a-load-balancer-health-probe"></a>Criar uma investigação de integridade do balanceador de carga
Uma investigação de integridade verifica periodicamente as máquinas virtuais que estão por trás de nosso balanceador de carga para garantir que elas estejam em operação e estejam respondendo às solicitações da forma definida. Se não estiverem, elas serão removidas da operação para garantir que os usuários não sejam direcionados a elas. Você pode definir verificações personalizadas para a investigação de integridade, bem como valores de tempo limite e intervalos. Para obter mais informações sobre investigações de integridade, consulte [Investigações do Balanceador de Carga](../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). O exemplo a seguir usa [az network lb probe create](/cli/azure/network/lb/probe#create) para criar uma investigação de integridade TCP chamada `myHealthProbe`:

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

Saída:

```json
{
  "etag": "W/\"757018f6-b70a-4651-b717-48b511d82ba0\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
  "intervalInSeconds": 15,
  "loadBalancingRules": null,
  "name": "myHealthProbe",
  "numberOfProbes": 4,
  "port": 80,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "requestPath": null,
  "resourceGroup": "myResourceGroup"
}
```

Aqui, especificamos um intervalo de 15 segundos para nossas verificações de integridade. Podemos perder um máximo de quatro investigações (um minuto) antes que o balanceador de carga considere que o host não está mais funcionando.

Também vamos aproveitar e criar uma regra de NAT para a porta TCP 80 para tráfego da Web, vinculando a regra aos nossos pools de IPs. Se vincularmos a regra a um pool de IPs, em vez de vincular a regra às nossas VMs individualmente, poderemos adicionar ou remover VMs do pool de IPs. O balanceador de carga ajusta automaticamente o fluxo de tráfego. O exemplo a seguir usa [az network lb rule create](/cli/azure/network/lb/rule#create) para criar uma regra chamada `myLoadBalancerRuleWeb` para mapear a porta TCP 80 para a porta 80, e conecta a investigação de integridade chamada `myHealthProbe`:

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

Saída:

```json
{
  "backendAddressPool": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "backendPort": 80,
  "enableFloatingIp": false,
  "etag": "W/\"f0d77680-bf42-4d11-bfab-5d2c541bee56\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 80,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
  "idleTimeoutInMinutes": 4,
  "loadDistribution": "Default",
  "name": "myLoadBalancerRuleWeb",
  "probe": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
    "resourceGroup": "myResourceGroup"
  },
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="verify-the-load-balancer"></a>Verificar o balanceador de carga
Agora, a configuração do balanceador de carga está concluída. Estas são as etapas que você seguiu:

1. Você criou um balanceador de carga.
2. Você criou um pool de IPs de front-end e atribuiu um IP público a ele.
3. Você criou um pool de IPs de back-end ao qual as VMs podem se conectar.
4. Você criou regras NAT que permitem o SSH para as VMs para gerenciamento, junto com uma regra que permite a porta TCP 80 para nosso aplicativo Web.
5. Você adicionou uma investigação de integridade para verificar as VMs periodicamente. Essa investigação de integridade que os usuários não tentem acessar uma VM que não está mais funcionando ou fornecendo conteúdo.

Vamos ver como o seu balanceador de carga está agora com [az network lb show](/cli/azure/network/lb#show):

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

Saída:

```json
{
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
  "frontendIpConfigurations": [
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/LoadBalancerFrontEnd",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "LoadBalancerFrontEnd",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/PublicIPmyLoadBalancer",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    },
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "myFrontEndPool",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "inboundNatPools": [],
  "inboundNatRules": [],
  "loadBalancingRules": [],
  "location": "westeurope",
  "name": "myLoadBalancer",
  "outboundNatRules": [],
  "probes": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "b5815801-b53d-4c22-aafc-2a9064f90f3c",
  "tags": {},
  "type": "Microsoft.Network/loadBalancers"
}
```

## <a name="create-a-network-security-group-and-rules"></a>Criar um grupo de segurança de rede e suas regras
Agora, criamos um grupo de segurança de rede e as regras de entrada que regem o acesso à NIC. Um grupo de segurança de rede pode ser aplicado a uma NIC ou sub-rede. Defina regras para controlar o fluxo de tráfego dentro e fora de suas VMs. O exemplo a seguir usa [az network nsg create](/cli/azure/network/nsg#create) para criar um grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Vamos adicionar a regra de entrada para o NSG com [az network nsg rule create](/cli/azure/network/nsg/rule#create) para permitir conexões de entrada na porta 22 (para dar suporte ao SSH). O seguinte exemplo cria uma regra chamada `myNetworkSecurityGroupRuleSSH` para permitir o TCP na porta 22:

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 22 --access allow
```

Agora, vamos adicionar a regra de entrada para o NSG para permitir conexões de entrada na porta 80 (para dar suporte ao tráfego da Web). O seguinte exemplo cria uma regra chamada `myNetworkSecurityGroupRuleHTTP` para permitir o TCP na porta 80:

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 80 --access allow
```

> [!NOTE]
> A regra de entrada é um filtro para conexões de rede de entrada. Neste exemplo, associaremos o NSG à NIC virtual das VMs, o que significa que qualquer solicitação para a porta 22 será passada para a NIC na nossa VM. Essa regra de entrada é sobre uma conexão de rede e não sobre um ponto de extremidade, que seria o caso em implantações clássicas. Para abrir uma porta, você deve deixar `--source-port-range` definido como “\*” (o valor padrão) para aceitar solicitações de entrada de **qualquer** porta de solicitação. Normalmente, as portas são dinâmicas.

Examine o grupo de segurança de rede e as regras com [az network nsg show](/cli/azure/network/nsg#show):

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Saída:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBound",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "westeurope",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "79c2c293-ee3e-4616-bf9c-4741ff1f708a",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleHTTP",
      "name": "myNetworkSecurityGroupRuleHTTP",
      "priority": 1001,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": {},
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Criar uma NIC para usar com a VM Linux
As NICs estão disponíveis por meio de programação porque você pode aplicar regras ao seu uso. Você também pode ter mais de uma. No comando [az network nic create](https://docs.microsoft.com/en-us/cli/azure/network/nic#create) a seguir, você vincula a NIC ao pool de IPs de back-end da carga e a associa à regra de NAT para permitir o tráfego SSH e o grupo de segurança de rede.

O exemplo a seguir cria uma NIC chamada `myNic1`:

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

Saída:

```json
{
  "newNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": []
    },
    "enableIPForwarding": false,
    "ipConfigurations": [
      {
        "etag": "W/\"a76b5c0d-14e1-4a99-afd4-5cd8ac0465ca\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1",
        "name": "ipconfig1",
        "properties": {
          "loadBalancerBackendAddressPools": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "primary": true,
          "privateIPAddress": "192.168.1.4",
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "subnet": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "networkSecurityGroup": {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "resourceGroup": "myResourceGroup"
    },
    "provisioningState": "Succeeded",
    "resourceGuid": "838977cb-cf4b-4c4a-9a32-0ddec7dc818b"
  }
}
```

Agora, vamos criar a segunda NIC, vinculando-a novamente ao nosso pool de IPs de back-end. Desta vez, a segunda regra NAT permite o tráfego SSH. O exemplo a seguir cria uma NIC chamada `myNic2`:

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```


## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade
Conjuntos de disponibilidade ajudam a difundir suas VMs em domínios de falha e domínios de atualização. Vamos criar um conjunto de disponibilidade para suas VMs com [az vm availability-set create](/cli/azure/vm/availability-set#create). O exemplo a seguir cria um conjunto de disponibilidade chamado `myAvailabilitySet`:

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet \
  --platform-fault-domain-count 3 --platform-update-domain-count 2
```

Os domínios de falha definem um agrupamento de máquinas virtuais que compartilham uma mesma fonte de alimentação e um mesmo comutador de rede. Por padrão, as máquinas virtuais que são configuradas dentro do seu conjunto de disponibilidade são separadas entre até três domínios de falha. A ideia é que um problema de hardware em um desses domínios de falha não afete todas as VMs que estiverem executando seu aplicativo. O Azure distribui automaticamente as VMs entre os domínios de falha ao colocá-las em um conjunto de disponibilidade.

Domínios de atualização indicam grupos de máquinas virtuais e o hardware físico subjacente que podem ser reinicializados ao mesmo tempo. A ordem de reinicialização dos domínios de atualização pode não ser sequencial durante a manutenção planejada, mas apenas uma atualização será reinicializada por vez. Mais uma vez, o Azure distribui automaticamente as VMs entre os domínios de atualização ao colocá-las em um site de disponibilidade.

Leia mais sobre como [gerenciar a disponibilidade de VMs](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="create-the-linux-vms"></a>Criar as VMs Linux
Você criou os recursos de rede para dar suporte a VMs que podem ser acessadas pela Internet. Agora, vamos criar essas VMs e protegê-las com uma chave SSH que não tem senha. Nesse caso, vamos criar uma VM do Ubuntu com base no LTS mais recente. Localizaremos as informações dessa imagem usando [az vm image list](/cli/azure/vm/image#list), conforme descrito em [localização de imagens de VM do Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Também especificamos uma chave SSH a ser usada para autenticação. Se você não tiver uma chave SSH, poderá criá-la usando [estas instruções](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Como alternativa, você pode usar o método `--admin-password` para autenticar suas conexões SSH após a criação da VM. Esse método normalmente é menos seguro.

Criamos a VM reunindo todos os nossos recursos e informações com o comando [az vm create](/cli/azure/vm#create). O exemplo a seguir cria uma VM chamada `myVM1` usando o Azure Managed Disks. Se você quiser usar discos não gerenciados, consulte a observação adicional abaixo.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Se você usar Azure Managed Disks, ignore esta etapa. Se você quiser usar discos não gerenciados e tiver criado uma conta de armazenamento nas etapas anteriores, adicione outros parâmetros ao comando de continuação. Adicione os seguintes parâmetros ao comando de continuação para criar os discos não gerenciados na conta de armazenamento denominada `mystorageaccount`: 

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
```

Saída:

```json
{
  "fqdn": "",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1",
  "macAddress": "",
  "privateIpAddress": "",
  "publicIpAddress": "",
  "resourceGroup": "myResourceGroup"
}
```

Você pode se conectar à sua VM imediatamente usando suas chaves SSH padrão. Certifique-se de especificar a porta apropriada, uma vez que estamos passando pelo balanceador de carga. (Para nossa primeira VM, configuramos a regra NAT para encaminhar a porta 4222 para nossa VM.)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222 -i ~/.ssh/id_rsa.pub
```

Saída:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Prossiga e crie sua segunda VM da mesma maneira:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Novamente, se você não usar o Azure Managed Disks padrão, adicione os seguintes parâmetros ao comando de continuação para criar os discos não gerenciados na conta de armazenamento denominada `mystorageaccount`:

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
``` 

Neste ponto, você está executando suas VMs Ubuntu atrás de um balanceador de carga no Azure, em que só pode fazer logon com seu par de chaves SSH (porque as senhas estão desabilitadas). Você pode instalar nginx ou httpd e implantar um aplicativo Web para ver o fluxo do tráfego por meio do balanceador de carga para ambas as VMs.


## <a name="export-the-environment-as-a-template"></a>Exportar o ambiente como um modelo
Agora que criou esse ambiente, e se você quiser criar um ambiente de desenvolvimento adicional usando os mesmos parâmetros ou um ambiente de produção corresponde? O Gerenciador de Recursos usa modelos JSON que definem todos os parâmetros para seu ambiente. Crie ambientes inteiros fazendo referência a esse modelo JSON. Você pode [criar modelos JSON manualmente](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou exportar um ambiente existente para criar o modelo JSON para você. Use [az group export](/cli/azure/group#export) para exportar seu grupo de recursos da seguinte maneira:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Esse comando cria o arquivo `myResourceGroup.json` no diretório de trabalho atual. Ao criar um ambiente com base nesse modelo, você receberá uma solicitação para fornecer todos os nomes de recursos, incluindo os nomes do balanceador de carga, das interfaces de rede ou VMs. Você pode popular esses nomes em seu arquivo de modelo adicionando o parâmetro `--include-parameter-default-value` ao comando **az group export** mostrado anteriormente. Edite seu modelo JSON para especificar os nomes dos recursos, ou [crie um arquivo parameters.json](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que especifica os nomes dos recursos.

Para criar um ambiente usando seu modelo, use [az group deployment create](/cli/azure/group/deployment#create) da seguinte maneira:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Pode ser útil ler [mais detalhes sobre a implantações de modelos](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Saiba como atualizar ambientes gradativamente, usar o arquivo de parâmetros e acessar os modelos de uma única localização de armazenamento.

## <a name="next-steps"></a>Próximas etapas
Agora, você está pronto para começar a trabalhar com vários componentes de rede e VMs. Você pode usar esse ambiente de exemplo para criar seu aplicativo usando os principais componentes introduzidos aqui.

