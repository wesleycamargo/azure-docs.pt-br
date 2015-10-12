<properties
  pageTitle="Criar um cluster do MongoDB no Ubuntu | Microsoft Azure"
  description="Crie um cluster MongoDB no Ubuntu usando um modelo do Gerenciador de Recursos do Azure por meio do Azure PowerShell ou da CLI do Azure"
  services="virtual-machines"
  documentationCenter=""
  authors="scoriani"
  manager="timlt"
  editor="tysonn"
  tags="azure-resource-manager"/>

<tags
  ms.service="virtual-machines"
  ms.workload="multiple"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="04/29/2015"
  ms.author="scoriani"/>

# Criar um cluster MongoDB no Ubuntu usando um modelo do Gerenciador de Recursos do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda a criação de recursos com o modelo de implantação do Gerenciador de Recursos.

O MongoDB é um banco de dados de software livre que oferece alto desempenho, alta disponibilidade e dimensionamento automático. Você pode instalar o MongoDB como um banco de dados autônomo ou em um cluster aproveitando os recursos internos de replicação. Em alguns casos, você pode usar a replicação para aumentar a capacidade de leitura. Os clientes têm a capacidade de enviar operações de leitura e gravação a servidores diferentes. Você também pode manter cópias em data centers diferentes para aumentar a localidade e a disponibilidade dos dados para aplicativos distribuídos. Com o MongoDB, a replicação também fornece redundância e aumenta a disponibilidade de dados. Com várias cópias de dados em servidores de bancos de dados diferentes, a replicação protege um banco de dados contra a perda de um único servidor. A replicação também permite recuperar-se de falhas de hardware e interrupções de serviço. Com as cópias adicionais dos dados, você pode dedicar um deles para recuperação de desastre, relatórios ou backup.

Além dos vários tipos que já estavam disponíveis no Azure Marketplace, agora você pode facilmente implantar um novo cluster MongoDB em VMs do Ubuntu usando um modelo do Gerenciador de Recursos do Azure implantado por meio do [Azure PowerShell](../powershell-install-configure.md) ou da [CLI do Azure](../xplat-cli-install.md).

Clusters recém-implantados com base nesse modelo terão a topologia descrita no diagrama a seguir, embora outras topologias possam ser facilmente obtidas por meio da personalização do modelo apresentado neste artigo.

![cluster-architecture](media/virtual-machines-mongodb-template/cluster-architecture.png)

