
Este artigo mostra como usar modelos do Gerenciador de Recursos do Azure e a CLI do Azure para automatizar as tarefas comuns de implantação e gerenciamento de máquinas virtuais do Azure a seguir. Para obter mais modelos que você possa usar, confira [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) e [Estruturas de aplicativos usando modelos](../articles/virtual-machines/virtual-machines-linux-app-frameworks.md).


- [Criar rapidamente uma máquina virtual no Azure](#quick-create-a-vm-in-azure)
- [Implantar uma máquina virtual no Azure com base em um modelo](#deploy-a-vm-in-azure-from-a-template)
- [Criar uma máquina virtual com base em uma imagem personalizada](#create-a-custom-vm-image)
- [Implantar uma máquina virtual que usa uma rede virtual e um balanceador de carga](#deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer)
- [Remover um grupo de recursos](#remove-a-resource-group)
- [Mostrar o log para uma implantação de grupo de recursos](#show-the-log-for-a-resource-group-deployment)
- [Exibir informações sobre uma máquina virtual](#display-information-about-a-virtual-machine)
- [Conectar-se a uma máquina virtual baseada em Linux](#log-on-to-a-linux-based-virtual-machine)
- [Parar uma máquina virtual](#stop-a-virtual-machine)
- [Iniciar uma máquina virtual](#start-a-virtual-machine)
- [Anexar um disco de dados](#attach-a-data-disk)

## Preparando-se

Para usar a CLI do Azure com grupos de recursos do Azure, você precisará ter a versão correta da CLI do Azure e uma conta do Azure. Se você não tiver a CLI do Azure, [instale-a](xplat-cli-install.md).

### Atualizar sua versão da CLI do Azure para 0.9.0 ou posterior

Digite `azure --version` para ver se você já tem a versão 0.9.0 ou posterior instalada.

	azure --version
    0.9.0 (node: 0.10.25)

Se a versão não for a 0.9.0 ou posterior, será necessário atualizá-la usando um dos instaladores nativos ou por meio do **npm** digitando `npm update -g azure-cli`.

Você também pode executar a CLI do Azure como um contêiner do Docker usando a [imagem do Docker](https://registry.hub.docker.com/u/microsoft/azure-cli/) abaixo. De um host Docker, execute o seguinte comando:

	docker run -it microsoft/azure-cli

### Definir sua conta e assinatura do Azure

Se ainda não tiver uma assinatura do Azure, mas tiver uma assinatura do MSDN, você poderá ativar seus [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Ou então, você poderá se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

Agora, [faça logon com a conta do Azure de modo interativo](../articles/xplat-cli-connect.md#use-the-log-in-method) digitando `azure login` e seguindo os prompts para ter uma experiência de logon interativa para sua conta do Azure.

> [AZURE.NOTE] Se você tiver uma ID corporativa ou de estudante e souber que não tem a autenticação de dois fatores habilitada, poderá **também** usar `azure login -u` juntamente com a ID corporativa ou de estudante para fazer logon *sem* uma sessão interativa. Se você não tiver uma ID corporativa ou de estudante, poderá [criar uma ID corporativa ou de estudante de sua conta pessoal da Microsoft](../articles/virtual-machines/virtual-machines-windows-create-aad-work-id.md) para fazer logon da mesma maneira.

Sua conta pode ter mais de uma assinatura. Você pode listar suas assinaturas digitando `azure account list`, que pode ser semelhante a:

    azure account list
    info:    Executing command account list
    data:    Name                              Id                                    Tenant Id                            Current
    data:    --------------------------------  ------------------------------------  ------------------------------------  -------
    data:    Contoso Admin                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  true
    data:    Fabrikam dev                      xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    data:    Fabrikam test                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    data:    Contoso production                xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  

Você pode definir a assinatura do Azure atual digitando o seguinte. Use a ID ou o nome da assinatura que tem os recursos que você deseja gerenciar.

	azure account set <subscription name or ID> true



### Alternar para o modo de grupo de recursos da CLI do Azure

Por padrão, a CLI do Azure é iniciada no modo de gerenciamento de serviços (modo **asm**). Digite o seguinte para alternar para o modo de grupo de recursos.

	azure config mode arm

## Noções básicas sobre grupos de recursos e modelos de recursos do Azure

A maioria dos aplicativos é criada a partir de uma combinação de diferentes tipos de recursos (como uma ou mais VMs e contas de Armazenamento, um banco de dados SQL, uma Rede Virtual ou uma rede de transmissão de conteúdo). A API de gerenciamento de serviço do Azure padrão e o portal clássico do Azure representava esses itens usando uma abordagem de acordo com o serviço. Essa abordagem requer que você implante e gerencie individualmente os serviços individuais (ou localize outras ferramentas fazê-lo) e não como uma única unidade lógica de implantação.

Porém, os *modelos do Gerenciador de Recursos do Azure* tornam possível implantar e gerenciar esses diferentes recursos como uma unidade lógica de implantação de forma declarativa. Em vez de informar imperativamente ao Azure o que implantar, em um comando após o outro, você descreve a implantação inteira em um arquivo JSON (todos os recursos e configurações e parâmetros de implantação associados) e instrui o Azure a implantar esses recursos como um grupo.

Em seguida, você pode gerenciar o ciclo de vida geral dos recursos do grupo usando os comandos de gerenciamento de recursos da CLI do Azure para:

- Parar, iniciar ou excluir todos os recursos do grupo de uma só vez.
- Aplicar regras de RBAC (Controle de Acesso Baseado em Função) para bloquear permissões de segurança em relação a eles.
- Auditar operações.
- Marcar recursos com metadados extras para ter melhor acompanhamento.

Você pode saber muito mais sobre os grupos de recursos do Azure e o que eles podem fazer por você na [Visão geral do Gerenciador de Recursos do Azure](../articles/resource-group-overview.md). Se você estiver interessado na criação de modelos, confira [Criando modelos do Gerenciador de Recursos do Azure](../articles/resource-group-authoring-templates.md).

## <a id="quick-create-a-vm-in-azure"></a>Tarefa: Criar rapidamente uma VM no Azure

Às vezes, você sabe de qual imagem precisa, precisa de uma VM dessa imagem agora e não se importa muito com a infraestrutura. Talvez você tenha que testar algo em uma VM limpa. É quando convém usar comando o `azure vm quick-create` e passar os argumentos necessários para criar uma VM e sua infraestrutura.

Em primeiro lugar, crie o grupo de recursos.

    azure group create coreos-quick westus
    info:    Executing command group create
    + Getting resource group coreos-quick
    + Creating resource group coreos-quick
    info:    Created resource group coreos-quick
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick
    data:    Name:                coreos-quick
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK


Em segundo lugar, você precisará de uma imagem. Para encontrar uma imagem com a CLI do Azure, confira [Navegando e selecionando imagens de máquinas virtuais do Azure com o PowerShell e a CLI do Azure](../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md). Porém, para este artigo, aqui está uma pequena lista de imagens populares. Vamos usar a imagem estável do CoreOS para esta criação rápida.

> [AZURE.NOTE] Para ComputeImageVersion, você pode também pode simplesmente fornecer 'latest' como o parâmetro tanto na linguagem do modelo quanto na CLI do Azure. Isso permitirá que você use sempre a versão mais recente e corrigida da imagem sem ter que modificar seus scripts ou modelos. Isso é mostrado abaixo.

| PublisherName | Oferta | Sku | Versão |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 | 7\.0.201503 |
| OpenLogic | CentOS | 7\.1 | 7\.1.201504 |
| CoreOS | CoreOS | Beta | 647\.0.0 |
| CoreOS | CoreOS | Estável | 633\.1.0 |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 | 8\.0.40459 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 | 1\.0.0 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Standard | 1\.0.0 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Enterprise | 1\.0.0 |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-DW | 12\.0.2430 |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-OLTP | 12\.0.2430 |
| Canônico | UbuntuServer | 12\.04.5-LTS | 12\.04.201504230 |
| Canonical | UbuntuServer | 14\.04.2-LTS | 14\.04.201503090 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | 3\.0.201503 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | 4\.0.201503 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview | 5\.0.201504 |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials | 1\.0.141204 |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 | 4\.3.4665 |

Basta criar sua VM inserindo o comando `azure vm quick-create` e preparando-se para os prompts. O resultado deve ser semelhante a esse:

    azure vm quick-create
    info:    Executing command vm quick-create
    Resource group name: coreos-quick
    Virtual machine name: coreos
    Location name: westus
    Operating system Type [Windows, Linux]: linux
    ImageURN (format: "publisherName:offer:skus:version"): coreos:coreos:stable:latest
    User name: ops
    Password: *********
    Confirm password: *********
    + Looking up the VM "coreos"
    info:    Using the VM Size "Standard_A1"
    info:    The [OS, Data] Disk or image configuration requires storage account
    + Retrieving storage accounts
    info:    Could not find any storage accounts in the region "westus", trying to create new one
    + Creating storage account "cli9fd3fce49e9a9b3d14302" in "westus"
    + Looking up the storage account cli9fd3fce49e9a9b3d14302
    + Looking up the NIC "coreo-westu-1430261891570-nic"
    info:    An nic with given name "coreo-westu-1430261891570-nic" not found, creating a new one
    + Looking up the virtual network "coreo-westu-1430261891570-vnet"
    info:    Preparing to create new virtual network and subnet
    / Creating a new virtual network "coreo-westu-1430261891570-vnet" [address prefix: "10.0.0.0/16"] with subnet "coreo-westu-1430261891570-sne+" [address prefix: "10.0.1.0/24"]
    + Looking up the virtual network "coreo-westu-1430261891570-vnet"
    + Looking up the subnet "coreo-westu-1430261891570-snet" under the virtual network "coreo-westu-1430261891570-vnet"
    info:    Found public ip parameters, trying to setup PublicIP profile
    + Looking up the public ip "coreo-westu-1430261891570-pip"
    info:    PublicIP with given name "coreo-westu-1430261891570-pip" not found, creating a new one
    + Creating public ip "coreo-westu-1430261891570-pip"
    + Looking up the public ip "coreo-westu-1430261891570-pip"
    + Creating NIC "coreo-westu-1430261891570-nic"
    + Looking up the NIC "coreo-westu-1430261891570-nic"
    + Creating VM "coreos"
    + Looking up the VM "coreos"
    + Looking up the NIC "coreo-westu-1430261891570-nic"
    + Looking up the public ip "coreo-westu-1430261891570-pip"
    data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Compute/virtualMachines/coreos
    data:    ProvisioningState               :Succeeded
    data:    Name                            :coreos
    data:    Location                        :westus
    data:    FQDN                            :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:
    data:    Storage Profile:
    data:      Image reference:
    data:        Publisher                   :coreos
    data:        Offer                       :coreos
    data:        Sku                         :stable
    data:        Version                     :633.1.0
    data:
    data:      OS Disk:
    data:        OSType                      :Linux
    data:        Name                        :cli9fd3fce49e9a9b3d-os-1430261892283
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :https://cli9fd3fce49e9a9b3d14302.blob.core.windows.net/vhds/cli9fd3fce49e9a9b3d-os-1430261892283.vhd
    data:
    data:    OS Profile:
    data:      Computer Name                 :coreos
    data:      User Name                     :ops
    data:      Linux Configuration:
    data:        Disable Password Auth       :false
    data:
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Network/networkInterfaces/coreo-westu-1430261891570-nic
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-30-72-E3
    data:          Provisioning State        :Succeeded
    data:          Name                      :coreo-westu-1430261891570-nic
    data:          Location                  :westus
    data:            Private IP alloc-method :Dynamic
    data:            Private IP address      :10.0.1.4
    data:            Public IP address       :104.40.24.124
    data:            FQDN                    :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
    info:    vm quick-create command OK

E, assim, você obtém sua nova VM.

## <a id="deploy-a-vm-in-azure-from-a-template"></a>Tarefa: implantar uma VM no Azure a partir de um modelo

Use as instruções nestas seções para implantar uma nova VM do Azure usando um modelo com a CLI do Azure. O modelo cria uma única máquina virtual em uma nova rede virtual com uma única sub-rede e, diferentemente de `azure vm quick-create`, permite descrever o que você deseja com precisão e repeti-lo sem erros. Aqui está o que o modelo cria:

![](./media/virtual-machines-common-cli-deploy-templates/new-vm.png)

### Etapa 1: examinar o arquivo JSON para os parâmetros de modelo

Aqui está o conteúdo do arquivo JSON para o modelo. (O modelo também está localizado no [GitHub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json).)

Os modelos são flexíveis, assim, o designer pode ter optado por lhe fornecer muitos parâmetros ou oferecer apenas alguns criando um modelo mais fixo. Para coletar as informações, você precisa passar o modelo como parâmetros, abrir o arquivo de modelo (este tópico tem um modelo embutido, abaixo) e examinar os valores de **parâmetros**.

Nesse caso, o modelo abaixo pedirá:

- Um nome de conta de armazenamento exclusivo.
- Um nome de usuário de administrador para a VM.
- Uma senha.
- Um nome de domínio a ser usado pelo mundo exterior.
- Um número de versão do Servidor do Ubuntu, mas apenas um de uma lista será aceito.

Após decidir quanto a esses valores, você estará pronto para criar um grupo e implantar esse modelo em sua assinatura do Azure.

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "newStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "Unique DNS name for the storage account where the virtual machine's disks will be placed."
        }
        },
        "adminUsername": {
        "type": "string",
        "metadata": {
            "description": "User name for the virtual machine."
        }
        },
        "adminPassword": {
        "type": "securestring",
        "metadata": {
            "description": "Password for the virtual machine."
        }
        },
        "dnsNameForPublicIP": {
        "type": "string",
        "metadata": {
            "description": "Unique DNS name for the public IP used to access the virtual machine."
        }
        },
        "ubuntuOSVersion": {
        "type": "string",
        "defaultValue": "14.04.2-LTS",
        "allowedValues": [
            "12.04.5-LTS",
            "14.04.2-LTS",
            "15.04"
        ],
        "metadata": {
            "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version. Allowed values: 12.04.5-LTS, 14.04.2-LTS, 15.04."
        }
        }
    },
    "variables": {
        "location": "West US",
        "imagePublisher": "Canonical",
        "imageOffer": "UbuntuServer",
        "OSDiskName": "osdiskforlinuxsimple",
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "storageAccountType": "Standard_LRS",
        "publicIPAddressName": "myPublicIP",
        "publicIPAddressType": "Dynamic",
        "vmStorageAccountContainerName": "vhds",
        "vmName": "MyUbuntuVM",
        "vmSize": "Standard_D1",
        "virtualNetworkName": "MyVNET",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
    },
    "resources": [
        {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[parameters('newStorageAccountName')]",
        "apiVersion": "2015-05-01-preview",
        "location": "[variables('location')]",
        "properties": {
            "accountType": "[variables('storageAccountType')]"
        }
        },
        {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('publicIPAddressName')]",
        "location": "[variables('location')]",
        "properties": {
            "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
            "dnsSettings": {
            "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
            }
        }
        },
        {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/virtualNetworks",
        "name": "[variables('virtualNetworkName')]",
        "location": "[variables('location')]",
        "properties": {
            "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
            },
            "subnets": [
            {
                "name": "[variables('subnetName')]",
                "properties": {
                "addressPrefix": "[variables('subnetPrefix')]"
                }
            }
            ]
        }
        },
        {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('nicName')]",
        "location": "[variables('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
        ],
        "properties": {
            "ipConfigurations": [
            {
                "name": "ipconfig1",
                "properties": {
                "privateIPAllocationMethod": "Dynamic",
                "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                },
                "subnet": {
                    "id": "[variables('subnetRef')]"
                }
                }
            }
            ]
        }
        },
        {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[variables('location')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
            "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
        ],
        "properties": {
            "hardwareProfile": {
            "vmSize": "[variables('vmSize')]"
            },
            "osProfile": {
            "computername": "[variables('vmName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
            "imageReference": {
                "publisher": "[variables('imagePublisher')]",
                "offer": "[variables('imageOffer')]",
                "sku": "[parameters('ubuntuOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            }
            },
            "networkProfile": {
            "networkInterfaces": [
                {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                }
            ]
            }
        }
        }
    ]
    }


### Etapa 2: criar a máquina virtual usando o modelo

Depois que tiver os valores de parâmetros prontos, você deverá criar um grupo de recursos para a implantação do modelo e, em seguida, implantar o modelo.

Para criar o grupo de recursos, digite `azure group create <group name> <location>` com o nome do grupo desejado e o local do data center no qual você deseja implantar. Isso acontece rapidamente:

    azure group create myResourceGroup westus
    info:    Executing command group create
    + Getting resource group myResourceGroup
    + Creating resource group myResourceGroup
    info:    Created resource group myResourceGroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup
    data:    Name:                myResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK


Agora, para criar a implantação, chame `azure group deployment create` e passe:

- O arquivo de modelo (caso você tenha salvo o modelo JSON acima em um arquivo local).
- Um URI de modelo (caso você deseje apontar para o arquivo no Github ou em algum outro endereço da Web).
- O grupo de recursos no qual você deseja implantar.
- E um nome de implantação opcional.

Você será solicitado a fornecer os valores dos parâmetros na seção "parâmetros" do arquivo JSON. Quando você tiver especificado todos os valores de parâmetros, a implantação se iniciará.

Aqui está um exemplo:

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json myResourceGroup firstDeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    newStorageAccountName: storageaccount
    adminUsername: ops
    adminPassword: password
    dnsNameForPublicIP: newdomainname

Você receberá o seguinte tipo de informações:

    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "firstDeployment"
    + Registering providers
    info:    Registering provider microsoft.storage
    info:    Registering provider microsoft.network
    info:    Registering provider microsoft.compute
    + Waiting for deployment to complete
    data:    DeploymentName     : firstDeployment
    data:    ResourceGroupName  : myResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T07:53:55.1828878Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                   Type          Value
    data:    ---------------------  ------------  -------------
    data:    newStorageAccountName  String        storageaccount
    data:    adminUsername          String        ops
    data:    adminPassword          SecureString  undefined
    data:    dnsNameForPublicIP     String        newdomainname
    data:    ubuntuOSVersion        String        14.10
    info:    group deployment create command OK



## <a id="create-a-custom-vm-image"></a>Tarefa: Criar uma imagem de VM personalizada

Você já viu o uso básico dos modelos acima. Portanto, agora podemos usar instruções semelhantes para criar uma VM personalizada por meio de um arquivo .vhd específico no Azure usando um modelo através da CLI do Azure. A diferença aqui é que esse modelo cria uma única máquina virtual de um VHD (disco rígido virtual) especificado.

### Etapa 1: examinar o arquivo JSON do modelo

Aqui está o conteúdo do arquivo JSON do modelo que esta seção usa como um exemplo.

Novamente, você precisará localizar os valores que deseja inserir para os parâmetros que não têm valores padrão. Quando você executar o comando `azure group deployment create`, a CLI do Azure solicitará que você insira esses valores.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
            "userImageStorageAccountName": {
                "type": "string",
                "defaultValue" : "userImageStorageAccountName"
            },
            "userImageStorageContainerName" : {
                "type" : "string",
                "defaultValue" : "userImageStorageContainerName"
            },
            "userImageVhdName" : {
                "type" : "string",
                "defaultValue" : "userImageVhdName"
            },
            "dnsNameForPublicIP" : {
                "type" : "string",
                "defaultValue": "uniqueDnsNameForPublicIP"
            },
            "adminUserName": {
                "type": "string"
            },
            "adminPassword": {
                "type": "securestring"
            },
            "osType" : {
                "type" : "string",
                "allowedValues" : [
                    "windows",
                    "linux"
                ]
            },
            "subscriptionId":{
                "type" : "string"
            },
            "location": {
                "type": "String",
                "defaultValue" : "West US"
            },
            "vmSize": {
                "type": "string",
                "defaultValue": "Standard_A2"
            },
            "publicIPAddressName": {
                "type": "string",
                "defaultValue" : "myPublicIP"
            },
            "vmName": {
                "type": "string",
                "defaultValue" : "myVM"
            },
            "virtualNetworkName":{
                "type" : "string",
                "defaultValue" : "myVNET"
            },
            "nicName":{
                "type" : "string",
                "defaultValue":"myNIC"
            }
        },
        "variables": {
            "addressPrefix":"10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix" : "10.0.0.0/24",
            "subnet2Prefix" : "10.0.1.0/24",
            "publicIPAddressType" : "Dynamic",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "userImageName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('userImageVhdName'))]",
            "osDiskVhdName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
          "apiVersion": "2014-12-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[variables('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[variables('subnet1Name')]",
                "properties" : {
                    "addressPrefix": "[variables('subnet1Prefix')]"
                }
              },
              {
                "name": "[variables('subnet2Name')]",
                "properties" : {
                    "addressPrefix": "[variables('subnet2Prefix')]"
                }
              }
            ]
          }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                {
                    "name": "ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod": "Dynamic",
                        "publicIPAddress": {
                            "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                        },
                        "subnet": {
                            "id": "[variables('subnet1Ref')]"
                        }
                    }
                }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "osDisk" : {
                        "name" : "[concat(parameters('vmName'),'-osDisk')]",
                        "osType" : "[parameters('osType')]",
                        "caching" : "ReadWrite",
                        "image" : {
                            "uri" : "[variables('userImageName')]"
                        },
                        "vhd" : {
                            "uri" : "[variables('osDiskVhdName')]"
                        }
                    }
                },
                "networkProfile": {
                    "networkInterfaces" : [
                    {
                        "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                    }
                    ]
                }
            }
        }
        ]
    }

