---
title: Disponibilizar conjuntos de dimensionamento de máquina Virtual no Azure Stack | Microsoft Docs
description: Saiba como um operador de nuvem pode adicionar conjuntos de dimensionamento de máquina Virtual no Azure Stack Marketplace
services: azure-stack
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 10/22/2018
ms.author: sethm
ms.reviewer: kivenkat
ms.openlocfilehash: accd36c9e0d09da0073570301600b362fff85f6e
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647333"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Disponibilizar conjuntos de dimensionamento de máquina Virtual no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*
  
Conjuntos de dimensionamento de máquina virtual são um recurso de computação do Azure Stack. Você pode usá-los para implantar e gerenciar um conjunto de máquinas virtuais idênticas. Com todas as máquinas virtuais configuradas da mesma forma, os conjuntos de dimensionamento não exigem a pré-provisionamento de máquinas virtuais. Ele é mais fácil criar serviços em grande escala que se destinam a computação de grande porte, big data e cargas de trabalho em contêineres.

Este artigo orienta você pelo processo de disponibilizar conjuntos de dimensionamento no Azure Stack Marketplace. Depois de concluir este procedimento, os usuários podem adicionar conjuntos de dimensionamento de máquina virtual para suas assinaturas.

Conjuntos de dimensionamento de máquina virtual no Azure Stack são semelhantes aos conjuntos de dimensionamento de máquina virtual no Azure. Para obter mais informações, consulte os seguintes vídeos:
* [Mark Russinovich fala sobre os conjuntos de dimensionamento do Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Conjuntos de Dimensionamento de Máquina Virtual com Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

No Azure Stack, conjuntos de dimensionamento de máquina virtual não dão suporte a dimensionamento automático. Você pode adicionar mais instâncias para um conjunto de dimensionamento usando modelos do Resource Manager, a CLI ou PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

- **O Marketplace:** Register do Azure Stack com o Azure global para habilitar a disponibilidade dos itens no Marketplace. Siga as instruções em [registrar o Azure Stack com o Azure](azure-stack-registration.md).
- **Imagem do sistema operacional:** antes de um conjunto de dimensionamento de máquinas virtuais (VMSS) pode ser criado, você deve baixar as imagens de VM para uso em VMSS do [Marketplace do Azure Stack](azure-stack-download-azure-marketplace-item.md). As imagens já devem estar presentes antes que um usuário pode criar um novo VMSS. 

## <a name="use-the-azure-stack-portal"></a>Usar o portal do Azure Stack 

>[!IMPORTANT]  
> As informações nesta seção se aplicam quando você usa a versão do Azure Stack 1808 ou posterior. Se sua versão for 1807 ou anterior, consulte [adicionar o conjunto de dimensionamento de máquina Virtual (antes do 1808)](#add-the-virtual-machine-scale-set-prior-to-version-1808).

1. Entrar no portal do Azure Stack. Em seguida, vá para **todos os serviços**, em seguida, **conjuntos de dimensionamento de máquina Virtual**e, em **de computação**, selecione **conjuntos de dimensionamento de máquina Virtual**. 
   ![Conjuntos de dimensionamento de máquina de virtual Select](media/azure-stack-compute-add-scalesets/all-services.png)

2. Selecione criar ***conjuntos de dimensionamento de máquina Virtual***.
   ![Criar um conjunto de dimensionamento de máquinas virtuais](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Preencha os campos vazios, escolha uma das listas suspensas para **imagem de disco do sistema operacional**, **assinatura**, e **tamanho da instância**. Selecione **Yes** para **usar discos gerenciados**. Em seguida, clique em **Criar**.
    ![Configurar e criar](media/azure-stack-compute-add-scalesets/create.png)

4. Para ver sua nova escala de máquina virtual definido, acesse **todos os recursos**, procure o nome do conjunto de dimensionamento de máquinas virtuais e, em seguida, selecione seu nome na pesquisa. 
   ![Exibir o conjunto de dimensionamento](media/azure-stack-compute-add-scalesets/search.png)

## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>Adicionar o conjunto de dimensionamento de máquina Virtual (antes da versão 1808)

>[!IMPORTANT]  
> As informações nesta seção se aplicam quando você usa uma versão do Azure Stack antes 1808. Se você usar a versão 1808 ou posterior, consulte [usar o portal do Azure Stack](#use-the-azure-stack-portal).

1. Abra o Azure Stack Marketplace e se conectar ao Azure. Selecione **gerenciamento do Marketplace**, em seguida, clique em **+ adicionar do Azure**.

    ![Gerenciamento do Marketplace](media/azure-stack-compute-add-scalesets/image01.png)

2. Adicionar e baixe o item do marketplace de conjunto de dimensionamento de máquina Virtual.

    ![Conjunto de Escalas da Máquina Virtual](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Atualizar as imagens em um conjunto de dimensionamento de máquina Virtual

Depois de criar um conjunto de dimensionamento de máquina virtual, os usuários podem atualizar imagens no conjunto de dimensionamento sem ter que ser recriados de conjunto de dimensionamento. O processo para atualizar uma imagem depende dos cenários a seguir:

1. Modelo de implantação do conjunto de dimensionamento de máquina virtual especifica **mais recente** para **versão**:  

   Quando o `version` é definido como **mais recente** no `imageReference` seção do modelo para uma escala definida, dimensionar as operações sobre o uso do conjunto de escala a versão mais recente disponível da imagem para instâncias do conjunto de escala. Após a conclusão da escala vertical, você pode excluir instâncias de conjuntos de dimensionamento de máquina virtual mais antigas. Os valores para `publisher`, `offer`, e `sku` permanecem inalterados. 

   O exemplo JSON a seguir especifica `latest`:  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   Antes de escalar verticalmente pode usar uma nova imagem, você deve baixar se a nova imagem:  

   - Quando a imagem no Marketplace é uma versão mais recente que a imagem no conjunto de dimensionamento, baixe a nova imagem que substitui a imagem mais antiga. Depois que a imagem for substituída, um usuário pode prosseguir para escalar verticalmente. 

   - Quando a versão da imagem no Marketplace é o mesmo que a imagem no conjunto de dimensionamento, exclua a imagem que está em uso no conjunto de dimensionamento e, em seguida, baixe a nova imagem. Durante o tempo entre a remoção da imagem original e o download da nova imagem, você não pode escalar verticalmente. 
      
     Esse processo é necessário para resyndicate imagens que tornam usam do formato de arquivo esparso, introduzido na versão 1803. 
 
2. Modelo de implantação de conjunto de dimensionamento de máquinas virtuais **não especificar mais recente** para **versão** e especifica um número de versão em vez disso:  

    Se você baixar uma imagem com uma versão mais recente (que altera a versão disponível), o conjunto de dimensionamento não é possível escalar verticalmente. Isso ocorre por design, como a versão da imagem especificada no modelo de conjunto de escala deve estar disponível.  

Para obter mais informações, consulte [discos do sistema operacional e imagens](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Dimensionar um conjunto de dimensionamento de máquina virtual

Você pode dimensionar o tamanho de um *conjunto de dimensionamento de máquina virtual* para torná-la maior ou menor.  

1. No portal, selecione seu conjunto de dimensionamento e, em seguida, selecione **Scaling**.

2. Use a barra deslizante para definir o novo nível de colocação em escala para esse conjunto de dimensionamento de máquina virtual e, em seguida, clique em **salvar**.
     ![O conjunto de dimensionamento](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="remove-a-virtual-machine-scale-set"></a>Remover um conjunto de dimensionamento de máquina Virtual

Para remover um item da Galeria de conjunto de dimensionamento de máquina Virtual, execute o seguinte comando do PowerShell:

```PowerShell  
Remove-AzsGalleryItem
```

> [!NOTE]
> O item da Galeria não pode ser removido imediatamente. Noturna você precisa atualizar o portal várias vezes antes do item mostra como removida do Marketplace.

## <a name="next-steps"></a>Próximas etapas

[Baixar itens do marketplace do Azure para o Azure Stack](azure-stack-download-azure-marketplace-item.md)