Por meio de um parâmetro, você pode definir o número de nós que serão implantados no novo cluster MongoDB e, com base em outro parâmetro, uma instância VM (Jumpbox) com um endereço IP público também pode ser implantada na mesma VNET, dando a você a capacidade de conectar-se ao cluster de Internet pública e realizar qualquer tipo de tarefa administrativa relacionada a esse cluster. Outra opção disponível como um parâmetro é a capacidade de adicionar um nó Arbiter ao conjunto de réplicas, que normalmente é sugerido quando ele tem um número par de membros. Para obter mais informações sobre topologias de replicação e detalhes do MongoDB, confira a [documentação do MongoDB](http://docs.mongodb.org/manual/core/replication-introduction/) oficial.

Depois que a implantação for concluída, você poderá acessar o Jumpbox usando o endereço DNS configurado na porta SSH 22.

Antes de lidar com mais detalhes relacionados ao Gerenciador de Recursos do Azure e ao modelo usado para essa implantação, verifique se você tem o PowerShell ou a CLI do Azure configurados corretamente.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Criar um cluster MongoDB com um modelo do Gerenciador de Recursos

Siga estas etapas para criar um cluster MongoDB usando um modelo do Gerenciador de Recursos do repositório de modelos Github. Cada etapa inclui instruções para o PowerShell do Azure e a CLI do Azure.

### Etapa 1-a: baixar os arquivos de modelo usando o PowerShell

Crie uma pasta local para o modelo JSON e outros arquivos associados (por exemplo, C:\\Azure\\Templates\\MongoDB).

No exemplo a seguir, substitua o nome da pasta de sua pasta local e execute os comandos.

    $folderName="C:\Azure\Templates\MongoDB"
    $webclient = New-Object System.Net.WebClient
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy.json"
    $filePath = $folderName + "\azuredeploy.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy-parameters.json"
    $filePath = $folderName + "\azuredeploy-parameters.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/arbiter-resources.json"
    $filePath = $folderName + "\arbiter-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/empty-resources.json"
    $filePath = $folderName + "\empty-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/jumpbox-resources.json"
    $filePath = $folderName + "\jumpbox-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D1.json"
    $filePath = $folderName + "\member-resources-D1.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D2.json"
    $filePath = $folderName + "\member-resources-D2.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D3.json"
    $filePath = $folderName + "\member-resources-D3.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D4.json"
    $filePath = $folderName + "\member-resources-D4.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D11.json"
    $filePath = $folderName + "\member-resources-D11.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D12.json"
    $filePath = $folderName + "\member-resources-D12.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D13.json"
    $filePath = $folderName + "\member-resources-D13.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D14.json"
    $filePath = $folderName + "\member-resources-D14.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/mongodb-ubuntu-install.sh"
    $filePath = $folderName + "\mongodb-ubuntu-install.sh"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/metadata.json"
    $filePath = $folderName + "\metadata.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/shared-resources.json"
    $filePath = $folderName + "\shared-resources.json"
    $webclient.DownloadFile($url,$filePath)  

### Etapa 1-b: baixar os arquivos de modelo usando a CLI do Azure

O exemplo a seguir mostra como clonar todo o repositório do modelo usando um cliente Git de sua escolha.

    git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Quando terminar, procure a pasta mongodb-high-availability no diretório C:\\Azure\\Templates.

### Etapa 2 (opcional): compreender os parâmetros do modelo

Ao implantar soluções não triviais, como um cluster MongoDB, você deve especificar um conjunto de parâmetros de configuração para lidar com as várias configurações necessárias. Declarando esses parâmetros na definição do modelo, você pode especificar valores durante a implantação por meio de um arquivo externo ou na linha de comando.

Na seção "parâmetros" no início do arquivo azuredeploy.json, você encontrará o conjunto de parâmetros necessários para o modelo configurar um cluster MongoDB. O exemplo a seguir mostra a seção de parâmetros de arquivo azuredeploy.json desse modelo.

    "parameters": {
      "adminUsername": {
          "type": "string",
          "metadata": {
            "Description": "Administrator user name used when provisioning virtual machines (which also becomes a system user administrator in MongoDB)"
          }
        },
        "adminPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Administrator password used when provisioning virtual machines (which is also a password for the system administrator in MongoDB)"
          }
        },
        "storageAccountName": {
          "type": "string",
          "defaultValue": "",
          "metadata": {
            "Description": "Unique namespace for the storage account where the Virtual Machine's disks will be placed (this name will be used as a prefix to create one or more storage accounts as per t-shirt size)"
          }
        },
        "region": {
          "type": "string",
          "metadata": {
            "Description": "Location where resources will be provisioned"
          }
        },
        "virtualNetworkName": {
          "type": "string",
          "defaultValue": "mongodbVnet",
          "metadata": {
            "Description": "The arbitrary name of the virtual network provisioned for the MongoDB deployment"
          }
        },
        "subnetName": {
          "type": "string",
          "defaultValue": "mongodbSubnet",
          "metadata": {
            "Description": "Subnet name for the virtual network that resources will be provisioned in to"
          }
        },
        "addressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/16",
          "metadata": {
            "Description": "The network address space for the virtual network"
          }
        },
        "subnetPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/24",
          "metadata": {
            "Description": "The network address space for the virtual subnet"
          }
        },
        "nodeAddressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.1",
          "metadata": {
            "Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
          }
        },
        "jumpbox": {
          "type": "string",
          "defaultValue": "Disabled",
          "allowedValues": [
          "Enabled",
          "Disabled"
          ],
          "metadata": {
            "Description": "The flag allowing to enable or disable provisioning of the jumpbox VM that can be used to access the MongoDB environment"
          }
        },
        "tshirtSize": {
          "type": "string",
          "defaultValue": "XSmall",
          "allowedValues": [
          "XSmall",
          "Small",
          "Medium",
          "Large",
          "XLarge",
          "XXLarge"
          ],
          "metadata": {
            "Description": "T-shirt size of the MongoDB deployment"
          }
        },
        "osFamily": {
          "type": "string",
          "defaultValue": "Ubuntu",
          "allowedValues": [
          "Ubuntu"
          ],
          "metadata": {
            "Description": "The target OS for the virtual machines running MongoDB"
          }
        },
        "mongodbVersion": {
          "type": "string",
          "defaultValue": "3.0.2",
          "metadata": {
            "Description": "The version of the MongoDB packages to be deployed"
          }
        },
        "replicaSetName": {
          "type": "string",
          "defaultValue": "rs0",
          "metadata": {
            "Description": "The name of the MongoDB replica set"
          }
        },
        "replicaSetKey": {
          "type": "string",
          "metadata": {
            "Description": "The shared secret key for the MongoDB replica set"
          }
        }
      },

