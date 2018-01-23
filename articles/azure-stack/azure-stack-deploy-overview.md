---
title: Avaliar o Kit de desenvolvimento de pilha do Azure | Microsoft Docs
description: "Saiba como implantar o Kit de desenvolvimento de pilha do Azure para fins de avaliação."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 4ad2e0a91e2fd5023417722fc0a1a6fae93960d0
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Início rápido: Avaliar o Kit de desenvolvimento de pilha do Azure

*Aplica-se a: Kit de desenvolvimento de pilha do Azure*

O [Kit de desenvolvimento de pilha do Azure](azure-stack-poc.md) é um ambiente de teste e desenvolvimento que pode ser implantado para avaliar e demonstrar os recursos de pilha do Azure e serviços. Para colocá-lo em funcionamento, você precisará preparar o ambiente hardware e executar alguns scripts (Isso levará várias horas). Depois disso, você pode entrar portais de administrador e usuário para gerenciar a pilha do Azure e testar ofertas. 

1. [**Planejar o hardware, software e rede**](azure-stack-deploy.md). O computador que hospeda o kit de desenvolvimento (o host do kit de desenvolvimento) deve atender aos requisitos de rede, software e hardware. Você também deve escolher entre usar o Active Directory do Azure ou serviços de Federação do Active Directory. Certifique-se de estar em conformidade com esses pré-requisitos antes de iniciar a implantação para que o processo de instalação seja executado sem problemas. 

2. [**Baixe e extraia o pacote de implantação**](azure-stack-run-powershell-script.md#download-and-extract-the-development-kit). Você pode baixar o pacote de implantação para o host do kit de desenvolvimento ou para um outro computador. Os arquivos extraídos implantação levarem até 60 GB de espaço livre em disco, por isso usando outro computador pode ajudar a reduzir os requisitos de hardware para o host do kit de desenvolvimento.

3. [**Preparar o host do kit de desenvolvimento** ](azure-stack-run-powershell-script.md) usando o instalador. Após essa etapa, o host do kit de desenvolvimento será inicializado a Cloudbuilder.vhdx (arquivos de instalação de um disco rígido virtual que inclui um sistema operacional inicializável e a pilha do Azure).

4. [**Implantar o kit de desenvolvimento** ](azure-stack-run-powershell-script.md) no host do kit de desenvolvimento.

5. Se sua implantação de pilha do Azure usa o Active Directory do Azure, você deve [registrar pilha do Azure com o Azure](azure-stack-register.md) para que você possa [baixar itens do marketplace do Azure](azure-stack-download-azure-marketplace-item.md) a pilha do Azure.

Depois de concluir essas etapas, você terá um ambiente do kit de desenvolvimento com portais de administrador e usuário. Em seguida, você pode [conecte-se e entrar](azure-stack-connect-azure-stack.md) no portal. Você pode iniciar a implantação de provedores de recursos, criar [oferece](azure-stack-key-features.md#regions-services-plans-offers-and-subscriptions)e preenchendo a pilha do Azure [marketplace](azure-stack-marketplace.md).
