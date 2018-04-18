---
title: Acelerador de Gravação do Azure para implantações do SAP | Microsoft Docs
description: Guia de operações para sistemas SAP HANA que são implantados em máquinas virtuais (VMs) do Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0cb9b4003faa2ccdd07ccc78c2095472690f0e7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-write-accelerator-for-sap-deployments"></a>Acelerador de Gravação do Azure para implantações do SAP
O Acelerador de Gravação do Azure é uma funcionalidade que está sendo implementada exclusivamente para as VMs da Série M. O Acelerador de Gravação do Azure não está disponível com nenhuma outra Série de VM no Azure, exceto a Série M. Como o nome indica, o objetivo da funcionalidade é melhorar a latência de E/S das gravações no Armazenamento Premium do Azure. 

>[!NOTE]
> Nesse momento, o Acelerador de Gravação do Azure está em visualização pública e exige uma lista de permissões da sua ID de assinatura do Azure

A funcionalidade do Acelerador de Gravação do Azure está disponível para implantação de série M como visualização pública em:

- Oeste dos EUA 2
- Europa Ocidental
- Sudeste Asiático

## <a name="planning-for-using-azure-write-accelerator"></a>Planejar a utilização do Acelerador de Gravação do Azure
O Acelerador de Gravação do Azure deve ser utilizado para os volumes, os quais contêm o log de transações ou logs de recuperação de um DBMS. Não é recomendável utilizar o Acelerador de Gravação do Azure para os volumes de dados de um DBMS. O motivo para essa restrição é que o Acelerador de Gravação do Azure requer que os VHDs do Armazenamento Premium do Azure sejam montados sem o armazenamento em cache de leitura adicional que está disponível para o Armazenamento Premium. Vantagens maiores com esse tipo de cache podem ser observadas com bancos de dados tradicionais. Como o Acelerador de Gravação afeta apenas as atividades de gravação e não acelera as leituras, o design com suporte para SAP é utilizar o Acelerador de Gravação no log de transações ou nas unidades de log de recuperação dos bancos de dados com suporte para SAP. 

