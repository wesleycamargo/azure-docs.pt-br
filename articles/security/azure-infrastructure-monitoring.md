---
title: Monitoramento da infraestrutura do Azure
description: Este artigo discute o monitoramento da rede de produção do Azure.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 17e7183ff56725462dc43cba21db418a86d86b51
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101276"
---
# <a name="monitoring-of-azure-infrastructure"></a>Monitoramento da infraestrutura do Azure   

## <a name="configuration-and-change-management"></a>Configuração e gerenciamento de alterações
O Microsoft Azure faz revisões e atualizações de definições de configuração e configurações de linha de base de dispositivos de hardware, software e rede anualmente. As alterações são desenvolvidas, testadas e aprovadas antes de entrar no ambiente de produção de um ambiente de desenvolvimento e/ou teste.

As configurações de linha de base necessárias para os serviços baseados no Azure são revisadas pela equipe de Conformidade e Segurança do Azure e pelas equipes de serviço. Uma revisão da equipe de serviço é parte do teste antes da implantação de seu serviço de produção.

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades
O gerenciamento de atualizações de segurança ajuda a proteger os sistemas de vulnerabilidades conhecidas. O Azure usa sistemas de implantação integrados para gerenciar a distribuição e a instalação de atualizações de segurança para software da Microsoft. O Azure também pode se basear nos recursos do Microsoft Security Response Center (MSRC). O MSRC identifica, monitora, resolve e responde a incidentes de segurança e vulnerabilidades de nuvem o tempo todo, todos os dias do ano.

## <a name="vulnerability-scanning"></a>Verificação de vulnerabilidade
A verificação de vulnerabilidade é executada em sistemas operacionais de servidor, bancos de dados e dispositivos de rede. As verificações de vulnerabilidade são executadas, no mínimo, a cada trimestre. Contratos do Microsoft Azure com avaliadores independentes para executar o teste de penetração dos limites do Microsoft Azure. Os exercícios de Red-Team também são executados rotineiramente e os resultados são usados para fazer aperfeiçoamentos de segurança.

## <a name="protective-monitoring"></a>Monitoramento de proteção
A segurança do Microsoft Azure definiu os requisitos para monitoramento ativo. As equipes de serviço configuram ferramentas de monitoramento ativo de acordo com esses requisitos. Ferramentas de monitoramento ativo incluem o Agente de Monitoramento (MA) e o System Center Operations Manager. Essas ferramentas são configuradas para fornecer alertas de tempo à equipe de Segurança do Microsoft Azure em situações que requerem ação imediata.

## <a name="incident-management"></a>Gerenciamento de incidentes
A Microsoft implementa um processo de gerenciamento de incidentes de segurança para facilitar uma resposta coordenada a incidentes caso eles ocorram.

Se a Microsoft souber de acesso não autorizado aos dados armazenados em seu equipamento ou em suas instalações, ou acesso não autorizado a tais equipamentos ou instalações resultando em perda, divulgação ou alteração de dados do cliente, a Microsoft tomará as seguintes ações:

- Notificará imediatamente o cliente sobre o incidente de segurança
- Investigará o incidente de segurança imediatamente e fornecerá ao cliente informações detalhadas sobre o ocorrido
- Tomará medidas razoáveis e imediatas para reduzir os efeitos e minimizar algum dano resultante do incidente de segurança.

Foi estabelecida uma estrutura de gerenciamento de incidentes com funções definidas e responsabilidades alocadas. A equipe do WASIM (Gerenciamento de Incidentes de Segurança do Microsoft Azure) é responsável por gerenciar incidentes de segurança, incluindo o escalonamento e garantindo o envolvimento das equipes de especialistas quando necessário. Os gerentes de operações do Azure são responsáveis por supervisionar a investigação e a resolução de incidentes de segurança e privacidade.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Microsoft Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento da integridade do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente no Azure](azure-protection-of-customer-data.md)
