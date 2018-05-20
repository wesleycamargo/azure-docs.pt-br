---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 5/9/2018
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 4db9fe907ab6625fcad74ceae59f17115458a3ea
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="write-accelerator"></a>Acelerador de Gravação
O Acelerador de Gravação é uma capacidade de disco máquinas virtuais (VMs) da Série M no Armazenamento Premium com Azure Managed Disks exclusivamente. Como o nome indica, o objetivo da funcionalidade é melhorar a latência de E/S das gravações no Armazenamento Premium do Azure. O Acelerador de Gravação é ideal para quando as atualizações do arquivo de log são necessárias para manter em disco em um modo de alto desempenho para bancos de dados modernos.

O Acelerador de Gravação está disponível para as VMs da Série M na nuvem pública.

## <a name="planning-for-using-write-accelerator"></a>Planejar a utilização do Acelerador de Gravação
O Acelerador de Gravação deve ser utilizado para os volumes, os quais contêm o log de transações ou logs de recuperação de um DBMS. Não é recomendável usar Aceleradores de Gravação para os volumes de dados de um DBMS porque o recurso foi otimizado para ser usado em discos de log.

O Acelerador de Gravação funciona somente em conjunto com o [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). 


> [!IMPORTANT]
> Se você quiser habilitar ou desabilitar o Acelerador de Gravação para um volume existente que é compilado de vários discos do Armazenamento Premium do Azure e distribuído utilizando gerenciadores de volume ou discos do Windows, SOFS (Servidor de Arquivos de Escalabilidade Horizontal do Windows), LVM do Linux ou MDADM, todos os discos compilando o volume devem ser habilitados ou desabilitados para o Acelerador de Gravação em etapas separadas. **Antes de habilitar ou desabilitar o Acelerador de Gravação nessa configuração, desligue a VM do Azure**. 


> [!IMPORTANT]
> Para habilitar o Acelerador de Gravação para um disco do Azure existente que NÃO faça parte de um volume compilado de vários discos com gerenciadores de volume ou discos do Windows, SOFS (Servidor de Arquivos de Escalabilidade Horizontal do Windows), LVM do Linux ou MDADM, a carga de trabalho que acessa o disco do Azure precisa ser desligada. Os aplicativos de banco de dados utilizando o disco do Azure devem ser desligados.

> [!IMPORTANT]
> Habilitar o Acelerador de Gravação para disco do sistema operacional da VM reiniciará a VM. 

Habilitar o Acelerador e Gravação para discos do sistema operacional não deve ser necessária para configurações de VM relacionadas ao SAP

### <a name="restrictions-when-using-write-accelerator"></a>Restrições ao utilizar o Acelerador de Gravação
Ao utilizar o Acelerador de Gravação para um VHD/disco do Azure, estas restrições são aplicáveis:

- O armazenamento em cache do disco Premium precisa ser definido como 'Nenhum' ou ‘Somente leitura’. Todos os outros modos de armazenamento em cache não têm suporte.
- O instantâneo no disco habilitado do Accelerator de Gravação ainda não tem suporte. Essa restrição bloqueia a capacidade do Serviço de Backup do Azure de executar um instantâneo consistente de todos os discos da máquina virtual.
- Somente E/S menores (<=32KiB) estão tomando o caminho mais rápido. Em situações de carga de trabalho em que os dados estão sendo carregados em massa ou quando buffers de log de transação dos diferentes DBMS são preenchidos a um maior grau antes de serem retidos no armazenamento, é provável que a E/S gravada em disco não esteja fazendo o caminho mais rápido.

Há limites de VHDs de Armazenamento Premium do Azure por VM que podem ter suporte pelo Acelerador de Gravação. Os limites atuais são:

| SKU da VM | Número de discos do Acelerador de Gravação | IOPS do Disco Acelerador de Gravação por VM |
| --- | --- | --- |
| M128ms | 16 | 8000 |
| M128s | 16 | 8000 |
| M64ms | 8 | 4000 |
| M64s | 8 | 4000 | 

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Habilitar o Acelerador de Gravação em um disco específico
As próximas seções descreverão como o Acelerador de Gravação pode ser habilitado nos VHDs do Armazenamento Premium do Azure.


### <a name="prerequisites"></a>pré-requisitos
Os pré-requisitos a seguir são aplicáveis ao uso do Acelerador de Gravação neste momento:

- Os discos aos quais você deseja aplicar o Acelerador de Gravação do Azure precisam ser [discos gerenciados do Azure](https://azure.microsoft.com/services/managed-disks/) no Armazenamento Premium.
- Você deve estar usando uma VM de Série M

### <a name="enabling-through-power-shell"></a>Habilitar por meio do Powershell
O módulo do PowerShell do Azure da versão 5.5.0 inclui as alterações nos cmdlets relevantes para habilitar ou desabilitar o Acelerador de Gravação para discos específicos do Armazenamento Premium do Azure.
Para habilitar ou implantar discos com suporte pelo Acelerador de Gravação, os seguintes comandos do PowerShell foram alterados e estendidos para aceitar um parâmetro para o Acelerador de Gravação.

Um novo parâmetro de opção, "WriteAccelerator" foi adicionado aos cmdlets a seguir: 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

Não fornecer o parâmetro define a propriedade como falso e implantará discos que não tenham suporte pelo Acelerador de Gravação.

Um novo parâmetro de opção, "OsDiskWriteAccelerator" foi adicionado aos cmdlets a seguir: 

- Set-AzureRmVmssStorageProfile

Não fornecer o parâmetro define a propriedade como falso e entregará discos que não aproveitam o Acelerador de Gravação.

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

#### <a name="adding-a-new-disk-supported-by-write-accelerator"></a>Adicionar novo disco com suporte pelo Acelerador de Gravação
É possível utilizar esse script para adicionar um novo disco à sua VM. O disco criado com esse script usará o Acelerador de Gravação.

```

# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
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
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

É necessário adaptar os nomes de VM, disco e grupo de recursos. O script acima adiciona o Acelerador de Gravação a um disco existente, onde o valor $newstatus é definido como '$true'. Usar o valor '$false' desabilitará o Acelerador de Gravação em um determinado disco.

> [!Note]
> Executar o script acima desanexará o disco especificado, habilitará o Acelerador de Gravação no disco e, em seguida, anexará o disco novamente

### <a name="enabling-through-azure-portal"></a>Habilitação por meio do Portal do Azure

Você pode habilitar o Acelerador de Gravação por meio do Portal onde pode especificar suas configurações de cache de disco: 

![Acelerador de Gravação no Portal do Azure](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

### <a name="enabling-through-azure-cli"></a>Habilitação por meio da CLI do Azure
Você pode usar a [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) para habilitar o Acelerador de Gravação. 

Para habilitar o Acelerador de Gravação em um disco existente, use o comando abaixo, substituindo o diskName, VMName e ResourceGroup para o seu próprio: 
```
az vm update -g group1 -n vm1 –write-accelerator 1=true
```
Para anexar um disco com o Acelerador de Gravação habilitado, use o comando abaixo com seus valores:
```
az vm disk attach -g group1 –vm-name vm1 –disk d1 --enable-write-accelerator
```
Para desabilitar o Acelerador de Gravação, defina a propriedade como false: 
```
az vm update -g group1 -n vm1 –write-accelerator 0=false 1=false
```

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

 