Cada parâmetro tem detalhes como tipo de dados e valores permitidos. Isso permite a validação dos parâmetros passados durante a execução do modelo em um modo interativo (por exemplo, PowerShell ou CLI do Azure), bem como uma interface de usuário de autodescoberta que pode ser criada dinamicamente ao analisar a lista de parâmetros necessários e suas descrições.

### Etapa 3-a: Implantar um cluster MongoDB com um modelo usando o PowerShell

Prepare um arquivo de parâmetros para a sua implantação criando um arquivo JSON que contém os valores de tempo de execução para todos os parâmetros. Em seguida, esse arquivo será passado como uma única entidade para o comando de implantação. Se você não incluir um arquivo de parâmetros, o PowerShell usará qualquer valor padrão especificado no modelo e solicitará que você preencha os valores restantes.

O exemplo a seguir mostra o conjunto de parâmetros do arquivo azuredeploy-parameters.json.

    {
      "adminUsername": {
          "value": "MongoAdmin"
      },
      "adminPassword": {
          "value": ""
      },
      "storageAccountName": {
          "value": ""
      },
      "region": {
          "value": "West US"
      },
      "virtualNetworkName": {
          "value": "mongodbVnet"
      },
      "subnetName": {
          "value": "mongodbSubnet"
      },
      "addressPrefix": {
          "value": "10.0.0.0/16"
      },
      "subnetPrefix": {
          "value": "10.0.0.0/24"
      },
      "nodeAddressPrefix": {
          "value": "10.0.0.1"
      },
      "jumpbox": {
          "value": "Disabled"
      },
      "tshirtSize": {
          "value": "XSmall"
      },
      "osFamily": {
          "value": "Ubuntu"
      },
      "mongodbVersion": {
          "value": "3.0.2"
      },
      "replicaSetName": {
          "value": "rs0"
      },
      "replicaSetKey":  {
          "value": ""
      }
    }

Preencha o nome da implantação do Azure, o nome do grupo de recursos, o local do Azure e a pasta em que você salvou os arquivos de implantação JSON. Em seguida, execute o comando a seguir.

    $deployName="<deployment name>"
    $RGName="<resource group name>"
    $locName="<Azure location, such as West US>"
    $folderName="<folder name, such as C:\Azure\Templates\MongoDB>"
    $templateFile= $folderName + "\azuredeploy.json"
    $templateParameterFile= $folderName + "\azuredeploy-parameters.json"

    New-AzureResourceGroup –Name $RGName –Location $locName

    New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Quando você executar o comando **New-AzureResourceGroupDeployment**, isso extrairá valores de parâmetros do arquivo de parâmetros JSON e iniciará a execução do modelo de forma adequada. A definição e o uso de vários arquivos de parâmetros com seus diferentes ambientes (por exemplo, Teste ou Produção) promoverão a reutilização do modelo e simplificarão soluções complexas com vários ambientes.