O Acelerador de Gravação do Azure funciona somente em conjunto com o [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Portanto, é necessário planejar adequadamente. 

>[!NOTE]
> Como a funcionalidade do Acelerador de Gravação do Azure ainda está em visualização pública, nenhuma implantação de cenário de produção tem suporte na funcionalidade.

Há limites de VHDs de Armazenamento Premium do Azure por VM que podem ter suporte pelo Acelerador de Gravação do Azure. Os limites atuais são:

- 16 VHDs para uma VM M128xx
- 8 VHDs para uma VM M64xx

> [!IMPORTANT]
> Se você quiser habilitar ou desabilitar o Acelerador de Gravação do Azure para um volume existente que é compilado de vários discos do Armazenamento Premium do Azure e distribuído utilizando gerenciadores de volume ou discos do Windows, Espaços do Armazenamento do Windows, SOFS (Servidor de Arquivos de Escalabilidade Horizontal do Windows), LVM do Linux ou MDADM, todos os discos compilando o volume devem ser habilitados ou desabilitados para o Acelerador de Gravação em etapas separadas. **Antes de habilitar ou desabilitar o Acelerador de Gravação nessa configuração, desligue a VM do Azure**. 


> [!IMPORTANT]
> Para habilitar o Acelerador de Gravação do Azure para um disco do Azure existente que NÃO faça parte de um volume compilado de vários discos com gerenciadores de volume ou discos do Windows, Espaços de Armazenamento do Windows, SOFS (Servidor de Arquivos de Escalabilidade Horizontal do Windows), LVM do Linux ou MDADM, a carga de trabalho que acessa o disco do Azure precisa ser desligada. Os aplicativos de banco de dados utilizando o disco do Azure devem ser desligados.

> [!IMPORTANT]
> Habilitar o Acelerador de Gravação para disco do sistema operacional da VM do Azure da VM reiniciará a VM. 

Habilitar o Acelerador e Gravação do Azure para discos operacionais não deve ser necessária para configurações de VM relacionadas ao SAP

### <a name="restrictions-when-using-azure-write-accelerator"></a>Restrições ao utilizar o Acelerador e Gravação do Azure
Ao utilizar o Acelerador de Gravação do Azure para um VHD/disco do Azure, estas restrições são aplicáveis:

- O armazenamento em cache do disco Premium precisa ser definido como 'Nenhum' ou ‘Somente leitura’. Todos os outros modos de armazenamento em cache não têm suporte.
- O instantâneo no disco habilitado do Accelerator de Gravação ainda não tem suporte. Essa restrição bloqueia a capacidade do Serviço de Backup do Azure de executar um instantâneo consistente de todos os discos da máquina virtual.
- Somente E/S menores estão fazendo o caminho mais rápido. Em situações de carga de trabalho em que os dados estão sendo carregados em massa ou quando buffers de log de transação dos diferentes DBMS são preenchidos a um maior grau antes de serem retidos no armazenamento, é provável que a E/S gravada em disco não esteja fazendo o caminho mais rápido.


## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Habilitar o Acelerador de Gravação em um disco específico
As próximas seções descreverão como o Acelerador de Gravação do Azure pode ser habilitado nos VHDs do Armazenamento Premium do Azure.

Neste momento, habilitar o Acelerador de Gravação por meio da API REST do Azure e do PowerShell é o único método. Outros métodos até suporte no Portal do Azure seguirão no decorrer das próximas semanas.

### <a name="prerequisites"></a>pré-requisitos
Os pré-requisitos a seguir são aplicáveis ao uso do Acelerador de Gravação do Azure neste momento:

- A ID de assinatura utilizada para implantar a VM e o armazenamento para a VM precisa estar na lista de permissões. Contate o seu Gerente de Contas, GBB ou CSA da Microsoft para obter sua ID de assinatura na lista de permissões. 
- Os discos aos quais você deseja aplicar o Acelerador de Gravação do Azure precisam ser [	discos gerenciados do Azure](https://azure.microsoft.com/services/managed-disks/).

### <a name="enabling-through-power-shell"></a>Habilitar por meio do Powershell
O módulo do Powershell do Azure da versão 5.5.0 inclui as alterações nos cmdlets relevantes para habilitar ou desabilitar o Acelerador de Gravação do Azure para discos específicos do Armazenamento Premium do Azure.
Para habilitar ou implantar discos com suporte pelo Acelerador de Gravação, os seguintes comandos do PowerShell foram alterados e estendidos para aceitar um parâmetro para o Acelerador de Gravação.

Um novo parâmetro de opção, "WriteAccelerator" foi adicionado aos cmdlets a seguir: 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

Não fornecer o parâmetro define a propriedade como falso e implantará discos que não tenham suporte pelo Acelerador de Gravação do Azure.

Um novo parâmetro de opção, "OsDiskWriteAccelerator" foi adicionado aos cmdlets a seguir: 

- Set-AzureRmVmssStorageProfile

Não fornecer o parâmetro define a propriedade como falso e entregará discos que não aproveitam o Acelerador de Gravação do Azure.

Um novo parâmetro opcional booliano (não anulável), "OsDiskWriteAccelerator" foi adicionado aos cmdlets a seguir: 

- Update-AzureRmVM
- Update-AzureRmVmss

Especifique se $true ou $false para controlar o suporte do Aelerador de Gravação do Azure com os discos.

Exemplos de comandos podem ser semelhantes a:

```

New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false 

```

Dois principais cenários podem ser em script, conforme mostrado nas seções a seguir.

#### <a name="adding--new-disk-supported-by-azure-write-accelerator"></a>Adicionar novo disco com suporte pelo Acelerador de Gravação do Azure
É possível utilizar esse script para adicionar um novo disco à sua VM. O disco criado com esse script usará o Acelerador de Gravação do Azure.

```

# Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzureRmVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```
É necessário adaptar os nomes de VM, disco, grupo de recursos, tamanho do disco e LunID do disco para a implantação específica.


#### <a name="enabling-azure-write-accelerator-on-an-existing-azure-disk"></a>Habilitar o Acelerador de Gravação do Azure em um disco do Azure existente
Se for necessário habilitar o Acelerador de Gravação em um disco existente, você poderá usar este script para executar a tarefa:

```

#Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "testsap-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

É necessário adaptar os nomes de VM, disco e grupo de recursos. O script acima adiciona o Acelerador de Gravação a um disco existente, pois o valor $newstatus é definido como '$true'. Usar o valor '$false' desabilitará o Acelerador de Gravação em um determinado disco.

> [!Note]
> Executar o script acima desanexará o disco especificado, habilitará o Acelerador de Gravação no disco e, em seguida, anexará o disco novamente




### <a name="enabling-through-rest-apis"></a>Habilitar por meio de APIs REST
Para implantar por meio da API REST do Azure, é necessário instalar o armclient do Azure

#### <a name="install-armclient"></a>Instalar o armclient

Para executar o armclient, é necessário instalá-lo por meio do Chocolatey. É possível instalá-lo por meio do cmd.exe ou Powershell. Utilize direitos elevados para esses comandos (“Executar como Administrador”).

Utilizando cmd.exe, execute o comando a seguir:

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

Utilizando o Powershell, é necessário usar:

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

Agora, é possível instalar o armclient com o comando abaixo em cmd.exe ou PowerShell

```
choco install armclient
```

#### <a name="getting-your-current-vm-configuration"></a>Obter a configuração atual da VM
Para alterar os atributos da configuração do disco, primeiro é necessário obter a configuração atual em um arquivo JSON. Você pode obter a configuração atual executando o comando a seguir:

```
armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>
```
Substitua os termos dentro de '<<   >>' pelos seus dados, incluindo o nome do arquivo que o arquivo JSON deve ter.

A saída pode ser semelhante a:

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

A próxima etapa é atualizar o arquivo JSON e habilitar o Acelerador de Gravação no disco chamado 'log1'. Essa etapa pode ser realizada adicionando esse atributo no arquivo JSON após a entrada do cache do disco. 

```
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Em seguida, atualize a implantação existente com este comando:

```
armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>

```

A saída deve ser semelhante à seguinte. É possível ver que há Acelerador de Gravação habilitado para um disco.

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

A partir do ponto das alterações, a unidade deverá ter suporte pelo Acelerador de Gravação.

 