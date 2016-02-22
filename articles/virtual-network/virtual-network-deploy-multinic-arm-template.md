<properties 
   pageTitle="Implantar VMs com várias NICs usando um modelo no Gerenciador de Recursos | Microsoft Azure"
   description="Saiba como implantar VMs com várias NICs usando um modelo no Gerenciador de Recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

# Implantar VMs com várias NICs usando um modelo

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Uma vez que você não pode ter VMs com uma única NIC e VMs com várias NICs no mesmo grupo de recursos neste momento, deve implementar servidores de back-end em um grupo de recursos e todos os outros componentes em outro grupo de segurança. As etapas a seguir usam um grupo de recursos chamado *IaaSStory* para o grupo de recursos principal e *IaaSStory-BackEnd* para os servidores de back-end.

## Pré-requisitos

Antes de implantar os servidor de back-end, você deve implantar o grupo de recursos principal com todos os recursos necessários para este cenário. Para implantar esses recursos, faça o seguinte:

1. Navegue até [a página do modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Na página do modelo, à direita do **Grupo de recursos pai**, clique em **Implantar no Azure**.
3. Caso necessário, altere os valores de parâmetro e siga as etapas no Portal de visualização do Azure para implantar o grupo de recursos.

> [AZURE.IMPORTANT] Verifique se os nomes da conta de armazenamento são exclusivos. Você não pode ter nomes de conta de armazenamento duplicados no Azure.

## Entender o modelo de implantação

Antes de implantar o modelo fornecido com esta documentação, você deve compreender o que ele faz. As etapas a seguir fornecem uma visão geral adequada do modelo abordado.

1. Navegue até [a página do modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Clique em **azuredeploy.json** para abrir o arquivo do modelo.
3. Observe o parâmetro *osType* descrito abaixo. Esse parâmetro é usado para selecionar a imagem da VM que deve ser usada para o servidor de banco de dados, juntamente com várias configurações relacionadas ao sistema operacional.

	    "osType": {
	      "type": "string",
	      "defaultValue": "Windows",
	      "allowedValues": [
	        "Windows",
	        "Ubuntu"
	      ],
	      "metadata": {
	        "description": "Type of OS to use for VMs: Windows or Ubuntu."
	      }
	    },

4. Role para baixo até a lista de variáveis e verifique a definição das variáveis **dbVMSetting** descritas abaixo. Ele recebe um dos elementos de matriz contidos na variável **dbVMSettings**. Se estiver familiarizado com a terminologia de desenvolvimento de software, você poderá exibir a variável **dbVMSettings** como uma tabela de hash ou como um dicionário.

		"dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"

5. Digamos que você pretenda implantar VMs do Windows que estejam executando o SQL no back-end. Nesse caso, o valor de **osType** seria *Windows* e a variável **dbVMSetting** incluiria o elemento descrito abaixo, que representa o primeiro valor da variável **dbVMSettings**.

	      "Windows": {
	        "vmSize": "Standard_DS3",
	        "publisher": "MicrosoftSQLServer",
	        "offer": "SQL2014SP1-WS2012R2",
	        "sku": "Standard",
	        "version": "latest",
	        "vmName": "DB",
	        "osdisk": "osdiskdb",
	        "datadisk": "datadiskdb",
	        "nicName": "NICDB",
	        "ipAddress": "192.168.2.",
	        "extensionDeployment": "",
	        "avsetName": "ASDB",
	        "remotePort": 3389,
	        "dbPort": 1433
	      },

6. Observe que o elemento **vmSize** contém o valor *Standard\_DS3*. Apenas determinados tamanhos de VM permitem o uso de várias NICs. Para verificar os tamanhos de VM habilitados para várias NICs, acesse a [visão geral sobre várias NICs](virtual-networks-multiple-nics.md).
7. Role para baixo até **recursos** e observe o primeiro elemento. Ele descreve uma conta de armazenamento. Essa conta de armazenamento é usada para manter os discos de dados usados por cada VM do banco de dados. Nesse cenário, cada VM do banco de dados tem um disco do sistema operacional em armazenamento regular e dois discos de dados em armazenamento SSD (premium).

	    {
	      "apiVersion": "2015-05-01-preview",
	      "type": "Microsoft.Storage/storageAccounts",
	      "name": "[parameters('prmStorageName')]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "Storage Account - Premium"
	      },
	      "properties": {
	        "accountType": "[parameters('prmStorageType')]"
	      }
	    },

8. Role para baixo até o próximo recurso, como mostrado a seguir. Esse recurso representa a NIC usada para acesso ao banco de dados em cada VM do banco de dados. Observe o uso da função **copy** deste recurso. O modelo permite implantar quantas VMs você desejar, com base no parâmetro **dbCount**. Portanto, você deve criar a mesma quantidade de NICs para acesso ao banco de dados, sendo uma para cada VM.

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Network/networkInterfaces",
	      "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "NetworkInterfaces - DB DA"
	      },
	      "copy": {
	        "name": "dbniccount",
	        "count": "[parameters('dbCount')]"
	      },
	      "properties": {
	        "ipConfigurations": [
	          {
	            "name": "ipconfig1",
	            "properties": {
	              "privateIPAllocationMethod": "Static",
	              "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
	              "subnet": {
	                "id": "[variables('backEndSubnetRef')]"
	              }
	            }
	          }
	        ]
	      }
	    },