Ao implantar, tenha em mente que uma nova Conta de Armazenamento do Azure precisará ser criada. Portanto, o nome fornecido como o parâmetro de conta de armazenamento precisa ser exclusivo e atender a todos os requisitos para uma Conta de Armazenamento do Azure (somente letras minúsculas e números).

Durante e após a implantação, você pode verificar todas as solicitações feitas durante o provisionamento, incluindo quaisquer erros ocorridos.

Para fazer isso, acesse o [Portal do Azure](https://portal.azure.com) e faça o seguinte:

- Clique em **Procurar** na barra de navegação à esquerda, role para baixo e clique em **Grupos de Recursos**.
- Depois que você clicar no grupo de recursos que acabou de criar, a folha Grupo de Recursos será mostrada.
- Ao clicar no gráfico de barras “Eventos” na parte “Monitoramento” da folha Grupo de Recursos, você poderá ver os eventos da sua implantação. Clicar em eventos individuais permite aprofundar-se ainda mais nos detalhes de cada operação individual feita em nome do modelo.

Depois dos testes, se você precisar remover esse grupo de recursos e todos os seus recursos (a conta de armazenamento, a máquina virtual e a rede virtual), use o comando a seguir.

    Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Etapa 3-b: Implantar um cluster MongoDB com um modelo usando a CLI do Azure

Para implantar um cluster MongoDB pela CLI do Azure, primeiro crie um Grupo de Recursos,especificando um nome e um local com o comando a seguir.

    azure group create mdbc "West US"

Passe o nome desse grupo de recursos, o local do arquivo de modelo JSON e o local do arquivo de parâmetros (consulte a seção acima sobre o PowerShell para saber mais) no comando a seguir.

    azure group deployment create mdbc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Você pode verificar o status das implantações de recursos individuais com o seguinte comando.

    azure group deployment list mdbc

## Um tour da estrutura do modelo do MongoDB e da organização de arquivos

Para criar um modelo robusto e reutilizável do Gerenciador de Recursos do Azure, é preciso realizar preparação adicional para organizar a série de tarefas complexas e inter-relacionadas necessárias durante a implantação de uma solução complexa como o MongoDB. Com o uso da *vinculação de modelos* e do *loop de recursos* do Gerenciador de Recursos do Azure, além da execução de scripts por meio de extensões relacionadas, é possível implementar uma abordagem modular que pode ser reutilizada com praticamente qualquer implantação complexa baseada em modelo.

O diagrama a seguir descreve as relações entre todos os arquivos baixados do GitHub para essa implantação.

![mongodb-files](media/virtual-machines-mongodb-template/mongodb-files.png)

Esta seção percorre a estrutura do arquivo azuredeploy.json para o cluster MongoDB.

### Seção Parâmetros

A seção de parâmetros do arquivo azuredeploy.json especifica os parâmetros modificáveis que são usados nesse modelo. O arquivo azuredeploy-parameters.json descrito anteriormente neste artigo é usado para passar valores para a seção de parâmetros de azuredeploy.json durante a execução do modelo.

### Seção Variáveis

A seção de variáveis especifica as variáveis que podem ser usadas em todo esse modelo. Ela contém vários campos (fragmentos ou tipos de dados JSON) que serão definidos como constantes ou valores calculados em tempo de execução. O exemplo a seguir mostra a seção variáveis desse modelo do MongoDB.

    "variables": {
          "_comment0": "/* T-shirt sizes may vary for different reasons, and some customers may want to modify these - so feel free to go ahead and define your favorite t-shirts */",
          "tshirtSizeXSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 1,
              "totalMemberCount": 2,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 2,
              "totalMemberCount": 3,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeMedium": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 3,
              "totalMemberCount": 4,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 2,
              "dataDiskSize": 250
          },
          "tshirtSizeLarge": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 250
          },
          "tshirtSizeXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 500
          },
          "tshirtSizeXXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 15,
              "totalMemberCount": 16,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 8,
              "dataDiskSize": 500
          },
          "osFamilyUbuntu": {
              "osName": "ubuntu",
              "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
              "installerPackages": "mongodb-org",
              "imagePublisher": "Canonical",
              "imageOffer": "UbuntuServer",
              "imageSKU": "14.04.2-LTS"
          },
          "vmStorageAccountContainerName": "vhd-mongodb",
          "vmStorageAccountDomain": ".blob.core.windows.net",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "sharedScriptUrl": "[concat('https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/', variables('osFamilySpec').osName, '/')]",
          "scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-high-availability/",
          "templateBaseUrl": "[variables('scriptUrl')]",
          "jumpboxTemplateEnabled": "jumpbox-resources.json",
          "jumpboxTemplateDisabled": "empty-resources.json",
          "arbiterTemplateEnabled": "arbiter-resources.json",
          "arbiterTemplateDisabled": "empty-resources.json",
          "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
          "jumpboxTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('jumpboxTemplate', parameters('jumpbox'))))]",
          "arbiterTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('arbiterTemplate', variables('clusterSpec').arbiter)))]",
          "commonSettings": {
              "availabilitySetName": "mongodbAvailSet",
              "region": "[parameters('region')]"
          },
          "storageSettings": {
              "vhdStorageAccountName": "[parameters('storageAccountName')]",
              "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
              "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]",
              "storageAccountCount": "[variables('clusterSpec').storageAccountCount]"
          },
          "networkSettings": {
              "virtualNetworkName": "[parameters('virtualNetworkName')]",
              "addressPrefix": "[parameters('addressPrefix')]",
              "subnetName": "[parameters('subnetName')]",
              "subnetPrefix": "[parameters('subnetPrefix')]",
              "subnetRef": "[concat(variables('vnetID'), '/subnets/', parameters('subnetName'))]",
              "machineIpPrefix": "[parameters('nodeAddressPrefix')]"
          },
          "machineSettings": {
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]",
              "machineNamePrefix": "mongodb-",
              "osImageReference": {
                  "publisher": "[variables('osFamilySpec').imagePublisher]",
                  "offer": "[variables('osFamilySpec').imageOffer]",
                  "sku": "[variables('osFamilySpec').imageSKU]",
                  "version": "latest"
              }
          },
          "clusterSpec": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
          "osFamilySpec": "[variables(concat('osFamily', parameters('osFamily')))]",
          "installCommand": "[concat('bash mongodb-', variables('osFamilySpec').osName, '-install.sh', ' -i ', variables('osFamilySpec').installerBaseUrl, ' -b ', variables('osFamilySpec').installerPackages, ' -r ', parameters('replicaSetName'), ' -k ', parameters('replicaSetKey'), ' -u ', parameters('adminUsername'), ' -p ', parameters('adminPassword'), ' -x ', variables('networkSettings').machineIpPrefix, ' -n ', variables('clusterSpec').totalMemberCount)]",
          "vmScripts": {
              "scriptsToDownload": [
                  "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
                  "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
              ],
              "regularNodeInstallCommand": "[variables('installCommand')]",
              "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
              "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
          },
          "_comment1": "/* The list of values below helps partition VM disks across multiple storage accounts to achieve a better throughput */",
          "_comment2": "/* Feel free to modify the default allocations if you are comfortable and understand what you are doing */",
          "storageAccountForXSmall_0": "0",
          "storageAccountForXSmall_1": "0",
          "storageAccountForSmall_0": "0",
          "storageAccountForSmall_1": "0",
          "storageAccountForSmall_2": "0",
          "storageAccountForMedium_0": "0",
          "storageAccountForMedium_1": "0",
          "storageAccountForMedium_2": "0",
          "storageAccountForMedium_3": "0",
          "storageAccountForLarge_0": "0",
          "storageAccountForLarge_1": "1",
          "storageAccountForLarge_2": "2",
          "storageAccountForLarge_3": "3",
          "storageAccountForLarge_4": "0",
          "storageAccountForLarge_5": "1",
          "storageAccountForLarge_6": "2",
          "storageAccountForLarge_7": "3",
          "storageAccountForXLarge_0": "0",
          "storageAccountForXLarge_1": "1",
          "storageAccountForXLarge_2": "2",
          "storageAccountForXLarge_3": "3",
          "storageAccountForXLarge_4": "0",
          "storageAccountForXLarge_5": "1",
          "storageAccountForXLarge_6": "2",
          "storageAccountForXLarge_7": "3",
          "storageAccountForXXLarge_0": "0",
          "storageAccountForXXLarge_1": "1",
          "storageAccountForXXLarge_2": "2",
          "storageAccountForXXLarge_3": "3",
          "storageAccountForXXLarge_4": "4",
          "storageAccountForXXLarge_5": "5",
          "storageAccountForXXLarge_6": "6",
          "storageAccountForXXLarge_7": "7",
          "storageAccountForXXLarge_8": "0",
          "storageAccountForXXLarge_9": "1",
          "storageAccountForXXLarge_10": "2",
          "storageAccountForXXLarge_11": "3",
          "storageAccountForXXLarge_12": "4",
          "storageAccountForXXLarge_13": "5",
          "storageAccountForXXLarge_14": "6",
          "storageAccountForXXLarge_15": "7"
      },