### Etapa 2: obter o VHD

Obviamente, você precisará de um .vhd para isso. Você pode usar um que já tenha no Azure ou pode carregar um.

Para uma máquina virtual baseada no Windows, consulte [Criar e carregar um VHD do Windows Server no Azure](../articles/virtual-machines/virtual-machines-windows-classic-createupload-vhd.md).

Para uma máquina virtual baseada no Linux, confira [Criando e carregando um disco rígido virtual que contém o sistema operacional Linux](../articles/virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md).

### Etapa 3: criar a máquina virtual usando o modelo

Agora, você está pronto para criar uma nova máquina virtual com base no .vhd. Crie um grupo para a implantação usando o `azure group create <location>`:

    azure group create myResourceGroupUser eastus
    info:    Executing command group create
    + Getting resource group myResourceGroupUser
    + Creating resource group myResourceGroupUser
    info:    Created resource group myResourceGroupUser
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupUser
    data:    Name:                myResourceGroupUser
    data:    Location:            eastus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK

Em seguida, crie a implantação usando a opção `--template-uri` para chamar o modelo diretamente (ou você pode utilizar a opção `--template-file` para usar um arquivo salvo localmente). Observe que, como o modelo tem padrões especificados, você será solicitado a fornecer somente alguns itens. Se você implantar o modelo em locais diferentes, talvez ocorram alguns conflitos de nomenclatura com os valores padrão (particularmente com o nome DNS que você criar).

    azure group deployment create \
    > --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json \
    > myResourceGroup \
    > customVhdDeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    adminUserName: ops
    adminPassword: password
    osType: linux
    subscriptionId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

