<properties
   pageTitle="Criar uma VM do Linux a partir do zero usando a CLI do Azure | Microsoft Azure"
   description="Criar uma VM do Linux, armazenamento, rede virtual e sub-rede, NIC, IP público, Grupo de Segurança de Rede, tudo a partir do zero, usando a CLI do Azure."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="squillace"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/29/2016"
   ms.author="iainfou"/>

# Criar uma VM Linux a partir do zero usando a CLI do Azure

Para criar uma VM Linux, você precisará do [CLI do Azure](../xplat-cli-install.md) no modo do gerenciador de recursos (`azure config mode arm`) e uma ferramenta de análise JSON. Estamos usando [jq](https://stedolan.github.io/jq/) para esse documento.

## Comandos rápidos

Criar o Grupo de recursos

```bash
azure group create TestRG -l westeurope
```

Verificar o GR usando o analisador JSON

```bash
azure group show TestRG --json | jq '.'
```

Criar a Conta de armazenamento

```bash
azure storage account create -g TestRG -l westeurope --type GRS computeteststore
```

Verificar o armazenamento usando o analisador JSON

```bash
azure storage account show -g TestRG computeteststore --json | jq '.'
```

Criar a Rede Virtual

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Criar a Sub-rede

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Verificar a Rede virtual e a Sub-rede usando o analisador JSON

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Criar um IP público

```bash
azure network public-ip create -g TestRG -n TestLBPIP -l westeurope -d testlb -a static -i 4
```

Criar o balanceador de carga

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Criar um pool de IPs de front-end para o balanceador de carga e associar o IP público

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Criar nosso pool de IPs de back-end para o balanceador de carga

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Criar regras de NAT de entrada SSH para o balanceador de carga

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

Criar regras de NAT de entrada da Web para o balanceador de carga

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Criar a investigação de integridade do balanceador de carga

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

Verificar o balanceador de carga, os pools de IPs e as regras de NAT usando o analisador JSON

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Criar a primeira NIC

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
```

Criar a segunda NIC

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
```

Verificar as NICs usando o analisador JSON

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
azure network nic show TestRG LB-NIC2 --json | jq '.'
```

Criar o NSG

```bash
azure network nsg create -g TestRG -n TestNSG -l westeurope
```

Adicionar as regras de entrada para o NSG

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow -g TestRG -a TestNSG -n SSHRule
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

Verificar o NSG e as regras de entrada usando o analisador JSON

```bash
azure network nsg show -g TestRG -n TestNSG --json | jq '.'
```

Associar o NSG às NICs

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

Criar o conjunto de disponibilidade

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Criar a primeira VM do Linux

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
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Criar a segunda VM do Linux

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
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Verificar tudo o que foi criado usando o analisador JSON

```bash
azure vm show -g TestRG -n TestVM1 --json | jq '.'
azure vm show -g TestRG -n TestVM2 --json | jq '.'
```

## Passo a passo detalhado

### Introdução

Este artigo cria uma implantação com duas VMs do Linux atrás de um balanceador de carga. Ele abrange toda a implantação básica imperativamente, comando por comando, até que você tenha VMs do Linux funcionais e seguras às quais possa se conectar de qualquer lugar na Internet.

Aos poucos você entenderá a hierarquia de dependência que o modelo de implantação do Gerenciador de Recursos oferece e todo o seu poder. Depois de ver como o sistema é criado, você pode recriá-lo de forma muito mais rápida usando comandos mais diretos da CLI do Azure (veja [aqui](virtual-machines-linux-quick-create-cli.md) aproximadamente a mesma implantação usando o comando `azure vm quick-create`) ou prosseguir para ter o domínio perfeito de como projetar e automatizar implantações inteiras de rede e aplicativo e atualizá-las usando os [modelos do Azure Resource Manager](../resource-group-authoring-templates.md). Após ver como as partes de sua implantação se encaixam, fica mais fácil criar modelos para automatizá-las.

Vamos criar uma rede e um balanceador de carga simples, com um par de VMs úteis para desenvolvimento e computação simples, e explicaremos conforme avançarmos. Em seguida, você poderá passar para implantações e redes mais complexas.

## Criar grupo de recursos e escolher locais de implantação

Os Grupos de Recursos do Azure são entidades de implantação lógica que contêm a configuração e outros metadados para habilitar o gerenciamento lógico de implantações de recursos.

```bash
azure group create TestRG westeurope
```

Saída

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

Você precisará de contas de armazenamento para seus discos de VM e quaisquer discos de dados adicionais que deseje adicionar, entre outros cenários. Em resumo, você sempre criará contas de armazenamento quase que imediatamente depois de criar grupos de recursos.

Aqui, usamos o comando `azure storage account create`, passando o local da conta, o grupo de recursos que a controlará e o tipo de suporte de armazenamento que você deseja.

```bash
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--type GRS \
computeteststore
```

Saída

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

Vamos usar a ferramenta [jq](https://stedolan.github.io/jq/) (você pode usar **jsawk** ou qualquer biblioteca de linguagem que preferir para analisar o JSON) em conjunto com a opção `--json` da CLI do Azure para examinar nosso grupo de recursos usando o comando `azure group show`.

```bash
azure group show TestRG --json | jq                                                                                      
```


Saída

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

Para investigar a conta de armazenamento usando a CLI, você precisa primeiro definir os nomes de conta e chaves usando uma variação do comando a seguir, substituindo o nome da conta de armazenamento deste artigo pela sua própria conta.

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"
```

Em seguida, você poderá exibir as informações de armazenamento facilmente:

```bash
azure storage container list
```

Saída

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## Criar sua rede virtual e a sub-rede

Você precisará criar uma rede virtual do Azure e uma sub-rede na qual possa instalar suas VMs.

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Saída

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

Novamente, vamos ver como estamos criando nossos recursos usando a opção --json de `azure group show` e **jq**. Agora, temos um recurso `storageAccounts` e um recurso `virtualNetworks`.

```bash
azure group show TestRG --json | jq '.'
```

Saída

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

Agora vamos criar uma sub-rede na rede virtual `TestVnet` na qual as VMs serão implantadas. Usamos o comando `azure network vnet subnet create`, em conjunto com os recursos que já criamos: o grupo de recursos `TestRG`, a rede virtual `TestVNet`, e adicionaremos o nome da sub-rede `FrontEnd` e o prefixo de endereço de sub-rede `192.168.1.0/24`, conforme demonstrado a seguir.

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Saída

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

Como a sub-rede está logicamente dentro da rede virtual, vamos buscar as informações da sub-rede com um comando ligeiramente diferente -- `azure network vnet show`, mas continuaremos examinando a saída JSON usando **jq**.

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Saída

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

## Crie o seu endereço IP público (PIP)

Agora, vamos criar seu PIP (endereço IP público), que atribuiremos ao seu balanceador de carga e que permitirá que você se conecte a suas VMs da Internet usando o comando `azure network public-ip create`. Como o padrão é um endereço dinâmico, criamos uma entrada DNS nomeada no domínio **cloudapp.azure.com** usando a opção `-d testsubdomain`.

```bash
azure network public-ip create -d testsubdomain TestRG TestPIP westeurope
```

Saída

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

Este também é um recurso de nível superior, então você pode vê-lo com `azure group show`.

```bash
azure group show TestRG --json | jq '.'
```

Saída

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

E, como sempre, você pode investigar mais detalhes do recurso, incluindo o FQDN (nome de domínio totalmente qualificado) do subdomínio usando o comando `azure network public-ip show` mais completo. Observe que o recurso de endereço IP público foi alocado logicamente, mas ainda não há um endereço específico atribuído. Para fazer isso, você precisará do balanceador de carga, que nós ainda não criamos.

```bash
azure network public-ip show TestRG TestPIP --json | jq '.'
```

Saída

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

## Criar seu balanceador de carga e pools de IPs
Criar um balanceador de carga permite que você distribua o tráfego entre várias VMs, como ao executar aplicativos Web. Ele também fornece redundância para seu aplicativo ao executar várias VMs que respondem às solicitações dos usuários em caso de manutenção ou cargas pesadas.

Nós criamos nosso balanceador de carga com:

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Saída

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
Nosso balanceador de carga está bem vazio, então vamos criar alguns pools de IPs. Queremos criar dois pools de IPs para nosso balanceador de carga - um para o front-end e outro para o back-end. O pool de IPs de front-end é o que teremos visível publicamente e onde vamos atribuir o PIP criado anteriormente. Quanto ao pool de back-end, nós o usaremos para que nossas VMs se conectem a ele, de modo que o tráfego flua pelo balanceador de carga até elas.

Primeiro, vamos criar nosso pool de IPs de front-end:

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Saída

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

Observe como usamos a opção `--public-ip-name` para passar o TestLBPIP que criamos anteriormente. Isso atribui o endereço IP público ao balanceador de carga para que possamos alcançar nossas VMs pela Internet.

Em seguida, vamos criar nosso segundo pool de IPs, desta vez para o tráfego de back-end:

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Saída

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : TestBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Podemos verificar como o balanceador de carga está, usando `azure network lb show` e examinando a saída JSON:

```bash
azure network lb show TestRG TestLB --json | jq '.'
```

Saída

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

## Criar as regras de NAT do balanceador de carga
Para fazer com que o tráfego, de fato, flua pelo balanceador de carga, precisamos criar regras de NAT que especifiquem as ações de entrada ou de saída. Você pode especificar o protocolo que está sendo usado e mapear portas externas para portas internas conforme desejado. Para nosso ambiente, vamos criar algumas regras que permitem que o SSH passe pelo balanceador de carga e chegue às VMs. Vamos configurar as portas TCP 4222 e 4223 para direcionar para a porta TCP 22 em nossas VMs (que criaremos posteriormente):

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
```

Saída

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

Também vamos aproveitar e criar uma regra de NAT para a porta TCP 80, vinculando a regra aos nossos pools de IPs. Em vez de conectar a regra a nossas VMs individualmente, podemos simplesmente adicionar ou remover VMs do pool de IPs e o balanceador de carga ajustará automaticamente o fluxo de tráfego:

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Saída

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

## Criar a investigação de integridade de seu balanceador de carga

Uma investigação de integridade verificará periodicamente as máquinas virtuais que estão por trás de nosso balanceador de carga para garantir que elas estejam em operação e estejam respondendo às solicitações da forma definida. Caso contrário, elas serão removidas da operação para garantir que os usuários não sejam direcionados a elas. Você pode definir verificações personalizadas para a investigação de integridade, bem como valores de tempo limite e intervalos. Para obter mais informações sobre as investigações de integridade, leia [Investigações do Balanceador de Carga](../load-balancer/load-balancer-custom-probe-overview.md).

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

Saída

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : HealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Http
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Aqui, especificamos um intervalo de 15 segundos para nossas verificações de integridade, e podemos pode perder um máximo de 4 investigações (1 minuto) antes que o balanceador de carga considere que o host não está funcionando.

## Verificar seu balanceador de carga
Toda a configuração do balanceador de carga está pronta. Você criou um balanceador de carga, criou um pool de IPs de front-end e atribuiu um IP público a ele, e depois criou um pool de IPs de back-end a que as VMs serão conectadas. Em seguida, você criou regras de NAT que permitirão o SSH para as VMs para fins de gerenciamento, em conjunto com uma regra que permite a porta TCP 80 para nosso aplicativo Web. Por fim, para garantir que nossos usuários não tentem acessar uma VM que não está mais funcionando e fornecendo conteúdo, você adicionou uma investigação de integridade para verificar periodicamente as VMs.

Vamos ver como seu balanceador de carga está agora:

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Saída

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
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/probes/HealthProbe",
      "protocol": "Http",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 4,
      "requestPath": "healthprobe.aspx",
      "provisioningState": "Succeeded",
      "name": "HealthProbe"
    }
  ]
}
```

## Criar uma NIC para usar com a VM do Linux

Até mesmo NICs estão disponíveis por meio de programação, já que você pode aplicar regras para seu uso e ter mais de uma. Observe que no comando `azure network nic create` a seguir, você vincula nossa NIC ao pool de IPs de back-end de carga e a associa a nossa regra de NAT para permitir o tráfego SSH. Para fazer isso, você precisa especificar a ID da sua assinatura do Azure no lugar de `<GUID>`:

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd -d
```

Saída

```bash 
/subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
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

Você pode ver os detalhes examinando o recurso diretamente, usando o comando `azure network nic show`.

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
```

Saída

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

Vamos prosseguir e criar a segunda NIC, vinculando-a ao nosso pool de IPs de back-end novamente e, desta vez, a segunda de nossas regras de NAT para permitir o tráfego SSH:

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd -d
```

Saída

```bash
 /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH
```

## Crie o seu Grupo de Segurança de Rede e suas regras

Agora podemos criar seu NSG (grupo de segurança de rede) e as regras de entrada que regulam o acesso à NIC.

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

> [AZURE.NOTE] A regra de entrada é um filtro para conexões de rede de entrada. Neste exemplo, associaremos o NSG à NIC (placa de interface de rede) virtual das VMs, o que significa que qualquer solicitação para a porta 22 será passada para a NIC na nossa VM. Como esta é uma regra sobre uma conexão de rede, e não sobre um ponto de extremidade como nas implantações clássicas, para abrir uma porta, você deve deixar `--source-port-range` definido como “*” (o valor padrão) para aceitar solicitações de entrada de **qualquer** porta solicitante, que geralmente são dinâmicas.

## Associar à NIC

Associe o NSG às NICs:

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
```

```bash
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

## Criar um conjunto de disponibilidade
Conjuntos de disponibilidade ajudam a difundir suas VMs no que chamamos de domínios de falha e domínios de atualização. Vamos criar um conjunto de disponibilidade para suas VMs:

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Os domínios de falha definem um agrupamento de máquinas virtuais que compartilham uma mesma fonte de alimentação e um mesmo comutador de rede. Por padrão, as máquinas virtuais configuradas dentro do seu conjunto de disponibilidade são separadas entre até três domínios de falha. A ideia é que um problema de hardware em um desses domínios de falha não afete todas as VMs que estiverem executando seu aplicativo. O Azure distribuirá automaticamente as VMs entre os domínios de falha ao colocá-las em um conjunto de disponibilidade.

Domínios de atualização indicam grupos de máquinas virtuais e o hardware físico subjacente que podem ser reinicializados ao mesmo tempo. A ordem de reinicialização dos domínios de atualização pode não ser sequencial durante a manutenção planejada, mas apenas uma atualização será reinicializada por vez. Mais uma vez, o Azure distribuirá automaticamente suas VMs entre os domínios de atualização quando elas forem colocadas em um site de disponibilidade.

Você pode ler mais sobre como [Gerenciar a disponibilidade de máquinas virtuais](./virtual-machines-linux-manage-availability.md)

## Criar suas VMs do Linux

Você criou os recursos de armazenamento e rede para dar suporte a VMs que podem ser acessadas pela Internet. Agora, vamos criar essas VMs e protegê-las com uma chave SSH sem senha. Nesse caso, vamos criar uma VM do Ubuntu com base no LTS mais recente. Localizaremos as informações dessa imagem usando `azure vm image list`, conforme descrito em [localização de imagens de VM do Azure](virtual-machines-linux-cli-ps-findimage.md). Selecionamos uma imagem usando o comando `azure vm image list westeurope canonical | grep LTS` e, nesse caso, usaremos `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`, mas para o último campo passaremos `latest` para que, no futuro, sempre obtenhamos o build mais recente (a cadeia de caracteres que usamos será `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`).

> [AZURE.NOTE] A próxima etapa é familiar para qualquer pessoa que já tenha criado um par de chaves privada e pública rsa ssh no Linux ou Mac usando **ssh-keygen -t rsa -b 2048**. Se não tiver nenhum par de chaves de certificado em seu diretório `~/.ssh`, você poderá criá-lo: <br /> 1. automaticamente usando a opção `azure vm create --generate-ssh-keys` 2. manualmente usando [as instruções para criá-lo você mesmo](virtual-machines-linux-ssh-from-linux.md) <br /> Como alternativa, você pode usar as opções `azure vm create --admin-username --admin-password` para usar o método de nome de usuário e senha, que normalmente é menos seguro, para autenticação de suas conexões ssh uma vez que a VM for criada.

Criamos a VM reunindo todos os nossos recursos e informações com o comando `azure vm create`.

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
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Saída

```bash
info:    Executing command vm create
+ Looking up the VM "TestVM1"
info:    Verifying the public key SSH file: /home/ifoulds/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_A1"
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

Imediatamente, você pode se conectar à sua VM usando suas chaves SSH padrão, certificando-se de especificar a porta apropriada, uma vez que estamos passando pelo balanceador de carga (para nossa primeira VM, configuramos a regra de NAT para encaminhar a porta 4222 para nossa VM):

```bash
 ssh ops@testlb.westeurope.cloudapp.azure.com -p 4222
```

Saída

```bash
The authenticity of host '[testlb.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[testlb.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-58-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Wed Apr 27 23:44:06 UTC 2016

  System load: 0.37              Memory usage: 5%   Processes:       81
  Usage of /:  37.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

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
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

E agora você pode usar o comando `azure vm show testrg testvm` para examinar o que criou. Neste ponto, você tem suas VMs do Ubuntu em execução atrás de um balanceador de carga no Azure, em que só pode efetuar logon com o par de chaves SSH que você tem; as senhas são desabilitadas. Você pode instalar nginx ou httpd e implantar um aplicativo Web para ver o fluxo do tráfego por meio do balanceador de carga para ambas as VMs.

```bash
azure vm show TestRG TestVM1
```

Saída

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "LB-NIC1"
data:    Id                              :/subscriptions/8fa5cd83-7fbb-431a-af16-4a20dede8802/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/TestVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :TestVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli1cca1d20a1dcf56c-os-1461800591317
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli1cca1d20a1dcf56c-os-1461800591317.vhd
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
data:          MAC Address               :00-0D-3A-20-F8-8B
data:          Provisioning State        :Succeeded
data:          Name                      :LB-NIC1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/availabilitySets/TESTAVAILSET
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://computeteststore.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK
```

## Próximas etapas

Agora você está pronto para começar a usar vários componentes de rede e VMs.

<!---HONumber=AcomDC_0504_2016-->