No exemplo anterior, você pode ver as duas abordagens diferentes. No primeiro fragmento, a variável "osFamilyUbuntu" será definida como um elemento JSON que contém seis pares de chave-valor.

    "osFamilyUbuntu": {
      "osName": "ubuntu",
      "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
      "installerPackages": "mongodb-org",
      "imagePublisher": "Canonical",
      "imageOffer": "UbuntuServer",
      "imageSKU": "14.04.2-LTS"
    },

No segundo fragmento, a variável "vmScripts" é atribuída a uma matriz JSON em que elementos únicos serão calculados em tempo de execução usando uma função de linguagem de modelo (concat) e o valor de outra variável mais constantes de cadeia de caracteres.

    "vmScripts": {
      "scriptsToDownload": [
      "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
      "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
    ],

Um conceito importante nesse modelo é a maneira como “tamanhos de camisetas” diferentes são definidos para clusters do MongoDB. Examinando uma das variáveis "tshirtSizeXXXX", você pode perceber que ela descreve características importantes do modo de implantação de um cluster. No exemplo a seguir, usamos o tamanho Médio como um exemplo.

    "tshirtSizeMedium": {
      "vmSizeMember": "Standard_D2",
      "vmSizeArbiter": "Standard_A1",
      "numberOfMembers": 3,
      "totalMemberCount": 4,
      "arbiter": "Enabled",
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
      "storageAccountCount": 2,
      "dataDiskSize": 250
    },

Um cluster MongoDB "Médio" usará D2 como Tamanho da VM para os três nós do MongoDB que hospedam dados, além de uma quarta VM A1 que será usada como arbitrador para fins de replicação. O submodelo correspondente invocado para implantar os nós de dados poderá ser `member-resources-D2.json` e os arquivos de dados (250 GB cada) serão armazenados em duas contas de armazenamento. Essa variáveis serão usadas na seção de recursos para orquestrar as implantações de nó e outras tarefas.

### Seção Recursos

A seção de recursos é onde acontece a maior parte da ação. Analisando cuidadosamente essa seção, você pode identificar imediatamente dois casos diferentes: o primeiro é um elemento definido do tipo `Microsoft.Resources/deployments` que, basicamente, significa a invocação de uma implantação aninhada dentro da principal. Por meio do elemento "templateLink" (e da propriedade de versão relacionada), é possível especificar um arquivo de modelo vinculado que será invocado passando um conjunto de parâmetros como entrada, como mostrado no próximo exemplo.

    {
      "name": "shared-resources",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2015-01-01",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('sharedTemplateUrl')]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "commonSettings": {
            "value": "[variables('commonSettings')]"
          },
          "storageSettings": {
            "value": "[variables('storageSettings')]"
          },
          "networkSettings": {
            "value": "[variables('networkSettings')]"
          }
        }
      }
    },

