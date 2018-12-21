---
title: Configurar um pool de capacidade para Azure NetApp Files | Microsoft Docs
description: Descreve como configurar um pool de capacidade para que você possa criar volumes nele.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 55a1d16ce1617ecf7bc28c7c62de8557ceeea311
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53412904"
---
# <a name="set-up-a-capacity-pool"></a>Configurar um pool de capacidade
Como configurar um pool de capacidade permite que você crie volumes dentro dele.  

## <a name="before-you-begin"></a>Antes de começar 
Você já deve ter criado uma conta do NetApp.   

[Criar uma conta do NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Etapas 

1. Vá para a folha de gerenciamento para sua conta do NetApp e, em seguida, no painel de navegação, selecione **Pools de capacidade**.

2. Clique em **+ Adicionar pools** para criar um novo pool de capacidade.   
    A janela Novo Pool de Capacidade é exibida.

3. Forneça as seguintes informações para o novo pool de capacidade:  
  * **Nome**  
    Especifique o nome para o pool de capacidade.  
    O nome do pool de capacidade deve ser exclusivo para cada conta do NetApp.

  * **Nível de serviço**   
    Esse campo mostra o desempenho de destino para o pool de capacidade.  
    Atualmente, apenas o nível de serviço Premium está disponível. 

  *  **Tamanho**     
      Especifique o tamanho do pool de capacidade que você está adquirindo.        
      O tamanho do pool de capacidade mínima é de 4 TiB. Você pode criar um pool com um tamanho de múltiplos de 4 TiB.   
      
      ![Novo pool de capacidade](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Clique em **OK**.

## <a name="next-steps"></a>Próximas etapas 

[Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)


