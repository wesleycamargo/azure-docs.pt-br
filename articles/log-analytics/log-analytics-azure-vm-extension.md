---
title: "Conectar máquinas virtuais do Azure ao Log Analytics | Microsoft Docs"
description: "Para máquinas virtuais Windows e Linux em execução no Azure, a maneira recomendada de coletar métricas e logs é instalando a extensão de VM do Azure do Log Analytics. Você pode usar o portal do Azure ou o PowerShell para instalar a extensão da máquina virtual do Log Analytics em VMs do Azure."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: ca39e586-a6af-42fe-862e-80978a58d9b1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: richrund
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 5bb3a67c999c1d41c50b2b660a97a53125511633
ms.lasthandoff: 03/25/2017


---
# <a name="connect-azure-virtual-machines-to-log-analytics-with-a-log-analytics-agent"></a>Conectar máquinas virtuais do Azure ao Log Analytics com um agente do Log Analytics

Para computadores Windows e Linux, o método recomendado para coletar métricas e logs é instalando o agente do Log Analytics.

A maneira mais fácil de instalar o agente do Log Analytics em máquinas virtuais do Azure é por meio da Extensão de VM do Log Analytics.  Usar a extensão simplifica o processo de instalação e configura automaticamente o agente para enviar dados para o espaço de trabalho do Log Analytics que você especificar. O agente também será automaticamente atualizado, garantindo que você disponha dos recursos e correções mais recentes.

Para máquinas virtuais do Windows, você deve habilitar a extensão de máquina virtual *Microsoft Monitoring Agent*.
Para máquinas virtuais do Linux, você deve habilitar a extensão de máquina virtual *Agente do OMS para Linux*.

