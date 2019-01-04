---
title: Monitorar a Sincronização de Dados SQL do Azure com o Log Analytics | Microsoft Docs
description: Saiba como monitorar a Sincronização de Dados SQL do Azure usando Log Analytics
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: d6a1f2829352e8589c8d132a195a425edcbe62c3
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872927"
---
# <a name="monitor-sql-data-sync-with-log-analytics"></a>Monitorar Sincronização de Dados SQL com o Log Analytics 

Para verificar o log de atividades da Sincronização de Dados SQL e detectar erros e avisos, anteriormente você precisava verificar a Sincronização de Dados SQL manualmente no portal do Azure ou usar o PowerShell ou a API REST. Siga as etapas deste artigo para configurar uma solução personalizada que melhora a experiência de monitoramento da Sincronização de Dados. Personalize essa solução de acordo com seu cenário.

Para obter uma visão geral da Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md).

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Painel de Monitoramento para todos os Grupos de Sincronização 

Você não precisa mais examinar os logs de cada Grupo de Sincronização individualmente para procurar problemas. Você pode monitorar todos os Grupos de Sincronização em qualquer uma de suas assinaturas em um único local usando uma exibição personalizada do Log Analytics. Essa exibição apresenta as informações importantes para os clientes da Sincronização de Dados SQL.