A saída será semelhante a:

    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "customVhdDeployment"
    + Registering providers
    info:    Registering provider microsoft.network
    info:    Registering provider microsoft.compute
    + Waiting for deployment to complete
    error:   Deployment provisioning state was not successful
    data:    DeploymentName     : customVhdDeployment
    data:    ResourceGroupName  : myResourceGroupUser
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T14:55:48.0963829Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                           Type          Value
    data:    -----------------------------  ------------  ------------------------------------
    data:    userImageStorageAccountName    String        userImageStorageAccountName
    data:    userImageStorageContainerName  String        userImageStorageContainerName
    data:    userImageVhdName               String        userImageVhdName
    data:    dnsNameForPublicIP             String        uniqueDnsNameForPublicIP
    data:    adminUserName                  String        ops
    data:    adminPassword                  SecureString  undefined
    data:    osType                         String        linux
    data:    subscriptionId                 String        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    data:    location                       String        West US
    data:    vmSize                         String        Standard_A2
    data:    publicIPAddressName            String        myPublicIP
    data:    vmName                         String        myVM
    data:    virtualNetworkName             String        myVNET
    data:    nicName                        String        myNIC
    info:    group deployment create command OK


## <a id="deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer"></a>Tarefa: implantar um aplicativo com várias VMs que usa uma rede virtual e um balanceador externo de carga

