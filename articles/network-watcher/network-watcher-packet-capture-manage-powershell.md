---
title: Gerenciar as capturas de pacotes com o Observador de Rede do Azure - PowerShell | Microsoft Docs
description: "Esta página explica como gerenciar o recurso de captura de pacotes do Observador de Rede usando o PowerShell"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 04d82085-c9ea-4ea1-b050-a3dd4960f3aa
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: d2a65104743d9497debdc85c134fd1a06114c514
ms.openlocfilehash: 1ecd1cd12a5c8b62ee3c8623bd38431879546760
ms.lasthandoff: 02/23/2017

---

# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Gerenciar as capturas de pacotes com o Observador de Rede do Azure usando o PowerShell

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI](network-watcher-packet-capture-manage-cli.md)
> - [API REST](network-watcher-packet-capture-manage-rest.md)

A captura de pacotes do Observador de Rede permite que você crie sessões de captura para controlar o tráfego para e a partir de uma máquina virtual. Os filtros são fornecidos para a sessão de captura para garantir que você capture somente o tráfego desejado. A captura de pacotes ajuda a diagnosticar os problemas de rede de modo reativo e proativo. Outros usos incluem a coleta das estatísticas de rede, obter informações sobre as invasões de rede, depurar as comunicações entre cliente e servidor, e muito mais. Sendo capaz de disparar remotamente as capturas de pacotes, esse recurso alivia a carga de executar uma captura de pacotes manualmente e no computador desejado, o que economiza tempo.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

Este artigo o guiará nas diferentes tarefas de gerenciamento que estão atualmente disponíveis para a captura de pacotes.