No exemplo anterior, fica claro como azuredeploy.json nesse cenário foi organizado como um mecanismo de orquestração, invocando vários outros arquivos de modelo, cada um deles responsável por parte das atividades de implantação necessárias.

Em particular, os seguintes modelos vinculados serão usados para essa implantação:

-	**Shared-resource.json**: contém a definição de todos os recursos que serão compartilhados na implantação. Os exemplos são contas de armazenamento usadas para armazenar os discos do sistema operacional da VM e redes virtuais.
-	**Jumpbox-resources.json**: quando habilitado, é responsável pela implantação de todos os recursos relacionados à VM do Jumpbox, com um endereço IP público que pode ser usado para acessar o cluster MongoDB na rede pública.
-	**Arbiter-resources.json**: quando habilitado, esse modelo implanta um membro arbitrador no cluster MongoDB. Um arbitrador não contêm dados, mas é usado quando um conjunto de réplicas contém um número par de nós para gerenciar eleições primárias.
-	**Member-resources-Dx.json**: especifica os modelos de recursos que estão implantando de maneira efetiva os nós do MongoDB. Um arquivo específico será usado com base na definição do tamanho de camiseta selecionada, em que cada arquivo só será diferente pelo número de discos conectados para cada nó.
-	**Mongodb-ubuntu-install.sh**: um arquivo de script Bash invocado pela extensão CustomScriptForLinux em todos os nós no cluster. Responsável pela montagem e formatação de discos de dados e instalação de bits do MongoDB no nó.

