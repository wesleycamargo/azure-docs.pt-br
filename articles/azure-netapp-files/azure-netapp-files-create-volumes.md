---
title: Criar um volume para o Azure NetApp Files
description: Descreve como criar um volume para Azure NetApp Files.
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
ms.date: 11/13/2018
ms.author: b-juche
ms.openlocfilehash: 8fc76c33055f7131444a073d2f8560e136c0701d
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413228"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Criar um volume para Azure NetApp Files

O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume.  Você pode criar vários volumes em um pool de capacidade, mas o consumo de capacidade total dos volumes não deve exceder o tamanho do pool. 

## <a name="before-you-begin"></a>Antes de começar 
Você deve já configurou um pool de capacidade.   
[Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede deve ser delegada ao Azure NetApp Files.  
[Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)


## <a name="steps"></a>Etapas 
1.  Clique na folha **Volumes** da folha Gerenciar Pools de Capacidade. 
2.  Clique em **+ Adicionar volume** para criar um volume.  
    A janela Novo Volume é exibida.

3.  Na janela Novo Volume, clique em **Criar** e forneça informações para os seguintes campos:   
    * **Nome**      
        Especifique o nome para o volume que você está criando.   

        O nome precisa ser exclusivo em um grupo de recursos. Deve ter pelo menos três caracteres de comprimento.  Deve usar qualquer caractere alfanumérico.

    * **Caminho do arquivo**  
        Especifique o caminho do arquivo que será usado para criar o caminho de exportação para o novo volume. O caminho de exportação é usado para montar e acessar o volume.   
     
        O nome do caminho do arquivo pode conter apenas letras, números e hifens ("-"). O nome deve ter entre 16 e 40 caracteres.  

    * **Cota**  
        Especifique a quantidade de armazenamento lógico que é alocada para o volume.  

        O campo **cota disponível** mostra a quantidade de espaço não utilizado no pool de capacidade escolhido que você pode usar para a criação de um novo volume. O tamanho do novo volume não pode exceder a cota disponível.  

    * **Rede virtual**  
        Especifique a rede virtual (VNet) do Azure da qual você deseja acessar o volume.  

        A Vnet especificada deve ter uma sub-rede delegada ao Azure NetApp Files. O serviço Azure NetApp Files pode ser acessado apenas da mesma Vnet ou de uma Vnet que esteja na mesma região que o volume por meio do Emparelhamento VNET. Também é possível acessar o volume da sua rede local por meio do ExpressRoute.   

    * **Sub-rede**  
        Especifique a sub-rede que você deseja usar para o volume.  
        A sub-rede especificada deve ser delegada ao Azure NetApp Files. 
        
        Se você não tiver delegado uma sub-rede, poderá clicar em **Criar novo** na página Criar um volume. Em seguida, na página Criar sub-rede, especifique as informações da sub-rede e selecione **Microsoft.NetApp/volumes** para delegar a sub-rede para o Azure NetApp Files.    
 
        ![Novo volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)


4.  Clique em **OK**. 
 
Um volume herda a assinatura, grupo de recursos, atributos de localização de seu pool de capacidade. Para monitorar o status de implantação do volume, você pode usar a guia Notificações.

## <a name="next-steps"></a>Próximas etapas  
* [Configurar a política de exportação para um volume (opcional)](azure-netapp-files-configure-export-policy.md)
* [Saiba mais sobre a integração de rede virtual para os serviços do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)