- [**Iniciar uma captura de pacotes**](#start-a-packet-capture)
- [**Parar uma captura de pacotes**](#stop-a-packet-capture)
- [**Excluir uma captura de pacotes**](#delete-a-packet-capture)
- [**Baixar uma captura de pacotes**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem os seguintes recursos:

* Uma instância do Observador de Rede na região na qual você deseja criar uma captura de pacotes

* Uma máquina virtual com a extensão da captura de pacotes habilitada.

> [!IMPORTANT]
> A captura de pacotes requer uma extensão da máquina virtual `AzureNetworkWatcherExtension`. Para instalar a extensão em uma VM do Windows, visite [Extensão da máquina virtual do Agente do Observador de Rede do Azure para Windows](../virtual-machines/virtual-machines-windows-extensions-nwa.md) e para a VM do Linux, visite [Extensão da máquina virtual do Agente do Observador de Rede do Azure para Linux](../virtual-machines/virtual-machines-linux-extensions-nwa.md).

## <a name="install-vm-extension"></a>Instalar a extensão da VM

### <a name="step-1"></a>Etapa 1

```powershell
$VM = Get-AzureRmVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Etapa 2

O exemplo a seguir recupera as informações da extensão necessárias para executar o cmdlet `Set-AzureRmVMExtension`. Esse cmdlet instala o agente de captura de pacotes na máquina virtual convidada.

> [!NOTE]
> O cmdlet `Set-AzureRmVMExtension` pode levar vários minutos para concluir.

Para as máquinas virtuais do Windows:

```powershell
$AzureNetworkWatcherExtension = Get-AzureRmVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Para as máquinas virtuais do Linux:

```powershell
$AzureNetworkWatcherExtension = Get-AzureRmVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
````

O exemplo a seguir é uma resposta bem-sucedida após executar o cmdlet `Set-AzureRmVMExtension`.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Etapa 3

Para garantir que o agente está instalado, execute o cmdlet `Get-AzureRmVMExtension` e passe o nome da máquina virtual e o nome da extensão.

```powershell
Get-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

A seguir está um exemplo de resposta ao executar `Get-AzureRmVMExtension`

```
ResourceGroupName       : testrg
VMName                  : testvm1
Name                    : AzureNetworkWatcherExtension
Location                : westcentralus
Etag                    : null
Publisher               : Microsoft.Azure.NetworkWatcher
ExtensionType           : NetworkWatcherAgentWindows
TypeHandlerVersion      : 1.4
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1/
                          extensions/AzureNetworkWatcherExtension
PublicSettings          : 
ProtectedSettings       : 
ProvisioningState       : Succeeded
Statuses                : 
SubStatuses             : 
AutoUpgradeMinorVersion : True
ForceUpdateTag          : 
```

## <a name="start-a-packet-capture"></a>Iniciar uma captura de pacotes

Depois que as etapas anteriores são concluídas, o agente de captura de pacotes é instalado na máquina virtual.

### <a name="step-1"></a>Etapa 1

A próxima etapa é recuperar a instância do Observador de Rede. Essa variável é passada para o cmdlet `New-AzureRmNetworkWatcherPacketCapture` na etapa 4.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName  
```

### <a name="step-2"></a>Etapa 2

Recupere uma conta de armazenamento. Essa conta de armazenamento é usada para armazenar o arquivo de captura de pacotes.

```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Etapa 3

Os filtros podem ser usados para limitar os dados armazenados pela captura de pacotes. O exemplo a seguir configura dois filtros.  Um filtro coleta o tráfego de saída TCP somente do IP local 10.0.0.3 até as portas de destino 20, 80 e 443.  O segundo filtro coleta apenas o tráfego UDP.

```powershell
$filter1 = New-AzureRmPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzureRmPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Vários filtros podem ser definidos para uma captura de pacotes.

### <a name="step-4"></a>Etapa 4

Execute o cmdlet `New-AzureRmNetworkWatcherPacketCapture` para iniciar o processo de captura de pacotes, passando os valores necessários recuperados nas etapas anteriores.
```powershell

New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filters $filter1, $filter2
```

O exemplo a seguir é a saída esperada ao executar o cmdlet `New-AzureRmNetworkWatcherPacketCapture`.

```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"3bf27278-8251-4651-9546-c7f369855e4e"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]


```

## <a name="get-a-packet-capture"></a>Obter uma captura de pacotes

Executar o cmdlet `Get-AzureRmNetworkWatcherPacketCapture` recupera o status de uma captura de pacotes atualmente em execução ou concluída.

```powershell
Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

O exemplo a seguir é a saída do cmdlet `Get-AzureRmNetworkWatcherPacketCapture`. O exemplo a seguir é após a conclusão da captura. O valor PacketCaptureStatus é Parado, com um StopReason de TimeExceeded. Esse valor mostra que a captura de pacotes foi bem-sucedida e executada em seu tempo.
```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"4b9a81ed-dc63-472e-869e-96d7166ccb9b"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]
CaptureStartTime        : 2/1/2017 10:43:01 PM
PacketCaptureStatus     : Stopped
StopReason              : TimeExceeded
PacketCaptureError      : []
```

## <a name="stop-a-packet-capture"></a>Parar uma captura de pacotes

Executando o cmdlet `Stop-AzureRmNetworkWatcherPacketCapture`, se uma sessão de captura estiver em andamento, ela será interrompida.

```powershell
Stop-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> O cmdlet retorna sem resposta quando executado em uma sessão de captura atualmente em execução ou uma sessão existente que já parou.

## <a name="delete-a-packet-capture"></a>Excluir uma captura de pacotes

```powershell
Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Excluir uma captura de pacotes não exclui o arquivo na conta de armazenamento.

## <a name="download-a-packet-capture"></a>Baixar um pacote de capturas

Assim que a sessão de captura de pacotes conclui, o arquivo de captura pode ser carregado no armazenamento de blobs ou em um arquivo local na VM. O local de armazenamento da captura de pacotes é definido na criação da sessão. Uma ferramenta conveniente para acessar esses arquivos de captura salvos em uma conta de armazenamento é o Gerenciador de Armazenamento do Microsoft Azure, que pode ser baixado aqui: http://storageexplorer.com/

Se uma conta de armazenamento for especificada, os arquivos de captura de pacotes serão salvos em uma conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Próximas etapas

Saiba como automatizar as capturas de pacotes com alertas da Máquina Virtual exibindo [Criar uma captura de pacotes disparada por alertas](network-watcher-alert-triggered-packet-capture.md)

Localizar se determinado tráfego é permitido dentro ou fora de sua VM visitando [Verificar o fluxo do IP](network-watcher-check-ip-flow-verify-portal.md)

<!-- Image references -->















