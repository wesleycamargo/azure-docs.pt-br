
---
title: Criar um ambiente Linux completo usando a CLI do Azure | Microsoft Docs
description: Crie um armazenamento, uma VM Linux, uma rede virtual e uma sub-rede, um balanceador de carga, uma NIC, um IP público e um grupo de segurança de rede, tudo do zero usando a CLI do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: iainfou

---
# Criar um ambiente Linux completo usando a CLI do Azure
Neste artigo, vamos criar uma rede simples com um balanceador de carga e com um par de VMs úteis para desenvolvimento e computação simples. Percorreremos o processo, comando por comando, até que você tenha duas VMs do Linux funcionais e seguras às quais possa se conectar de qualquer lugar na Internet. Em seguida, você poderá passar para redes e ambientes mais complexos.

Durante o processo, você entenderá sobre a hierarquia de dependência que o modelo de implantação do Gerenciador de Recursos lhe oferece e todos os recursos que proporciona. Após ver como o sistema é criado, você poderá recriá-lo muito mais rapidamente usando [modelos do Azure Resource Manager](../resource-group-authoring-templates.md). Além disso, após aprender como as partes de seu ambiente se encaixam, fica mais fácil criar modelos para automatizá-las.

O ambiente contém:

* Duas VMs dentro de um conjunto de disponibilidade.
* Um balanceador de carga com uma regra de balanceamento de carga na porta 80.
* Regras de NSG (grupo de segurança de rede) para proteger sua VM contra tráfego indesejado.

