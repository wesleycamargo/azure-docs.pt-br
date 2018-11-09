---
title: Detecção de ameaças – Banco de Dados SQL do Azure | Microsoft Docs
description: A Detecção de Ameaças detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança contra o banco de dados em um banco de dados individual ou um pool elástico.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 10/25/2018
ms.openlocfilehash: 2882bd782359697cf714214e68166c9f997f52e4
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093540"
---
# <a name="azure-sql-database-threat-detection"></a>Detecção de Ameaças do Banco de Dados SQL do Azure

A detecção de ameaças do SQL do Azure para [Banco de Dados SQL](sql-database-technical-overview.md) e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

A Detecção de Ameaças faz parte da oferta [ATP](sql-advanced-threat-protection.md) (Proteção Avançada contra Ameaças) do SQL, um pacote unificado de funcionalidades avançadas de segurança do SQL. A Detecção de Ameaças pode ser acessada e gerenciada por meio do portal central da ATP do SQL.


> [!NOTE] 
> Este tópico aplica-se ao servidor SQL do Azure e aos bancos de dados SQL e SQL Data Warehouse criados no servidor do SQL do Azure. Para simplificar, o banco de dados SQL é usado quando se refere ao Banco de Dados SQL e ao SQL Data Warehouse.


## <a name="what-is-threat-detection"></a>O que é Detecção de Ameaças?

A Detecção de Ameaças do SQL fornece uma nova camada de segurança, que permite que os clientes detectem e respondam às ameaças potenciais conforme elas ocorrem, fornecendo alertas de segurança sobre atividades anômalas. Os usuários receberão um alerta em caso de atividades suspeitas em bancos de dados, possíveis vulnerabilidades e ataques de injeção de SQL, bem como padrões anômalos de consultas e acesso a banco de dados. A Detecção de Ameaças de SQL integra alertas à [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/), que fornece detalhes de atividades suspeitas e ação recomendada sobre como investigar e atenuar a ameaça. A Detecção de Ameaças de SQL torna simples tratar as possíveis ameaças no banco de dados sem a necessidade de ser um especialista em segurança ou gerenciar os sistemas de monitoramento de segurança avançados. 