Esse modelo permite que você crie duas máquinas virtuais sob um balanceador de carga e configure uma regra de balanceamento de carga na porta 80. Esse modelo também implanta uma Conta de Armazenamento, Rede Virtual, Endereço IP Público, Conjunto de Disponibilidade e Interfaces de Rede.

![](./media/virtual-machines-common-cli-deploy-templates/multivmextlb.png)

Siga estas etapas para implantar um aplicativo com várias VMs que usa uma rede virtual e um balanceador de carga usando um modelo do Gerenciador de Recursos no repositório de modelos do Github através dos comandos do Azure PowerShell.

### Etapa 1: examinar o arquivo JSON do modelo

Aqui está o conteúdo do arquivo JSON para o modelo. Se você quiser a versão mais recente, ela está localizada [no repositório Github para modelos](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json). Este tópico usa a opção `--template-uri` para chamar o modelo, mas você também pode usar a opção `--template-file` para passar uma versão local.


    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "location": {
                "type": "string",
                "metadata": {
                  "description": "Location of resources"
                }
            },
            "storageAccountName": {
                "type": "string",
                "metadata": {
                  "description": "Name of storage account"
                }
            },
            "adminUsername": {
                "type": "string",
                "metadata": {
                  "description": "Admin user name"
                }
            },
            "adminPassword": {
                "type": "securestring",
                "metadata": {
                  "description": "Admin password"
                }
            },
            "dnsNameforLBIP": {
                "type": "string",
                "metadata": {
                  "description": "DNS for load balancer IP"
                }
            },
            "backendPort": {
                "type": "int",
                "defaultValue": 3389,
                "metadata": {
                  "description": "Back-end port"
                }
            },
            "vmNamePrefix": {
                "type": "string",
                "defaultValue": "myVM",
                "metadata": {
                  "description": "Prefix to use for VM names"
                }
            },
            "vmSourceImageName": {
                "type": "string",
                "defaultValue": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd"
            },
            "lbName": {
                "type": "string",
                "defaultValue": "myLB",
                "metadata": {
                  "description": "Load balancer name"
                }
            },
            "nicNamePrefix": {
                "type": "string",
                "defaultValue": "nic",
                "metadata": {
                  "description": "Network interface name prefix"
                }
            },
            "publicIPAddressName": {
                "type": "string",
                "defaultValue": "myPublicIP",
                "metadata": {
                  "description": "Public IP name"
                }
            },
            "vnetName": {
                "type": "string",
                "defaultValue": "myVNET",
                "metadata": {
                  "description": "Virtual network name"
                }
            },
            "vmSize": {
                "type": "string",
                "defaultValue": "Standard_A1",
                "metadata": {
                  "description": "Size of the VM"
                }
            }
        },
        "variables": {
            "storageAccountType": "Standard_LRS",
            "vmStorageAccountContainerName": "vhds",
            "availabilitySetName": "myAvSet",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet-1",
            "subnetPrefix": "10.0.0.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
            "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
            "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
            "numberOfInstances": 2,
            "nicId1": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 0))]",
            "nicId2": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 1))]",
            "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LBFE')]",
            "backEndIPConfigID1": "[concat(variables('nicId1'),'/ipConfigurations/ipconfig1')]",
            "backEndIPConfigID2": "[concat(variables('nicId2'),'/ipConfigurations/ipconfig1')]",
            "sourceImageName": "[concat('/', subscription().subscriptionId,'/services/images/',parameters('vmSourceImageName'))]",
            "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/LBBE')]",
            "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
        },
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[parameters('storageAccountName')]",
                "apiVersion": "2015-05-01-preview",
                "location": "[parameters('location')]",
                "properties": {
                    "accountType": "[variables('storageAccountType')]"
                }
            },
            {
                "type": "Microsoft.Compute/availabilitySets",
                "name": "[variables('availabilitySetName')]",
                "apiVersion": "2015-05-01-preview",
                "location": "[parameters('location')]",
                "properties": { }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameforLBIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('vnetName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnetName')]",
                            "properties": {
                                "addressPrefix": "[variables('subnetPrefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
                "location": "[parameters('location')]",
                "copy": {
                    "name": "nicLoop",
                    "count": "[variables('numberOfInstances')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "subnet": {
                                    "id": "[variables('subnetRef')]"
                                }
                            },
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/backendAddressPools/LBBE')]"
                                }
                            ],
                            "loadBalancerInboundNatRules": [
                                {
                                    "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/inboundNatRule/RDP-VM', copyindex())]"
                                }
                            ]


                        }
                    ]

                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "name": "[parameters('lbName')]",
                "type": "Microsoft.Network/loadBalancers",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "nicLoop",
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LBFE",
                            "properties": {
                                "publicIPAddress": {
                                    "id": "[variables('publicIPAddressID')]"
                                }
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LBBE"

                        }
                    ],
                    "inboundNatRules": [
                        {
                            "name": "RDP-VM1",
                            "properties": {
                                "frontendIPConfiguration":
                                    {
                                        "id": "[variables('frontEndIPConfigID')]"
                                    },
                                "protocol": "tcp",
                                "frontendPort": 50001,
                                "backendPort": 3389,
                                "enableFloatingIP": false
                            }
                        },
                        {
                            "name": "RDP-VM2",
                            "properties": {
                                "frontendIPConfiguration":
                                    {
                                        "id": "[variables('frontEndIPConfigID')]"
                                    },
                                "protocol": "tcp",
                                "frontendPort": 50002,
                                "backendPort": 3389,
                                "enableFloatingIP": false
                            }
                        }
                    ],
                    "loadBalancingRules": [
                        {
                            "name": "LBRule",
                            "properties": {
                                "frontendIPConfiguration": {
                                    "id": "[variables('frontEndIPConfigID')]"
                                },
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID')]"
                                },
                                "protocol": "tcp",
                                "frontendPort": 80,
                                "backendPort": 80,
                                "enableFloatingIP": false,
                                "idleTimeoutInMinutes": 5,
                                "probe": {
                                    "id": "[variables('lbProbeID')]"
                                }
                            }
                        }
                    ],
                    "probes": [
                        {
                            "name": "tcpProbe",
                            "properties": {
                                "protocol": "tcp",
                                "port": 80,
                                "intervalInSeconds": "5",
                                "numberOfProbes": "2"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
                "copy": {
                    "name": "virtualMachineLoop",
                    "count": "[variables('numberOfInstances')]"
                },
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
                    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
                ],
                "properties": {
                    "availabilitySet": {
                        "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
                    },
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[concat(parameters('vmNamePrefix'), copyIndex())]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]"
                    },
                    "storageProfile": {
                        "sourceImage": {
                            "id": "[variables('sourceImageName')]"
                        },
                        "destinationVhdsContainer": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/')]"
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
                            }
                        ]
                    }
                }
            }
        ]
    }

