---
title: Disponibilizar conjuntos de dimensionamento de máquina Virtual no Azure Stack | Microsoft Docs
description: Saiba como um operador de nuvem pode adicionar conjuntos de dimensionamento de máquina Virtual no Azure Stack Marketplace
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 06/05/2018
ms.author: brenduns
ms.reviewer: kivenkat
ms.openlocfilehash: 4e77e187d969af7ea2a12754b18d4a218daceed6
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411899"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Disponibilizar conjuntos de dimensionamento de máquina Virtual no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Conjuntos de dimensionamento de máquina virtual são um recurso de computação do Azure Stack. Você pode usá-los para implantar e gerenciar um conjunto de máquinas virtuais idênticas. Com todas as máquinas virtuais configuradas da mesma, conjuntos de dimensionamento não exigem pré-provisionamento de máquinas virtuais. Ele é mais fácil criar serviços em grande escala que se destinam a computação de grande porte, big data e cargas de trabalho em contêineres.

Este artigo orienta você pelo processo de disponibilizar conjuntos de dimensionamento no Azure Stack Marketplace. Depois de concluir este procedimento, os usuários podem adicionar conjuntos de dimensionamento de máquina virtual para suas assinaturas.

Conjuntos de dimensionamento de máquina virtual no Azure Stack são como conjuntos de dimensionamento de máquina virtual no Azure. Para obter mais informações, consulte os seguintes vídeos:
* [Mark Russinovich fala sobre os conjuntos de dimensionamento do Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Conjuntos de Dimensionamento de Máquina Virtual com Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

No Azure Stack, os conjuntos de dimensionamento de máquina virtual não dão suporte a dimensionamento automático. Você pode adicionar mais instâncias para um conjunto de dimensionamento usando modelos do Resource Manager, a CLI ou PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

- **O Marketplace**  
    Registro do Azure Stack com o Azure global para habilitar a disponibilidade dos itens no Marketplace. Siga as instruções em [registrar o Azure Stack com o Azure](azure-stack-registration.md).
- **Imagem do sistema operacional**  
    Se você ainda não adicionou uma imagem do sistema operacional para o Azure Stack Marketplace, consulte [adicionar um item do marketplace do Azure Stack da Azure](asdk/asdk-marketplace-item.md).

## <a name="add-the-virtual-machine-scale-set"></a>Adicionar o conjunto de dimensionamento de máquina Virtual

1. Abra o Azure Stack Marketplace e se conectar ao Azure. Selecione **gerenciamento do Marketplace**> **+ adicionar do Azure**.

    ![Gerenciamento do Marketplace](media/azure-stack-compute-add-scalesets/image01.png)

2. Adicionar e baixe o item do marketplace de conjunto de dimensionamento de máquina Virtual.

    ![Conjunto de Escalas da Máquina Virtual](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Atualizar as imagens em um conjunto de dimensionamento de máquina Virtual

Depois de criar um conjunto de dimensionamento de máquina virtual, os usuários podem atualizar imagens no conjunto de dimensionamento sem ter que ser recriados de conjunto de dimensionamento. O processo para atualizar uma imagem depende dos cenários a seguir:

1. Modelo de implantação de conjunto de dimensionamento de máquinas virtuais **especifica mais recente** para *versão*:  

   Quando o *versão* é definido como **mais recente** no *imageReference* seção do modelo para uma escala definida, escalar verticalmente as operações sobre o uso do conjunto de escala a versão disponível mais recente instâncias do conjunto da imagem para a escala. Depois que um backup de dimensionamento for concluído, você pode excluir instâncias de conjuntos de dimensionamento de máquina virtual mais antigas.  (Os valores para *publisher*, *oferecem*, e *sku* permanecem inalterados). 

   A seguir está um exemplo da especificação *mais recente*:  

    ```Json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   Antes de escalar verticalmente pode usar uma nova imagem, você deve baixar se a nova imagem:  

   - Quando a imagem no Marketplace é uma versão mais recente que a imagem no conjunto de dimensionamento: baixar a nova imagem que substitui a imagem mais antiga. Depois que a imagem for substituída, um usuário pode prosseguir para escalar verticalmente. 

   - Quando a versão da imagem no Marketplace é o mesmo que a imagem no conjunto de dimensionamento: excluir a imagem que está em uso no conjunto de dimensionamento e, em seguida, baixar a nova imagem. Durante o tempo entre a remoção da imagem original e o download da nova imagem, você não pode escalar verticalmente. 
      
     Esse processo é necessário para resyndicate imagens que tornam usam do formato de arquivo esparso, introduzido na versão 1803. 
 

2. Modelo de implantação de conjunto de dimensionamento de máquinas virtuais **não especificar mais recente** para *versão* e especifica um número de versão em vez disso:  

    Se você baixar uma imagem com uma versão mais recente (que altera a versão disponível), o conjunto de dimensionamento não é possível escalar verticalmente. Isso ocorre por design, como a versão da imagem especificada no modelo de conjunto de escala deve estar disponível.  

Para obter mais informações, consulte [discos do sistema operacional e imagens](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images).  


## <a name="remove-a-virtual-machine-scale-set"></a>Remover um conjunto de dimensionamento de máquina Virtual

Para remover uma máquina virtual do item da Galeria de conjunto de dimensionamento, execute o seguinte comando do PowerShell:

```PowerShell  
    Remove-AzsGalleryItem
````

> [!NOTE]
> O item da Galeria não pode ser removido imediatamente. Noturna você precisa atualizar o portal várias vezes antes do item mostra como removida do Marketplace.

## <a name="next-steps"></a>Próximas etapas
[Perguntas frequentes para o Azure Stack](azure-stack-faq.md)