![Painel de monitoramento da Sincronização de Dados](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Notificações automatizadas por email

Você não precisa mais verificar o log manualmente no Portal do Azure ou por meio do PowerShell ou da API REST. Com o [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), você pode criar alertas que vão diretamente para os endereços de email daqueles que precisam vê-los em caso de erro.

![Notificações por email da Sincronização de Dados](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Como configurar esses recursos de monitoramento? 

Implemente um solução de monitoramento personalizado do Log Analytics para a Sincronização de Dados SQL em menos de uma hora, realizando os procedimentos a seguir:

É necessário configurar três componentes:

-   Um runbook do PowerShell para alimentar os dados de log da Sincronização de Dados SQL no Log Analytics.

-   Um alerta do Log Analytics para notificações por email.

-   Uma exibição do Log Analytics para o monitoramento.

### <a name="samples-to-download"></a>Amostras para download

Baixe os dois seguintes exemplos:

-   [Runbook do PowerShell do Log da Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Modo de exibição do Log Analytics de Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Pré-requisitos

Verifique se você configurou o seguinte:

-   Uma conta da Automação do Azure

-   Workspace do Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Runbook do PowerShell para obter o Log da Sincronização de Dados SQL 

Use um runbook do PowerShell hospedado na Automação do Azure para extrair os dados de log da Sincronização de Dados SQL e enviá-los ao Log Analytics. Um script de exemplo é incluído. Como pré-requisito, você precisa ter uma conta da Automação do Azure. Em seguida, você precisa criar um runbook e agendá-lo para execução. 

### <a name="create-a-runbook"></a>Criar um runbook

Para obter mais informações sobre como criar um runbook, consulte [Meu primeiro runbook do PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Em sua conta da Automação do Azure, selecione a guia **Runbooks** em Automação de Processo.

2.  Selecione **Adicionar um Runbook** no canto superior esquerdo da página Runbooks.

3.  Selecione **Importar um runbook existente**.

4.  Em **Arquivo de runbook**, use o determinado arquivo `DataSyncLogPowerShellRunbook`. Defina o **Tipo de runbook** como `PowerShell`. Forneça um nome para o runbook.

5.  Selecione **Criar**. Agora você tem um runbook.

6.  Em sua Conta da Automação do Azure, selecione a guia **Variáveis** em Recursos Compartilhados.

7.  Selecione **Adicionar uma variável** na página Variáveis. Crie uma variável para armazenar o último tempo de execução para o runbook. Caso você tenha vários runbooks, precisará de uma variável para cada runbook.

8.  Defina o nome da variável como `DataSyncLogLastUpdatedTime` e defina seu Tipo como DateTime.

9.  Selecione o runbook e clique no botão Editar na parte superior da página.

10. Faça as alterações necessárias em sua conta e na configuração da Sincronização de Dados SQL. (Consulte o script de exemplo para obter informações mais detalhadas.)

    1.  Informações do Azure.

    2.  Informações do Grupo de Sincronização.

    3.  Informações do Log Analytics. Encontre essas informações em portal do Azure | Configurações | Fontes Conectadas. Para obter mais informações sobre como enviar dados para o Log Analytics, consulte [Enviar dados para o Log Analytics com a API do Coletor de Dados HTTP (versão prévia)](../azure-monitor/platform/data-collector-api.md).

11. Execute o runbook no painel Teste. Verifique se ele foi bem-sucedido.

    Caso você encontre erros, verifique se você tem o último módulo do PowerShell instalado. Você pode instalar o módulo mais recente do PowerShell nos **Galeria de Módulos** na sua Conta de Automação.

12. Clique em **Publicar**

### <a name="schedule-the-runbook"></a>Agendar o runbook

Para agendar o runbook:

1.  No runbook, selecione a guia **Agendamentos** em Recursos.

2.  Selecione **Adicionar um Agendamento** na página Agendamentos.

3.  Selecione **Vincular um agendamento ao runbook**.

4.  Selecione **Criar um novo agendamento**.

5.  Defina **Recorrência** como Recorrente e defina o intervalo desejado. Use o mesmo intervalo aqui, no script e no Log Analytics.

6.  Selecione **Criar**.

### <a name="check-the-automation"></a>Verificar a automação

Para monitorar se a automação está funcionando conforme esperado, em **Visão Geral** de sua conta de automação, localize a exibição **Estatísticas de Trabalho** em **Monitoramento**. Fixe isto no painel para facilitar a visualização. As execuções com êxito do runbook são mostradas como “Concluídas” e as execuções com falha são mostradas como “Com Falha."

## <a name="create-a-log-analytics-reader-alert-for-email-notifications"></a>Criar um alerta do Leitor do Log Analytics para notificações por email

Para criar um alerta que usa o Log Analytics, realize as etapas a seguir. Como pré-requisito, você precisa ter o Log Analytics vinculado a um Workspace do Log Analytics.

1.  No portal do Azure, selecione **Pesquisa de Logs**.

2.  Crie uma consulta para selecionar os erros e avisos por grupo de sincronização dentro do intervalo selecionado. Por exemplo: 

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  Depois de executar a consulta, selecione o sino que indica **Alerta**.

4.  Em **Gerar alerta com base em**, selecione **Medição Métrica**.

    1.  Defina o Valor de Agregação como **Maior que**.

    2.  Após **Maior que**, insira o limite a ser decorrido antes que você receba notificações. Erros transitórios são esperados na Sincronização de Dados. Para reduzir o ruído, defina o limite para 5.

5.  Em **Ações**, defina **Notificação por email** como “Sim." Insira os destinatários de email desejados.

6.  Clique em **Salvar**. Agora, os destinatários especificados recebem notificações por email quando ocorrem erros.

## <a name="create-a-log-analytics-view-for-monitoring"></a>Criar uma exibição do Log Analytics para monitoramento

Esta etapa cria uma exibição do Log Analytics para monitorar visualmente todos os grupos de sincronização especificados. A exibição inclui vários componentes:

-   Um bloco de visão geral, que mostra quantos erros, êxitos e avisos existem em todos os grupos de sincronização.

-   Um bloco para todos os grupos de sincronização, que mostra a contagem de erros e avisos por grupo de sincronização. Grupos sem problemas não são exibidos neste bloco.

-   Um bloco para cada Grupo de Sincronização, que mostra o número de erros, êxitos e avisos, bem como as mensagens de erro recentes.

Para configurar a exibição do Log Analytics, faça o seguinte:

1.  Na home page do Log Analytics, selecione o sinal de adição à esquerda para abrir o **designer de exibição**.

2.  Selecione **Importação** na barra superior do designer de exibição. Em seguida, selecione o arquivo de exemplo “DataSyncLogOMSView”.

3.  A exibição de exemplo serve para gerenciar dois grupos de sincronização. Edite essa exibição de acordo com seu cenário. Clique em **Editar** e faça as seguintes alterações:

    1.  Crie novos objetos de “Rosca e Lista” por meio da Galeria, conforme necessário.

    2.  Em cada bloco, atualize as consultas com suas informações.

        1.  Em cada bloco, altere o intervalo de TimeStamp_t conforme desejado.

        2.  Nos blocos de cada Grupo de Sincronização, atualize os nomes do Grupo de Sincronização.

    3.  Em cada bloco, atualize o título, conforme necessário.

4.  Clique em **Salvar** e a exibição estará pronta.

## <a name="cost-of-this-solution"></a>Custo desta solução

Na maioria dos casos, essa solução é gratuita.

**Automação do Azure**: Pode haver um custo com a conta da Automação do Azure, dependendo do uso. Os primeiros 500 minutos do tempo de execução do trabalho por mês são gratuitos. Na maioria dos casos, espera-se que essa solução use menos de 500 minutos por mês. Para evitar encargos, agende o runbook para que ele seja executado em um intervalo de duas horas ou mais. Para obter mais informações, consulte [Preços da Automação](https://azure.microsoft.com/pricing/details/automation/).

**Log Analytics:** Pode haver um custo associado ao Log Analytics, dependendo do uso. A camada gratuita inclui 500 MB de dados ingeridos por dia. Na maioria dos casos, espera-se que essa solução ingira menos de 500 MB por dia. Para diminuir o uso, use a filtragem somente falha incluída no runbook. Caso você esteja usando mais de 500 MB por dia, faça atualização para a camada paga para evitar o risco da interrupção da análise quando o limite for atingido. Para obter mais informações, consulte [Preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Exemplos de código

Baixe os exemplos de código descritos neste artigo nos seguintes locais:

-   [Runbook do PowerShell do Log da Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Modo de exibição do Log Analytics de Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Sincronização de Dados SQL, veja:

-   Visão geral - [Sincronize dados em vários bancos de dados locais e na nuvem com o Azure SQL Data Sync](sql-database-sync-data.md)
-   Configurar sincronização de dados
    - No portal - [Tutorial: Configurar sincronização de dados SQL para sincronizar dados entre o banco de dados SQL e SQL Server local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Usar o PowerShell para sincronização entre vários banco de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um banco de dados local do SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agente de Sincronização de Dados - [Agente de Sincronização de Dados para Sincronização de Dados SQL do Azure](sql-database-data-sync-agent.md)
-   Práticas recomendadas - [Práticas recomendadas para a Sincronização de Dados SQL do Azure](sql-database-best-practices-data-sync.md)
-   Solucionar problemas - [Solucionar problemas com o SQL Data Sync do Azure](sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com Transact-SQL - [Automatize a replicação de alterações de esquema no Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Com o PowerShell - [usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

Para saber mais sobre o Banco de Dados SQL, veja:

-   [Visão geral do Banco de Dados SQL](sql-database-technical-overview.md)
-   [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
