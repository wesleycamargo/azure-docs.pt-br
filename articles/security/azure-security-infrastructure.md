---
title: Segurança da infraestrutura do Azure | Microsoft Docs
description: O artigo descreve como a Microsoft garante a segurança dos datacenters do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 397bd1f904b676a6ba020ec78fb1cad05c460be1
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903849"
---
# <a name="azure-infrastructure-security"></a>Segurança da infraestrutura do Azure
O Microsoft Azure é executado em datacenters gerenciados e operados pela Microsoft. Esses datacenters geograficamente dispersos estão em conformidade com os principais padrões do setor, como ISO/IEC 27001:2013 e o NIST SP 800-53, em relação a segurança e confiabilidade. Os datacenters são gerenciados, monitorados e administrados pela equipe de operações da Microsoft. A equipe de operações tem anos de experiência no fornecimento dos maiores serviços online do mundo, com continuidade 24 horas por dia, 7 dias por semana.

Esta série de artigos fornece informações sobre o que a Microsoft faz para proteger a infraestrutura do Azure. O artigos aborda:

- [Segurança física](azure-physical-security.md)
- [Disponibilidade](azure-infrastructure-availability.md)
- [Componentes e limites](azure-infrastructure-components.md)
- [Arquitetura de rede](azure-infrastructure-network.md)
- [Rede de produção](azure-production-network.md)
- [Banco de Dados SQL](azure-infrastructure-sql.md)
- [Operações](azure-infrastructure-operations.md)
- [Monitoramento](azure-infrastructure-monitoring.md)
- [Integridade](azure-infrastructure-integrity.md)
- [Proteção de dados](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>Modelo de responsabilidade compartilhada
É importante entender a divisão da responsabilidade entre você e a Microsoft. No local, você possui a pilha inteira, mas conforme muda para a nuvem, algumas responsabilidades são transferidas para a Microsoft. A matriz de responsabilidades a seguir mostra as áreas da pilha em uma implantação de SaaS (software como serviço), de PaaS (plataforma como serviço) e de IaaS (infraestrutura como serviço) pelas quais você é responsável e pelas quais a Microsoft é responsável.

![Responsabilidade compartilhada][1]

As responsabilidades são sempre retidas por você, independentemente do tipo de implantação, são:

- Dados
- Pontos de extremidade
- Conta
- gerenciamento de acesso

Tenha certeza de que você entende a divisão da responsabilidade entre você e a Microsoft em uma implantação de SaaS, PaaS e IaaS. Confira [Responsabilidades compartilhadas para computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Limites e componentes do sistema de informações do Azure](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Microsoft Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento da infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente no Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