Para uma experiência de investigação completa, é recomendável habilitar a [Auditoria de Banco de Dados SQL](sql-database-auditing.md), que grava eventos de banco de dados em um log de auditoria na conta de armazenamento do Azure.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Configurar a detecção de ameaças para seu banco de dados no Portal do Azure
1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue para a página de configuração do servidor de Banco de Dados SQL do Azure que você deseja proteger. Nas configurações de segurança, selecione **Proteção Avançada contra Ameaças**.
3. Na página de configuração de **Proteção Avançada contra Ameaças**:

   - Habilite a Proteção Avançada contra Ameaças no servidor.
   - Em **Configurações de Detecção de Ameaças**, na caixa de texto **Enviar alertas para**, forneça a lista de endereços de email para receber alertas de segurança na detecção de atividades anômalas de banco de dados.
  
   ![Configurar detecção de ameaças](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Configurar a detecção de ameaças usando o PowerShell

Para obter um exemplo de script, confira [Configurar a auditoria e a detecção de ameaças usando o PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Explore as atividades anormais do banco de dados na detecção de um evento suspeito

Você receberá uma notificação por email na detecção das atividades anormais do banco de dados. O email fornecerá informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anômalas, o nome do banco de dados, o nome do servidor, o nome do aplicativo e a hora do evento. Além disso, o email fornecerá informações sobre as possíveis causas e as ações recomendadas para investigar e atenuar a ameaça em potencial no banco de dados.

![Relatórios de atividades anômalas](./media/sql-database-threat-detection/anomalous_activity_report.png)
     
1. Clique em **Exibir alertas recentes do SQL** no email para iniciar o Portal do Azure e mostrar a página de alertas da Central de Segurança do Azure, que fornece uma visão geral das ameaças ativas detectadas no banco de dados SQL.

   ![Ameaças de atividade](./media/sql-database-threat-detection/active_threats.png)

2. Clique em um alerta específico para obter os detalhes e as ações adicionais para investigar essa ameaça e corrigir ameaças futuras.

   Por exemplo, a injeção de SQL é um dos problemas mais comuns de segurança do aplicativo da Web na Internet, usada para atacar os aplicativos orientados a dados. Os invasores aproveitam as vulnerabilidades do aplicativo para inserir instruções SQL mal-intencionadas nos campos de entrada do aplicativo, violando ou modificando os dados no banco de dados. Para os alertas de Injeção de SQL, os detalhes do alerta incluem a instrução SQL vulnerável que foi explorada.

   ![Alerta específico](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Explorar os alertas de detecção de ameaças para seu banco de dados no Portal do Azure

A Detecção de Ameaças do Banco de Dados SQL integra seus alertas à [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/). Os blocos dinâmicos de detecção de ameaças SQL dentro do banco de dados e das folhas ATP SQL no portal do Azure acompanham o status de ameaças ativas.

Clique em **Alerta de detecção de ameaças** para iniciar a página de alertas da Central de Segurança do Azure e obter uma visão geral das ameaças SQL ativas detectadas no banco de dados ou no data warehouse.

   ![Alerta de detecção de ameaças](./media/sql-database-threat-detection/threat_detection_alert.png)
   
   ![Alerta de detecção de ameaças 2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="azure-sql-database-threat-detection-alerts"></a>Alertas de Detecção de Ameaças do Banco de Dados SQL do Azure 
A Detecção de Ameaças para Banco de Dados SQL do Azure detecta atividades anormais que indicam tentativas potencialmente perigosas e incomuns para acessar ou explorar bancos de dados e pode disparar os alertas a seguir:
- **Vulnerabilidade à injeção de SQL**: Esse alerta é disparado quando um aplicativo gera uma instrução de SQL com erro no banco de dados. Isso pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Há dois motivos possíveis para a geração de uma instrução com erro:
   - Um defeito no código do aplicativo que cria a instrução SQL com erro
   - O código do aplicativo ou procedimentos armazenados não limpam a entrada do usuário ao construir a instrução SQL com erro, o que pode ser explorado para injeção de SQL
- **Potencial injeção de SQL**: Este alerta é disparado quando acontece uma exploração ativa em uma vulnerabilidade do aplicativo identificada para injeção de SQL. Isso significa que o invasor está tentando inserir instruções SQL maliciosas usando o código de aplicativo ou procedimentos armazenados vulneráveis.
- **Acesso a partir de um local incomum**: Este alerta é disparado quando há uma alteração no padrão de acesso ao SQL Server, em que alguém fez logon no SQL Server a partir de um local geográfico incomum. Em alguns casos, o alerta detecta uma ação legítima (um novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso a partir de um data center do Azure incomum**: Este alerta é disparado quando há uma alteração no padrão de acesso ao SQL Server, onde alguém fez logon no SQL Server a partir de um data center do Azure incomum que foi visto neste servidor durante um período recente. Em alguns casos, o alerta detecta uma ação legítima (seu novo aplicativo no Azure, o Power BI, Azure SQL Query Editor). Em outros casos, o alerta detecta uma ação mal-intencionada de um recurso/serviço do Azure (funcionário antigo, invasor externo).
- **Acesso a partir de uma entidade de segurança não familiar**: Este alerta é disparado quando há uma alteração no padrão de acesso ao SQL Server, onde alguém fez logon no SQL Server a partir de uma entidade de segurança incomum (usuário SQL). Em alguns casos, o alerta detecta uma ação legítima (novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso a partir de um aplicativo potencialmente prejudicial**: Este alerta é disparado quando um aplicativo potencialmente prejudicial é usado para acessar o banco de dados. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta um ataque usando ferramentas comuns de ataque.
- **Credenciais SQL de força bruta**: Este alerta é disparado quando há um número alto anormal de logons com falha com credenciais diferentes. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta ataques de força bruta.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Proteção Avançada contra Ameaças do SQL](sql-advanced-threat-protection.md). 
* Saiba mais sobre a [Auditoria do Banco de Dados SQL do Azure](sql-database-auditing.md)
* Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para saber mais sobre preços, visite a [página de Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
