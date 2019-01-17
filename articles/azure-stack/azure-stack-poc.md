---
title: O que é o Azure Stack? | Microsoft Docs
description: O Azure Stack permite que você execute os serviços do Azure em seu data center.
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
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 530bb7b164ec7d7b31e6d4a58bca97aa17dc62fa
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54357871"
---
# <a name="what-is-azure-stack"></a>O que é o Azure Stack?

O Microsoft Azure Stack é uma plataforma desktop em nuvem híbrida que possibilita fornecer serviços do Azure no seu datacenter. Essa plataforma foi projetada para dar suporte a seus requisitos de negócios em constante evolução. O Azure Stack pode habilitar novos cenários para seus aplicativos modernos, como ambientes de borda e desconectados, ou corresponder a requisitos específicos de segurança e conformidade.

O Azure Stack é oferecido em duas opções de implantação para atender às suas necessidades.


## <a name="azure-stack-development-kit"></a>Kit de Desenvolvimento do Azure Stack

O [Kit de Desenvolvimento do Azure Stack (ASDK)](./asdk/asdk-what-is.md) da Microsoft é uma implantação de nó único do Azure Stack que pode usar para avaliar e saber sobre o Azure Stack.  Também pode usar o ASDK como um ambiente de desenvolvimento para criar aplicativos usando as APIs e ferramentas em consistência com o Azure.

>[!Note]
>O ASDK não se destina a ser usado como um ambiente de produção.

O ASDK tem as seguintes limitações:

* O ASDK está associado a um único provedor de identidade do Azure Active Directory (Azure AD) ou dos  Serviços de Federação do Active Directory (AD FS). Você pode criar vários usuários nesse diretório e atribuir assinaturas a cada usuário.
* Por os componentes do Azure Stack estarem implantados num computador anfitrião, há recursos físicos limitados disponíveis para os recursos do locatário. Essa configuração não se destina à avaliação de desempenho ou de escala.
* Os cenários de redes estão limitados devido a requisitos de implantação NIC e de anfitrião único.

## <a name="azure-stack-integrated-systems"></a>Sistemas integrados do Azure Stack
Os sistemas integrados do Azure Stack são oferecidos por meio de uma parceria da Microsoft e [parceiros de hardware](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), criando uma solução que oferece inovação ao ritmo da nuvem e simplicidade no gerenciamento de computação. Como o Azure Stack é oferecido como um sistema integrado de hardware e software, tem a flexibilidade e controle de que precisa, bem como a capacidade de inovar a partir da nuvem. Sistemas de pilha integrado do Azure variam de tamanho de 4 a 16 nós e têm suporte em conjunto pela Microsoft e parceiros de hardware.  Use os sistemas integrados do Azure Stack para criar novos cenários e implantar novas soluções para suas cargas de trabalho de produção.

## <a name="next-steps"></a>Próximas etapas

[Principais recursos e conceitos](azure-stack-key-features.md)

[Azure Stack: Uma extensão do Azure (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