Para implantar um cluster MongoDB, uma lógica específica é necessária para poder configurar corretamente um conjunto de réplicas. O exemplo a seguir mostra a sequência específica que você precisa usar durante a implantação.

IMPLANTAR MEMBROS DE DADOS (em paralelo) => IMPLANTAR ÚLTIMO MEMBRO DE DADOS => (opcional) IMPLANTAR ARBITRADOR

Nessa sequência, a implantação de vários nós de dados ocorrerá em paralelo, com exceção do último nó. É aí que o cluster será formado e o novo conjunto de réplicas será implantado, de modo que todos os nós anteriores deverão estar em execução antes desse momento. A última etapa será implantar o nó opcional do Arbitrador (apenas para os tamanhos de camiseta onde for necessário).

Observando novamente nosso modelo principal (azuredeploy.json), vamos ver como essa lógica é implementada, começando com todos os membros de dados no exemplo a seguir.

    {
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('member-resources', copyindex())]",
      "apiVersion": "2015-01-01",
      "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
      ],
      "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').numberOfMembers]"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('clusterSpec').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "commonSettings": {
            "value": "[variables('commonSettings')]"
          },
          "storageSettings": {
            "value": {
              "vhdStorageAccountName": "[concat(variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())))]",
                              "vhdContainerName": "[variables('storageSettings').vhdContainerName]",
                              "destinationVhdsContainer": "[concat('https://', variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())), variables('vmStorageAccountDomain'), '/', variables('storageSettings').vhdContainerName, '/')]"
            }
          },
          "networkSettings": {
            "value": "[variables('networkSettings')]"
          },
          "machineSettings": {
            "value": {
              "adminUsername": "[variables('machineSettings').adminUsername]",
                              "adminPassword": "[variables('machineSettings').adminPassword]",
                              "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                              "osImageReference": "[variables('machineSettings').osImageReference]",
                              "vmSize": "[variables('clusterSpec').vmSizeMember]",
                              "dataDiskSize": "[variables('clusterSpec').dataDiskSize]",
                              "machineIndex": "[copyindex()]",
                              "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                              "commandToExecute": "[variables('vmScripts').regularNodeInstallCommand]"
            }
          }
        }
      }
    },

