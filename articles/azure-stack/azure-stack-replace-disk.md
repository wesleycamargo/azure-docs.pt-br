---
title: "Substituir um disco físico na pilha do Azure | Microsoft Docs"
description: "Descreve o processo de como substituir um disco físico na pilha do Azure."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: a95617a8dd2a8f296164c672e2b4b2628574ce5a
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Substituir um disco físico na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Este artigo descreve o processo geral para substituir um disco físico na pilha do Azure. Se um disco físico falhar, você deve substituí-lo assim que possível.

Você pode usar este procedimento para sistemas integrados e para implantações do kit de desenvolvimento que possuem discos trocados.

Substituição de disco real etapas irão variar com base no seu fornecedor de hardware do fabricante (OEM). Consulte a documentação do fornecedor campo FRU (unidade renovável) para obter etapas detalhadas que são específicas para seu sistema. 

## <a name="review-disk-alert-information"></a>Revise as informações de alerta do disco
Quando um disco falhar, você receberá um alerta informando que a conectividade foi perdida em um disco físico. 

 ![Conectividade mostrando alerta perdida no disco físico](media/azure-stack-replace-disk/DiskAlert.png)

Se você abrir o alerta, a descrição do alerta contém o nó de unidade de escala e o local exato de slot físico para o disco que você deve substituir. Ainda mais a pilha do Azure ajuda a identificar o disco com falha, usando recursos do LED indicador.

 ## <a name="replace-the-disk"></a>Substituir o disco

Siga as instruções de FRU de seu fornecedor hardware de OEM para substituição do disco atual.

Para evitar o uso de um disco sem suporte em um sistema integrado, o sistema bloqueia os discos que não são suportados pelo seu fornecedor. Se você tentar usar um disco sem suporte, um novo alerta informa que um disco tem sido colocado em quarentena devido a um modelo sem suporte ou o firmware.

Depois que você substituir o disco, Azure pilha automaticamente detecta o novo disco e inicia o processo de reparo de disco virtual.  
 
 ## <a name="check-the-status-of-virtual-disk-repair"></a>Verificar o status de reparo de disco virtual
 
 Depois que você substituir o disco, você pode monitorar o status de integridade do disco virtual e repare o andamento do trabalho usando o ponto de extremidade com privilégios. Siga estas etapas de qualquer computador que tenha conectividade de rede para o ponto de extremidade com privilégios.

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

3. Execute o seguinte comando para exibir o status atual do trabalho de armazenamento:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ````
      ![Saída do comando Get-StorageJob do PowerShell](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>Solucionar problemas de reparo de disco virtual

Se o disco virtual reparar trabalho aparece preso, execute o seguinte comando para reiniciar o trabalho:
  ````PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```` 
