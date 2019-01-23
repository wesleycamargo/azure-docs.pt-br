---
title: Substituir um disco físico no Azure Stack | Microsoft Docs
description: Descreve o processo de como substituir um disco físico no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2019
ms.author: mabrigg
ms.openlocfilehash: d0b455261649fad95a92f7ad75f7af26d633cf5a
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54476879"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Substituir um disco físico no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este artigo descreve o processo geral para substituir um disco físico no Azure Stack. Se um disco físico falhar, você deve substituí-lo assim que possível.

Você pode usar este procedimento para sistemas integrados e para implantações do kit de desenvolvimento que possuem discos intercambiáveis.

Substituição do disco atual etapas irão variar com base em seu fornecedor de hardware do fabricante original do equipamento (OEM). Consulte a documentação do fornecedor campo FRU (unidade) para obter etapas detalhadas que são específicas para seu sistema.

## <a name="review-disk-alert-information"></a>Examine as informações de alerta de disco
Quando um disco falhar, você receberá um alerta informando que a conectividade foi perdida para um disco físico.

 ![Alerta mostrando conectividade perdida para o disco físico](media/azure-stack-replace-disk/DiskAlert.png)

Se você abrir o alerta, a descrição do alerta contém o nó de unidade de escala e o local físico exato de slot para o disco que você deve substituir. Ainda mais o Azure Stack ajuda a identificar o disco com falha, usando recursos de indicador do LED.

 ## <a name="replace-the-disk"></a>Substituir o disco

Siga as instruções de FRU do seu fornecedor hardware de OEM para substituição do disco atual.

> [!note]
> Substitua discos para um nó de unidade de escala por vez. Aguarde até que os trabalhos de reparo de disco virtual ser concluída antes de passar para o próximo nó de unidade de escala

Para evitar o uso de um disco sem suporte em um sistema integrado, o sistema bloqueia os discos que não são suportados pelo seu fornecedor. Se você tentar usar um disco sem suporte, um novo alerta informa que um disco tiver sido colocado em quarentena devido a um modelo sem suporte ou firmware.

Depois de substituir o disco, o Azure Stack automaticamente detecta o novo disco e inicia o processo de reparo de disco virtual.
 
 ## <a name="check-the-status-of-virtual-disk-repair"></a>Verificar o status de reparo de disco virtual
 
 Depois de substituir o disco, você pode monitorar o status de integridade do disco virtual e reparar o andamento do trabalho usando o ponto de extremidade com privilégios. Siga estas etapas em qualquer computador que tenha conectividade de rede para o ponto de extremidade com privilégios.

1. Abra uma sessão do Windows PowerShell e conecte-se ao ponto de extremidade com privilégios.
    ````PowerShell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```` 
  
2. Execute o seguinte comando para exibir a integridade do disco virtual:
    ````PowerShell
        Get-VirtualDisk -CimSession s-cluster
    ````
   ![Saída do comando Get-VirtualDisk do PowerShell](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Execute o seguinte comando para exibir o status de trabalho de armazenamento atual:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ````
      ![Saída do comando Get-StorageJob do PowerShell](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>Solucionar problemas de reparo de disco virtual

Se o disco virtual reparar trabalho parece paralisado, execute o seguinte comando para reiniciar o trabalho:
  ````PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```` 