### Etapa 2: criar a implantação usando o modelo

Crie um grupo de recursos para o modelo usando o `azure group create <location>`. Em seguida, crie uma implantação nesse grupo de recursos usando `azure group deployment create` e transmitindo o grupo de recursos e um nome de implantação, e respondendo aos prompts para os parâmetros no modelo que não tinham valores padrão.


    azure group create lbgroup westus
    info:    Executing command group create
    + Getting resource group lbgroup
    + Creating resource group lbgroup
    info:    Created resource group lbgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/lbgroup
    data:    Name:                lbgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK


Agora, use o comando `azure group deployment create` e a opção `--template-uri` para implantar o modelo. Esteja pronto para fornecer os valores de parâmetros quando eles forem solicitados, conforme mostrado a seguir.

    azure group deployment create \
    > --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json \
    > lbgroup \
    > newdeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    location: westus
    newStorageAccountName: storagename
    adminUsername: ops
    adminPassword: password
    dnsNameforLBIP: lbdomainname
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "newdeployment"
    + Registering providers
    info:    Registering provider microsoft.storage
    info:    Registering provider microsoft.compute
    info:    Registering provider microsoft.network
    + Waiting for deployment to complete
    data:    DeploymentName     : newdeployment
    data:    ResourceGroupName  : lbgroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T20:58:40.1678876Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                   Type          Value
    data:    ---------------------  ------------  ----------------------
    data:    location               String        westus
    data:    newStorageAccountName  String        storagename
    data:    adminUsername          String        ops
    data:    adminPassword          SecureString  undefined
    data:    dnsNameforLBIP         String        lbdomainname
    data:    backendPort            Int           3389
    data:    vmNamePrefix           String        myVM
    data:    imagePublisher         String        MicrosoftWindowsServer
    data:    imageOffer             String        WindowsServer
    data:    imageSKU               String        2012-R2-Datacenter
    data:    lbName                 String        myLB
    data:    nicNamePrefix          String        nic
    data:    publicIPAddressName    String        myPublicIP
    data:    vnetName               String        myVNET
    data:    vmSize                 String        Standard_A1
    info:    group deployment create command OK

