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
ms.openlocfilehash: 0e9203f5b4e2a9043e242b804c82017cf6fc3ee1
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010796"
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

1. [Criar um volume do Azure NetApp Files](azure-netapp-files-create-volumes.md)
2. [Configurar a política de exportação para um volume (opcional)](azure-netapp-files-configure-export-policy.md)

