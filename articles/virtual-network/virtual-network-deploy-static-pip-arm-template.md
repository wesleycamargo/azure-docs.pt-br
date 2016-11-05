---
title: Implantar uma VM com um IP público estático usando um modelo no Gerenciador de Recursos | Microsoft Docs
description: Saiba como implantar VMs com um IP público estático usando um modelo no Gerenciador de Recursos
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial

---
# Implantar uma VM com um IP público estático usando um modelo
[!INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

modelo de implantação clássica.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## Recursos de IP público em um arquivo de modelo
Você pode exibir e baixar o [modelo de exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json).

A seção a seguir mostra a definição de recurso de IP público, com base no cenário acima.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('webVMSetting').pipName]",
        "location": "[variables('location')]",
        "properties": {
          "publicIPAllocationMethod": "Static"
        },
        "tags": {
          "displayName": "PublicIPAddress - Web"
        }
      },

Observe a propriedade **publicIPAllocationMethod**, que está definida como *Estática*. Essa propriedade pode ser *Dinâmica* (valor padrão) ou *Estática*. Defini-la como estática assegura que o endereço IP público atribuído nunca será alterado.

A seção a seguir mostra a associação do endereço IP público com uma interface de rede.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('webVMSetting').nicName]",
        "location": "[variables('location')]",
        "tags": {
          "displayName": "NetworkInterface - Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
          "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Static",
                "privateIPAddress": "[variables('webVMSetting').ipAddress]",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
                },
                "subnet": {
                  "id": "[variables('frontEndSubnetRef')]"
                }
              }
            }
          ]
        }
      },

Observe a propriedade **publicIPAddress** que aponta para a **Id** de um recurso chamado **variables('webVMSetting').pipName**. Esse é o nome do recurso de IP público mostrado acima.

Por fim, a interface de rede acima está listada na propriedade **networkProfile** da VM que está sendo criada.

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## Implantar o modelo usando o clique para implantar
O modelo de exemplo disponível no repositório público usa um arquivo de parâmetro que contém os valores padrão usados para gerar o cenário descrito acima. Para implantar esse modelo usando a implantação com um clique, clique em **Implantar no Azure** no arquivo Readme.md para o modelo [VM com PIP estático](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP). Substitua os valores do parâmetro padrão, se desejado, e insira valores para os parâmetros em branco. Siga as instruções no portal para criar uma máquina virtual com um endereço IP público estático.

## Implantar o modelo usando o PowerShell
Para implantar o modelo baixado usando o PowerShell, faça o seguinte.

1. Se você nunca usou o Azure PowerShell, consulte [Como Instalar e Configurar o Azure PowerShell](../powershell-install-configure.md) e siga as instruções nas etapas 1 a 3.
2. Em um console do PowerShell, execute o cmdlet **New-AzureRmResourceGroup** para criar um novo grupo de recursos, se necessário. Se você já tiver criado um grupo de recursos, vá para a etapa 3.
   
        New-AzureRmResourceGroup -Name PIPTEST -Location westus
   
    Saída esperada:
   
        ResourceGroupName : PIPTEST
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP
3. Em um console do PowerShell, execute o cmdlet **New-AzureRmResourceGroupDeployment** para implantar o modelo.
   
        New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
            -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
            -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json
   
    Saída esperada:
   
        DeploymentName    : DeployVM
        ResourceGroupName : PIPTEST
        ProvisioningState : Succeeded
        Timestamp         : <Deployment date> <Deployment time>
        Mode              : Incremental
        TemplateLink      :
                            Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
                            ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
                            ContentVersion : 1.0.0.0
   
        Parameters        :
                            Name                      Type                       Value     
                            ========================  =========================  ==========
                            vnetName                  String                     WTestVNet
                            vnetPrefix                String                     192.168.0.0/16
                            frontEndSubnetName        String                     FrontEnd  
                            frontEndSubnetPrefix      String                     192.168.1.0/24
                            storageAccountNamePrefix  String                     iaasestd  
                            stdStorageType            String                     Standard_LRS
                            osType                    String                     Windows   
                            adminUsername             String                     adminUser
                            adminPassword             SecureString                         
   
        Outputs           :

## Implantar o modelo usando a CLI do Microsoft Azure
Para implantar o modelo usando a CLI do Microsoft Azure, faça o seguinte:

1. Se você nunca usou a CLI do Azure, siga as etapas no artigo [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) e, em seguida, as etapas para conectar a CLI à sua assinatura na seção "Usar o logon do Azure para autenticar interativamente" do artigo [Conectar-se a uma assinatura do Azure a partir da interface de linha de comando do Azure (CLI do Azure)](../xplat-cli-connect.md).
2. Execute o comando **azure config mode** para alternar para o modo do Gerenciador de Recursos, como mostrado abaixo.
   
        azure config mode arm
   
    Este é o resultado esperado para o comando descrito acima:
   
        info:    New mode is arm
3. Abra o [arquivo de parâmetro](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), selecione o seu conteúdo e salve-o em um arquivo em seu computador. Neste exemplo, os parâmetros são salvos em um arquivo chamado *parameters.json*. Altere os valores de parâmetro dentro do arquivo, se desejado. No mínimo, é recomendável que você altere o valor do parâmetro adminPassword para uma senha exclusiva e complexa.
4. Execute o cmdlet **azure group deployment create** para implantar a nova rede virtual usando o modelo e os arquivos de parâmetro que você baixou e modificou acima. No comando abaixo, substitua <path> pelo caminho no qual você salvou o arquivo.
   
        azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json
   
    Saída esperada (lista os valores de parâmetro usados):
   
        info:    Executing command group create
        + Getting resource group PIPTEST2
        + Creating resource group PIPTEST2
        info:    Created resource group PIPTEST2
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/<Subscription ID>/resourceGroups/PIPTEST2
        data:    Name:                PIPTEST2
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

<!---HONumber=AcomDC_0810_2016-->