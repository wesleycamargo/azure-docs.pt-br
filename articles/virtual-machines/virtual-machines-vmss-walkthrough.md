<properties
	pageTitle="Dimensionar automaticamente conjuntos de escalas de máquina virtual | Microsoft Azure"
	description="Introdução à criação e ao gerenciamento dos seus primeiros conjuntos de escalas de máquina virtual do Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="guybo"/>

# Dimensionar automaticamente máquinas em um conjunto de escalas de máquina virtual

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-windows-powershell-service-manager.md).


Este documento é um guia rápido sobre como começar a criar e a gerenciar seus primeiros Conjuntos de Escalas de Máquina Virtual do Azure durante a visualização pública.

O guia supõe que você já esteja familiarizado com os modelos do ARM (Gerenciador de Recursos do Azure) para implantar recursos do Azure, como máquinas virtuais e Redes Virtuais. Caso não esteja, os três primeiros links na seção Recursos fornecem uma visão geral do Gerenciador de Recursos.

## O que são conjuntos de escalas de VM e por que usá-los?

Os conjuntos de escalas de VM são um recurso de computação do Azure que você pode usar para implantar e gerenciar um conjunto de VMs idênticas.

Com todas as VMs configuradas igualmente, os conjuntos de escalas de VM foram desenvolvidos para oferecer suporte ao verdadeiro dimensionamento automático — não é necessário o pré-provisionamento das VMs — e, desse modo, facilitar a criação de serviços em grande escala visando big compute, big data, cargas de trabalho contidas e todos os aplicativos que precisam reduzir e expandir os recursos de computação. As operações em escala são implicitamente equilibradas entre os domínios de atualização e falha. Para ver uma introdução aos conjuntos de escalas de VM, consulte o recente [comunicado no blog Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview).

## Criando e trabalhando com conjuntos de escalas de VM

Os conjuntos de escalas de VM podem ser definidos e implantados usando modelos JSON e chamadas REST, assim como as VMs individuais do Gerenciador de Recursos do Azure. Portanto, todos os métodos de implantação padrão do Gerenciador de Recursos do Azure podem ser usados, e este documento mostrará alguns exemplos.

As áreas de integração de VMSS específicas, como suporte a comandos obrigatórios em linguagens de scripts e programação, e a integração completa do portal estão em desenvolvimento e serão lançadas em fases durante a visualização.

## Implantando e gerenciando conjuntos de escalas de VM com o portal do Azure

