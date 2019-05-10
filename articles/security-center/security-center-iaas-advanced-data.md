---
title: Segurança de dados avançada para IaaS na Central de segurança do Azure | Microsoft Docs
description: " Saiba como habilitar a segurança de dados avançados para IaaS na Central de segurança do Azure. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2019
ms.author: monhaber
ms.openlocfilehash: e601bbaa0d15078fc2b19b5b7c536e3a1f6d20ad
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442741"
---
# <a name="advanced-data-security-for-sql-servers-on-iaas"></a>Segurança avançada de dados para os servidores SQL IaaS
Segurança avançada de dados para os servidores SQL IaaS é um pacote unificado para recursos de segurança avançados do SQL. Atualmente, inclui a funcionalidade para faceando e atenuar as vulnerabilidades potenciais de banco de dados e detectar atividades anormais que podem indicar uma ameaça para seu banco de dados.

Essa oferta para IaaS SQL servers é baseada na mesma tecnologia fundamental usada na [pacote de segurança de dados avançada do Azure SQL banco de dados](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Visão geral

Segurança de dados avançados (ADS) fornece um conjunto de recursos avançados de segurança SQL, consistindo de avaliação de vulnerabilidade e proteção avançada contra ameaças.

* A [Avaliação de vulnerabilidade ](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) é um serviço fácil de ser configurado que pode descobrir, acompanhar e ajudar a corrigir possíveis vulnerabilidades do banco de dados. Ele fornece visibilidade sobre o estado de segurança e inclui as etapas para resolver problemas de segurança e aperfeiçoar seu fortifications de banco de dados.
* [Proteção avançada contra ameaças](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) detecta atividades anômalas indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar o SQL server. Ele monitora o banco de dados para atividades suspeitas continuamente e fornece alertas de segurança orientadas a ações nos padrões de acesso de banco de dados anormais. Esses alertas fornecem os detalhes de atividades suspeitas e ações recomendadas para investigar e atenuar a ameaça.

## <a name="get-started-with-ads-for-iaas"></a>Introdução aos anúncios para IaaS

As etapas a seguir que você comece anúncios para IaaS.

### <a name="set-up-ads-for-iaas"></a>Configurar o ADS para IaaS

**Antes de começar**: Você precisa de um espaço de trabalho do Log Analytics para armazenar os logs de segurança que está sendo analisados. Se você não tiver uma, você pode criar um com facilidade, conforme explicado em [criar um espaço de trabalho do Log Analytics no portal do Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

1. Conecte-se a VM que hospeda o SQL server para o espaço de trabalho do Log Analytics. Para obter instruções, consulte [computadores Windows conectar-se para o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. No Azure Marketplace, vá para o [solução de segurança de dados do SQL avançada](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(Você pode encontrá-la usando a opção de pesquisa de mercado, como na imagem a seguir, consulte.) O **segurança de dados do SQL avançada** página será aberta.

    ![Segurança de dados avançados para IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Clique em **Criar**. Os espaços de trabalho são exibidos.

    ![Criar segurança avançada de dados](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Selecione o espaço de trabalho e clique em **criar**.

   ![Selecione o workspace](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Reinicie o [servidor SQL da VM](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017).


## <a name="explore-and-investigate-security-alerts"></a>Explorar e investigar alertas de segurança

Você pode exibir e gerenciar seus alertas de segurança atual.

1. Clique em **Central de segurança** > **alertas de segurança**e clique em um alerta.

    ![Localizar o alerta](./media/security-center-advanced-iaas-data/find-alert.png)

1. Dos **recurso atacado** coluna, clique em um recurso que foi atacado.

1. Para exibir detalhes de alertas e ações para investigar as ameaças atuais e futuras ameaças para lidar, role para baixo a **informações gerais** página e, nas **etapas de correção** seção, clique no  **ETAPAS de investigação** link.

    ![Etapas de Correção](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. Para exibir os logs que estão associados com o disparo do alerta, vá para **espaços de trabalho de análise de Log** e siga as etapas a seguir:

     > [!NOTE]
     > Se **espaços de trabalho de análise de Log** não aparecer no menu à esquerda, clique em **todos os serviços**e procure **espaços de trabalho de análise de Log**.

    1. Certifique-se de que as colunas são exibidos os **tipo de preço** e **WorkspaceID** colunas. (**Espaços de trabalho de análise de log** > **Editar colunas**, adicione **tipo de preço** e **WorkspaceID**.)

     ![Editar Colunas](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Clique no espaço de trabalho que tem os logs de alerta.

    1. Sob o **gerais** menu, clique em **Logs**

    1. Clique o olho lado **SQLAdvancedThreatProtection** tabela. Os logs são listados.

     ![Exibir Logs](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>Configurar notificação de Email para alertas do ATP 

Você pode definir uma lista de destinatários para receber uma notificação por email quando alertas do ASC são geradas. O email contém um link direto para o alerta na Central de segurança do Azure com todos os detalhes relevantes. 

1. Vá para **Central de segurança** > **política de segurança** e, na linha do assinatura relevante, clique em **Editar Configurações >**.

    ![Configurações de assinatura](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Dos **as configurações** menu, clique em **notificações por Email**. 
1. No **endereço de Email** texto, digite os endereços de email para receber as notificações. Você pode inserir mais de um endereço de email, separando os endereços de email com uma vírgula (,).  Por exemplo admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

      ![Configurações de email](./media/security-center-advanced-iaas-data/email-settings.png)

1. No **notificação por Email** configurações, defina as seguintes opções:
  
    * **Enviar notificação por email para alertas de severidade alta**: Em vez de enviar emails para todos os alertas, envie somente para alertas de severidade alta.
    * **Também enviar notificações por email aos proprietários da assinatura**:  Envie notificações para os proprietários de assinaturas muito.

1. Na parte superior dos **notificações por Email** tela, clique em **salvar**.

  > [!NOTE]
  > Clique em **salve** antes de fechar a janela ou nova **notificação por Email** configurações não serão salvas.

## <a name="explore-vulnerability-assessment-reports"></a>Explorar os relatórios de avaliação de vulnerabilidade

O painel de avaliação de vulnerabilidade fornece uma visão geral dos resultados da avaliação em todos os seus bancos de dados. Você pode exibir a distribuição dos bancos de dados de acordo com a versão do SQL Server, juntamente com um resumo da falha versus passando um resumo geral das verificações de acordo com a distribuição do risco de falha e bancos de dados.

Você pode exibir seus relatórios diretamente do Log Analytics e os resultados da avaliação de vulnerabilidade.

1. Navegue até seu espaço de trabalho do Log Analytics com a solução de anúncios.
1. Navegue até **soluções** e selecione o **avaliação de vulnerabilidade de SQL** solução.
1. No **resumo** painel, clique em **Exibir resumo** e selecione seu **relatório de avaliação de vulnerabilidade de SQL**.

    ![Relatório de avaliação do SQL](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    Carrega o painel do relatório. Verifique se a janela de tempo está definida pelo menos o **últimos 7 dias** como verificações de avaliação de vulnerabilidade são executadas em seus bancos de dados em uma agenda fixa de uma vez a cada 7 dias.

    ![Defina os últimos 7 dias](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. Para fazer drill down para obter mais detalhes, clique em qualquer um dos elementos do painel. Por exemplo:

   1. Clique em uma verificação de vulnerabilidade na **Failed verifica resumo** seção para exibir uma tabela de Log Analytics com os resultados para essa verificação em todos os bancos de dados. Aqueles que têm os resultados são listados primeiro.

   1. Clique para exibir detalhes de cada vulnerabilidade, incluindo a descrição da vulnerabilidade e impacto, status, risco associada e os resultados reais nesse banco de dados. Você também pode ver a consulta real que foi executada para executar essa verificação e informações de correção para resolver essa vulnerabilidade.

    ![Selecione o workspace](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Selecione o workspace](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. Você pode executar todas as consultas do Log Analytics em seus dados de resultados de avaliação de vulnerabilidade, para dividir e separar os dados de acordo com suas necessidades.

## <a name="advanced-threat-protection-for-sql-servers-on-iaas-alerts"></a>Proteção avançada contra ameaças para servidores SQL em IaaS alertas
Alertas são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar servidores SQL. Esses eventos podem disparar os alertas a seguir:

### <a name="anomalous-access-pattern-alerts"></a>Alertas de padrão de acesso anormais

* **Acesso de um local incomum:** este alerta é disparado quando há uma alteração no padrão de acesso ao SQL Server, em que alguém faz logon no SQL Server de um local geográfico incomum. Possíveis causas:
     * Um empregam mal-intencionado do invasor ou antiga acessou seu servidor SQL.
     * Um usuário legítimo acessou seu servidor SQL de um novo local.
* **Acesso a partir de um aplicativo potencialmente prejudicial**: seu alerta é disparado quando um aplicativo potencialmente prejudicial é usado para acessar o banco de dados. Possíveis causas:
     * Um invasor tentando invadir sua SQL usando ferramentas comuns de ataque.
     * Uma legítimo os testes de penetração em ação.
* **Acesso de uma entidade de segurança desconhecida**: esse alerta é disparado quando há uma alteração no padrão de acesso ao SQL Server, em que alguém faz logon no SQL Server de uma entidade de segurança incomum (usuário SQL). Possíveis causas:
     * Um empregam mal-intencionado do invasor ou antiga acessou seu servidor SQL. 
     * Um usuário legítimo acessou o SQL Server de com uma nova entidade.
* **Credenciais SQL de força bruta**: esse alerta é disparado quando há um número alto e anormal de logons com falha com credenciais diferentes. Possíveis causas:
     * Um invasor tentando invadir sua SQL força bruta.
     * Uma legítimo os testes de penetração em ação.

### <a name="potential-sql-injection-attacks-coming"></a>Possíveis ataques de injeção de SQL (caminho)

* **Vulnerabilidade à injeção de SQL**: esse alerta é disparado quando um aplicativo gera uma instrução SQL com erro no banco de dados. Esse alerta pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Possíveis causas:
     * Um defeito no código do aplicativo que cria a instrução SQL com erro
     * O código do aplicativo ou procedimentos armazenados não limpam a entrada do usuário ao construir a instrução SQL com erro, o que pode ser explorado para injeção de SQL
* **Potencial injeção de SQL**: esse alerta é disparado quando acontece uma exploração ativa contra uma vulnerabilidade identificada do aplicativo para injeção de SQL. Isso significa que o invasor está tentando inserir instruções SQL maliciosas usando o código de aplicativo ou procedimentos armazenados vulneráveis.
