---
title: Usar imagens do cliente do Windows no Azure | Microsoft Docs
description: "Como usar os benefícios da assinatura do Visual Studio para implantar o Windows 7/8/10 no Azure em cenários de desenvolvimento/teste"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/28/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 568bd06d1dfd0e253b960dcf2fb5409a390da91b
ms.openlocfilehash: 2116abf974974c285d94d673b6a614360edbc593


---
# <a name="using-windows-client-in-azure-for-devtest-scenarios"></a>Usando o cliente do Windows no Azure para cenários de desenvolvimento/teste
Você pode usar o Windows 7, Windows 8 ou Windows 10 no Azure para cenários de desenvolvimento + teste, desde que você tenha uma assinatura apropriada do Visual Studio (anteriormente conhecido como MSDN). Este artigo descreve os requisitos de qualificação para execução do cliente do Windows no Azure e uso das imagens da Galeria do Azure.

## <a name="subscription-eligibility"></a>Qualificação de assinatura
Os assinantes ativos do Visual Studio (pessoas que tem adquiriram uma licença de assinatura do Visual Studio) podem usar o cliente do Windows para fins de teste e desenvolvimento. O cliente do Windows pode ser usado em seu próprio hardware e nas máquinas virtuais do Azure em execução em qualquer tipo de assinatura do Azure. O cliente do Windows não pode ser implantado nem usado no Azure para uso em produção normal ou usado por pessoas que não são assinantes ativos do Visual Studio.

Para sua conveniência, disponibilizamos algumas imagens do Windows 10 da Galeria do Azure em [ofertas de desenvolvimento/teste qualificadas](#eligible-offers). Os assinantes do Visual Studio em qualquer tipo de oferta também podem [preparar e criar de forma adequada](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) uma imagem de 64 bits do Windows 7, Windows 8 ou Windows 10 e [carregá-la no Azure](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). O uso permanece limitado ao desenvolvimento/teste por assinantes ativos do Visual Studio.

## <a name="eligible-offers"></a>Ofertas qualificadas
A tabela a seguir fornece detalhes sobre as IDs de oferta que são qualificadas para implantar o Windows 10 por meio da Galeria do Azure. As imagens do Windows 10 são visíveis apenas para as ofertas a seguir. Os assinantes do Visual Studio que precisam executar o cliente do Windows em um tipo diferente de oferta exigem a [preparação e criação adequadas](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) de uma imagem de 64 bits do Windows 7, Windows 8 ou Windows 10 e seu [carregamento no Azure](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Nome da oferta | Número da oferta | Imagens de cliente disponíveis |
|:--- |:---:|:---:|
| [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Assinantes do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Assinantes do Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Assinantes do Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium com MSDN (benefício)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Assinantes do Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Assinantes do Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/ofers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Verificar sua assinatura do Azure
Se você não souber sua ID de oferta, poderá obtê-lo por meio do portal do Azure ou do portal de Contas.

A ID de oferta de assinatura está anotada na folha “Assinaturas” no portal do Azure:

![Detalhes da ID de oferta no portal do Azure](./media/virtual-machines-windows-client-images/offer_id_azure_portal.png) 

Você também pode exibir a ID de oferta na [guia “Assinaturas”](http://account.windowsazure.com/Subscriptions) do portal de Contas do Azure:

![Detalhes da ID de oferta no portal de Contas do Azure](./media/virtual-machines-windows-client-images/offer_id_azure_account_portal.png) 

## <a name="next-steps"></a>Próximas etapas
Agora você pode implantar suas VMs usando o [PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), os [modelos do Resource Manager](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou o [Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).




<!--HONumber=Nov16_HO5-->