Saiba mais sobre [extensões de máquina virtual do Azure](../virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e o [agente Linux](../virtual-machines/virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ao usar a coleta com base em agente para dados de log, você deve configurar [fontes de dados no Log Analytics](log-analytics-data-sources.md) para especificar os logs e métricas a coletar.

> [!IMPORTANT]
> Se você configurar o Log Analytics para indexar dados de log usando o [diagnóstico do Azure](log-analytics-azure-storage.md) e se você configurar o agente para coletar os mesmos logs, eles serão coletados duas vezes. Você será cobrado por ambas as fontes de dados. Se você tiver o agente instalado, você deverá coletar dados de log usando apenas o agente – não configurar o Log Analytics para coletar dados de log do Diagnóstico do Azure.
>
>

Há três maneiras fáceis de habilitar a extensão da máquina virtual do Log Analytics:

* Usando o Portal do Azure
* Usando o Azure PowerShell
* Usando um modelo do Azure Resource Manager

## <a name="enable-the-vm-extension-in-the-azure-portal"></a>Habilitar a extensão de VM no Portal do Azure
Você pode instalar o agente para o Log Analytics e conectar a máquina virtual do Azure na qual ele é executado usando o [Portal do Azure](https://portal.azure.com).

### <a name="to-install-the-log-analytics-agent-and-connect-the-virtual-machine-to-a-log-analytics-workspace"></a>Para instalar o agente do Log Analytics e conectar a máquina virtual a um espaço de trabalho do Log Analytics
1. Faça logon no [Portal do Azure](http://portal.azure.com).
2. Selecione **Procurar** no lado esquerdo do portal e, em seguida, vá para **Log Analytics (OMS)** e selecione-o.
3. Em sua lista de espaços de trabalho do Log Analytics, selecione aquele que você deseja utilizar com a VM do Azure.  
   ![Espaços de trabalho do OMS](./media/log-analytics-azure-vm-extension/oms-connect-azure-01.png)
4. Em **Gerenciamento do Log Analytics**, selecione **Máquinas virtuais**.  
   ![Máquinas virtuais](./media/log-analytics-azure-vm-extension/oms-connect-azure-02.png)
5. Na lista de **Máquinas virtuais**, selecione a máquina virtual em que deseja instalar o agente. O **status de conexão do OMS** para a VM indicará que ela **Não está conectada**.  
   ![VM não conectada](./media/log-analytics-azure-vm-extension/oms-connect-azure-03.png)
6. Nos detalhes de sua máquina virtual, selecione **Conectar**. O agente é automaticamente instalado e configurado para o seu espaço de trabalho do Log Analytics. Esse processo leva alguns minutos, período durante o qual o status da conexão do OMS é *Conectando-se...*  
   ![Conectar a VM](./media/log-analytics-azure-vm-extension/oms-connect-azure-04.png)
7. Quando o agente estiver instalado e conectado, o status da **Conexão do OMS** será atualizado para mostrar **Este espaço de trabalho**.  
   ![Conectado](./media/log-analytics-azure-vm-extension/oms-connect-azure-05.png)

## <a name="enable-the-vm-extension-using-powershell"></a>Habilitar a extensão de VM usando o PowerShell
Ao configurar sua máquina virtual usando o PowerShell, você precisa fornecer o **workspaceId** e o **workspaceKey**. Observe que os nomes de propriedade em sua configuração de json **diferenciam maiúsculas de minúsculas**.

É possível encontrar a ID e a chave na página **Configurações** do portal do OMS, ou então usando o PowerShell como mostrado no exemplo acima.

![ID do Espaço de Trabalho e Chave Primária](./media/log-analytics-azure-vm-extension/oms-analyze-azure-sources.png)

Há comandos diferentes para máquinas virtuais clássicas do Azure e máquinas virtuais do Resource Manager. Exemplos tanto de máquinas virtuais clássicas quanto do Resource Manager são fornecidos abaixo.

Para máquinas virtuais clássicas, use o seguinte exemplo do PowerShell:

```
Add-AzureAccount

$workspaceId = "enter workspace ID here"
$workspaceKey = "enter workspace key here"
$hostedService = "enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService

# For Windows VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

# For Linux VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'OmsAgentForLinux' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

Para máquinas virtuais do Resource Manager, use o seguinte exemplo do PowerShell:

```
Login-AzureRMAccount
Select-AzureSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName)
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRmVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

# For Windows VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"

# For Linux VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'OmsAgentForLinux' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'OmsAgentForLinux' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"


```

## <a name="deploy-the-vm-extension-using-a-template"></a>Implantar a extensão de VM usando um modelo
Usando o Azure Resource Manager, você pode criar um modelo simples (no formato JSON) que define a implantação e a configuração do seu aplicativo. Esse modelo é conhecido como um modelo do Gerenciador de Recursos e fornece uma forma declarativa de definir a implantação. Usando um modelo, você pode implantar seu aplicativo em todo seu ciclo de vida repetidamente e com a confiança que seus recursos estão sendo implantados em um estado consistente.

Ao incluir o agente do Log Analytics como parte do seu modelo do Resource Manager, você pode assegurar que cada máquina virtual seja pré-configurada para gerar relatórios para seu espaço de trabalho do Log Analytics.

Para obter mais informações sobre os modelos do Resource Manager, veja [Criação de Modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

A seguir está um exemplo de um modelo do Resource Manager que é usado para implantar uma máquina virtual que esteja executando o Windows com a extensão Microsoft Monitoring Agent instalada. Este é um modelo de máquina virtual típico, com as seguintes adições:

* parâmetros workspaceId e workspaceName
* Seção de extensão de recurso Microsoft.EnterpriseCloud.Monitoring
* Saídas para pesquisar o workspaceId e o workspaceSharedKey

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "dnsLabelPrefix": {
       "type": "string",
       "metadata": {
          "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
       }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "OMS workspace ID"
      }
    },
    "workspaceName": {
      "type": "string",
      "metadata": {
         "description": "OMS workspace name"
      }
    },
    "windowsOSVersion": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "Windows-Server-Technical-Preview"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]",
    "apiVersion": "2015-06-15",
    "imagePublisher": "MicrosoftWindowsServer",
    "imageOffer": "WindowsServer",
    "OSDiskName": "osdiskforwindowssimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyWindowsVM",
    "vmSize": "Standard_DS1",
    "virtualNetworkName": "MyVNET",
    "resourceId": "[resourceGroup().id]",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsLabelPrefix')]"
        }
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
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
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
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
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
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
            "sku": "[parameters('windowsOSVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
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
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
             "enabled": "true",
             "storageUri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net')]"
          }
        }
      },
      "resources": [
        {
          "type": "extensions",
          "name": "Microsoft.EnterpriseCloud.Monitoring",
          "apiVersion": "[variables('apiVersion')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
          ],
          "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "workspaceId": "[parameters('workspaceId')]"
            },
            "protectedSettings": {
              "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-03-20').primarySharedKey]"
            }
          }
        }
      ]
    }
  ],
  "outputs": {
      "sharedKeyOutput": {
         "value": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').primarySharedKey]",
         "type": "string"
      },
      "workspaceIdOutput": {
         "value": "[reference(concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').customerId]",
        "type" : "string"
      }
  }
}
```

Você pode implantar um modelo usando o comando do PowerShell a seguir:

```
New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath
```

## <a name="troubleshooting-the-log-analytics-vm-extension"></a>Solução de problemas da extensão de VM de Log Analytics
Normalmente, você receberá uma mensagem quando as coisas não funcionam no portal do Azure ou no Azure PowerShell.

1. Faça logon no [Portal do Azure](http://portal.azure.com).
2. Localize a VM e abra os detalhes da VM.
3. Clique em **Extensões** para verificar se a extensão do OMS está habilitada ou não.

   ![Exibição da Extensão de VM](./media/log-analytics-azure-vm-extension/oms-vmview-extensions.png)

4. Clique na extensão *MicrosoftMonitoringAgent*(Windows) ou *OmsAgentForLinux*(Linux) e exiba os detalhes. 

   ![Detalhes da Extensão da VM](./media/log-analytics-azure-vm-extension/oms-vmview-extensiondetails.png)

### <a name="troubleshooting-windows-virtual-machines"></a>Solucionando problemas das Máquinas Virtuais do Windows
Se a extensão do agente de VM *Microsoft Monitoring Agent* não está instalando ou reportando, você pode executar as etapas a seguir para solucionar o problema.

1. Verifique se o agente de VM do Azure está instalado e funcionando corretamente usando as etapas em [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Você também pode examinar o arquivo de log do agente de VM `C:\WindowsAzure\logs\WaAppAgent.log`
   * Se o log não existir, isso significará que o agente de VM não está instalado.
     * [Instalar o Agente de VM do Azure em VMs clássicas](../virtual-machines/windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
2. Confirme que a tarefa de pulsação da extensão do Microsoft Monitoring Agent está em execução usando as seguintes etapas:
   * Fazer logon na máquina virtual
   * Abrir o agendador de tarefas e localizar a tarefa `update_azureoperationalinsight_agent_heartbeat`
   * Confirmar que a tarefa está habilitada e está executando a cada minuto
   * Verificar o arquivo de log de pulsação em `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. Examinar os arquivos de log de extensão de VM do Microsoft Monitoring Agent em `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
4. Certificar-se de que a máquina virtual pode executar scripts do PowerShell
5. Certificar-se de que as permissões em C:\Windows\temp não foram alteradas
6. Exibir o status do Microsoft Monitoring Agent, digitando o seguinte em uma janela do PowerShell com privilégios elevados na máquina virtual `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. Examinar os arquivos de log de instalação do Microsoft Monitoring Agent em `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Para obter mais informações, consulte [Solucionando problemas em extensões do Windows](../virtual-machines/virtual-machines-windows-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="troubleshooting-linux-virtual-machines"></a>Solucionando Problemas em Máquinas Virtuais Linux
Se a extensão do agente de VM *Agente do OMS para Linux* não estiver instalando ou reportando, você pode executar as etapas a seguir para solucionar o problema.

1. Se o status da extensão for *Desconhecido*, verifique se o agente de VM do Azure está instalado e funcionando corretamente, examinando o arquivo de log do agente de VM `/var/log/waagent.log`
   * Se o log não existir, isso significará que o agente de VM não está instalado.
   * [Instalar o Agente de VM do Azure em VMs Linux](../virtual-machines/virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
2. Para outros status não íntegros, examine o Agente do OMS para arquivos de log de extensão de VMs do Linux em `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` e `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Se o status da extensão estiver íntegro mas os dados não estiverem sendo carregados, examine o Agente do OMS para arquivos de log do Linux em `/var/opt/microsoft/omsagent/log/omsagent.log`

Para obter mais informações, consulte [Solucionando problemas em extensões do Linux](../virtual-machines/virtual-machines-linux-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas
* Configurar [fontes de dados no Log Analytics](log-analytics-data-sources.md) para especificar os logs e as métricas a coletar.
* [Adicionar soluções do Log Analytics da Galeria de Soluções](log-analytics-add-solutions.md) para coletar dados de máquinas virtuais.
* [Coletar dados usando o Diagnóstico do Azure](log-analytics-azure-storage.md) para outros recursos em execução no Azure.

Para computadores que não estão no Azure, você pode instalar o agente do Log Analytics usando os métodos descritos nos seguintes artigos:

* [Conectar computadores Windows ao Log Analytics](log-analytics-windows-agents.md)
* [Conectar computadores Linux ao Log Analytics](log-analytics-linux-agents.md)

