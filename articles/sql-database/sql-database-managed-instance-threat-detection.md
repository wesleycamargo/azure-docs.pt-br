---
title: Detecção de Ameaças – Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: A Detecção de Ameaças detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados.
services: sql-database
author: rmatchoro
manager: cguyer
ms.service: sql-database
ms.custom: security, managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: f8c08bc16d622516958b8bd182179d07edfa4891
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-sql-database-managed-instance-threat-detection"></a>Detecção de Ameaças da Instância Gerenciada do Banco de Dados SQL do Azure

A Detecção de Ameaças do SQL detecta atividades anormais que indicam tentativas potencialmente perigosas e incomuns para acessar ou explorar bancos de dados em uma Instância Gerenciada do Banco de Dados SQL do Azure (versão prévia).

## <a name="overview"></a>Visão geral

A Detecção de Ameaças detecta atividades anormais do banco de dados que indicam possíveis ameaças à segurança para Instância Gerenciada. No momento, a Detecção de Ameaças está em versão prévia para Instância Gerenciada.

A Detecção de Ameaças fornece uma nova camada de segurança que permite aos clientes detectar e responder a ameaças potenciais na medida em que ocorrem, fornecendo alertas de segurança em atividades anormais do banco de dados. A Detecção de Ameaças facilita o tratamento de possíveis ameaças à Instância Gerenciada sem a necessidade de ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança avançados. Para uma experiência de investigação completa, é recomendável habilitar a Auditoria de Instância Gerenciada do Azure, que grava eventos de banco de dados em um log de auditoria na conta de armazenamento do Azure. 

A Detecção de Ameaças do SQL integra alertas com a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) e, cada Instância Gerenciada protegida é cobrada no mesmo preço que a camada Standard da Central de Segurança do Azure, que é US$ 15/nó/mês, onde cada Instância Gerenciada protegida é contada como um nó.  

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Configurar a Detecção de Ameaças para a Instância Gerenciada no Portal do Azure
1. Inicie o Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até a página de configuração da Instância Gerenciada que você quer proteger. Na página **Configurações**, selecione **Detecção de Ameaças**. 
3. Na página de configurações de Detecção de Ameaças 
   - **ATIVE** a Detecção de ameaças.
   - Configure a **lista de emails** para receber os alertas de segurança após a detecção das atividades de banco de dados anormais.
   - Selecione a **Conta de Armazenamento do Azure** onde os registros de auditoria de ameaças anormais são salvos. 
4.  Clique em **Salvar** para salvar a política de detecção de ameaças atualizada ou nova.

   ![detecção de ameaças](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="explore-anomalous-managed-instance-activities-upon-detection-of-a-suspicious-event"></a>Explore atividades anormais da Instância Gerenciada após a detecção de um evento suspeito

1. Você receberá uma notificação por email na detecção das atividades anormais do banco de dados. 

   O email fornece informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anormais, o nome do banco de dados, o nome do servidor e a hora do evento. Além disso, fornece informações sobre possíveis causas e ações recomendadas para investigar e mitigar a ameaça potencial para a Instância Gerenciada.

   ![email de detecção de ameaças](./media/sql-database-managed-instance-threat-detection/threat-detection-email.png)

2. Clique em **Exibir alertas recentes do SQL** no email para iniciar o Portal do Azure e mostrar a página de alertas da Central de Segurança do Azure, que fornece uma visão geral das ameaças do SQL ativas detectadas no banco de dados da Instância Gerenciada.

   ![ameaças ativas](./media/sql-database-managed-instance-threat-detection/active-threats.png)

3. Clique em um alerta específico para obter os detalhes e as ações adicionais para investigar essa ameaça e corrigir ameaças futuras.

   Por exemplo, a injeção de SQL é um dos problemas comuns de segurança de aplicativo Web na Internet. A injeção de SQL é utilizada para atacar aplicativos controlado por dados. Os invasores aproveitam as vulnerabilidades do aplicativo para inserir instruções SQL mal-intencionadas nos campos de entrada do aplicativo, violando ou modificando os dados no banco de dados. Para os alertas de Injeção de SQL, os detalhes do alerta incluem a instrução SQL vulnerável que foi explorada.

   ![injeção de SQL](./media/sql-database-managed-instance-threat-detection/sql-injection.png)

## <a name="managed-instance-threat-detection-alerts"></a>Alertas de Detecção de Ameaças da Instância Gerenciada 

A Detecção de Ameaças para Instância Gerenciada detecta atividades anormais que indicam tentativas potencialmente perigosas e incomuns para acessar ou explorar bancos de dados e pode disparar os alertas a seguir:
- **Vulnerabilidade à injeção de SQL**: Esse alerta é disparado quando um aplicativo gera uma instrução de SQL com erro no banco de dados. Isso pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Há dois motivos possíveis para a geração de uma instrução com erro:
 - Um defeito no código do aplicativo que cria a instrução SQL com erro
 - O código do aplicativo ou procedimentos armazenados não limpam a entrada do usuário ao construir a instrução SQL com erro, o que pode ser explorado para injeção de SQL
- **Potencial injeção de SQL**: Este alerta é disparado quando acontece uma exploração ativa em uma vulnerabilidade do aplicativo identificada para injeção de SQL. Isso significa que o invasor está tentando injetar instruções SQL mal-intencionadas utilizando procedimentos armazenados ou código do aplicativo vulnerável.
- **Acesso a partir de uma localização incomum**: esse alerta é disparado quando há uma alteração no padrão de acesso a uma Instância Gerenciada, em que alguém fez logon na Instância Gerenciada a partir de uma localização geográfica incomum. Em alguns casos, o alerta detecta uma ação legítima (um novo aplicativo ou operação de manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo e etc.).
- **Acesso a partir de um data center do Azure incomum**: esse alerta é disparado quando há uma alteração no padrão de acesso para a Instância Gerenciada, em que alguém fez logon na Instância Gerenciada a partir de um Data Center do Azure que não foi visto acessando essa Instância Gerenciada durante o período recente. Em alguns casos, o alerta detecta uma ação legítima (o novo aplicativo no Azure, Power BI, Editor de Consultas SQL do Azure e etc.). Em outros casos, o alerta detecta uma ação mal-intencionada de um recurso/serviço do Azure (funcionário antigo, invasor externo).
- **Acesso a partir de uma entidade de segurança não familiar**: esse alerta é disparado quando há uma alteração no padrão de acesso ao servidor da Instância Gerenciada, em que alguém fez logon na Instância Gerenciada utilizando uma entidade de segurança incomum (usuário SQL). Em alguns casos, o alerta detecta uma ação legítima (operação de manutenção do desenvolvedor do aplicativo novo). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso a partir de um aplicativo potencialmente prejudicial**: Este alerta é disparado quando um aplicativo potencialmente prejudicial é usado para acessar o banco de dados. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta um ataque usando ferramentas comuns de ataque.
- **Credenciais SQL de força bruta**: Este alerta é disparado quando há um número alto anormal de logons com falha com credenciais diferentes. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta um ataque de força bruta.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre Instância Gerenciada, consulte [O que é uma Instância Gerenciada](sql-database-managed-instance.md)
- Saiba mais sobre [Auditoria de Instância Gerenciada](https://go.microsoft.com/fwlink/?linkid=869430) 
- Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
