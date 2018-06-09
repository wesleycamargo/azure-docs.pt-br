---
title: O que é o Azure Stack? | Microsoft Docs
description: A pilha do Azure permite que você execute os serviços do Azure em seu data center.
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
ms.date: 06/04/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 848df59b01cc0c03b9a5f3dae2644d469c8651bb
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234879"
---
# <a name="what-is-azure-stack"></a>O que é o Azure Stack?

A pilha do Microsoft Azure é uma plataforma de nuvem híbrida que lhe permite oferecer serviços do Azure em seu data center. Essa plataforma foi projetada para dar suporte a seus requisitos de negócios em constante evolução. A pilha do Azure pode habilitar novos cenários para aplicativos modernos, como a borda e ambientes desconectados, ou atender às necessidades específicas de segurança e conformidade.

A pilha do Azure é oferecida em duas opções de implantação para atender às suas necessidades.

## <a name="azure-stack-integrated-systems"></a>Sistemas integrados do Azure Stack
Pilha do Azure sistemas integrados são oferecidos por meio de uma parceria da Microsoft e [parceiros de hardware](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), criando uma solução que oferece inovação individual de nuvem e computação simplicidade de gerenciamento. Como o Azure pilha é oferecida como um sistema de software e hardware integrado, você tem a flexibilidade e o controle que você precisa, juntamente com a capacidade de inovar da nuvem. Sistemas de pilha integrado do Azure variam em tamanho de 4-12 nós e têm suporte em conjunto de parceiros de hardware e da Microsoft.  Use sistemas de pilha do Azure integrado para criar novos cenários e implantar novas soluções para suas cargas de trabalho de produção.

## <a name="azure-stack-development-kit"></a>Kit de Desenvolvimento do Azure Stack

Microsoft [Kit de desenvolvimento na pilha do Azure (ASDK)](.\asdk\asdk-what-is.md) é uma implantação de nó único da pilha do Azure, que você pode usar para avaliar e saber mais sobre a pilha do Azure.  Você também pode usar ASDK como um ambiente de desenvolvedor para criar aplicativos usando as APIs e as ferramentas que é consistente com o Azure.

>[!Note]
>O ASDK não se destina a ser usado como um ambiente de produção.

O ASDK tem as seguintes limitações:

* ASDK está associado um único do Azure Active Directory (AD do Azure) ou o provedor de identidade de serviços de Federação do Active Directory (AD FS). Você pode criar vários usuários nesse diretório e atribuir assinaturas a cada usuário.
* Como os componentes de pilha do Azure são implantados em um computador host, limitada de recursos físicos estão disponíveis para recursos de locatário. Essa configuração não se destina a avaliação de desempenho ou escala.
* Cenários de rede são limitados devido a host único e requisitos de implantação de NIC.

## <a name="next-steps"></a>Próximas etapas

- [Principais recursos e conceitos](azure-stack-key-features.md)
- [Pilha do Azure: Extensão do Azure (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