Observe que esse modelo implanta uma imagem do Windows Server. No entanto, ela poderia ser facilmente substituída por qualquer imagem do Linux. Deseja criar um cluster Docker com vários gerenciadores por nuvem? [Você consegue](https://azure.microsoft.com/documentation/templates/docker-swarm-cluster/).

## <a id="remove-a-resource-group"></a>Tarefa: Remover um grupo de recursos

Lembre-se que você pode reimplantar em um grupo de recursos, mas se terminar de usá-lo, poderá excluí-lo usando `azure group delete <group name>`.

    azure group delete myResourceGroup
    info:    Executing command group delete
    Delete resource group myResourceGroup? [y/n] y
    + Deleting resource group myResourceGroup
    info:    group delete command OK

## <a id="show-the-log-for-a-resource-group-deployment"></a>Tarefa: Mostrar o log para uma implantação do grupo de recursos

Essa é uma tarefa comum ao se criar ou usar modelos. A chamada para exibir os logs de implantação de um grupo é `azure group log show <groupname>`, que exibe várias informações úteis para entender por que algo aconteceu ou não. (Para obter mais informações sobre como solucionar problemas em suas implantações, bem como outras informações sobre problemas, confira [Solucionando problemas das implantações do grupo de recursos no Azure](../articles/resource-manager-troubleshoot-deployments-cli.md).)

Para lidar com falhas específicas, por exemplo, você pode usar ferramentas como **jq** para consultar itens de forma um pouco mais precisa, por exemplo, quais falhas individuais precisam ser corrigidas. O exemplo a seguir usa **jq** para analisar um log de implantação para **lbgroup**, procurando falhas.

    azure group log show lbgroup -l --json | jq '.[] | select(.status.value == "Failed") | .properties'

Você pode descobrir rapidamente qual foi o problema, corrigi-lo e tentar novamente. No caso a seguir, o modelo estava criando duas VMs ao mesmo tempo, o que criava um bloqueio no .vhd. (Depois termos modificado o modelo, a implantação teve êxito rapidamente.)

    {
      "statusCode": "Conflict",
      "statusMessage": "{"status":"Failed","error":{"code":"ResourceDeploymentFailure","message":"The resource operation completed with terminal provisioning state 'Failed'.","details":[{"code":"AcquireDiskLeaseFailed","message":"Failed to acquire lease while creating disk 'osdisk' using blob with URI http://storage.blob.core.windows.net/vhds/osdisk.vhd."}]}}"
    }


## <a id="display-information-about-a-virtual-machine"></a>Tarefa: Exibir informações sobre uma máquina virtual

É possível ver informações sobre VMs específicas em seu grupo de recursos usando o `azure vm show <groupname> <vmname> command`. Se você tiver mais de uma VM em seu grupo, talvez seja necessário primeiro listar as VMs em um grupo usando o `azure vm list <groupname>`.

    azure vm list zoo
    info:    Executing command vm list
    + Getting virtual machines
    data:    Name   ProvisioningState  Location  Size
    data:    -----  -----------------  --------  -----------
    data:    myVM0  Succeeded          westus    Standard_A1
    data:    myVM1  Failed             westus    Standard_A1

E, em seguida, procurar por myVM1:

    azure vm show zoo myVM1
    info:    Executing command vm show
    + Looking up the VM "myVM1"
    + Looking up the NIC "nic1"
    data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/virtualMachines/myVM1
    data:    ProvisioningState               :Failed
    data:    Name                            :myVM1
    data:    Location                        :westus
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:
    data:    Storage Profile:
    data:      Image reference:
    data:        Publisher                   :MicrosoftWindowsServer
    data:        Offer                       :WindowsServer
    data:        Sku                         :2012-R2-Datacenter
    data:        Version                     :latest
    data:
    data:      OS Disk:
    data:        OSType                      :Windows
    data:        Name                        :osdisk
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :http://zoostorageralph.blob.core.windows.net/vhds/osdisk.vhd
    data:
    data:    OS Profile:
    data:      Computer Name                 :myVM1
    data:      User Name                     :ops
    data:      Windows Configuration:
    data:        Provision VM Agent          :true
    data:        Enable automatic updates    :true
    data:
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Network/networkInterfaces/nic1
    data:          Primary                   :false
    data:          Provisioning State        :Succeeded
    data:          Name                      :nic1
    data:          Location                  :westus
    data:            Private IP alloc-method :Dynamic
    data:            Private IP address      :10.0.0.5
    data:
    data:    AvailabilitySet:
    data:      Id                            :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/availabilitySets/MYAVSET
    info:    vm show command OK


> [AZURE.NOTE] Se desejar armazenar e manipular de forma programática a saída dos seus comandos de console, convém usar uma ferramenta de análise JSON, como **[jq](https://github.com/stedolan/jq)** ou **[jsawk](https://github.com/micha/jsawk)**, ou bibliotecas de idiomas que sejam adequadas à tarefa.

## <a id="log-on-to-a-linux-based-virtual-machine"></a>Tarefa: Fazer logon em uma máquina virtual baseada no Linux

Normalmente, as máquinas Linux são conectadas por meio de SSH. Para obter mais informações, veja [Como usar o SSH com o Linux no Azure](../articles/virtual-machines/virtual-machines-linux-ssh-from-linux.md).

## <a id="stop-a-virtual-machine"></a>Tarefa: Parar uma VM

Execute este comando:

    azure vm stop <group name> <virtual machine name>

>[AZURE.IMPORTANT] Use esse parâmetro para manter o VIP (IP virtual) da rede virtual, caso essa seja a última VM nessa rede virtual. <br><br> Se você usar o parâmetro `StayProvisioned`, ainda será cobrado pela VM.

## <a id="start-a-virtual-machine"></a>Tarefa: iniciar uma VM

Execute este comando:

    azure vm start <group name> <virtual machine name>

## <a id="attach-a-data-disk"></a>Tarefa: Anexar um disco de dados

Você também precisará decidir se deseja anexar um novo disco ou um que contenha dados. Para um novo disco, o comando cria o arquivo .vhd e anexa-o no mesmo comando.

Para anexar um novo disco, execute este comando:

     azure vm disk attach-new <resource-group> <vm-name> <size-in-gb>

Para anexar um disco de dados existente, execute este comando:

    azure vm disk attach <resource-group> <vm-name> [vhd-url]

Em seguida, você precisará montar o disco, como faria normalmente no Linux (ou no Windows).


## Próximas etapas

Para obter mais exemplos de uso da CLI do Azure com o modo **arm**, confira [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciador de Recursos do Azure](../articles/xplat-cli-azure-resource-manager.md). Para saber mais sobre os recursos do Azure e seus conceitos, confira [Visão geral do Gerenciador de Recursos do Azure](../articles/resource-group-overview.md).


Para obter mais modelos que você pode usar, confira [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) e [Estruturas de aplicativos usando modelos](../articles/virtual-machines/virtual-machines-linux-app-frameworks.md).

<!---HONumber=AcomDC_0330_2016-->