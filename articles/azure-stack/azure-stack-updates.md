---
title: Gerenciar atualizações na visão geral do Azure Stack | Microsoft Docs
description: Saiba mais sobre o gerenciamento de atualizações para sistemas integrados do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.openlocfilehash: 5b0dbf5ad78ff345d386024ff873618a781917ac
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579030"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Gerenciar atualizações na visão geral do Azure Stack

*Aplica-se a: sistemas integrados do Azure Stack*

Pacotes de atualização da Microsoft para o Azure Stack integrado sistemas geralmente liberar em torno da quarta terça-feira de cada mês. Pedir ao OEM sobre seu processo de notificação específica para garantir que as notificações de atualização atingir a sua organização. Você também pode verificar na biblioteca de documentação sob **visão geral** > **notas de versão** para obter informações sobre as versões que estão em suporte do Active Directory. 

Cada versão das atualizações de software da Microsoft é fornecido como um pacote de atualização única. Como um operador do Azure Stack, você pode importar, instalar e monitorar o progresso da instalação desses atualizar pacotes do portal do administrador. 

O fornecedor de hardware do fabricante original do equipamento (OEM) também será liberar atualizações, como atualizações de firmware e driver. Embora essas atualizações são entregues como pacotes separados pelo seu fornecedor de hardware de OEM, eles são importados, instalado e gerenciado os mesmos pacotes de atualização de maneira da Microsoft, pacotes de atualização são importados, instalados e gerenciados.

Para manter seu sistema de suporte, você deve manter atualizado para um nível de versão específica do Azure Stack. Certifique-se de que você examine os [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).

> [!NOTE]
> É possível aplicar os pacotes de atualização de pilha do Azure para o Kit de desenvolvimento do Azure Stack. Os pacotes de atualização são projetados para sistemas integrados. Para obter informações, consulte [reimplantar o ASDK](https://docs.microsoft.com/azure/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>O provedor de recursos de atualização

O Azure Stack inclui um provedor de recursos de atualização que orquestra a aplicação das atualizações de software da Microsoft. Este provedor de recursos garante que as atualizações são aplicadas a todos os hosts físicos, aplicativos do Service Fabric e tempos de execução e todas as máquinas virtuais de infraestrutura e seus serviços associados.

Como instalar atualizações, você pode exibir o status de alto nível como os destinos de processo de atualização de vários subsistemas no Azure Stack (por exemplo, hosts físicos e máquinas virtuais de infraestrutura).

## <a name="plan-for-updates"></a>Planejar atualizações de

É altamente recomendável que você notifica os usuários de qualquer operação de manutenção, e que você agende as janelas de manutenção normal durante o horário não comercial se possível. Operações de manutenção podem afetar a cargas de trabalho de locatário e operações do portal.

## <a name="using-the-update-tile-to-manage-updates"></a>Usando o bloco de atualização para gerenciar atualizações
Gerenciar atualizações do portal do administrador. Como um operador do Azure Stack, você pode usar o bloco de atualização no painel:

- Exibir informações importantes, como a versão atual.
- instalar atualizações e monitorar o progresso.
- Examine o histórico de atualização para atualizações instaladas anteriormente.
 
## <a name="determine-the-current-version"></a>Determinar a versão atual

O bloco de atualização mostra a versão atual do Azure Stack. Você pode obter o bloco de atualização usando qualquer um dos métodos a seguir no portal do administrador:

- No painel, exibir a versão atual na **atualização** lado a lado.
 
   ![Atualizações de bloco no painel de controle padrão](./media/azure-stack-updates/image1.png)
 
- Sobre o **gerenciamento de região** lado a lado, clique no nome da região. Exibir a versão atual na **atualização** lado a lado.

## <a name="next-steps"></a>Próximas etapas

- [Política de manutenção de pilha do Azure](azure-stack-servicing-policy.md) 
- [Gerenciamento de região no Azure Stack](azure-stack-region-management.md)     


