---
title: Azure Blockchain Service suporte para versões do razão, aplicação de patch e atualização
description: Visão geral das versões razões com suporte no serviço de Blockchain do Azure, incluindo as políticas em relação a atualizações gerenciadas pelo usuário e de sistemas para aplicação de patches e gerenciado pelo sistema.
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 63c9a8b9e266dacbb0fb6faba50fb44ac9a4b46e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027879"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versões com suporte de razão do serviço do Azure Blockchain

Azure Blockchain Service usa o Ethereum baseado [Quorum](https://github.com/jpmorganchase/quorum/wiki) razão projetado para o processamento de transações privadas dentro de um grupo de participantes conhecidos, identificado como um consórcio no serviço de Blockchain do Azure.

Atualmente, o serviço de Blockchain do Azure suporta [Quorum versão 2.2.1](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) e [Gerenciador de transações Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades

Controle de versão do Quorum é feito por meio de uma versão principal, versão de ponto principal e uma versão de ponto. Por exemplo, se o Quorum versão 2.0.1, 2 é uma versão principal, 0 é uma versão principal ponto e 1 é a versão de ponto. O serviço automaticamente cria patches para as versões de ponto do razão. Atualmente, não há suporte para a atualização de versões de ponto de principais e principais.

## <a name="availability-of-new-ledger-versions"></a>Disponibilidade de novas versões do razão

Azure Blockchain Service fornece as versões mais recentes do razão dentro de 60 dias de estarem disponíveis do fabricante do razão. Um máximo de quatro versões de ponto principal são fornecidos para consórcios de escolha ao provisionamento de um novo membro e consortium.

## <a name="next-steps"></a>Próximas etapas

[Limites de serviço de Blockchain do Azure](limits.md)
