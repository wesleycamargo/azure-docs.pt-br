---
title: Detecção de Ameaças Avançadas – Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba sobre a funcionalidade para descobrir e classificar dados sensíveis, gerenciando suas vulnerabilidades do banco de dados e detectando atividades anômalas que podem indicar uma ameaça ao banco de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 12/16/2018
ms.openlocfilehash: 40f6b6effa509fd2cfa40ecfc758ac37e0a66778
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538589"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Proteção Avançada contra Ameaças para o Banco de Dados SQL do Microsoft Azure

Proteção Avançada contra Ameaças SQL é um pacote unificado para recursos de segurança avançados do SQL. Ele inclui a funcionalidade para descobrir e classificar dados confidenciais, identificando e atenuante banco de dados vulnerabilidades potenciais e detectar atividades anormais que podem indicar uma ameaça para seu banco de dados. Fornece um local único para habilitar e gerenciar esses recursos. 

## <a name="overview"></a>Visão geral

A Proteção Contra Ameaça Avançada SQL (ATP) fornece um conjunto de recursos avançados de segurança SQL, incluindo a descoberta de dados e classificação, avaliação de vulnerabilidades e detecção de ameaças. 

- [Descoberta de Dados e Classificação](sql-database-data-discovery-and-classification.md) (atualmente na versão prévia) fornece recursos internos avançados no Banco de Dados SQL do Microsoft Azure para descobrir, classificar, rotular e proteger os dados sensíveis em seus bancos de dados. Pode ser usada para fornecer visibilidade em seu estado de classificação do banco de dados e para controlar o acesso a dados confidenciais no banco de dados e, além de suas bordas.
- A [Avaliação de Vulnerabilidade ](sql-vulnerability-assessment.md) é um serviço fácil de ser configurado que pode descobrir, acompanhar e ajudá-lo a corrigir vulnerabilidades potenciais do banco de dados. Fornece visibilidade sobre o estado de segurança e inclui etapas acionáveis para resolver problemas de segurança e aperfeiçoar as fprtificações do banco de dados.
- A [Detecção de Ameaças](sql-database-threat-detection-overview.md) detecta atividades anômalas, indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. Monitora continuamente o banco de dados com relação a atividades suspeitas e fornece alertas de segurança imediata sobre vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso anormal do banco de dados. Os alertas da detecção de ameaças fornecem detalhes de atividades suspeitas e recomendam ação de como investigar e atenuar a ameaça.

Ativar ATP SQL depois habilitar todos esses recursos incluídos. Com um clique, você pode habilitar a ATP em todo o seu servidor de banco de dados, aplicando-se a todos os bancos de dados no servidor. Habilitar ou gerenciar as configurações ATP requer pertencer à função do [Gerenciador de Segurança de SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager), função de administrador do banco de dados SQL ou função de administrador do servidor SQL. 

O preço da ATP está alinhado à camada padrão da Central de Segurança do Azure, na qual cada servidor de Banco de Dados SQL protegido é contado como um nó. Recursos protegidos recentemente se qualificam para uma avaliação gratuita da camada Standard da Central de Segurança. Para obter mais informações, confira a [página de preços](https://azure.microsoft.com/pricing/details/security-center/) da Central de Segurança do Azure.


## <a name="getting-started-with-atp"></a>Introdução a ATP 
As etapas a seguir começar o ATP. 

## <a name="1-enable-atp"></a>1. Habilite ATP

Habilite o ATP navegando para **Proteção Avançada contra Ameaças** no cabeçalho **Segurança** do painel do Banco de Dados SQL do Azure. Para habilitar ATP em todos os bancos de dados no servidor, clique em **Habilitar proteção avançada contra ameaças no servidor**.

![Habilite ATP](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> O custo da ATP está alinhado aos preços da camada Standard da Central de Segurança do Azure por nó, em que um nó é todo o servidor lógico SQL. Portanto, você está pagando apenas uma vez para proteger todos os bancos de dados no servidor com ATP. Você pode experimentar a ATP inicialmente com uma avaliação gratuita.

## <a name="2-configure-vulnerability-assessment"></a>2. Configura a avaliação de vulnerabilidade

Para começar a usar a Avaliação de Vulnerabilidade, você precisa configurar uma conta de armazenamento onde os resultados da varredura são salvos. Para fazer isso, clique no cartão de Avaliação de Vulnerabilidade.

![Configurar VA](./media/sql-advanced-protection/configure_va.png) 

Selecione ou crie uma conta de armazenamento para salvar os resultados de escaneamento. Você também pode ativar exames periódicos recorrentes para configurar a Avaliação de Vulnerabilidade para executar exames automáticos uma vez por semana. Um resumo dos resultados da verificação será enviado para os endereços de email fornecidos.

![Configurações da VA](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Iniciar dados de classificação, vulnerabilidades de rastreamento e investigação de alertas de ameaça

Clique no cartão **Descoberta de Dados e Classificação** para ver colunas sensíveis recomendadas para classificar e classificar seus dados com rótulos de sensibilidade persistente. Clique no cartão **Avaliação de Vulnerabilidade** para exibir e gerenciar relatórios e verificações de vulnerabilidade e relatórios e acompanhar sua estatura de segurança. Ao receber alertas de segurança, clique no cartão **Detecção de Ameaças** para exibir detalhes de alertas e visualizar um relatório consolidado em todos os alertas na sua assinatura do Microsoft Azure por meio da página de alertas de segurança da Central de Segurança do Azure.

## <a name="4-manage-atp-settings-on-your-sql-server"></a>4. Gerenciar configurações de ATP no SQL server

Para exibir e gerenciar as configurações de Proteção de Ameaça Avançada, navegue até **Proteção Contra Ameaça Avançada** em **Segurança** no seu painel do SQL server. Nesta página, você pode habilitar ou desabilitar ATP e modificar as configurações de Detecção contra Ameaça para o seu servidor SQL inteiro.

![Configurações do servidor](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-atp-settings-for-a-sql-database"></a>5. Gerenciar configurações de ATP para banco de dados SQL

Para substituir as configurações de Detecção de Ameaças ATP para um determinado banco de dados, marque a caixa de seleção **Habilitar Proteção Avançada contra ameaças no nível do banco de dados**. Use esta opção somente se você tiver uma necessidade específica para receber alertas de detecção de ameaças separada para o banco de dados individual, em vez ou além dos alertas recebidos para todos os bancos de dados no servidor. 

Quando a caixa de seleção for selecionada, clique em **Configurações de Detecção de Ameaças para este banco de dados** e, em seguida, defina as configurações relevantes para esse banco de dados.

![Configurações de banco de dados e detecção ameaça](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Configurações de Proteção Contra Ameaças Avançadas para o servidor também podem ser alcançados do painel do banco de dados ATP. Clique em **Configurações** no painel de ATP principal e, em seguida, clique em **Visualizar configurações do servidor de Proteção Contra Ameaça Avançada**. 

![Configurações de banco de dados](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Próximas etapas 

- Saiba mais sobre [Descoberta e Classificação de Dados](sql-database-data-discovery-and-classification.md) 
- Saiba mais sobre [Avaliação de Vulnerabilidade](sql-vulnerability-assessment.md) 
- Saiba mais sobre a [Detecção de Ameaças](sql-database-threat-detection.md)
- Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
