---
title: Conjuntos de escala de máquinas virtuais de tornar disponível na pilha do Azure | Microsoft Docs
description: Saiba como um operador de nuvem pode adicionar escala de máquinas virtuais para a pilha do Azure Marketplace
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: kivenkat
ms.openlocfilehash: 12425ab53ca16bb985a0a8658b5058998565b01a
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Disponibilizar conjuntos de escala de máquina virtual na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Conjuntos de escala de máquinas virtuais são um recurso de computação da pilha do Azure. Você pode usá-los para implantar e gerenciar um conjunto de máquinas virtuais idênticas. Com todas as máquinas virtuais configurados da mesma, conjuntos de escala não exigem pré-provisionamento de máquinas virtuais. É mais fácil de criar serviços em grande escala que se destinam a computação intensa, dados grandes e cargas de trabalho em contêineres.

Este artigo o orienta durante o processo de disponibilizar conjuntos de escala na pilha do Azure Marketplace. Depois de concluir este procedimento, seus usuários podem adicionar define a escala de máquina virtual para suas assinaturas.

Conjuntos de escala de máquina virtual na pilha do Azure são como conjuntos de escala de máquinas virtuais no Azure. Para obter mais informações, consulte os seguintes vídeos:
* [Mark Russinovich fala sobre os conjuntos de dimensionamento do Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Conjuntos de Dimensionamento de Máquina Virtual com Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Na pilha do Azure, os conjuntos de escala de máquinas virtuais não oferecem suporte a dimensionamento automático. Você pode adicionar mais instâncias em uma escala definida usando o portal de pilha do Azure, o Gerenciador de recursos de modelos ou o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos
* **PowerShell e ferramentas**

   Instalar e PowerShell configurado para a pilha do Azure e as ferramentas de pilha do Azure. Consulte [colocar em funcionamento com o PowerShell no Azure pilha](azure-stack-powershell-configure-quickstart.md).

   Depois de instalar as ferramentas de pilha do Azure, certifique-se de importar o módulo do PowerShell a seguir (caminho relativo para o. pasta \ComputeAdmin AzureStack-ferramentas-master):
  ````PowerShell
        Import-Module .\AzureStack.ComputeAdmin.psm1
  ````

* **Imagem do sistema operacional**

   Se você não adicionou uma imagem do sistema operacional para a pilha do Azure Marketplace, consulte [adicionar a imagem de VM do Windows Server 2016 Marketplace do Azure pilha](azure-stack-add-default-image.md).

   Para obter suporte do Linux, baixe Ubuntu Server 16.04 e adicioná-lo usando ```Add-AzsPlatformImage``` com os seguintes parâmetros: ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```.


## <a name="add-the-virtual-machine-scale-set"></a>Adicionar o conjunto de escala de máquina virtual

Edite o seguinte script do PowerShell para o seu ambiente e, em seguida, execute-o para adicionar uma escala de máquina virtual definida como a pilha do Azure Marketplace. 

``$User`` é a conta usada para conectar-se o portal do administrador. Por exemplo, serviceadmin@contoso.onmicrosoft.com.

````PowerShell  
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
````

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Atualizar as imagens em um conjunto de escala de máquinas virtuais 
Depois de criar um conjunto de escala de máquina virtual, os usuários podem atualizar imagens em escala definida sem que a escala definida precisar ser recriado. O processo para atualizar uma imagem depende dos seguintes cenários:

1. Conjunto de escala de máquina virtual de modelo de implantação **especifica mais recente** para *versão*:  

   Quando o *versão* é definido como **mais recente** no *imageReference* seção do modelo para uma escala de conjunto, dimensionar as operações sobre o uso do conjunto de escala a versão disponível mais recente da imagem para a escala definida instâncias. Depois de uma escala de backup for concluída, você pode excluir instâncias de conjuntos de escala de máquina virtual mais antigas.  (Os valores para *publicador*, *oferecem*, e *sku* permanecem inalterados). 

   A seguir está um exemplo da especificação *mais recente*:  

    ```Json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   Antes de usa uma nova imagem de expansão, você deve baixar essa nova imagem:  

   - Quando a imagem no Marketplace é uma versão mais recente que a imagem no conjunto de escala: baixar a nova imagem que substitui a imagem anterior. Depois que a imagem for substituída, um usuário poderá aumentar. 

   - Quando a versão da imagem no Marketplace é o mesmo que a imagem no conjunto de escala: excluir a imagem que está em uso no conjunto de escala e, em seguida, baixe a nova imagem. Durante o tempo entre a remoção da imagem original e o download da nova imagem, você não pode escalar verticalmente. 
      
     Esse processo é necessário para resyndicate imagens que tornam usam o formato de arquivo esparso, introduzido com a versão 1803. 
 

2. Conjunto de escala de máquina virtual de modelo de implantação **não especifique mais recente** para *versão* e especifica um número de versão em vez disso:  

     Se você baixar uma imagem com uma versão mais recente (que altera a versão disponível), o conjunto de escala não pode escalar verticalmente. Isso ocorre por design, como a versão da imagem especificada no modelo do conjunto de escala deve estar disponível.  

Para obter mais informações, consulte [discos do sistema operacional e imagens](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images).  


## <a name="remove-a-virtual-machine-scale-set"></a>Remover um conjunto de escala de máquinas virtuais

Para remover uma máquina virtual dimensionar o conjunto de item da galeria, execute o seguinte comando do PowerShell:

```PowerShell  
    Remove-AzsVMSSGalleryItem
````

> [!NOTE]
> O item da Galeria não pode ser removido imediatamente. Noturna você precisa atualizar o portal várias vezes antes do item mostra como removida do Marketplace.

## <a name="next-steps"></a>Próximas etapas
[Perguntas frequentes para a pilha do Azure](azure-stack-faq.md)