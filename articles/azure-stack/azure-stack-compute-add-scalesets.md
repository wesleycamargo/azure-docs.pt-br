---
title: "Disponibilizar conjuntos de escala de máquina virtual na pilha do Azure"
description: "Saiba como um administrador de nuvem pode adicionar escala de máquinas virtuais para a pilha do Azure Marketplace"
services: azure-stack
author: anjayajodha
ms.service: azure-stack
ms.topic: article
ms.date: 9/25/2017
ms.author: anajod
keywords: 
ms.openlocfilehash: 31aeb963bdf4fd32712bc6f29f64060ec1c77cb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Disponibilizar conjuntos de escala de máquina virtual na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Conjuntos de escala de máquinas virtuais são um recurso de computação da pilha do Azure. Você pode usá-los para implantar e gerenciar um conjunto de máquinas virtuais idênticas. Com todas as máquinas virtuais configurados da mesma, conjuntos de escala não exigem pré-provisionamento de máquinas virtuais. É mais fácil de criar serviços em grande escala que se destinam a computação intensa, dados grandes e cargas de trabalho em contêineres.

Este tópico o orienta durante o processo de disponibilizar conjuntos de escala na pilha do Azure Marketplace. Depois de concluir este procedimento, seus usuários podem adicionar define a escala de máquina virtual para suas assinaturas.

Conjuntos de escala de máquina virtual na pilha do Azure são como conjuntos de escala de máquinas virtuais no Azure. Para obter mais informações, consulte os seguintes vídeos:
* [Mark Russinovich fala sobre os conjuntos de dimensionamento do Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Conjuntos de Dimensionamento de Máquina Virtual com Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Na pilha do Azure, conjuntos de escala de máquina Virtual não dão suporte a dimensionamento automático. Você pode adicionar mais instâncias em uma escala definida usando o portal de pilha do Azure, o Gerenciador de recursos de modelos ou o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos
* **PowerShell e ferramentas**

   Instalar e PowerShell configurado para a pilha do Azure e as ferramentas de pilha do Azure. Consulte [colocar em funcionamento com o PowerShell no Azure pilha](azure-stack-powershell-configure-quickstart.md).

   Depois de instalar as ferramentas de pilha do Azure, certifique-se de importar o módulo do PowerShell a seguir (caminho relativo para o. pasta \ComputeAdmin AzureStack-ferramentas-master):

        Import-Module .\AzureStack.ComputeAdmin.psm1

* **Imagem do sistema operacional**

   Se você não adicionou uma imagem do sistema operacional para a pilha do Azure Marketplace, consulte [adicionar a imagem de VM do Windows Server 2016 Marketplace do Azure pilha](azure-stack-add-default-image.md).

   Para obter suporte do Linux, baixe Ubuntu Server 16.04 e adicioná-lo usando ```Add-AzsVMImage``` com os seguintes parâmetros: ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```.

## <a name="add-the-virtual-machine-scale-set"></a>Adicionar o conjunto de escala de máquina virtual

Edite o seguinte script do PowerShell para o seu ambiente e, em seguida, execute-o para adicionar uma escala de máquina virtual definida como a pilha do Azure Marketplace. 

``$User``é a conta usada para conectar-se o portal do administrador. Por exemplo: serviceadmin@contoso.onmicrosoft.com.

```
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds
Select-AzureRmProfile -Profile $AzsEnvContext

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="remove-a-virtual-machine-scale-set"></a>Remover um conjunto de escala de máquinas virtuais

Para remover uma máquina virtual dimensionar o conjunto de item da galeria, execute o seguinte comando do PowerShell:

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> O item da Galeria não pode ser removido imediatamente. Talvez seja necessário atualizar o portal várias vezes antes de ser removido do Marketplace.


## <a name="next-steps"></a>Próximas etapas
[Perguntas frequentes para a pilha do Azure](azure-stack-faq.md)

