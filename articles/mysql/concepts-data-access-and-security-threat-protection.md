---
title: Proteção Avançada contra Ameaças – Banco de Dados do Azure para MySQL | Microsoft Docs
description: A Proteção Avançada contra Ameaças detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados.
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 09/20/2018
ms.openlocfilehash: 739e1637eaeef86028848e5b8c8a453fcbc6195e
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543777"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Proteção Avançada contra Ameaças do Banco de Dados do Azure para MySQL

A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

A Proteção Avançada contra Ameaças faz parte da oferta de Segurança Avançada de Dados, que é um pacote unificado para funcionalidades avançadas de segurança. A Proteção Avançada contra Ameaças pode ser acessada e gerenciada por meio do [Portal do Azure](https://portal.azure.com) e, atualmente, está em versão prévia.

> [!NOTE]
> O recurso de Proteção Avançada contra Ameaças **não** está disponível nas seguintes regiões de nuvem soberana e do Azure Governamental: US Gov - Texas, US Gov - Arizona, US Gov Iowa, EUA, US Gov - Virgínia, US DoD Leste, US DoD Central, Alemanha Central, Norte da Alemanha, Leste da China, Leste da China 2. Visite [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para ver a disponibilidade geral do produto.
> 

## <a name="what-is-advanced-threat-protection"></a>O que é a Proteção Avançada contra Ameaças?

A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL fornece uma nova camada de segurança que possibilita aos clientes detectar e responder a possíveis ameaças conforme elas ocorrem, apresentando alertas de segurança sobre atividades anômalas. Os usuários receberão um alerta em caso de atividades suspeitas em bancos de dados, possíveis vulnerabilidades, bem como padrões anômalos de consultas e acesso a banco de dados. A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL integra alertas à [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/), que incluem detalhes de atividades suspeitas e recomenda ações sobre como investigar e atenuar a ameaça. A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL simplifica a abordagem contra possíveis ameaças no banco de dados, sem a necessidade de ser um especialista em segurança ou de gerenciar sistemas avançados de monitoramento de segurança. 

![Conceito da Proteção Avançada contra Ameaças](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Alertas da Proteção Avançada contra Ameaças 
A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e pode disparar os seguintes alertas:
- **Acesso de um local incomum**: Esse alerta é disparado quando há uma alteração no padrão de acesso ao servidor do Banco de Dados do Azure para MySQL, em que alguém fez logon no servidor do Banco de Dados do Azure para MySQL a partir de um local geográfico incomum. Em alguns casos, o alerta detecta uma ação legítima (um novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso de um data center do Azure incomum**: Esse alerta é disparado quando há uma alteração no padrão de acesso ao servidor do Banco de Dados do Azure para MySQL, em que alguém fez logon no servidor de um datacenter incomum do Azure que foi visto neste servidor durante o período recente. Em alguns casos, o alerta detecta uma ação legítima (o novo aplicativo no Azure, no Power BI e no Editor de Consultas do Banco de Dados do Azure para MySQL). Em outros casos, o alerta detecta uma ação mal-intencionada de um recurso/serviço do Azure (funcionário antigo, invasor externo).
- **Acesso de uma entidade de segurança desconhecida**: Esse alerta é disparado quando há uma alteração no padrão de acesso ao servidor do Banco de Dados do Azure para MySQL, em que alguém fez logon no servidor usando uma entidade de segurança incomum (usuário do Banco de Dados do Azure para MySQL). Em alguns casos, o alerta detecta uma ação legítima (novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso de um aplicativo potencialmente prejudicial**: esse alerta é disparado quando um aplicativo potencialmente prejudicial é usado para acessar o banco de dados. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta um ataque usando ferramentas comuns de ataque.
- **Credenciais de força bruta do Banco de Dados do Azure para MySQL**: Esse alerta é disparado quando há um número alto e anormal de logons com falha com credenciais diferentes. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta ataques de força bruta.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para saber mais sobre preço, confira a [página Preço do Banco de Dados do Azure para MySQL](https://azure.microsoft.com/pricing/details/mysql/) 
* Configurar a [Proteção Avançada contra Ameaças do Banco de Dados do Azure para MySQL](howto-database-threat-protection-portal.md) usando o portal do Azure  
