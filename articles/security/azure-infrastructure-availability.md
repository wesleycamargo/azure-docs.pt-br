---
title: Disponibilidade da infraestrutura do Azure
description: O artigo descreve os níveis de redundância para fornecer a máxima disponibilidade possível para dados dos clientes.
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
ms.openlocfilehash: 18c6b87c9926b93eec26cca4028a38e472912c46
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902570"
---
# <a name="azure-infrastructure-availability"></a>Disponibilidade da infraestrutura do Azure
O Azure fornece uma disponibilidade robusta com base em uma ampla redundância obtida com a tecnologia de virtualização. O Azure fornece vários níveis de redundância para fornecer a máxima disponibilidade possível aos dados dos clientes.

## <a name="temporary-outages-and-natural-disaster"></a>Desastres naturais e interrupções temporárias
A equipe de Operações e a Infraestrutura do Microsoft Cloud projeta, cria, opera e protege a infraestrutura de nuvem. Essa equipe garante que a infraestrutura do Azure esteja sempre fornecendo alta disponibilidade e confiabilidade, alta eficiência, escalabilidade inteligente e uma nuvem segura, privada e confiável.

Fontes de alimentação ininterrupta e grandes bancos de baterias garantem que a eletricidade permaneça contínua caso ocorra uma interrupção de energia de curto prazo.

Geradores de emergência fornecem energia de backup para interrupções prolongadas e manutenção planejada. O datacenter é operado com reservas de combustível locais para o caso de um desastre natural.

Redes de fibra óptica robustas e de alta velocidade conectam os datacenters a outros hubs principais e a usuários da Internet. Nós de computação hospedam cargas de trabalho mais próximas dos usuários finais para reduzir a latência, fornecem redundância geográfica e aumentam a resiliência geral do serviço. Uma equipe de engenheiros trabalha constantemente para garantir que os serviços estejam sempre disponíveis aos clientes.

A Microsoft garante a alta disponibilidade por meio de funcionalidades avançadas de monitoramento e resposta a incidentes, suporte a serviços e failover de backup. Centros de operações da Microsoft distribuídos geograficamente operam 24 horas por dia, 7 dias por semana, 365 dias por ano. A rede do Azure é uma das maiores do mundo. As redes de fibra óptica e de distribuição de conteúdo conectam os datacenters aos nós de borda para garantir o alto desempenho e a confiabilidade.

## <a name="disaster-recovery"></a>Recuperação de desastre
O Azure mantém os dados do cliente duráveis em dois locais com o cliente oferecendo a possibilidade de escolher o local do site de backup. Em ambos os locais, o Azure mantém constantemente várias réplicas íntegras (3) de dados do cliente.

## <a name="database-availability"></a>Disponibilidade do banco de dados
O Azure garante que os bancos de dados estejam acessíveis na Internet por meio de um gateway de Internet com a disponibilidade contínua do banco de dados. O monitoramento avalia a integridade e o estado dos bancos de dados ativos em intervalos de 5 minutos.

## <a name="storage-availability"></a>Disponibilidade de armazenamento
O Azure oferece armazenamento por meio de um serviço de armazenamento altamente escalonável e durável, que fornece pontos de extremidade de conectividade, permitindo que ele esteja acessível diretamente por um aplicativo de consumo. Por meio do serviço de armazenamento, as solicitações de armazenamento de entrada serão processadas de forma eficiente com integridade transacional.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Limites e componentes do sistema de informações do Azure](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Microsoft Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento da infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente no Azure](azure-protection-of-customer-data.md)
