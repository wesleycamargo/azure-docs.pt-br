---
title: "Gerenciar atualizações na visão geral de pilha do Azure | Microsoft Docs"
description: "Saiba mais sobre o gerenciamento de atualizações para os sistemas de pilha do Azure integradas."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 3d0d5ea6cc3f3cc7bc0550b83dabbf0ae6af8a27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-updates-in-azure-stack-overview"></a>Gerenciar atualizações na visão geral de pilha do Azure

*Aplica-se a: sistemas integrados de pilha do Azure*

A Microsoft lançará pacotes de atualização para sistemas de pilha do Azure integradas com regularidade. Cada versão das atualizações de software da Microsoft é fornecido como um pacote de atualização. Como um operador de pilha do Azure, você pode importar facilmente, instalar e monitorar o progresso da instalação desses atualizar pacotes do portal do administrador. 

O fornecedor do hardware fabricante (OEM) também irá liberar atualizações, como atualizações de firmware e driver. Essas atualizações são entregues como pacotes separados pelo seu fornecedor de hardware de OEM e são gerenciadas separadamente das atualizações da Microsoft.

Para manter o sistema em suporte, você deve manter a pilha Azure atualizado para uma versão específica. Certifique-se de que você examine o [pilha Azure manutenção política](azure-stack-servicing-policy.md).

> [!NOTE]
> Você não pode aplicar os pacotes de atualização de pilha do Azure para o Kit de desenvolvimento de pilha do Azure. Os pacotes de atualização são projetados para sistemas integrados.

## <a name="the-update-resource-provider"></a>O provedor de recursos de atualização

A pilha do Azure inclui um provedor de recursos de atualização que coordena a aplicação das atualizações de software da Microsoft. Este provedor de recursos garante que as atualizações são aplicadas em todos os hosts físicos, os aplicativos do Service Fabric e tempos de execução e todas as máquinas virtuais de infraestrutura e seus serviços associados.

Como instalar atualizações, você pode facilmente exibir status de alto nível como os destinos do processo de atualização os vários subsistemas na pilha do Azure (por exemplo, hosts físicos e máquinas virtuais de infraestrutura).

## <a name="plan-for-updates"></a>Planejar atualizações de

É altamente recomendável que você notificar os usuários de qualquer operação de manutenção e agendar as janelas de manutenção normal durante o horário comercial tanto quanto possível. As operações de manutenção podem afetar as operações de portal e cargas de trabalho de locatário.

## <a name="using-the-update-tile-to-manage-updates"></a>Usando o bloco de atualização para gerenciar atualizações
Gerenciamento de atualizações do portal do administrador é um processo simple. Um operador de pilha do Azure pode navegar para o bloco de atualização no painel de controle para:

- Exibir informações importantes, como a versão atual.
- instalar atualizações e monitorar o progresso.
- Examine o histórico de atualização de atualizações instaladas anteriormente.
 
## <a name="determine-the-current-version"></a>Determinar a versão atual

O bloco de atualização mostra a versão atual da pilha do Azure. Você pode obter o bloco de atualização usando um dos métodos a seguir no portal do administrador:

- No painel, exibir a versão atual no **atualização** lado a lado.
 
   ![Atualizações de bloco no painel de controle padrão](./media/azure-stack-updates/image1.png)
 
- Sobre o **gerenciamento região** lado a lado, clique no nome da região. Exibir a versão atual no **atualização** lado a lado.

## <a name="next-steps"></a>Próximas etapas

- [Política de manutenção de pilha do Azure](azure-stack-servicing-policy.md) 
- [Gerenciamento de região na pilha do Azure](azure-stack-region-management.md)     