9. Role para baixo até o próximo recurso, como mostrado a seguir. Esse recurso representa a NIC usada para gerenciamento em cada VM do banco de dados. Mais uma vez, você precisará de uma dessas NICs para cada VM do banco de dados. Observe o elemento **networkSecurityGroup**, vinculando um NSG que permite acesso ao RDP/SSH apenas para essa NIC.

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Network/networkInterfaces",
	      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "NetworkInterfaces - DB RA"
	      },
	      "copy": {
	        "name": "dbniccount",
	        "count": "[parameters('dbCount')]"
	      },
	      "properties": {
	        "ipConfigurations": [
	          {
	            "name": "ipconfig1",
	            "properties": {
	              "networkSecurityGroup": {
	                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
	              },
	              "privateIPAllocationMethod": "Static",
	              "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
	              "subnet": {
	                "id": "[variables('backEndSubnetRef')]"
	              }
	            }
	          }
	        ]
	      }
	    },

10. Role para baixo até o próximo recurso, como mostrado a seguir. Esse recurso representa uma conjunto de disponibilidade a ser compartilhado por todas as VMs do banco de dados. Dessa forma, você garante que sempre haverá uma única VM no conjunto em execução durante a manutenção.

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Compute/availabilitySets",
	      "name": "[variables('dbVMSetting').avsetName]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "AvailabilitySet - DB"
	      }
	    },

11. Role para baixo até o próximo recurso. Esse recurso representa as VMs do banco de dados, como exibido nas primeiras linhas a seguir. Observe novamente o uso da função **copy**, assegurando a criação de várias VMs com base no parâmetro **dbCount**. Observe também a coleção **dependsOn**. Ela descreve duas NICs, que devem ser criadas antes da implantação da VM, juntamente com o conjunto de disponibilidade e a conta de armazenamento.

		  "apiVersion": "2015-06-15",
		  "type": "Microsoft.Compute/virtualMachines",
		  "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
		  "location": "[variables('location')]",
		  "dependsOn": [
		    "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
		    "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
		    "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
		    "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
		  ],
		  "tags": {
		    "displayName": "VMs - DB"
		  },
		  "copy": {
		    "name": "dbvmcount",
		    "count": "[parameters('dbCount')]"
		  },

12. Role para baixo no recurso VM até o elemento **networkProfile**, como descrito a seguir. Observe que há duas NICs como referência para cada VM. Quando você cria várias NICs para uma VM, deve definir a propriedade **primary** de uma das NICs como *true* e o restante como *false*.

        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
              "properties": { "primary": true }
            },
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
              "properties": { "primary": false }
            }
          ]
        }
      }

## Implantar o modelo ARM usando clique para implantar

> [AZURE.IMPORTANT] Não deixe de seguir as etapas [necessárias](#Pre-requisites) antes de seguir as instruções abaixo.

O modelo de exemplo disponível no repositório público usa um arquivo de parâmetro que contém os valores padrão usados para gerar o cenário descrito acima. Para implantar esse modelo usando o recurso clicar para implantar, clique [neste link](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), à direita do ** grupo de recursos Back-end (confira a documentação)** e clique em **Implantar no Azure**. Caso necessário, substitua os valores de parâmetro padrão e siga as instruções no portal.

A figura a seguir mostra o conteúdo do novo grupo de recursos, após a implantação.

![Grupo de recursos Back-end](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## Implantar o modelo usando o PowerShell

Para implantar o modelo baixado usando o PowerShell, faça o seguinte.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. Execute o cmdlet **`New-AzureRmResourceGroup`** para criar um grupo de recursos usando o modelo.

		New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
		    -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'	

	Saída esperada:

		ResourceGroupName : IaaSStory-Backend
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		Permissions       : 
		                    Actions  NotActions
		                    =======  ==========
		                    *                  
		                    
		Resources         : 
		                    Name                 Type                                 Location
		                    ===================  ===================================  ========
		                    ASDB                 Microsoft.Compute/availabilitySets   westus  
		                    DB1                  Microsoft.Compute/virtualMachines    westus  
		                    DB2                  Microsoft.Compute/virtualMachines    westus  
		                    NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
		                    NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
		                    NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
		                    NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
		                    wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
		                    
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## Implantar o modelo usando a CLI do Microsoft Azure

Para implantar o modelo usando a CLI do Microsoft Azure, faça o seguinte:

1. Se você nunca usou a CLI do Azure, consulte [Instalar e configurar a CLI do Azure](xplat-cli.md) e siga as instruções até o ponto em que você seleciona sua conta e assinatura do Azure.
2. Execute a opção de comando **`azure config mode`** para alternar para o modo do Gerenciador de Recursos, como mostrado abaixo.

		azure config mode arm

	Este é o resultado esperado para o comando descrito acima:

		info:    New mode is arm

3. Abra o [arquivo de parâmetro](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), selecione o seu conteúdo e salve-o em um arquivo em seu computador. Para este exemplo, salvamos o arquivo de parâmetros em *parameters.json*.

4. Execute o cmdlet **`azure group deployment create`** para implantar a nova rede virtual usando os arquivos de modelo e parâmetro que você baixou e modificou acima. A lista exibida após a saída explicar os parâmetros usados.

		azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json

	Saída esperada:

		info:    Executing command group create
		+ Getting resource group IaaSStory-Backend
		+ Creating resource group IaaSStory-Backend
		info:    Created resource group IaaSStory-Backend
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
		data:    Name:                IaaSStory-Backend
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

<!---HONumber=AcomDC_0211_2016-->