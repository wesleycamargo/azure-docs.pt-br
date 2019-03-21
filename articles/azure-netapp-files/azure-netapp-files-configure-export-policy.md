---
title: Configurar a política de exportação para um volume de Azure NetApp Files | Microsoft Docs
description: Descreve como configurar a política de exportação para controlar o acesso a um volume de Azure NetApp Files
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
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 1098c5c2f42b242b3ba2f68ec7480de90dd7c22e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766641"
---
# <a name="configure-export-policy-for-a-volume"></a>Configurar a política de exportação para um volume

Opcionalmente, você pode configurar a política de exportação para controlar o acesso a um volume de Azure NetApp Files. 

## <a name="steps"></a>Etapas 

1.  Clique na folha **Criar Política de Exportação** na folha Gerenciar Volume. 

2.  Especifique informações para os campos a seguir para criar uma regra de política de exportação:   
    *  **Index**   
        Especifique o número de índice para a regra.  
        Uma política de exportação pode consistir em até cinco regras. As regras são avaliadas de acordo com sua ordem na lista de números de índice. As regras com números de índice mais baixos são avaliadas primeiro. Por exemplo, a regra com o número de índice 1 é avaliada antes da regra com o número de índice 2. 

    * **Clientes permitidos**   
        Especifique o valor em um dos seguintes formatos:  
        * Endereço IPv4, por exemplo, `10.1.12.24` 
        * Endereço IPv4 com uma máscara de sub-rede expressada como um número de bits, por exemplo, `10.1.12.10/4`

    * **Access**  
        Selecione um dos seguintes tipos de acesso:  
        * Sem Acesso 
        * Leitura e Gravação
        * Somente leitura

    * **Protocolos**   
        Especifique o protocolo a ser usado para a política de exportação.   
        Atualmente, o Azure NetApp Files dá suporte somente a NFSv3.

    ![Exportar política](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Próximas etapas 
* [Gerenciar volumes](azure-netapp-files-manage-volumes.md)
* [Montar ou desmontar um volume para máquinas virtuais](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gerenciar instantâneos](azure-netapp-files-manage-snapshots.md)
