---
title: Coletando dados de Log Analytics com um runbook na Automação do Azure | Microsoft Docs
description: Tutorial passo a passo que orienta a criação de um runbook na Automação do Azure para coletar dados para o repositório para análise serão analisados pelo Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 67378a5911e5bd83888342aa3773f7f5ed4ccf29
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60453898"
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Coletar dados no Log Analytics com um runbook na Automação do Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Você pode coletar uma quantidade significativa de dados no Log Analytics de uma variedade de fontes, inclusive [fontes de dados](../../azure-monitor/platform/agent-data-sources.md) nos agentes e também os [dados coletados do Azure](../../azure-monitor/platform/collect-azure-metrics-logs.md). Porém, há cenários em que você precisa coletar dados que não estão acessíveis por meio dessas fontes padrão. Nesses casos, você pode usar a [API do Coletor de Dados HTTP](../../azure-monitor/platform/data-collector-api.md) para gravar dados ao Log Analytics de qualquer cliente de API REST. Um método comum para realizar essa coleta de dados é usar um runbook na Automação do Azure.

Este tutorial explica o processo para criar e agendar um runbook na Automação do Azure para gravar os dados no Log Analytics.

## <a name="prerequisites"></a>Pré-requisitos
Esse cenário exige os seguintes recursos configurados na sua assinatura do Azure. Ambos podem ser uma conta gratuita.