Um conceito importante a ser destacado é como é possível implantar várias cópias de um único tipo de recurso e, para cada instância, poder definir valores exclusivos para as configurações necessárias. Esse conceito é conhecido como *Loop de Recursos*.

No exemplo anterior, um parâmetro (número de nós a serem implantados no cluster) será usado para definir uma variável (“numberOfMembers”), que será transmitida para o elemento **“copy”** para disparar um número (loop) de implantações filho, sendo que cada uma delas resultará na instanciação do modelo para cada membro do cluster. Para poder definir todas as configurações onde são necessários valores exclusivos entre instâncias, a função **copyindex()** função pode ser usada para obter um valor numérico que indica o índice nessa criação de loop de recurso em particular.

Outro conceito importante na criação de recursos é a capacidade de especificar dependências e precedências entre recursos, como você pode observar na matriz JSON **dependsOn**. Neste modelo específico, a implantação de cada nó depende da implantação bem-sucedida anterior de **recursos compartilhados**.

Os discos anexados são formatados como parte das atividades de preparação do nó disparadas pela execução do arquivo de script mongodb-ubuntu-install.sh. Na verdade, dentro deste arquivo você encontrará uma instância de chamada a seguir.

    bash ./vm-disk-utils-0.1.sh -b $DATA_DISKS -s

vm-disk-utils-0.1.sh faz parte da pasta shared\_scripts\\ubuntu, no repositório Github azure-quickstart-templates, e contém funções muito úteis para a montagem, formatação e distribuição de discos, que podem ser reutilizados sempre que você precisar executar tarefas semelhantes como parte da criação do modelo.

Outro fragmento interessante para explorar é aquele relacionado às extensões de VM CustomScriptForLinux. Elas são instaladas como um tipo de recurso separado, com uma dependência em cada modelo de implantação de nó de cluster. Por exemplo, consulte o seguinte fragmento no final de cada arquivo member-resources-Dx.json.

    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installmongodb')]",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('commonSettings').region]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
      ],
      "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
          "fileUris": "[parameters('machineSettings').vmScripts]", "commandToExecute":"[parameters('machineSettings').commandToExecute]"
        }
      }
    }

Familiarizando-se com os outros arquivos incluídos nessa implantação, você poderá compreender todos os detalhes e as práticas recomendadas necessárias para organizar e orquestrar estratégias de implantação complexas para soluções com vários nós, com base em qualquer tecnologia, utilizando modelos do Gerenciador de Recursos do Azure. Embora não seja obrigatório, uma abordagem recomendada é estruturar seus arquivos de modelos conforme mostrado no diagrama a seguir.

![mongodb-template-structure](media/virtual-machines-mongodb-template/mongodb-template-structure.png)

Essencialmente, essa abordagem sugere que você:

-	Defina o arquivo de modelo principal como um ponto central de orquestração para todas as atividades de implantação específicas, aproveitando a vinculação de modelos para invocar execuções de submodelos.
-	Crie arquivos de modelo específicos que implantarão todos os recursos compartilhados entre todas as outras tarefas de implantação específicas (por exemplo, contas de armazenamento e configuração de vnet, entre outros). Isso pode ser amplamente reutilizado entre implantações que tenham requisitos semelhantes em termos de infraestrutura comum.
-	Inclua modelos de recursos opcionais para requisitos de spot específicos de determinado recurso.
-	Para membros de um grupo de recursos idênticos (nós em um cluster etc.), crie modelos específicos que usam o loop de recursos para implantar várias instâncias com propriedades exclusivas.
-	Para todas as tarefas de pós-implantação (por exemplo, instalação de produtos e configurações, entre outros), aproveite as extensões de implantação de scripts e crie scripts específicos para cada tecnologia.

Para obter mais informações, consulte [Linguagem de modelo do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

<!---HONumber=Oct15_HO1-->