<properties
   pageTitle="Criar uma VM do Linux a partir do zero usando a CLI do Azure | Microsoft Azure"
   description="Criar uma VM do Linux, armazenamento, rede virtual e sub-rede, NIC, IP público, Grupo de Segurança de Rede, tudo a partir do zero, usando a CLI do Azure."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="vlivech"
   manager="squillace"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/04/2016"
   ms.author="v-livech"/>

# Criar uma VM Linux a partir do zero usando a CLI do Azure

Para criar uma VM Linux, você precisará do [CLI do Azure](../xplat-cli-install.md) no modo do gerenciador de recursos (`azure config mode arm`) e uma ferramenta de análise JSON. Estamos usando [jq](https://stedolan.github.io/jq/) para esse documento.

## Comandos rápidos

```bash
# Create the Resource Group
chrisL@fedora$ azure group create TestRG westeurope

# Create the Storage Account
chrisL@fedora$ azure storage account create \  
--location westeurope \
--resource-group TestRG \
--type GRS \
computeteststore

# Verify the RG using the JSON parser
chrisL@fedora$ azure group show testrg --json | jq '.'

# Create the Virtual Network
chrisL@fedora$ azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope

# Verify the RG
chrisL@fedora$ azure group show testrg --json | jq '.'

# Create the Subnet
chrisL@fedora$ azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

# Verify the VNet and Subnet
chrisL@fedora$ azure network vnet show testrg testvnet --json | jq '.'

# Create the NIC
chrisL@fedora$ azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd

# Verify the NIC
chrisL@fedora$ azure network nic show testrg testnic --json | jq '.'

# Create the NSG
chrisL@fedora$ azure network nsg create testrg testnsg westeurope

# Add an inbound rule for the NSG
chrisL@fedora$ azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule

# Creat the Public facing NIC
chrisL@fedora$ azure network public-ip create -d testsubdomain testrg testpip westeurope

# Verify the NIC
chrisL@fedora$ azure network public-ip show testrg testpip --json | jq '.'

# Associate the Public IP to the NIC
chrisL@fedora$ azure network nic set --public-ip-name testpip testrg testnic

# Bind the NSG to the NIC
chrisL@fedora$ azure network nic set --network-security-group-name testnsg testrg testnic

# Create the Linux VM
chrisL@fedora$ azure vm create \            
    --resource-group testrg \
    --name testvm \
    --location westeurope \
    --os-type linux \
    --nic-name testnic \
    --vnet-name testvnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops

# Verify everything built
chrisL@fedora$ azure vm show testrg testvm

```

## Passo a passo detalhado

### Introdução

Este artigo compila uma implantação com uma VM Linux dentro de uma sub-rede de rede virtual. Ele abrange toda a implantação básica imperativamente, comando por comando, até que você tenha uma VM do Linux funcional e segura à qual você pode se conectar de qualquer lugar na Internet.

Aos poucos você entenderá a hierarquia de dependência que o modelo de implantação do Gerenciador de Recursos oferece e todo o seu poder. Depois de ver como o sistema é criado, é possível recriá-lo de forma muito mais rápida usando comandos da CLI do Azure mais diretos (veja [isso](virtual-machines-linux-quick-create-cli.md) para, aproximadamente, a mesma implantação usando o comando `azure vm quick-create`) ou continuar para ter domínio perfeito de como projetar e automatizar implantações inteiras de rede e aplicativo e atualizá-las usando os [modelos do Azure Resource Manager](../resource-group-authoring-templates.md). Após ver como as partes de sua implantação se encaixam, fica mais fácil criar modelos para automatizá-las.

Vamos criar uma rede simples com uma VM útil para o desenvolvimento e computação simples e explicaremos conforme avançamos. Em seguida, você poderá passar para implantações e redes mais complexas.

### Criar grupo de recursos e escolher locais de implantação

Os Grupos de Recursos do Azure são entidades de implantação lógica que contêm a configuração e outros metadados para habilitar o gerenciamento lógico de implantações de recursos.

```
chrisL@fedora$ azure group create TestRG westeurope                        
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

### Criar uma conta de armazenamento

Você precisará de contas de armazenamento para seus discos de VM e quaisquer discos de dados adicionais que deseje adicionar, entre outros cenários. Em resumo, você sempre criará contas de armazenamento quase que imediatamente depois de criar grupos de recursos.

Aqui usamos o comando `azure storage account create`, passando o local da conta, o grupo de recursos que vai controlá-la e o tipo de suporte de armazenamento que você deseja.

```
chrisL@fedora$ azure storage account create \  
--location westeurope \
--resource-group TestRG \
--type GRS \
computeteststore
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
rasquill•~/workspace/keygen» azure group show testrg
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

Vamos usar a ferramenta [jq](https://stedolan.github.io/jq/) (você pode usar **jsawk** ou qualquer biblioteca de linguagem que preferir para analisar o JSON) junto com a opção `--json` de CLI do Azure para examinar nosso grupo de recursos usando o comando `azure group show`.

```
chrisL@fedora$ azure group show testrg --json | jq '.'                                                                                        
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

```
chrisL@fedora$ azure storage container list
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```
### Criar sua rede virtual e a sub-rede

Você precisará criar uma rede virtual do Azure e uma sub-rede na qual você pode instalar a VM.

```
chrisL@fedora$ azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
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

Novamente, vamos ver como estamos criando nossos recursos usando a opção --json de `azure group show` e **jq**. Agora temos um recurso `storageAccounts` e um recurso `virtualNetworks`.

```
chrisL@fedora$ azure group show testrg --json | jq '.'
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

Agora vamos criar uma sub-rede na rede virtual `TestVnet` na qual a VM será implantada. Usamos o comando `azure network vnet subnet create`, junto com os recursos que já criamos: o grupo de recursos `TestRG`, a rede virtual `TestVNet` e adicionaremos o nome da sub-rede `FrontEnd` e o prefixo de endereço de sub-rede `192.168.1.0/24`, conforme demonstrado a seguir.

```
chrisL@fedora$ azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
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

Como a sub-rede está logicamente dentro da rede virtual, vamos observar as informações de sub-rede com um comando ligeiramente diferente -- `azure network vnet show`, mas ainda examinando o JSON de saída usando **jq**.

```
chrisL@fedora$ azure network vnet show testrg testvnet --json | jq '.'
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

### Criar uma NIC para usar com a VM do Linux

Até mesmo NICs estão disponíveis por meio de programação, já que você pode aplicar regras para seu uso e ter mais de uma.

```
chrisL@fedora$ azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd
info:    Executing command network nic create
+ Looking up the network interface "TestNIC"
+ Looking up the subnet "FrontEnd"
+ Creating network interface "TestNIC"
+ Looking up the network interface "TestNIC"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
data:    Name                            : TestNIC
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : NIC-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.101
data:      Private IP Allocation Method  : Static
data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:
info:    network nic create command OK
```

Como o recurso NIC está associado a uma VM e um Grupo de Segurança de Rede, você pode vê-lo como um recurso de nível superior ao examinar o grupo de recursos `TestRG`:

```
chrisL@fedora$ azure group show testrg --json | jq '.'
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

Você pode ver os detalhes examinando o recurso diretamente, usando o comando `azure network nic show`.

```
chrisL@fedora$ azure network nic show testrg testnic --json | jq '.'
{
"ipConfigurations": [
    {
    "loadBalancerBackendAddressPools": [],
    "loadBalancerInboundNatRules": [],
    "privateIpAddress": "192.168.1.101",
    "privateIpAllocationMethod": "Static",
    "subnet": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
    },
    "provisioningState": "Succeeded",
    "name": "NIC-config",
    "etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/NIC-config"
    }
],
"tags": {},
"dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
},
"enableIPForwarding": false,
"provisioningState": "Succeeded",
"etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
"id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
"name": "TestNIC",
"location": "westeurope"
}
```

### Crie o seu Grupo de Segurança de Rede e suas regras

Agora podemos criar seu NSG (grupo de segurança de rede) e as regras de entrada que regulam o acesso à NIC.

```
chrisL@fedora$ azure network nsg create testrg testnsg westeurope
```

Vamos adicionar regra de entrada para o nsg para permitir conexões de entrada na porta 22 (para suportar SSH):

```
chrisL@fedora$ azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule
```

> [AZURE.NOTE] A regra de entrada é um filtro para conexões de rede de entrada. Neste exemplo, ligaremos o NSG à placa de interface de rede virtual (nic) às VMs, o que significa que qualquer solicitação para a porta 22 será passada para a nic na nossa VM. Como esta é uma regra sobre uma conexão de rede, e não um ponto de extremidade como nas implantações clássicas, para abrir uma porta, você deve deixar o `--source-port-range` definido como “*” (o valor padrão) para aceitar solicitações de entrada de **qualquer** porta solicitante, que são geralmente dinâmicas.

### Crie o seu endereço IP público (PIP)

Agora vamos criar seu endereço IP público (PIP) que permite que você se conecte à sua VM por meio da Internet usando o comando `azure network public-ip create`. Como o padrão é um endereço dinâmico, podemos criar uma entrada DNS nomeada no domínio **cloudapp.azure.com** usando a opção `-d testsubdomain`.

```
chrisL@fedora$ azure network public-ip create -d testsubdomain testrg testpip westeurope
info:    Executing command network public-ip create
+ Looking up the public ip "testpip"
+ Creating public ip address "testpip"
+ Looking up the public ip "testpip"
data:    Id                              : /subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip
data:    Name                            : testpip
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : testsubdomain
data:    FQDN                            : testsubdomain.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

Isso também é um recurso de nível superior, então você pode ver com `azure group show`.

```
chrisL@fedora$ azure group show testrg --json | jq '.'
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

E, como sempre, você pode investigar mais detalhes do recurso, incluindo o nome do domínio totalmente qualificado (FQDN) do subdomínio usando o comando `azure network public-ip show` mais completo. Observe que o recurso de endereço IP público foi alocado logicamente, mas ainda não há um endereço específico atribuído. Para fazer isso, você precisará de uma VM, que nós ainda não criamos.

```
azure network public-ip show testrg testpip --json | jq '.'
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

### Associar o IP público e o grupo de segurança de rede à NIC

```
chrisL@fedora$ azure network nic set --public-ip-name testpip testrg testnic
```

Associe o NSG à NIC:

```
chrisL@fedora$ azure network nic set --network-security-group-name testnsg testrg testnic
```

### Crie sua VM do Linux

Você criou os recursos de armazenamento e rede para oferecer suporte à VM que pode ser acessada pela Internet. Agora vamos criar essa VM e protegê-la com uma chave ssh sem senha. Nesse caso, vamos criar uma VM do Ubuntu com base no LTS mais recente. Localizaremos as informações dessa imagem usando `azure vm image list`, conforme descrito em [localização de imagens de VM do Azure](virtual-machines-linux-cli-ps-findimage.md). Selecionamos uma imagem usando o comando `azure vm image list westeurope canonical | grep LTS`, e nesse caso usaremos `canonical:UbuntuServer:14.04.3-LTS:14.04.201509080`, mas para o último campo vamos passar `latest` para que no futuro, sempre obtenhamos o build mais recente (a cadeia de caracteres que usamos será `canonical:UbuntuServer:14.04.3-LTS:latest`).

> [AZURE.NOTE] A próxima etapa é familiar a qualquer pessoa que já tenha criado um par de chaves privada e pública rsa ssh em Linux ou Mac usando **ssh-keygen -t rsa -b 2048**. Se você não tem nenhum par de chaves de certificado em seu diretório `~/.ssh`, você pode criá-los: <br /> 1. automaticamente usando a opção `azure vm create --generate-ssh-keys` 2. manualmente usando [as instruções para criá-los você mesmo](virtual-machines-linux-ssh-from-linux.md) <br /> como alternativa, você pode usar as opções `azure vm create --admin-username --admin-password` para usar o método de nome de usuário e senha normalmente menos seguro de autenticação de suas conexões ssh uma vez que a VM é criada.

Criamos a VM colocando todos os nossos recursos e informações junto com o comando `azure vm create`.

```
chrisL@fedora$ azure vm create \            
--resource-group testrg \
--name testvm \
--location westeurope \
--os-type linux \
--nic-name testnic \
--vnet-name testvnet \
--vnet-subnet-name FrontEnd \
--storage-account-name computeteststore \
--image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username ops
info:    Executing command vm create
+ Looking up the VM "testvm"
info:    Verifying the public key SSH file: /Users/user/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_A1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account computeteststore
+ Looking up the NIC "testnic"
info:    Found an existing NIC "testnic"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "testnic"
info:    This NIC IP configuration has a public ip already configured "/subscriptions/guid/resourcegroups/testrg/providers/microsoft.network/publicipaddresses/testpip", any public ip parameters if provided, will be ignored.
+ Creating VM "testvm"
info:    vm create command OK
```

Imediatamente, você pode se conectar à sua VM usando as chaves ssh padrão.

```
chrisL@fedora$ ssh ops@testsubdomain.westeurope.cloudapp.azure.com           
The authenticity of host 'testsubdomain.westeurope.cloudapp.azure.com (XX.XXX.XX.XXX)' can't be established.
RSA key fingerprint is b6:a4:7g:4b:cb:cd:76:87:63:2d:84:83:ac:12:2d:cd.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'testsubdomain.westeurope.cloudapp.azure.com,XX.XXX.XX.XXX' (RSA) to the list of known hosts.
Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Mon Sep 28 18:45:02 UTC 2015

System load: 0.64              Memory usage: 5%   Processes:       81
Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

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

ops@testvm:~$
```

E agora você pode usar o comando `azure vm show testrg testvm` para examinar o que você criou. Neste ponto, há uma VM do Ubuntu em execução no Azure que você só pode efetuar login com o par de chaves ssh que você possui; as senhas são desabilitadas.

```
chrisL@fedora$ azure vm show testrg testvm
info:    Executing command vm show
+ Looking up the VM "testvm"
+ Looking up the NIC "testnic"
+ Looking up the public ip "testpip"
data:    Id                              :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm
data:    ProvisioningState               :Succeeded
data:    Name                            :testvm
data:    Location                        :westeurope
data:    FQDN                            :testsubdomain.westeurope.cloudapp.azure.com
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.3-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli4eecdddc349d6015-os-1443465824206
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli4eecdddc349d6015-os-1443465824206.vhd
data:
data:    OS Profile:
data:      Computer Name                 :testvm
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:        SSH Public Keys:
data:          Public Key #1:
data:            Path                    :/home/ops/.ssh/authorized_keys
data:            Key                     :MIIBrTCCAZigAwIBAgIBATALBgkqhkiG9w0BAQUwADAiGA8yMDE1MDkyODE4MzM0
<snip>
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Id                        :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-21-8E-AE
data:          Provisioning State        :Succeeded
data:          Name                      :testnic
data:          Location                  :westeurope
data:            Private IP alloc-method :Dynamic
data:            Private IP address      :192.168.1.101
data:            Public IP address       :40.115.48.189
data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

### Próximas etapas

Agora você está pronto para começar a usar vários componentes de rede e VMs.

<!---HONumber=AcomDC_0413_2016-->