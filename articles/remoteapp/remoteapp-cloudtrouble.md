---
title: "Solucionar problemas de coleções na nuvem do RemoteApp – criação | Microsoft Docs"
description: "Saiba como solucionar problemas de falhas de criação de coleção de nuvem do RemoteApp"
services: remoteapp
documentationcenter: 
author: vkbucha
manager: mbaldwin
ms.assetid: 95eb7797-7b5e-4781-ad23-f15dd85b213d
ms.service: remoteapp
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 304ba7c5057b27c459bccbb75d3a711567757675
ms.lasthandoff: 03/31/2017


---
# <a name="troubleshoot-creating-remoteapp-cloud-collections"></a>Solucionar problemas na criação de coleções em nuvem do RemoteApp
> [!IMPORTANT]
> O Azure RemoteApp será descontinuado até 31 de agosto de 2017. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Se você estiver tendo problemas ao criar uma coleção de nuvem, confira as informações a seguir.

## <a name="your-image-is-invalid"></a>A imagem é inválida
Se você receber uma mensagem como “GoldImageInvalid” quando estiver aguardando enquanto o Azure provisiona sua coleção, isso significa que a sua imagem de modelo não atende aos [requisitos de imagem definidos](remoteapp-imagereqs.md). Portanto, leia esses [requisitos](remoteapp-imagereqs.md), corrija sua imagem e tente criar novamente sua coleção.

## <a name="common-errors-seen-in-the-azure-management-portal"></a>Erros comuns vistos no portal de Gerenciamento do Azure
    DNS server could not be reached
    ProvisioningTimeout

Coleções de nuvem geralmente falham durante a criação por você estar usando imagens personalizadas.  Se você receber um dos erros anteriores e estiver usando uma imagem personalizada para criar a coleção, verifique os seguintes itens:

* Certifique-se de que a imagem personalizada carregada atende aos requisitos de imagem.
* Geralmente, o problema comum é que a imagem não foi corretamente preparada para o sistema.  
* Verifique se a imagem pode inicializar no Hyper-V ou tente criar uma VM IAAS diretamente na assinatura do Azure usando a imagem. Se a VM falhar ao inicializar e não iniciar, isso geralmente indica que a imagem personalizada não estava preparada corretamente.  Verifique se a imagem personalizada foi criada seguindo as instruções em Como criar uma imagem de modelo personalizado para o RemoteApp

Se você estiver usando uma das imagens Microsoft incluídas em sua assinatura, tente criar novamente a coleção. Se o problema persistir, contate o suporte da Microsoft.

    PlatformImageTrialModeOnly

Se você receber esse erro, isso geralmente significa que você atualizou para uma conta paga, mas está tentando usar uma imagem fornecido pela Microsoft que é válida somente durante o modo de avaliação do serviço. Nesse caso, tente criar novamente sua coleção de nuvem, mas não se esqueça de especificar a imagem correta.