![Visão geral do ambiente básico](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

Para criar esse ambiente personalizado, você precisará da versão mais recente da [CLI do Azure](../xplat-cli-install.md) no modo do Gerenciador de Recursos (`azure config mode arm`). Você também precisará de uma ferramenta de análise de JSON. Este exemplo usa [jq](https://stedolan.github.io/jq/).

## Comandos rápidos
Você pode usar os seguintes comandos rápidos para criar seu ambiente personalizado. Para saber mais sobre o que cada comando está fazendo à medida que você cria o ambiente, leia as [etapas detalhadas do seguinte passo a passo](#detailed-walkthrough).

Crie o grupo de recursos:

```bash
azure group create TestRG -l westeurope
```

Verificar o grupo de recursos usando o analisador JSON:

```bash
azure group show TestRG --json | jq '.'
```

Criar a conta de armazenamento:

```bash
azure storage account create -g TestRG -l westeurope --kind Storage --sku-name GRS computeteststore
```

Verificar a conta de armazenamento usando o analisador JSON:

```bash
azure storage account show -g TestRG computeteststore --json | jq '.'
```

Crie a rede virtual:

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Criar a sub-rede:

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Verificar a rede virtual e a sub-rede usando o analisador JSON:

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Criar um IP público:

```bash
azure network public-ip create -g TestRG -n TestLBPIP -l westeurope -d testlb -a static -i 4
```

Criar o balanceador de carga:

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Criar um pool de IPs de front-end para o balanceador de carga e associar o IP público:

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Criar o pool de IPs de back-end para o balanceador de carga:

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Criar regras NAT de entrada SSH para o balanceador de carga:

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

Criar as regras NAT de entrada Web para o balanceador de carga:

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Criar a investigação de integridade do balanceador de carga:

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "tcp" -i 15 -c 4
```

Verificar o balanceador de carga, os pools de IPs e as regras NAT usando o analisador JSON:

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Criar a primeira NIC (placa de interface de rede):

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
```

Criar a segunda NIC:

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
```

Verificar as NICs usando o analisador JSON:

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
azure network nic show TestRG LB-NIC2 --json | jq '.'
```

Criar o NSG:

```bash
azure network nsg create -g TestRG -n TestNSG -l westeurope
```

Adicionar as regras de entrada para o NSG:

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow -g TestRG -a TestNSG -n SSHRule
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

Verificar o NSG e as regras de entrada usando o analisador JSON:

```bash
azure network nsg show -g TestRG -n TestNSG --json | jq '.'
```

Associe o NSG às NICs:

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

Criar o conjunto de disponibilidade:

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Criar a primeira VM Linux:

```bash
azure vm create \
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Criar a segunda VM Linux:

```bash
azure vm create \
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Usar o analisador JSON para verificar tudo o que foi criado:

```bash
azure vm show -g TestRG -n TestVM1 --json | jq '.'
azure vm show -g TestRG -n TestVM2 --json | jq '.'
```

Exportar o ambiente que você criou para um modelo para recriar novas instâncias rapidamente:

```bash
azure resource export TestRG
```

## Passo a passo detalhado
As etapas detalhadas a seguir explicam o que cada comando faz enquanto você cria seu ambiente. Esses conceitos são úteis durante a criação de seus próprios ambientes personalizados para desenvolvimento ou produção.

## Criar grupos de recursos e escolher locais de implantação
Os grupos de recursos do Azure são entidades de implantação lógica que contêm metadados e informações de configuração para habilitar o gerenciamento lógico de implantações de recursos.

```bash
azure group create TestRG westeurope
```

Saída:

```bash                        
info:    Executing command group create
+ Getting resource group TestRG
+ Creating resource group TestRG
info:    Created resource group TestRG
data:    Id:                  /subscriptions/<yoursub>/resourceGroups/TestRG
data:    Name:                TestRG
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## Criar uma conta de armazenamento
Você precisa de contas de armazenamento para seus discos de VM e quaisquer discos de dados adicionais que deseje adicionar. Você cria contas de armazenamento quase que imediatamente depois de criar grupos de recursos.

Aqui, usamos o comando `azure storage account create`, passando a localização da conta, o grupo de recursos que a controla e o tipo de suporte de armazenamento que você deseja.

```bash
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--kind Storage --sku-name GRS \
computeteststore
```

Saída:

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
ahmet•~/workspace/keygen» azure group show testrg
info:    Executing command group show
+ Listing resource groups
+ Listing resources for the group
data:    Id:                  /subscriptions/<guid>/resourceGroups/TestRG
data:    Name:                TestRG
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:    Resources:
data:
data:      Id      : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore
data:      Name    : computeteststore
data:      Type    : storageAccounts
data:      Location: westeurope
data:      Tags    :
data:
data:    Permissions:
data:      Actions: *
data:      NotActions:
data:
info:    group show command OK
```

Para examinar nosso grupo de recursos usando o comando `azure group show`, vamos usar a ferramenta [jq](https://stedolan.github.io/jq/) junto com a opção `--json` da CLI do Azure. (Você pode usar **jsawk** ou qualquer biblioteca de linguagem que preferir para analisar o JSON.)

```bash
azure group show TestRG --json | jq                                                                                      
```


Saída:

```bash
{
  "tags": {},
  "id": "/subscriptions/<guid>/resourceGroups/TestRG",
  "name": "TestRG",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
      "name": "computeteststore",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Para investigar a conta de armazenamento usando a CLI, primeiro você precisa definir os nomes e as chaves da conta usando uma variação do comando a seguir. Substitua o nome da conta de armazenamento no exemplo a seguir pelo nome que você escolher:

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"
```

Em seguida, você poderá exibir as informações de armazenamento facilmente:

```bash
azure storage container list
```

Saída:

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## Criar a rede virtual e a sub-rede
Em seguida, você precisará criar uma rede virtual em execução no Azure e uma sub-rede na qual possa instalar suas VMs.

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Saída:

```bash
info:    Executing command network vnet create
+ Looking up virtual network "TestVNet"
+ Creating virtual network "TestVNet"
+ Loading virtual network state
data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
data:    Name                            : TestVNet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Novamente, vamos usar a opção --json `azure group show` e **jq** para ver como estamos criando nossos recursos. Agora, temos um recurso `storageAccounts` e um recurso `virtualNetworks`.

```bash
azure group show TestRG --json | jq '.'
```

Saída:

```bash
{
  "tags": {},
  "id": "/subscriptions/<guid>/resourceGroups/TestRG",
  "name": "TestRG",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
      "name": "TestVNet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
      "name": "computeteststore",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Agora vamos criar uma sub-rede na rede virtual `TestVnet` na qual as VMs são implantadas. Usamos o comando `azure network vnet subnet create`, em conjunto com os recursos que já criamos: o grupo de recursos `TestRG` e a rede virtual `TestVNet`. Adicionamos o nome da sub-rede `FrontEnd` e o prefixo de endereço da sub-rede `192.168.1.0/24`, da seguinte maneira:

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Saída:

```bash
info:    Executing command network vnet subnet create
+ Looking up the subnet "FrontEnd"
+ Creating subnet "FrontEnd"
+ Looking up the subnet "FrontEnd"
data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : FrontEnd
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Como a sub-rede está logicamente dentro da rede virtual, vamos buscar as informações da sub-rede com um comando ligeiramente diferente. O comando que usaremos é `azure network vnet show`, mas continuaremos examinando a saída JSON usando **jq**.

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Saída:

```bash
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "FrontEnd",
      "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
  "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
  "name": "TestVNet",
  "location": "westeurope"
}
```

## Criar um PIP (endereço IP público)
Agora, vamos criar o PIP (endereço IP público) que atribuiremos ao seu balanceador de carga. Ele permite que você conecte suas VMs da Internet usando o comando `azure network public-ip create`. Como o endereço padrão é dinâmico, criaremos uma entrada DNS nomeada no domínio **cloudapp.azure.com** usando a opção `-d testsubdomain`.

```bash
azure network public-ip create -d testsubdomain TestRG TestPIP westeurope
```

Saída:

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "TestPIP"
+ Creating public ip address "TestPIP"
+ Looking up the public ip "TestPIP"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
data:    Name                            : TestPIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : testsubdomain
data:    FQDN                            : testsubdomain.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

O endereço IP público também é um recurso de nível superior, então você pode vê-lo com `azure group show`.

```bash
azure group show TestRG --json | jq '.'
```

Saída:

```bash
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/TestRG",
"name": "TestRG",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
    "name": "TestNIC",
    "type": "networkInterfaces",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
    "name": "testpip",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
    "name": "TestVNet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
    "name": "computeteststore",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Você pode investigar mais detalhes do recurso, incluindo o FQDN (nome de domínio totalmente qualificado) do subdomínio usando o comando completo `azure network public-ip show`. O recurso de endereço IP público foi alocado logicamente, mas ainda não há um endereço específico atribuído. Para obter um endereço IP, você precisará de um balanceador de carga, que nós ainda não criamos.

```bash
azure network public-ip show TestRG TestPIP --json | jq '.'
```

Saída:

```bash
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "testsubdomain",
    "fqdn": "testsubdomain.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/"c63154b3-1130-49b9-a887-877d74d5ebc5"",
"id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
"name": "testpip",
"location": "westeurope"
}
```

## Criar um balanceador de carga e pools de IPs
Quando você cria um balanceador de carga, ele permite que você distribua o tráfego entre várias VMs. Ele também fornece redundância para seu aplicativo ao executar várias VMs que respondem às solicitações dos usuários em caso de manutenção ou cargas pesadas.

Nós criamos nosso balanceador de carga com:

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Saída:

```bash
info:    Executing command network lb create
+ Looking up the load balancer "TestLB"
+ Creating load balancer "TestLB"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB
data:    Name                            : TestLB
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```
Nosso balanceador de carga está bem vazio, então vamos criar alguns pools de IPs. Queremos criar dois pools de IPs para nosso balanceador de carga - um para o front-end e um para o back-end. O pool de IPs de front-end fica visível publicamente. Também é o local ao qual atribuiremos o PIP que criamos anteriormente. Em seguida, usaremos o pool de back-end como uma localização à qual nossas VMs se conectarão. Dessa forma, o tráfego pode fluir por meio do balanceador de carga para as VMs.

Primeiro, vamos criar nosso pool de IPs de front-end:

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Saída:

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "TestLB"
+ Looking up the public ip "TestLBPIP"
+ Updating load balancer "TestLB"
data:    Name                            : TestFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP
info:    network lb frontend-ip create command OK
```

Observe como usamos a opção `--public-ip-name` para passar o TestLBPIP que criamos anteriormente. A atribuição do endereço IP público ao balanceador de carga permite que você acesse suas VMs pela Internet.

Em seguida, vamos criar nosso segundo pool de IPs, desta vez para o tráfego de back-end:

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Saída:

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : TestBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Podemos ver como está nosso balanceador de carga usando `azure network lb show` e examinando a saída JSON:

```bash
azure network lb show TestRG TestLB --json | jq '.'
```

Saída:

```bash
{
  "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## Criar regras NAT do balanceador de carga
Para fazer com que o tráfego flua pelo balanceador de carga, precisamos criar regras NAT que especifiquem as ações de entrada ou de saída. Você pode especificar o protocolo a ser usado e mapear portas externas para portas internas conforme desejado. Para nosso ambiente, vamos criar algumas regras que permitem que o SSH passe pelo balanceador de carga e chegue às VMs. Vamos configurar as portas TCP 4222 e 4223 para direcionar para a porta TCP 22 em nossas VMs (que criaremos posteriormente):

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
```

Saída:

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "TestLB"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default frontend IP configuration "TestFrontEndPool"
+ Updating load balancer "TestLB"
data:    Name                            : VM1-SSH
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Repita o procedimento para sua segunda regra de NAT para o SSH:

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

Também vamos aproveitar e criar uma regra de NAT para a porta TCP 80, vinculando a regra aos nossos pools de IPs. Se vincularmos a regra ao pool de IPs, em vez de vincular a regra a nossas VMs individualmente, poderemos simplesmente adicionar ou remover VMs do pool de IPs. Em seguida, o balanceador de carga ajusta automaticamente o fluxo de tráfego:

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Saída:

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "TestLB"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "TestLB"
data:    Name                            : WebRule
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
info:    network lb rule create command OK
```

## Criar uma investigação de integridade do balanceador de carga
Uma investigação de integridade verifica periodicamente as máquinas virtuais que estão por trás de nosso balanceador de carga para garantir que elas estejam em operação e estejam respondendo às solicitações da forma definida. Se não estiverem, elas serão removidas da operação para garantir que os usuários não sejam direcionados a elas. Você pode definir verificações personalizadas para a investigação de integridade, bem como valores de tempo limite e intervalos. Para obter mais informações sobre investigações de integridade, consulte [Investigações do Balanceador de Carga](../load-balancer/load-balancer-custom-probe-overview.md).

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "tcp" -i 15 -c 4
```

Saída:

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : HealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Aqui, especificamos um intervalo de 15 segundos para nossas verificações de integridade. Podemos perder um máximo de quatro investigações (um minuto) antes que o balanceador de carga considere que o host não está mais funcionando.

## Verificar o balanceador de carga
Agora, a configuração do balanceador de carga está concluída. Estas são as etapas que você seguiu:

1. Primeiro, você criou um balanceador de carga.
2. Depois, você criou um pool de IPs de front-end e atribuiu um IP público a ele.
3. Em seguida, você criou um pool de IPs de back-end ao qual as VMs podem se conectar.
4. Depois disso, você criou regras NAT que permitem o SSH para as VMs para fins de gerenciamento, em conjunto com uma regra que permite a porta TCP 80 para nosso aplicativo Web.
5. Por fim, você adicionou uma investigação de integridade para verificar periodicamente as VMs. Essa investigação de integridade que os usuários não tentem acessar uma VM que não está mais funcionando ou fornecendo conteúdo.

Vamos ver como seu balanceador de carga está agora:

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Saída:

```bash
{
  "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM1-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM2-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "WebRule",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "frontendPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "HealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/probes/HealthProbe"
    }
  ]
}
```

## Criar uma NIC para usar com a VM Linux
 As NICs estão disponíveis por meio de programação porque você pode aplicar regras ao seu uso. Você também pode ter mais de uma. No comando `azure network nic create` a seguir, você vincula nossa NIC ao pool de IPs de back-end de carga e a associa a nossa regra de NAT para permitir o tráfego SSH. Para fazer isso, você precisa especificar a ID da sua assinatura do Azure no lugar de `<GUID>`:

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
     -d /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
     -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
```

Saída:

```bash
info:    Executing command network nic create
+ Looking up the subnet "FrontEnd"
+ Looking up the network interface "LB-NIC1"
+ Creating network interface "LB-NIC1"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1
data:    Name                            : LB-NIC1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
data:
info:    network nic create command OK
```

Você pode ver os detalhes examinando o recurso diretamente. Examine os recursos usando o comando `azure network nic show`:

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
```

Saída:

```bash
{
  "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1",
  "name": "LB-NIC1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Agora, vamos criar a segunda NIC, vinculando-a novamente ao nosso pool de IPs de back-end. Desta vez, a segunda regra NAT permite tráfego SSH:

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d  /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
    -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH
```

## Criar um grupo de segurança de rede e suas regras
Agora, criamos seu NSG e as regras de entrada que regulam o acesso à NIC.

```bash
azure network nsg create TestRG TestNSG westeurope
```

Vamos adicionar a regra de entrada para o NSG para permitir conexões de entrada na porta 22 (para dar suporte ao SSH):

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow TestRG TestNSG SSHRule
```

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

> [!NOTE]
> A regra de entrada é um filtro para conexões de rede de entrada. Neste exemplo, associaremos o NSG à NIC virtual das VMs, o que significa que qualquer solicitação para a porta 22 será passada para a NIC na nossa VM. Essa regra de entrada é sobre uma conexão de rede e não sobre um ponto de extremidade, que seria o caso em implantações clássicas. Para abrir uma porta, você deve deixar `--source-port-range` definido como “*” (o valor padrão) para aceitar solicitações de entrada de **qualquer** porta de solicitação. Normalmente, as portas são dinâmicas.
> 
> 

## Associar à NIC
Associe o NSG às NICs:

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
```

```bash
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

## Criar um conjunto de disponibilidade
Conjuntos de disponibilidade ajudam a difundir suas VMs em domínios de falha e domínios de atualização. Vamos criar um conjunto de disponibilidade para suas VMs:

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Os domínios de falha definem um agrupamento de máquinas virtuais que compartilham uma mesma fonte de alimentação e um mesmo comutador de rede. Por padrão, as máquinas virtuais que são configuradas dentro do seu conjunto de disponibilidade são separadas entre até três domínios de falha. A ideia é que um problema de hardware em um desses domínios de falha não afete todas as VMs que estiverem executando seu aplicativo. O Azure distribui automaticamente as VMs entre os domínios de falha ao colocá-las em um conjunto de disponibilidade.

Domínios de atualização indicam grupos de máquinas virtuais e o hardware físico subjacente que podem ser reinicializados ao mesmo tempo. A ordem de reinicialização dos domínios de atualização pode não ser sequencial durante a manutenção planejada, mas apenas uma atualização será reinicializada por vez. Mais uma vez, o Azure distribui automaticamente as VMs entre os domínios de atualização ao colocá-las em um site de disponibilidade.

Leia mais sobre como [gerenciar a disponibilidade de VMs](virtual-machines-linux-manage-availability.md).

## Criar as VMs Linux
Você criou os recursos de armazenamento e rede para dar suporte a VMs que podem ser acessadas pela Internet. Agora, vamos criar essas VMs e protegê-las com uma chave SSH que não tem senha. Nesse caso, vamos criar uma VM do Ubuntu com base no LTS mais recente. Localizaremos as informações dessa imagem usando `azure vm image list`, conforme descrito em [localização de imagens de VM do Azure](virtual-machines-linux-cli-ps-findimage.md).

Selecionamos uma imagem usando o comando `azure vm image list westeurope canonical | grep LTS`. Neste caso, usamos `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Para o último campo, vamos passar `latest` para que no futuro sempre tenhamos o build mais recente. (A cadeia de caracteres usada é `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

A próxima etapa é familiar para qualquer pessoa que já tenha criado um par de chaves privada e pública rsa ssh no Linux ou Mac usando **ssh-keygen -t rsa -b 2048**. Se não tiver pares de chave de certificado no seu diretório `~/.ssh`, você poderá criá-los:

* Automaticamente, usando a opção `azure vm create --generate-ssh-keys`.
* Manualmente, usando [as instruções para criá-las você mesmo](virtual-machines-linux-mac-create-ssh-keys.md).

Como alternativa, você pode usar o método --admin--password para autenticar suas conexões SSH após a criação da VM. Esse método normalmente é menos seguro.

Criamos a VM reunindo todos os nossos recursos e informações com o comando `azure vm create`:

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Saída:

```bash
info:    Executing command vm create
+ Looking up the VM "TestVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account computeteststore
+ Looking up the availability set "TestAvailSet"
info:    Found an Availability set "TestAvailSet"
+ Looking up the NIC "LB-NIC1"
info:    Found an existing NIC "LB-NIC1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "LB-NIC1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://computeteststore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Você pode se conectar à sua VM imediatamente usando suas chaves SSH padrão. Certifique-se de especificar a porta apropriada, uma vez que estamos passando pelo balanceador de carga. (Para nossa primeira VM, configuramos a regra NAT para encaminhar a porta 4222 para nossa VM):

```bash
 ssh ops@testlb.westeurope.cloudapp.azure.com -p 4222
```

Saída:

```bash
The authenticity of host '[testlb.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[testlb.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

ops@TestVM1:~$
```

Prossiga e crie sua segunda VM da mesma maneira:

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

E agora você pode usar o comando `azure vm show testrg testvm` para examinar o que criou. Neste ponto, você está executando suas VMs Ubuntu atrás de um balanceador de carga no Azure, em que só pode fazer logon com seu par de chaves SSH (porque as senhas estão desabilitadas). Você pode instalar nginx ou httpd e implantar um aplicativo Web para ver o fluxo do tráfego por meio do balanceador de carga para ambas as VMs.

```bash
azure vm show TestRG TestVM1
```

Saída:

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "LB-NIC1"
data:    Id                              :/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/TestVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :TestVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :TestVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LB-NIC1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/TESTAVAILSET
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://computeteststore.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## Exportar o ambiente como um modelo
Agora que criou esse ambiente, e se você quiser criar um ambiente de desenvolvimento adicional usando os mesmos parâmetros ou um ambiente de produção corresponde? O Gerenciador de Recursos usa modelos JSON que definem todos os parâmetros para seu ambiente. Crie ambientes inteiros fazendo referência a esse modelo JSON. Você pode [criar modelos JSON manualmente](../resource-group-authoring-templates.md) ou simplesmente exportar um ambiente existente para criar o modelo JSON para você:

```bash
azure group export TestRG
```

Esse comando cria o arquivo `TestRG.json` no diretório de trabalho atual. Ao criar um ambiente com base nesse modelo, você receberá uma solicitação para fornecer todos os nomes de recursos, incluindo os nomes do balanceador de carga, das interfaces de rede ou VMs. Você pode preencher esses nomes em seu arquivo de modelo adicionando o parâmetro `-p` ou `--includeParameterDefaultValue` ao comando `azure group export` que foi mostrado anteriormente. Edite seu modelo JSON para especificar os nomes dos recursos, ou [crie um arquivo parameters.json](../resource-group-authoring-templates.md#parameters) que especifica os nomes dos recursos.

Para criar um ambiente a partir de seu modelo:

```bash
azure group deployment create -f TestRG.json -g NewRGFromTemplate
```

Pode ser útil ler [mais detalhes sobre a implantações de modelos](../resource-group-template-deploy-cli.md). Saiba como atualizar ambientes gradativamente, usar o arquivo de parâmetros e acessar os modelos de uma única localização de armazenamento.

## Próximas etapas
Agora, você está pronto para começar a trabalhar com vários componentes de rede e VMs. Você pode usar esse ambiente de exemplo para criar seu aplicativo usando os principais componentes introduzidos aqui.

<!---HONumber=AcomDC_0914_2016-->