Inicialmente, você não pode criar um conjunto de escalas de VM do zero no portal do Azure. O suporte ao portal está sendo trabalhado e, na primeira fase desse trabalho, você poderá ver no portal apenas os conjuntos de escalas que você já criou, e terá que usar uma abordagem de script/modelo para criá-los. Em todos os casos, "portal" significa o novo portal do Azure: [https://portal.azure.com/](https://portal.azure.com/).

No entanto, é possível usar o recurso do portal para implantar um modelo e, com ele, qualquer recurso, incluindo conjuntos de escalas. Uma maneira fácil de implantar um conjunto simples de escalas é usar um link como este:

_https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>_

Um conjunto de modelos de exemplo para conjuntos de escalas de VM pode ser encontrado no repositório do GitHub de modelos de Início Rápido do Azure em: [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) — procure modelos com _vmss_ no título.

Nos arquivos readme desses modelos, você verá um botão como esse que se vincula ao recurso de implantação do portal.

Para implantar o conjunto de escalas, clique no botão e preencha todos os parâmetros obrigatórios no portal. Observação: se você não tiver certeza se um recurso oferece suporte a letras maiúsculas ou combinadas, é mais seguro usar sempre valores de parâmetro em letras minúsculas.

**Monitorando o recurso de conjunto de escalas de VM no portal/Gerenciador de Recursos**

Depois que um conjunto de escalas tiver sido implantado, o portal mostrará uma exibição básica dele, mas inicialmente, durante a visualização, ele não mostrará as propriedades detalhadas, nem permitirá que você analise as VMs em execução no conjunto de escalas de VM.

Até que a integração completa do portal esteja definida, é recomendável usar o **Gerenciador de Recursos do Azure** : [https://resources.azure.com](https://resources.azure.com) para exibir conjuntos de escalas de VM. Os conjuntos de escala de VM são um recurso encontrado em Microsoft.Compute, de modo que nesse site, você pode vê-los expandindo os seguintes links:

Assinaturas -> sua assinatura -> resourceGroups -> provedores -> Microsoft.Compute -> virtualMachineScaleSets -> seu conjunto de escalas de VM -> etc.

## Implantando e gerenciando conjuntos de escalas de VM usando o PowerShell

É possível implantar modelos de VMSS e recursos de consulta usando qualquer versão atual do Azure PowerShell.

**Observação importante:** os exemplos abaixo são para o [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/), que introduziu o prefixo _AzureRm_ em muitos comandos. Se você estiver usando uma versão anterior do PowerShell, como 0.9.8 ou inferior, remova " **Rm**" dos comandos de exemplo. Os exemplos também supõem que você já estabeleceu uma conexão de logon com o Azure no ambiente do PowerShell (_Login-AzureRmAccount_).

Exemplos:

&#35; **Criar um grupo de recursos**

New-AzureRmResourcegroup -name myrg -location 'Sudeste da Ásia'

&#35; **Criar um conjunto de escalas de VM de 2 VMs**

New-AzureRmResourceGroupDeployment -name dep1 -vmSSName myvmss -instanceCount 3 -ResourceGroupName myrg -TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

Você deverá fornecer os parâmetros que faltam (como local, nome de usuário, senha neste exemplo).

&#35; **Obter detalhes do conjunto de escalas de VM**

Get-AzureRmResource -name myvmss -ResourceGroupName myrg -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15

&#35; ou para obter mais detalhes, insira-o no pipeline | ConvertTo-Json -Depth 10

&#35; ou para ver ainda mais detalhes, adicione –debug ao comando.

&#35; Expandindo ou reduzindo

New-AzureRmResourceGroupDeployment -name dep2 -vmSSName myvmss -instanceCount 2 -ResourceGroupName myrg –TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json)

&#35; **Remover um conjunto de escalas**

&#35; Fácil: remova o grupo de recursos inteiro e tudo que há nele:

Remove-AzureRmResourceGroup -Name myrg

&#35; Preciso: remova um recurso específico:

Remove-AzureRmResource -Name myvmss -ResourceGroupName myrg -ApiVersion 2015-06-15 -ResourceType Microsoft.Compute/virtualMachineScaleSets

### Comandos obrigatórios do PowerShell para conjuntos de escalas

Uma compilação futura do Azure PowerShell incluirá um conjunto de comandos obrigatórios para criar e gerenciar conjuntos de escalas de VM sem usar modelos. Essa compilação está disponível em visualização em: [https://github.com/AzureRT/azure-powershell/releases](https://github.com/AzureRT/azure-powershell/releases). Exemplos de como usar a API obrigatória podem ser encontrados em:

[https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1](https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1)

## Implantando e gerenciando conjuntos de escalas de VM usando a CLI do Azure

É possível implantar modelos de VMSS e recursos de consulta usando qualquer versão atual da CLI do Azure.

A maneira mais fácil de instalar a CLI é de um contêiner do Docker. Para instalar, consulte: [https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/](https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/)

Para usar a CLI, consulte: [https://azure.microsoft.com/documentation/articles/xplat-cli/](https://azure.microsoft.com/documentation/articles/xplat-cli/)

### Exemplos de CLI para conjunto de escalas de VM

&#35; **criar um grupo de recursos**

azure group create myrg "Sudeste da Ásia"

&#35; **criar um conjunto de escalas de VM**

azure group deployment create -g myrg -n dep2 --template-uri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

&#35; isso deve pedir parâmetros dinamicamente, ou você pode especificá-los como argumentos

&#35; **obter detalhes do conjunto de escalas**

azure resource show -n vmssname -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g myrg

&#35; ou para obter mais detalhes:

azure resource show –n vmssname –r Microsoft.Compute/virtualMachineScaleSets –o 2015-06-15 –g myrg –json –vv

Uma compilação futura da CLI do Azure incluirá comandos obrigatórios para implantar e gerenciar conjuntos de escalas de VM diretamente sem modelos e executar operações em VMs nos conjuntos de escalas de VM. Você pode rastrear essa compilação em: [https://github.com/AzureRT/azure-xplat-cli/releases/](https://github.com/AzureRT/azure-xplat-cli/releases/)

## Modelos do conjunto de escalas de VM

Esta seção explora um modelo simples do Azure para criar um conjunto de escalas de VM e o contrasta com modelos usados para criar máquinas virtuais de única instância. Também há uma análise minuciosa útil em vídeo de um modelo de conjunto de escalas de VM em: [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

### Anatomia de um modelo

Vamos começar com um modelo simples que cria um conjunto de escalas de VM de máquinas virtuais do Ubuntu e dividi-lo em componentes. Este exemplo também cria os recursos dos quais um conjunto de escalas de VM depende, como rede virtual, conta de armazenamento etc. O exemplo pode ser encontrado em: [https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json](https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json). Observe que para esse exemplo, "hello world", não haverá uma maneira direta de se conectar a VMs no conjunto de escalas de VM de fora da sua rede virtual, pois as VMs recebem endereços IP internos. Consulte a seção Cenários e exemplos nos [modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates) para ver como se conectar de fora usando balanceadores de carga ou jump boxes.

### Cabeçalho do modelo.

Especifique o esquema e a versão do conteúdo:

{

   "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json",

   "contentVersion": "1.0.0.0",

### Parâmetros

Assim como em qualquer modelo do ARM (Gerenciador de Recursos do Azure), esta seção define parâmetros que são especificadas no momento da implantação, incluindo, neste exemplo, o nome do seu conjunto de escalas de VM, o número de instâncias da VM com as quais começar, uma cadeia de caracteres exclusiva a ser usada na criação de contas de armazenamento (sempre use letras minúsculas ao inserir valores para objetos como contas de armazenamento, a menos que você saiba como as letras maiúsculas/minúsculas são tratadas).

````
 "parameters": {

    "vmSSName": {

      "type": "string",

      "metadata": {

        "description": "Scale Set name, also used in this template as a base for naming resources (hence limited less than 10 characters)."

      },

      "maxLength": 10

    },

    "instanceCount": {

      "type": "int",

      "metadata": {

        "description": "Number of VM instances"

      },

      "maxValue": 100

    },

    "adminUsername": {

      "type": "string",

      "metadata": {

        "description": "Admin username on all VMs."

      }

    },

    "adminPassword": {

      "type": "securestring",

      "metadata": {

        "description": "Admin password on all VMs."

      }

    }

  },
````

### Variáveis

Um componente padrão do modelo ARM que define variáveis às quais você fará referência posteriormente no modelo. Neste exemplo, usaremos variáveis para definir qual sistema operacional queremos, alguns detalhes da configuração de rede, configurações de armazenamento e local. Para local, usaremos a função _location()_ para selecioná-la no grupo de recursos onde ela está sendo implantada.

Observe que uma matriz de cadeias de caracteres exclusivas é definida para um prefixo de conta de armazenamento. Consulte a seção Armazenamento para ver uma explicação.

````
  "variables": {

    "apiVersion": "2015-06-15",

    "newStorageAccountSuffix": "[concat(parameters('vmssName'), 'sa')]",

    "uniqueStringArray": [

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '0')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '1')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '2')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '3')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '4')))]"

    ],

    "vmSize": "Standard\_A1",

    "vhdContainerName": "[concat(parameters('vmssName'), 'vhd')]",

    "osDiskName": "[concat(parameters('vmssName'), 'osdisk')]",

    "virtualNetworkName": "[concat(parameters('vmssName'), 'vnet')]",

    "subnetName": "[concat(parameters('vmssName'), 'subnet')]",

    "nicName": "[concat(parameters('vmssName'), 'nic')]",

    "ipConfigName": "[concat(parameters('vmssName'), 'ipconfig')]",

    "addressPrefix": "10.0.0.0/16",

    "subnetPrefix": "10.0.0.0/24",

    "storageAccountType": "Standard\_LRS",

    "location": "[resourceGroup().location]",

    "osType": {

      "publisher": "Canonical",

      "offer": "UbuntuServer",

      "sku": "15.10",

      "version": "latest"

    },

    "imageReference": "[variables('osType')]"

  },
