---
title: Extensão de VM de Key Vault para Windows | Microsoft Docs
description: Implante um agente que execute a atualização automática de segredos de Cofre de Chaves em máquinas virtuais usando uma extensão da máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: dragav
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: dragosav
ms.openlocfilehash: 19e177f086ea9fa817a36e67ace77aed39ee89b5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944748"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Extensão da máquina virtual de Key Vault para Windows

## <a name="overview"></a>Visão geral

A extensão de VM de Key Vault fornece a atualização automática dos segredos armazenados no Azure Key Vault. Especificamente, os monitores de extensão observado de uma lista de certificados armazenados no cofre de chaves e, ao detectar uma alteração, recupera e instala os certificados correspondentes. A extensão de VM de Key Vault é publicada e suportada pela Microsoft, no momento em VMs do Windows. Este documento detalha as plataformas com opções de plataformas, configurações e implantação com suporte para a extensão da VM de Key Vault para Windows. 

## <a name="prerequisites"></a>pré-requisitos

A extensão de VM de Key Vault depende da extensão de VM de Identidade de Serviço Gerenciada, para se autenticar para o serviço de Key Vault. Consulte por favor a documentação da Extensão de VM MSI para obter mais detalhes.

### <a name="operating-system"></a>Sistema operacional

A extensão de VM de Key Vault tem suporte atualmente para todas as versões do Windows.

| Distribuição | Versão |
|---|---|
| Windows | Núcleo |

### <a name="internet-connectivity"></a>Conectividade com a Internet

A extensão da VM de Key Vault para Windows requer que a máquina virtual de destino esteja conectada à Internet. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão da VM de Key Vault. A extensão não requer configurações protegidas - todas as suas configurações são consideradas informações sem impacto de segurança. A extensão requer uma lista dos segredos monitorados, a frequência de sondagem e o repositório de certificados de destino. Especificamente:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

### <a name="property-values"></a>Valores de propriedade

| NOME | Valor/Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2016-10-01 | data |
| publicador | Microsoft.Azure.KeyVault.Edp | string |
| Tipo | KeyVaultForWindows | string |
| typeHandlerVersion | 0,0 | int |
| pollingIntervalInS | 3600 | int |
| certificateStoreName | MY | string |
| certificateStoreLocation  | LOCAL_MACHINE | string |
| observedCertificates  | ["https://myvault.vault.azure.net:443/secrets/mycertificate"] | Matriz de cadeia de caracteres


## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. Modelos são ideais ao implantar uma ou mais máquinas virtuais que exigem renovação de certificados pós-implantação. A extensão pode ser implantada em VMs individuais ou conjuntos de escala de VM. O esquema e a configuração são comuns a ambos os tipos de modelo. 

A configuração de JSON para uma extensão de máquina virtual deve estar aninhada no fragmento do recurso de máquina virtual do modelo, especificamente objeto `"resources": []` da máquina virtual.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

## <a name="azure-powershell-deployment"></a>Implantação do Azure PowerShell

O Azure PowerShell pode ser usado para implantar a extensão da VM do Diagnóstico de Key Vault em uma máquina virtual existente ou em um conjunto de dimensionamento de máquina virtual. 

* Para implantar a extensão em uma VM:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMName> + '/KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Set-AzureRmVmExtension -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Para implantar a extensão em um conjunto de escala VM, por exemplo, como parte de um cluster do Service Fabric:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMSSName> + '_KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Add-AzureRmVmssExtension -VirtualMachineScaleSet <VMSS> -Name $extName -Publisher $extPublisher -Type $extType -Setting $settings
    
    ```

Por favor esteja ciente das seguintes restrições/exigências:
- A implantação deve ser feita na região Centro Sul dos EUA
- Restrições de Key Vault:
    - Ele deve existir no momento da implantação 
    - Deve estar localizado na mesma região e grupo de recursos da implantação
    - Deve ser habilitado para implantação e implantação de modelo

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

Exemplos para implantar a extensão de VM de Key Vault na CLI do Azure estarão disponíveis em breve. 

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados no Portal do Azure usando o Azure PowerShell. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir usando o Azure PowerShell.

```powershell
Get-AzureRMVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

A saída de execução da extensão é registrada no seguinte arquivo:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.Edp.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
