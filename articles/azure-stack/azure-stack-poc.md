---
title: O que é o Azure Stack? | Microsoft Docs
description: A pilha do Azure permite executar serviços do Azure em seu data center.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 863c1ec562cd71af0df69ccc0547e16d02c7ee82
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="what-is-azure-stack"></a>O que é o Azure Stack?

A pilha do Microsoft Azure é uma plataforma de nuvem híbrida que lhe permite oferecer serviços do Azure do datacenter de sua organização.  A pilha do Azure foi projetada para permitir novos cenários para os aplicativos modernos em cenários mais importantes, como requisitos específicos de segurança e conformidade reunião e ambientes desconectados ou borda.  A pilha do Azure é oferecida em duas opções de implantação para atender às suas necessidades.

## <a name="azure-stack-integrated-systems"></a>Sistemas integrados do Azure Stack
Pilha do Azure sistemas integrados são oferecidos por meio de uma parceria da Microsoft e [parceiros de hardware](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), criando uma solução que oferece inovação individual nuvem balanceada com simplicidade no gerenciamento.  Porque a pilha do Azure é oferecida como um sistema integrado de hardware e software, você terá a quantidade certa de flexibilidade e controle, enquanto ainda adotando inovação da nuvem.  Sistemas de pilha integrado do Azure variam em tamanho de 4-12 nós e têm suporte em conjunto de parceiros de hardware e da Microsoft.  Use sistemas de pilha do Azure integradas para ativar novos cenários para suas cargas de trabalho de produção.    

## <a name="azure-stack-development-kit"></a>Kit de Desenvolvimento do Azure Stack
Microsoft [Kit de desenvolvimento na pilha do Azure (ASDK)](.\asdk\asdk-what-is.md) é uma implantação de nó único da pilha do Azure, que você pode usar para avaliar e saber mais sobre a pilha do Azure.  Você também pode usar o ASDK como um ambiente de desenvolvedor, onde você pode desenvolver usando APIs e ferramentas consistentes com o Azure. O ASDK não se destina a ser usado como um ambiente de produção.

O ASDK tem as seguintes limitações:
* ASDK está associado um único do Azure Active Directory (AD do Azure) ou o provedor de identidade de serviços de Federação do Active Directory (AD FS). Você pode criar vários usuários nesse diretório e atribuir assinaturas a cada usuário.
* Com todos os componentes implantados em um único computador, há recursos físicos limitados disponíveis para os recursos do locatário. Essa configuração não se destina a avaliação de desempenho ou escala.
* Os cenários de rede são limitados devido ao requisito de um único host/NIC.  

## <a name="next-steps"></a>Próximas etapas
[Principais recursos e conceitos](azure-stack-key-features.md)

[Pilha do Azure: Extensão do Azure (pdf)](https://azure.microsoft.com/en-us/resources/azure-stack-an-extension-of-azure/)