- [Workspace do Log Analytics](../../azure-monitor/learn/quick-create-workspace.md).
- [Conta de automação do Azure](../..//automation/automation-quickstart-create-account.md).

## <a name="overview-of-scenario"></a>Visão geral do cenário
Para este tutorial, você escreverá um runbook que coleta informações sobre trabalhos de Automação. Os runbooks na Automação do Azure são implementados com o PowerShell, portanto, comece gravando e testando um script no editor da Automação do Azure. Depois de confirmar que você está coletando as informações necessárias, você gravará esses dados no Log Analytics e verificará o tipo de dados personalizado. Por fim, você criará um agendamento para iniciar o runbook em intervalos regulares.

> [!NOTE]
> Você pode configurar a automação do Azure para enviar informações de trabalho para o Log Analytics sem esse runbook. Esse cenário é usado principalmente para oferecer suporte ao tutorial e é recomendável que você envie os dados para um workspace de teste.

## <a name="1-install-data-collector-api-module"></a>1. Instalar o módulo de API do Coletor de Dados
Cada [solicitação da API do Coletor de Dados HTTP](../../azure-monitor/platform/data-collector-api.md#create-a-request) deve ser formatado corretamente e inclui um cabeçalho de autorização. Você pode fazer isso em seu runbook, mas pode reduzir a quantidade de código necessária para usar um módulo que simplifica esse processo. Um módulo que você pode usar é o [módulo OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI) na Galeria do PowerShell.

Para usar um [módulo](../../automation/automation-integration-modules.md) em um runbook, ele deverá ser instalado na sua conta de Automação.  Qualquer runbook na mesma conta pode usar as funções no módulo. Você pode instalar um novo módulo selecionando **Ativos** > **Módulos** > **Adicionar um módulo** na sua conta de Automação.

No entanto, a Galeria do PowerShell oferece uma opção rápida para implantar um módulo diretamente em sua conta de automação para que você possa usar essa opção para este tutorial.

![Módulo OMSIngestionAPI](media/runbook-datacollect/OMSIngestionAPI.png)

1. Acesse a [Galeria do PowerShell](https://www.powershellgallery.com/).
2. Procure **OMSIngestionAPI**.
3. Clique no botão **Implantar a Automação do Azure**.
4. Selecione sua conta de automação e clique em **OK** para instalar o módulo.

## <a name="2-create-automation-variables"></a>2. Criar variáveis de Automação
As [variáveis de Automação](../../automation/automation-variables.md) contêm valores que podem ser usados por todos os runbooks na sua conta de Automação. Eles tornam os runbooks mais flexíveis permitindo que você altere esses valores sem editar o runbook real. Todas as solicitações da API do Coletor de Dados HTTP exige a identificação e a chave do espaço de trabalho do Log Analytics e os ativos de variável são ideais para armazenar essas informações.

![variáveis](media/runbook-datacollect/variables.png)

1. No portal do Azure, abra sua Conta de Automação.
2. Selecione **Variáveis** em **Recursos Compartilhados**.
2. Clique em **Adicionar uma variável** e crie duas variáveis na tabela a seguir.

| Propriedade | Valor da ID do Workspace | Valor da Chave do Workspace |
|:--|:--|:--|
| NOME | WorkspaceId | WorkspaceKey |
| Type | Cadeia de caracteres | Cadeia de caracteres |
| Valor | Cole a ID do espaço de trabalho do seu espaço de trabalho do Log Analytics. | Cole com a chave primária ou secundária do seu espaço de trabalho do Log Analytics. |
| Criptografado | Não  | Sim |

## <a name="3-create-runbook"></a>3. Criar runbook

A Automação do Azure tem um editor no portal onde você pode editar e testar seu runbook. Você tem a opção de usar o editor de scripts para trabalhar com o [PowerShell diretamente](../../automation/automation-edit-textual-runbook.md) ou [criar um runbook gráfico](../../automation/automation-graphical-authoring-intro.md). Para este tutorial, você trabalhará com um script do PowerShell.

![Editar runbook](media/runbook-datacollect/edit-runbook.png)

1. Abra sua conta de Automação.
2. Clique em **Runbooks** > **Adicionar um runbook** > **Criar um novo runbook**.
3. Para o nome do runbook, digite **Collect-Automation-jobs**. Para o tipo de runbook, selecione **PowerShell**.
4. Clique em **Criar** para criar o runbook e iniciar o editor.
5. Copie e cole o seguinte código no runbook. Consulte os comentários no script para obter a explicação do código.
    ```
    # Get information required for the automation account from parameter values when the runbook is started.
    Param
    (
        [Parameter(Mandatory = $True)]
        [string]$resourceGroupName,
        [Parameter(Mandatory = $True)]
        [string]$automationAccountName
    )
    
    # Authenticate to the Automation account using the Azure connection created when the Automation account was created.
    # Code copied from the runbook AzureAutomationTutorial.
    $connectionName = "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName
    Connect-AzAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    
    # Set the $VerbosePreference variable so that we get verbose output in test environment.
    $VerbosePreference = "Continue"
    
    # Get information required for Log Analytics workspace from Automation variables.
    $customerId = Get-AutomationVariable -Name 'WorkspaceID'
    $sharedKey = Get-AutomationVariable -Name 'WorkspaceKey'
    
    # Set the name of the record type.
    $logType = "AutomationJob"
    
    # Get the jobs from the past hour.
    $jobs = Get-AzAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
    
    if ($jobs -ne $null) {
        # Convert the job data to json
        $body = $jobs | ConvertTo-Json
    
        # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
        Write-Verbose $body
    
        # Send the data to Log Analytics.
        Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
    }
    ```

## <a name="4-test-runbook"></a>4. Runbook de teste
A Automação do Azure inclui um ambiente de [testar seu runbook](../../automation/automation-testing-runbook.md) antes de publicá-lo. Você pode inspecionar os dados coletados pelo runbook e verificar se ele grava no Log Analytics conforme o esperado antes de publicá-lo em produção.

![Runbook de teste](media/runbook-datacollect/test-runbook.png)

1. Clique em **Salvar** para salvar o runbook.
1. Clique em **Painel de teste** para abrir o runbook no ambiente de teste.
1. Como o runbook tem parâmetros, você será solicitado a inserir valores para eles. Insira o nome do grupo de recursos e a conta de automação da qual você coletará informações do trabalho.
1. Clique em **Iniciar** para iniciar o runbook.
1. O runbook iniciará com um status de **Em fila** antes da transferência para **Executando**.
1. O runbook deve exibir a saída detalhada com os trabalhos coletados no formato json. Se nenhum trabalho estiver listado, isso significará que talvez nenhum trabalho tenha sido criado na conta de automação na última hora. Tente iniciar qualquer runbook na conta de automação e execute o teste novamente.
1. Verifique se a saída não mostra todos os erros no comando post para o Log Analytics. Você deve ver uma página semelhante a esta.

    ![Saída de postagem](media/runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. Verificar registros no Log Analytics
Após o runbook ter sido concluído no teste e você ter verificado que a saída foi recebida com êxito, você poderá verificar se os registros foram criados usando uma [pesquisa de log no Log Analytics](../../azure-monitor/log-query/log-query-overview.md).

![Saída de log](media/runbook-datacollect/log-output.png)

1. No Portal do Azure, selecione o espaço de trabalho do Log Analytics.
2. Clique em **Pesquisa de Logs**.
3. Digite o seguinte comando `Type=AutomationJob_CL` e clique no botão de pesquisa. Observe que o tipo de registro inclui _CL, que não foi especificado no script. Esse sufixo é acrescentado automaticamente para o tipo de registro para indicar que ele é um tipo de registro personalizado.
4. Você deve ver um ou mais registros retornados indicando que o runbook está funcionando conforme o esperado.

## <a name="6-publish-the-runbook"></a>6. Publicar o runbook
Depois de verificar se o runbook está funcionando corretamente, você precisará publicá-lo para poder executá-lo em produção. Você pode continuar a editar e testar o runbook sem modificar a versão publicada.

![Publicar runbook](media/runbook-datacollect/publish-runbook.png)

1. Retorne para sua conta de automação.
2. Clique em **Runbooks** e selecione **Collect-Automation-jobs**.
3. Clique em **Editar** e **Publicar**.
4. Clique em **Sim** quando for solicitado para verificar se deseja substituir a versão anteriormente publicada.

## <a name="7-set-logging-options"></a>7. Definir opções de log
Para teste, você conseguiu exibir [saída detalhada](../../automation/automation-runbook-output-and-messages.md#message-streams) porque definiu a variável $VerbosePreference no script. Para produção, você precisa definir as propriedades de log para o runbook se deseja exibir a saída detalhada. Para o runbook usado neste tutorial, isso exibirá os dados json enviados para o Log Analytics.

![Log e rastreamento](media/runbook-datacollect/logging.png)

1. Nas propriedades para o seu runbook, selecione **Log e rastreamento** em **Configurações do Runbook**.
2. Altere a configuração para **Registros detalhados de Log** para **Ativado**.
3. Clique em **Salvar**.

## <a name="8-schedule-runbook"></a>8. Agendar runbook
A maneira mais comum para iniciar um runbook que coleta dados de monitoramento é agendá-lo para ser executado automaticamente. Você pode fazer isso criando uma [agenda na Automação do Azure](../../automation/automation-schedules.md) e anexá-la ao seu runbook.

![Agendar runbook](media/runbook-datacollect/schedule-runbook.png)

1. Nas propriedades do seu runbook, selecione **Agendas** em **Recursos**.
2. Clique em **Adicionar uma agenda** > **Vincular uma agenda ao runbook** > **Criar uma nova agenda**.
5. Digite os seguintes valores para o agendamento e clique em **Criar**.

| Propriedade | Valor |
|:--|:--|
| NOME | AutomationJobs-Hourly |
| Inicia | Selecione qualquer horário pelo menos 5 minutos após a hora atual. |
| Recorrência | Recorrente |
| Repetir a cada | 1 hora |
| Definir a validade | Não  |

Depois de criar a agenda, você precisará definir os valores de parâmetro que serão usados sempre que essa agenda iniciar o runbook.

1. Clique em **Configurar parâmetros e configurações de execução**.
1. Preencha os valores para **ResourceGroupName** e **AutomationAccountName**.
1. Clique em **OK**.

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Verificar se o runbook é iniciado no agendamento
Toda vez que um runbook é iniciado, [um trabalho é criado](../../automation/automation-runbook-execution.md) e qualquer saída é registrada. Na verdade, esses são os mesmos trabalhos que o runbook está coletando. Você pode verificar se o runbook é iniciado conforme o esperado, verificando os trabalhos para o runbook após a hora de início da agenda.

![Trabalhos](media/runbook-datacollect/jobs.png)

1. Nas propriedades do seu runbook, selecione **Trabalhos** em **Recursos**.
2. Você deverá ver uma lista dos trabalhos para cada vez que o runbook foi iniciado.
3. Clique em um dos trabalhos para exibir seus detalhes.
4. Clique em **Todos os logs** para exibir os logs e a saída do runbook.
5. Role para baixo para localizar uma entrada semelhante à imagem a seguir.<br>![Detalhado](media/runbook-datacollect/verbose.png)
6. Clique nessa entrada para exibir os dados JSON detalhados que foram enviados para o Log Analytics.

## <a name="next-steps"></a>Próximas etapas
- Use [Criador de Modos de Exibição](../../azure-monitor/platform/view-designer.md) para criar uma exibição exibindo os dados coletados no repositório do Log Analytics.
- Empacotar seu runbook em um [solução de gerenciamento](../../azure-monitor/insights/solutions-creating.md) para distribuir para os clientes.
- Saiba mais sobre o [Log Analytics](https://docs.microsoft.com/azure/log-analytics/).
- Saiba mais sobre a [Automação do Azure](https://docs.microsoft.com/azure/automation/).
- Saiba mais sobre a [API do Coletor de Dados HTTP](../../azure-monitor/platform/data-collector-api.md).
