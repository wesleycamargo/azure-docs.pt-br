---
title: "Monitorar a Sincronização de Dados SQL do Azure com o Log Analytics do OMS | Microsoft Docs"
description: "Saiba como monitorar a Sincronização de Dados SQL do Azure usando a análise de logs do OMS"
services: sql-database
ms.date: 11/7/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: e1099d2cd7eeccbe76d762028a0c5d5f95f53026
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2017
---
# <a name="monitor-azure-sql-data-sync-preview-with-oms-log-analytics"></a>Monitorar a Sincronização de Dados SQL do Azure (versão prévia) com o Log Analytics do OMS 

Para verificar o log de atividades da Sincronização de Dados SQL e detectar erros e avisos, anteriormente você precisava verificar a Sincronização de Dados SQL manualmente no portal do Azure ou usar o PowerShell ou a API REST. Siga as etapas deste artigo para configurar uma solução personalizada que melhora a experiência de monitoramento da Sincronização de Dados. Personalize essa solução de acordo com seu cenário.

Para obter uma visão geral da Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure (versão prévia)](sql-database-sync-data.md).

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Painel de Monitoramento para todos os Grupos de Sincronização 

Você não precisa mais examinar os logs de cada Grupo de Sincronização individualmente para procurar problemas. Você pode monitorar todos os Grupos de Sincronização em qualquer uma de suas assinaturas em um único local usando uma exibição personalizada do OMS (Operations Management Suite). Essa exibição apresenta as informações importantes para os clientes da Sincronização de Dados SQL.

![Painel de monitoramento da Sincronização de Dados](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.jpg)

## <a name="automated-email-notifications"></a>Notificações automatizadas por email

Você não precisa mais verificar o log manualmente no Portal do Azure ou por meio do PowerShell ou da API REST. Aproveitando o [Log Analytics do OMS](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), você pode criar alertas que vão diretamente para os endereços de email daqueles que precisam vê-los em caso de erro.

![Notificações por email da Sincronização de Dados](media/sql-database-sync-monitor-oms/sync-email-notifications.jpg)

## <a name="how-do-you-set-this-up"></a>Como isso é configurado? 

Implemente um solução de monitoramento personalizado do OMS para a Sincronização de Dados SQL em menos de uma hora, realizando os procedimentos a seguir.

Você precisará configurar três componentes:

-   Um runbook do PowerShell para alimentar os dados de log da Sincronização de Dados SQL no OMS.

-   Um alerta do Log Analytics do OMS para notificações por email.

-   Uma Exibição do OMS para monitoramento.

### <a name="samples-to-download"></a>Amostras para download

Baixe os dois seguintes exemplos:

-   [Runbook do PowerShell do Log da Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Exibição do OMS do Log da Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Pré-requisitos

Verifique se você configurou o seguinte:

-   Uma conta da Automação do Azure

-   Log Analytics vinculado ao Espaço de Trabalho do OMS

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Runbook do PowerShell para obter o Log da Sincronização de Dados SQL 

Use um runbook do PowerShell hospedado na Automação do Azure para extrair os dados de log da Sincronização de Dados SQL e enviá-los ao OMS. Um script de exemplo é incluído. Como pré-requisito, você precisa ter uma conta da Automação do Azure. Em seguida, você precisa criar um runbook e agendá-lo para execução. 

### <a name="create-a-runbook"></a>Criar um runbook

Para obter mais informações sobre como criar um runbook, consulte [Meu primeiro runbook do PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Em sua conta da Automação do Azure, selecione a guia **Runbooks** em Automação de Processo.

2.  Selecione **Adicionar um Runbook** no canto superior esquerdo da página Runbooks.

3.  Selecione **Importar um runbook existente**.

4.  Em **Arquivo de runbook**, use o determinado arquivo `DataSyncLogPowerShellRunbook`. Defina o **Tipo de runbook** como `PowerShell`. Forneça um nome para o runbook.

5.  Selecione **Criar**. Agora você tem um runbook.

6.  Em sua Conta da Automação do Azure, selecione a guia **Variáveis** em Recursos Compartilhados.

7.  Selecione **Adicionar uma variável** na página Variáveis. Precisamos criar uma variável para armazenar o último tempo de execução para o runbook. Caso você tenha vários runbooks, precisará de uma variável para cada runbook.

8.  Defina o nome da variável como `DataSyncLogLastUpdatedTime` e defina seu Tipo como DateTime.

9.  Selecione o runbook e clique no botão Editar na parte superior da página.

10. Faça as alterações necessárias em sua conta e na configuração da Sincronização de Dados SQL. (Consulte o script de exemplo para obter informações mais detalhadas.)

    1.  Informações do Azure.

    2.  Informações do Grupo de Sincronização.

    3.  Informações do OMS. Encontre essas informações no Portal do OMS | Configurações | Fontes Conectadas. Para obter mais informações sobre como enviar dados para o Log Analytics, consulte [Enviar dados para o Log Analytics com a API do Coletor de Dados HTTP (visualização pública)](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api).

11. Execute o runbook no painel Teste. Verifique se ele foi bem-sucedido.

    Caso você encontre erros, verifique se você tem o último módulo do PowerShell instalado. Faça isso na **Galeria de Módulos** de sua Conta de Automação.

12. Clique em **Publicar**

### <a name="schedule-the-runbook"></a>Agendar o runbook

Para agendar o runbook:

1.  No runbook, selecione a guia **Agendamentos** em Recursos.

2.  Selecione **Adicionar um Agendamento** na página Agendamentos.

3.  Selecione **Vincular um agendamento ao runbook**.

4.  Selecione **Criar um novo agendamento**.

5.  Defina **Recorrência** como Recorrente e defina o intervalo desejado. Use o mesmo intervalo aqui, no script e no OMS.

6.  Selecione **Criar**.

### <a name="check-the-automation"></a>Verificar a automação

Para monitorar se a automação está funcionando conforme esperado, em **Visão Geral** de sua conta de automação, localize a exibição **Estatísticas de Trabalho** em **Monitoramento**. Fixe isto no painel para facilitar a visualização. As execuções com êxito do runbook são mostradas como “Concluídas” e as execuções com falha são mostradas como “Com Falha”.

## <a name="create-an-oms-log-reader-alert-for-email-notifications"></a>Criar um alerta do Leitor de Log do OMS para notificações por email

Para criar um alerta que usa o Log Analytics do OMS, realize as etapas a seguir. Como pré-requisito, você precisa ter o Log Analytics vinculado a um Espaço de Trabalho do OMS.

1.  No portal do OMS, selecione **Pesquisa de Logs**.

2.  Crie uma consulta para selecionar os erros e avisos por grupo de sincronização dentro do intervalo selecionado. Por exemplo:

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  Depois de executar a consulta, selecione o sino que indica **Alerta**.

4.  Em **Gerar alerta com base em**, selecione **Medição Métrica**.

    1.  Defina o Valor de Agregação como **Maior que**.

    2.  Após **Maior que**, insira o limite a ser decorrido antes que você receba notificações. Erros transitórios são esperados na Sincronização de Dados. Recomendamos que você defina o limite como 5 para reduzir o ruído.

5.  Em **Ações**, defina **Notificação por email** como “Sim”. Insira os destinatários de email desejados.

6.  Clique em **Salvar**. Agora, os destinatários especificados recebem notificações por email quando ocorrem erros.

## <a name="create-an-oms-view-for-monitoring"></a>Criar uma exibição do OMS para monitoramento

Esta etapa cria uma exibição do OMS para monitorar visualmente todos os grupos de sincronização especificados. A exibição inclui vários componentes:

-   Um bloco de visão geral, que mostra quantos erros, êxitos e avisos existem em todos os grupos de sincronização.

-   Um bloco para todos os grupos de sincronização, que mostra a contagem de erros e avisos por grupo de sincronização. Grupos sem problemas não são exibidos neste bloco.

-   Um bloco para cada Grupo de Sincronização, que mostra o número de erros, êxitos e avisos, bem como as mensagens de erro recentes.

Para configurar a exibição do OMS, siga estas etapas:

1.  Na home page do OMS, selecione o sinal de adição à esquerda para abrir o **designer de exibição**.

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

**Automação do Azure:** pode haver um custo com a conta da Automação do Azure, dependendo do uso. Os primeiros 500 minutos do tempo de execução do trabalho por mês são gratuitos. Na maioria dos casos, espera-se que essa solução use menos de 500 minutos por mês. Para evitar encargos, agende o runbook para que ele seja executado em um intervalo de duas horas ou mais. Para obter mais informações, consulte [Preços da Automação](https://azure.microsoft.com/pricing/details/automation/).

**Log Analytics do OMS:** pode haver um custo associado ao OMS, dependendo do uso. A camada gratuita inclui 500 MB de dados ingeridos por dia. Na maioria dos casos, espera-se que essa solução ingira menos de 500 MB por dia. Para diminuir o uso, use a filtragem somente falha incluída no runbook. Caso você esteja usando mais de 500 MB por dia, faça atualização para a camada paga para evitar o risco da interrupção da análise quando o limite for atingido. Para obter mais informações, consulte [Preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Exemplos de código

Baixe os exemplos de código descritos neste artigo nos seguintes locais:

-   [Runbook do PowerShell do Log da Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Exibição do OMS do Log da Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Próximas etapas
ou para obter mais informações sobre a Sincronização de Dados SQL, consulte:

-   [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md)
-   [Introdução à Sincronização de Dados SQL do Azure](sql-database-get-started-sql-data-sync.md)
-   [Solucionar problemas com a Sincronização de Dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

-   Conclua os exemplos do PowerShell que mostram como configurar a Sincronização de Dados SQL:
    -   [Usar o PowerShell para sincronização entre vários banco de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um banco de dados local do SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Baixe a documentação da API REST de Sincronização de Dados SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para saber mais sobre o Banco de Dados SQL, veja:

-   [Visão geral do Banco de Dados SQL](sql-database-technical-overview.md)
-   [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