````

### Recursos

Nesta seção, cada tipo de recurso é definido.

````
   "resources": [
````


**Armazenamento.** Ao criar um conjunto de escalas de VM, você especifica uma matriz de contas de armazenamento. Os discos do sistema operacional para instâncias da VM serão distribuídos uniformemente entre cada conta. No futuro, os conjuntos de escalas de VM serão alternados para usar uma abordagem de disco gerenciada, em que você não precisa gerenciar contas de armazenamento e, em vez disso, apenas descrever as propriedades do armazenamento como parte da definição do conjunto de escalas. Por enquanto, você precisará criar quantas contas de armazenamento forem necessárias, antecipadamente. É recomendável criar cinco contas de armazenamento, que confortavelmente oferecerão suporte a até 100 VMs em um conjunto de escalas (o máximo atual).

Quando um conjunto de contas de armazenamento é criado, as contas são distribuídas entre partições em um carimbo de armazenamento, e o esquema de distribuição depende das primeiras letras do nome da conta de armazenamento. Por esse motivo, para conseguir um desempenho ideal, é recomendável que cada conta de armazenamento que você cria comece com uma letra diferente do alfabeto. Você pode nomeá-las manualmente ou, para este exemplo, use a função uniqueString() para fornecer uma distribuição pseudoaleatória:

````
    {

      "type": "Microsoft.Storage/storageAccounts",

      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "copy": {

        "name": "storageLoop",

        "count": 5

      },

      "properties": {

        "accountType": "[variables('storageAccountType')]"

      }

    },
````

**Rede Virtual.** Crie uma Rede Virtual. Observe que você pode ter vários conjuntos de escala, bem como VMs individuais na mesma rede virtual.

````
    {

      "type": "Microsoft.Network/virtualNetworks",

      "name": "[variables('virtualNetworkName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

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
````

### O recurso virtualMachineScaleSets

Em muitos aspectos, o recurso _virtualMachineScaleSets_ é como um recurso _virtualMachines_. Ambos são fornecidos pelo provedor de recursos Microsoft.Compute e estão no mesmo nível. A principal diferença é que você fornece um valor de _capacity_ que informa quantas VMs está criando e que o que você define aqui é para todas as VMs no conjunto de escalas de VM.

Observe como a seção _dependsOn_ faz referência às contas de armazenamento e à rede virtual criadas acima e a capacidade faz referência ao parâmetro _instanceCount_.

````
    {

      "type": "Microsoft.Compute/virtualMachineScaleSets",

      "name": "[parameters('vmssName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "dependsOn": [

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"

      ],

      "sku": {

        "name": "[variables('vmSize')]",

        "tier": "Standard",

        "capacity": "[parameters('instanceCount')]"

      },
````

**Propriedades**

Os conjuntos de escalas de VM têm uma configuração upgradePolicy. As versões futuras oferecerão suporte a atualizações divididas (por exemplo, altere a configuração para 20% de minhas VMs por vez), mas por enquanto upgradePolicy pode ser definida para manual ou automática. Manual significa que se você alterar o modelo e reimplantá-lo, as alterações entrarão em vigor somente quando novas VMs forem criadas ou as extensões forem atualizadas, por exemplo, quando você aumenta _capacity_. Automática significa atualizar todas as VMs como uma "explosão", reiniciando tudo. Manual geralmente é uma abordagem mais segura. Você pode excluir VMs individuais e reimplantar conforme a necessidade para atualizar gradualmente.

````
      "properties": {

         "upgradePolicy": {

         "mode": "Manual"

        },
````

**Propriedades -> virtualMachineProfile**

Aqui, você pode fazer referência às contas de armazenamento criadas acima como contêineres para VMs. Não é preciso pré-criar os contêineres reais, apenas as contas.

````
        "virtualMachineProfile": {

          "storageProfile": {

            "osDisk": {

              "vhdContainers": [

                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"

              ],

              "name": "[variables('osDiskName')]",

              "caching": "ReadOnly",

              "createOption": "FromImage"

            },

            "imageReference": "[variables('imageReference')]"

          },
````

**Propriedades -> osProfile**

Uma diferença entre os conjuntos de escalas de VM e as VMs individuais é que o nome do computador é um prefixo. As instâncias da VM no conjunto de escalas de VM serão criadas com nomes como: myvm\_0, myvm\_1, etc.

````
          "osProfile": {

            "computerNamePrefix": "[parameters('vmSSName')]",

            "adminUsername": "[parameters('adminUsername')]",

            "adminPassword": "[parameters('adminPassword')]"

          },
````

**Propriedades -> networkProfile**

Ao definir o perfil de rede para um VMSS, lembre-se de que a configuração da NIC e a configuração de IP têm um nome. A configuração da NIC tem uma definição "_primary_" e a id da sub-rede faz referência de volta ao recurso de sub-rede que foi criado como parte da rede virtual acima.

````
          "networkProfile": {

            "networkInterfaceConfigurations": [

              {

                "name": "[variables('nicName')]",

                "properties": {

                  "primary": "true",

                  "ipConfigurations": [

                    {

                      "name": "[variables('ipConfigName')]",

                      "properties": {

                        "subnet": {

                          "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'), '/subnets/', variables('subnetName'))]"

                        }

                      }

                    }

                  ]

                }

              }

            ]

          }
````

**Propriedades -> extensionProfile**

O exemplo simples acima não exige um perfil de extensão. Você pode ver um em ação neste exemplo que implanta o Apache e o PHP usando a extensão CustomScript: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale). Observe que, neste exemplo, as propriedades IP da rede também fazem referência a um balanceador de carga. Os balanceadores de carga serão descritos na seção Cenários do conjunto de escalas de VM.

## Cenários do conjunto de escalas de VM

Esta seção explora alguns cenários típicos do conjunto de escalas de VM e exemplos de modelos. Embora eles exijam modelos por enquanto, alguns deles serão integrados ao portal no futuro. Além disso, alguns serviços do Azure de nível mais alto (como Lote, Service Fabric, Serviço de Contêiner do Azure) usarão esses cenários.

## RDP/SSH para instâncias do conjunto de escalas de VM

Um conjunto de escalas de VM é criado dentro de uma rede virtual e as VMs individuais dentro dele não são endereços IP públicos alocados. Isso é bom, pois geralmente você não quer as despesas e os custos gerais de gerenciamento de alocar endereços IP separados para todos os recursos sem estado na sua grade de computação, e você pode se conectar facilmente a essas VMs de outros recursos na sua rede virtual, incluindo aqueles que têm endereços IP públicos como balanceadores de carga ou máquinas virtuais autônomas.

Duas maneiras simples de se conectar às VMs VMSS são descritas aqui:

### Conectar-se a VMs usando regras NAT

É possível criar um endereço IP público, atribuí-lo a um balanceador de carga e definir regras NAT de entrada que mapeiam uma porta no endereço IP para uma porta em uma VM no conjunto de escalas de VM. Por exemplo

IP público->Porta 50000 -> vmss\_0->IP público da porta 22->Porta 50001 -> vmss\_1->IP público da porta 22->Porta 50002-> vmss\_2->Porta 22

Veja um exemplo de como criar um conjunto de escalas de VM que usa regras NAT para habilitar a conexão SSH para cada VM em um conjunto de escalas usando um único IP público: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

Veja um exemplo de como fazer o mesmo com RDP e Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

### Conectar-se a VMs usando uma "jump box"

Se você criar um conjunto de escalas de VM e uma VM autônoma na mesma rede virtual, a VM autônoma e as VMs VMSS podem se conectar uma a outra usando os respectivos endereços IP internos, conforme definido pela rede virtual/sub-rede.

Se você criar um endereço IP público e atribuí-lo à VM autônoma, será possível aplicar RDP ou SSH à VM autônoma e se conectar dessa máquina às suas instâncias de VMSS. Você pode perceber, nesse momento, que um simples conjunto de escalas de VM é inerentemente mais seguro do que uma simples VM autônoma com um endereço IP público em sua configuração padrão.

Para obter um exemplo dessa abordagem, este modelo cria um cluster Mesos simples que consiste em uma VM mestre autônoma que gerencia um cluster de VMs baseado no conjunto de escalas: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

## Balanceamento de carga round robin para instâncias do conjunto de escalas de VM

Se desejar entregar trabalho a um cluster de VMs de computação usando uma abordagem "round-robin", você poderá configurar um balanceador de carga do Azure com regras de balanceamento de carga apropriadas. Também é possível definir investigações para verificar se o aplicativo está em execução ao executar ping de portas com um protocolo especificado, intervalo e caminho de solicitação.

Veja um exemplo que cria um conjunto de escalas de VM de VMs que executam o servidor Web IIS e usa um balanceador de carga para equilibrar a carga que cada VM recebe. Ele também usa o protocolo HTTP para executar ping de uma URL específica em cada VM: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) — observe o tipo de recurso Microsoft.Network/loadBalancers, bem como networkProfile e extensionProfile no virtualMachineScaleSet.

## Instâncias do conjunto de escalas de VM com dimensionamento automático do Azure

Se desejar variar a contagem da instância (_capacity_) do seu conjunto de escalas de VM, dependendo do uso de CPU/memória/disco ou outros eventos, um rico conjunto de configurações de dimensionamento automático está disponível no provedor de recursos Microsoft.Insights. Você pode ler sobre as configurações disponíveis na documentação REST do Insights: [https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx).

O Dimensionamento Automático do Insights integra-se diretamente aos conjuntos de escalas de VM. Para configurá-lo, você define um tipo de recurso Microsoft.Insights/autoscaleSettings que tem uma _targetResourceUri_ e _metricResourceUri_ que faz referência de volta ao conjunto de escalas. Ao definir o conjunto de escalas, você inclui um _extensionProfile_, que instala o agente do Diagnóstico do Azure (WAD) no Windows ou o LDE (Linux Diagnostic Extension) no Linux. Veja um exemplo do Linux que adiciona VMs até um limite predefinido quando o uso médio da CPU é superior a 50%, com uma granularidade de tempo de 1 minuto por um período de amostragem de 5 minutos:

[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale).

No futuro, o dimensionamento automático com conjuntos de escalas de VM também será integrado diretamente ao portal do Azure.

## Implantando um conjunto de escalas de VM como um cluster de cálculo em um gerenciador de clusters PaaS

Às vezes, os conjuntos de escalas de VM são descritos como uma função de trabalho de última geração. É uma descrição válida, mas há o risco de confundir recursos do conjunto de escalas com recursos da função de trabalho PaaS v1.

De certo modo, os conjuntos de escalas de VM fornecem uma verdadeira "função de trabalho" ou recurso de trabalho, já que eles fornecem um recurso de computação generalizado que é independente de plataforma/tempo de execução, personalizável e se integra à IaaS do Gerenciador de Recursos do Azure.

Uma função de trabalho PaaS v1, embora limitada em termos de suporte à plataforma/ao tempo de execução (apenas imagens da plataforma Windows) também inclui serviços como troca de VIP, definições de atualização configuráveis, configurações específicas de implantação de aplicativo/tempo de execução, que _ainda_ não estão disponíveis nos conjuntos de escalas de VM ou serão fornecidos por outros serviços PaaS de nível mais alto, como o Service Fabric. Com isso em mente, você pode observar os conjuntos de escalas de VM como uma infraestrutura que oferece suporte à PaaS. Isto é, as soluções PaaS, como o Service Fabric ou os gerenciadores de clusters, como o Mesos, podem ser criadas com base nos conjuntos de escalas de VM como uma camada de computação escalonável.

Veja um exemplo de um cluster Mesos que implanta um conjunto de escalas de VM na mesma rede virtual como uma VM autônoma. A VM autônoma é um mestre Mesos, e o conjunto de escalas de VM representa um conjunto de nós subordinados: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). As versões futuras do [Serviço de Contêiner do Azure](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) implantarão versões mais complexas/reforçadas desse cenário com base nos conjuntos de escalas de VM.

## Expandindo e reduzindo um conjunto de escalas de VM

Para aumentar ou diminuir o número de máquinas virtuais em um conjunto de escalas de VM, basta alterar a propriedade _capacity_ e reimplantar o modelo. Essa simplicidade facilita escrever sua própria camada de dimensionamento personalizada se você quiser definir eventos de escala personalizada que não têm suporte do dimensionamento automático do Azure.

Se estiver reimplantando um modelo para alterar a capacidade, você pode definir um modelo muito menor que inclui apenas a SKU e a capacidade atualizada. Um exemplo disso é mostrado em: [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

## Orientação de escala e desempenho do conjunto de escalas de VM

- Durante a visualização pública, não crie mais de 500 VMs em vários conjuntos de escalas de VM por vez. Esse limite será aumentado no futuro.
- Planeje-se para ter não mais que 40 VMs por conta de armazenamento.
- Diversifique o máximo possível as primeiras letras dos nomes da conta de armazenamento. Os modelos de VMSS de exemplo nos [modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/) fornecem exemplos de como fazer isso.
- Se estiver usando VMs personalizadas, planeje-se para ter não mais que 40 VMs por conjunto de escalas de VM, em uma única conta de armazenamento. Você precisará da imagem previamente copiada na conta de armazenamento para poder começar a implantação do conjunto de escalas de VM. Consulte as perguntas frequentes para obter mais informações.
- Planeje-se para ter não mais que 2048 VMs por rede virtual. Esse limite será aumentado no futuro.
- O número de VMs que você pode criar é limitado pela sua cota de núcleo de computação em qualquer região. Talvez seja necessário contatar o Atendimento ao Cliente para aumentar o limite da cota de computação mesmo que hoje você tenha um limite alto de núcleos para uso com serviços de nuvem ou IaaS v1. Para consultar sua cota, você pode executar o seguinte comando da CLI do Azure: _azure vm list-usage_ e o seguinte comando do PowerShell: _Get-AzureRmVMUsage_ (se estiver usando uma versão do PowerShell abaixo de 1.0, use _Get-AzureVMUsage_).


## Perguntas frequentes do conjunto de escalas de VM

**P. Quantas VMs você pode ter em um conjunto de escalas de VM?**

R. 100, se você usar imagens de plataforma que podem ser distribuídas entre várias contas de armazenamento. Se você usar imagens personalizadas, até 40, desde que as imagens personalizadas sejam limitadas a uma única conta de armazenamento durante a visualização.

**Que outros limites de recurso existem para conjuntos de escalas de VM?**

R. Aplicam-se os limites existentes do serviço do Azure: [https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/)

Você também está limitado a criar não mais de 500 VMs nos vários conjuntos de escalas por região durante o período de visualização.

**P. Há suporte para os discos de dados nos conjuntos de escalas de VM?**

R. Não na versão inicial. Suas opções de armazenamento de dados são:

- Arquivos do Azure (unidades compartilhada de SMB)

- Unidade do sistema operacional

- Unidade TEMP (local, não tem relação com o armazenamento do Azure)

- Serviço de dados externo (por exemplo, banco de dados remoto, tabelas do Azure, blobs do Azure)

**P. Quais são as regiões do Azure que oferecem suporte ao VMSS?**

R. Qualquer região que ofereça suporte ao Gerenciador de Recursos do Azure oferece suporte aos conjuntos de escalas de VM.

**P. Como criar um VMSS usando uma imagem personalizada?**

R. Deixe a propriedade vhdContainers em branco, por exemplo:

````
"storageProfile": {
   "osDisk": {
      "name": "vmssosdisk",
      "caching": "ReadOnly",
      "createOption": "FromImage",
      "image": {
         "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
     },
     "osType": "Windows"
  }
},
````

**P. Se eu reduzir a capacidade do meu VMSS de 20 para 15, quais VMs serão removidas?**

R. As últimas 5 VMs (índices maiores) serão removidas.

**P. E se eu aumentar a capacidade de 15 para 18?**

Se você aumentar para 18, as VMs com índice 15, 16, 17 serão criadas. Em ambos os casos, as VMs são balanceadas entre FDs e UDs.

**P. Ao usar várias extensões em um conjunto de escalas de VM, posso impor uma sequência de execução?**

R. Não diretamente, mas para a extensão customScript, seu script pode aguardar por outra extensão para ser concluída (por exemplo, monitorando o log de extensão — consulte [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**P. Os conjuntos de escalas de VM funcionam com os conjuntos de disponibilidade do Azure?**

R. Sim. Um conjunto de escalas é um conjunto de disponibilidade implícito com 3 FDs e 5 UDs. Não é preciso configurar nada em virtualMachineProfile. Em versões futuras, os conjuntos de escalas provavelmente incluirão vários locatários, mas, por enquanto, um conjunto de escalas é um único conjunto de disponibilidade.

## Próximas etapas

Durante a visualização dos conjuntos de escalas de VM, a documentação continuará evoluindo e mais recursos de integração, como portal e dimensionamento automático, serão explorados.

Seus comentários são muito importantes para nos ajudar a criar um serviço que forneça os recursos necessários. Diga-nos o que funciona, o que não funciona e o que pode ser melhorado. Envie seus comentários para [vmssfeedback@microsoft.com](mailto:vmssfeedback@microsoft.com).

## Recursos

| **Tópico** | **Link** |
| --- | --- |
| Visão Geral do Gerenciador de Recursos do Azure | [https://azure.microsoft.com/documentation/articles/resource-group-overview/](https://azure.microsoft.com/documentation/articles/resource-group-overview/) |
| Modelos do Gerenciador de Recursos do Azure | [https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) |
| Funções de modelo do Gerenciador de Recursos do Azure | [https://azure.microsoft.com/documentation/articles/resource-group-template-functions/](https://azure.microsoft.com/documentation/articles/resource-group-template-functions/) |
| Modelos de exemplo (github) | [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) |
| Guia da API REST do conjunto de escalas de VM | [https://msdn.microsoft.com/library/mt589023.aspx](https://msdn.microsoft.com/library/mt589023.aspx) |
| Vídeos do conjunto de escalas de VM | [https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) [https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player) |
| Configurações de dimensionamento automático (MSDN) | [https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx) |
| Solucionando problemas de implantações de grupos de recursos no Azure | [https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/](https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/) |

<!---HONumber=Nov15_HO3-->