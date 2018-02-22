---
title: "Como agendar o tempo de execução de integração do Azure-SSIS | Microsoft Docs"
description: "Este artigo descreve como agendar o início e a parada de um tempo de execução de integração do Azure-SSIS usando a Automação do Azure e o Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: article
ms.date: 01/25/2018
ms.author: spelluru
ms.openlocfilehash: 814ef63f317c2c0c9081579c16a12a908c05ff74
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-schedule-starting-and-stopping-of-an-azure-ssis-integration-runtime"></a>Como agendar o início e a parada de um tempo de execução de integração do Azure-SSIS 
A execução de um IR (tempo de execução de integração) do Azure-SSIS (SQL Server Integration Services) tem uma carga associada a ele. Portanto, convém executar o IR somente quando você precisar executar pacotes SSIS no Azure e parar quando não for mais necessário. Você poderá usar a interface do usuário do Data Factory ou do Azure PowerShell para [iniciar ou parar manualmente um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md)). Este artigo descreve como agendar o início e a parada de um IR (tempo de execução de integração) do Azure-SSIS usando a Automação do Azure e o Azure Data Factory. A seguir, são apresentadas as etapas de alto nível descritas neste artigo:

1. **Criar e testar um runbook de Automação do Azure.** Nesta etapa, você cria um runbook do PowerShell com o script que inicia ou para um IR do Azure-SSIS. Em seguida, você testa o runbook nos cenários INICIAR e PARAR e confirma se o IR inicia ou para. 
2. **Criar dois agendamentos para o runbook.** Para o primeiro agendamento, você configura o runbook com INICIAR como a operação. Para o segundo agendamento, configura o runbook com PARAR como a operação. Para ambos os agendamentos é necessário especificar a cadência na qual o runbook executa. Por exemplo, você pode querer agendar o primeiro para ser executado às 8h00 todos os dias e o segundo para ser executado às 23h00 todos os dias. Quando o primeiro runbook é executado, ele inicia o IR do Azure-SSIS. Quando o segundo runbook é executado, ele para o IR do Azure-SSIS. 
3. **Crie dois webhooks para o runbook**, um para a operação INICIAR e o outro para a operação PARAR. Use as URLs desses webhooks ao configurar atividades da Web em um pipeline do Data Factory. 
4. **Criar um pipeline do Data Factory**. O pipeline que você criar consistirá em quatro atividades. A primeira atividade da **Web** invoca o primeiro webhook para iniciar o IR do Azure-SSIS. A atividade de **Espera** aguarda 30 minutos (1800 segundos) para que o IR do Azure-SSIS inicie. A atividade de **Procedimento Armazenado** executa um script SQL que executa o pacote SSIS. A segunda atividade da **Web** para o IR do Azure-SSIS. Para obter mais informações sobre como invocar um pacote SSIS de um pipeline do Data Factory usando a atividade de Procedimento Armazenado, consulte [Invocar um pacote SSIS](how-to-invoke-ssis-package-stored-procedure-activity.md). Em seguida, você cria um gatilho de agendamento para agendar o pipeline a executar na cadência que você especificar.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço do Data Factory, que está disponível, consulte [Chamar pacotes do SSIS usando atividade de procedimento armazenado na versão 1](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

 
## <a name="prerequisites"></a>pré-requisitos
Se um tempo de execução de integração do Azure-SSIS ainda não estiver provisionado, provisione-o seguindo instruções no [tutorial](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-test-an-azure-automation-runbook"></a>Criar e testar um runbook de Automação do Azure
Nesta seção, você executa as seguintes etapas: 

1. Criar uma conta de Automação do Azure.
2. Criar uma runbook do PowerShell na conta de Automação do Azure. O script do PowerShell associado ao runbook inicia ou para um IR do Azure-SSIS baseado no comando que você especificou para o parâmetro OPERAÇÃO. 
3. Teste o runbook em ambos os cenários de início e parada para confirmar está funcionando. 

### <a name="create-an-azure-automation-account"></a>Criar uma conta de Automação do Azure
Se você não possuir uma conta de Automação do Azure, crie uma seguindo as instruções nesta etapa. Para obter as etapas detalhadas, consulte [Criar uma conta de Automação do Azure](../automation/automation-quickstart-create-account.md). Como parte dessa etapa, crie uma conta **Executar como do Azure** (uma entidade de serviço no seu Microsoft Azure Active Directory) e adicione-a à função **Colaborador** da sua assinatura do Azure. Certifique-se de que seja o mesmo da assinatura contendo o data factory que tem o IR do Azure-SSIS. A Automação do Azure usa essa conta para autenticar-se no Azure Resource Manager e operar em seus recursos. 

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface de usuário do Data Factory tem suporte apenas em navegadores da Web Microsoft Edge e Google Chrome.
2. Faça logon no [Portal do Azure](https://portal.azure.com/).    
3. Selecione **Novo** no menu esquerdo, selecione **Monitoramento + Gerenciamento** e selecione **Automação**. 

    ![Novo -> Monitoramento + Gerenciamento -> Automação](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. Na janela **Adicionar Conta de Automação**, execute as seguintes etapas: 

    1. Especifique um **nome** para a conta de automação. 
    2. Selecione a **assinatura** te tem o data factory com o IR do Azure-SSIS. 
    3. Para **Grupo de recursos**, selecione **Criar novo** para criar um novo grupo de recursos ou selecione **Usar existente** para selecionar um grupo de recursos existente. 
    4. Selecione um **local** para a conta de automação. 
    5. Confirme se **Criar Conta Executar como** está definida para **Sim**. Uma entidade de serviço é criada no seu Azure Active Directory. É adicionada à função **Colaborador** da sua assinatura do Azure
    6. Selecione **Fixar no Painel** para que você visualize no painel do portal. 
    7. Selecione **Criar**. 

        ![Novo -> Monitoramento + Gerenciamento -> Automação](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. Você verá o **Status de Implantação** no painel de controle e nas notificações. 
    
    ![Implantar automação](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. A página inicial da conta de automação será exibida após ter sido criada com êxito. 

    ![Home page da Automação do Azure](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>Importar módulos do Data Factory

1. Selecione **Módulos** na seção **RECURSOS COMPARTILHADOS** no menu esquerdo e verifique se você tem **AzureRM.Profile** e **AzureRM.DataFactoryV2** na lista de módulos. Se não tiver, selecione **Procurar na Galeria** na barra de ferramentas.

    ![Home page da Automação do Azure](./media/how-to-schedule-azure-ssis-integration-runtime/automation-modules.png)
2. Na janela **Procurar na Galeria** digite **AzureRM.Profile** na janela de pesquisa e pressione **ENTER**. Selecione **AzureRM.Profile** na lista. Em seguida, clique em **Importar** na barra de ferramentas. 

    ![Selecione AzureRM.Profile](./media/how-to-schedule-azure-ssis-integration-runtime/select-azurerm-profile.png)
1. Na janela **Importar**, selecione a opção **Eu concordo em atualizar todos os módulos do Azure** e clique em **OK**.  

    ![Importar AzureRM.Profile](./media/how-to-schedule-azure-ssis-integration-runtime/import-azurerm-profile.png)
4. Feche a janela para retornar à janela **Módulos**. Você deverá ver o status da importação na lista. Selecione **Atualizar** para atualizar a lista. Aguarde até que o **STATUS** seja exibido como **Disponível**.

    ![Importar status](./media/how-to-schedule-azure-ssis-integration-runtime/module-list-with-azurerm-profile.png)
1. Repita as etapas para importar o módulo **AzureRM.DataFactoryV2**. Confirme se o status desse módulo está definido como **Disponível** antes de continuar. 

    ![Status final de importação](./media/how-to-schedule-azure-ssis-integration-runtime/module-list-with-azurerm-datafactoryv2.png)

### <a name="create-a-powershell-runbook"></a>Criar runbook do PowerShell
O procedimento a seguir fornece as etapas para criar um runbook do PowerShell. O script associado ao runbook inicia ou para um IR do Azure-SSIS baseado no comando que você especificou para o parâmetro **OPERAÇÃO**. Esta seção não fornece todos os detalhes para criar um runbook. Para obter mais informações, consulte o artigo [Criar um runbook](../automation/automation-quickstart-create-runbook.md).

1. Alterne a guia **Runbooks** e selecione **+ Adicionar um runbook** da barra de tarefas. 

    ![Botão Adicionar um runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. Selecione **Criar um novo runbook**, e execute as seguintes etapas: 

    1. Para **Nome**, digite **StartStopAzureSsisRuntime**.
    2. Para **Tipo de runbook**, selecione **PowerShell**.
    3. Selecione **Criar**.
    
        ![Botão Adicionar um runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Copie/cole o script a seguir na janela de script do runbook. Salve e, em seguida, publique o runbook usando os botões **Salvar** e **Publicar** na barra de ferramentas. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Add-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

    ![Editar runbook do PowerShell](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
5. Teste o runbook selecionando o botão **Iniciar** na barra de ferramentas. 

    ![Botão Iniciar runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. Na janela **Iniciar runbook**, execute as seguintes etapas: 

    1. Para o **NOME DO GRUPO DE RECURSOS**, digite o nome do grupo de recursos com o data factory que possui o IR do Azure-SSIS. 
    2. Para o **NOME DE DATA FACTORY**, insira o nome do data factory que tem o IR do Azure-SSIS. 
    3. Para o **AZURESSISNAME**, insira o nome do IR do Azure-SSIS. 
    4. Para **OPERAÇÃO**, insira **INICIAR**. 
    5. Selecione **OK**.  

        ![Janela Iniciar runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. Na janela de trabalho, selecione o bloco **Saída**. Na janela de saída do trabalho, aguarde até visualizar a mensagem **##### Completed #####** depois de **##### Starting #####**. Iniciar um IR do Azure-SSIS demora aproximadamente 20 minutos. Feche a janela **Trabalho** e retorne para a janela **Runbook**.

    ![IR do Azure-SSIS - iniciado](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  Repita as duas etapas anteriores, mas usando **PARAR** como o valor para a **OPERAÇÃO**. Inicie o runbook novamente selecionando o botão **Iniciar** na barra de ferramentas. Especifique o nome do grupo de recursos, o nome de data factory e o nome do IR do Azure-SSIS. Para **OPERAÇÃO**, insira **PARAR**. 

    Na janela de saída do trabalho, espere até visualizar a mensagem **##### Completed #####** depois de **##### Stopping #####**. Parar um IR do Azure-SSIS não demora o tempo de início do IR do Azure-SSIS. Feche a janela **Trabalho** e retorne para a janela **Runbook**.

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>Criar agendamentos para o runbook iniciar/parar o IR do Azure-SSIS
Na seção anterior, você criou uma runbook de Automação do Azure que pode iniciar ou parar um IR do Azure-SSIS. Nesta seção, você cria dois agendamentos para o runbook. Ao configurar o primeiro agendamento, você especifica INICIAR para o parâmetro OPERAÇÃO. Da mesma forma, ao configurar o segundo agendamento, especifique PARAR para a OPERAÇÃO. Para obter as etapas detalhadas para criar o agendamento, consulte [Criar um agendamento](../automation/automation-schedules.md#creating-a-schedule).

1. Na janela **Runbook**, selecione **Agendamentos**, e selecione **+ Adicionar um agendamento** na barra de ferramentas. 

    ![IR do Azure-SSIS - iniciado](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. Na janela **Agendar runbook**, execute as seguintes etapas: 

    1. Selecione **Vincular um agendamento ao runbook**. 
    2. Selecione **Criar um novo agendamento**.
    3. Na janela **Novo agendamento**, digite **Iniciar IR diariamente** por **Nome**. 
    4. No **Início da seção**, para a hora, especifique um tempo de alguns minutos após a hora atual. 
    5. Para **Recorrência**, selecione **Recorrente**. 
    6. Na seção **Repetir a cada**, selecione **Dia**. 
    7. Selecione **Criar**. 

        ![Agendar para o início do IR do Azure-SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. Alterne para a guia **Parâmetros e configurações de execução**. Especifique o nome do grupo de recursos, o nome de data factory e o nome do IR do Azure-SSIS. Para **OPERAÇÃO**, insira **INICIAR**. Selecione **OK**. Selecione **OK** novamente para visualizar o agendamento na página **Agendamentos** do runbook. 

    ![Agendar para iniciar o IR do Azure-SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. Repita as duas etapas anteriores para criar um agendamento chamado **Parar IR diariamente**. Desta vez, especifique o tempo pelo menos 30 minutos após o tempo especificado para o agendamento **Iniciar IR diariamente**. Para **OPERAÇÃO**, especifique **PARAR**. 
5. Na janela **Runbook**, selecione **Trabalhos** no menu esquerdo. Você deverá visualizar os trabalhos criados pelos agendamentos nos horários especificados e os status. É possível ver os detalhes sobre o trabalho, como a saída semelhante ao visualizado quando você testou o runbook. 

    ![Agendar para iniciar o IR do Azure-SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. Após concluir o teste, desabilite os agendamentos editando-os e selecionando **NÃO** para **Habilitado**. Selecione **Agendamentos** no menu esquerdo, selecione o **Iniciar IR diariamente/Parar IR diariamente**e selecione **Não** para **Habilitado**. 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>Criar webhooks para iniciar e parar o IR do Azure- SSIS
Sigas as instruções em [Criar um webhook](../automation/automation-webhooks.md#creating-a-webhook) para criar dois webhooks para o runbook. Para o primeiro, especifique INICIAR como a operação e, para o segundo, especifique PARAR como a operação. Salve AS URLs para os webhooks em algum lugar (como um arquivo de texto ou um Bloco de anotações do OneNote). Você usa essas URLs ao configurar atividades da web no pipeline do Data Factory. A imagem a seguir mostra um exemplo de criar um webhook que inicia o IR do Azure-SSIS:

1. Na janela **Runbook**, selecione **Webhooks** do menu esquerdo e selecione **+ Adicionar Webhook** na barra de ferramentas. 

    ![Webhooks -> Adicionar Webhook](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. Na janela **Adicionar Webhook**, selecione **Criar novo webhook** e execute as seguintes ações: 

    1. Para **Nome**, insira **StartAzureSsisIR**. 
    2. Conforme se **Habilitado** está definido para **Sim**. 
    3. Copie a **URL** e salve-a em algum local. Essa etapa é importante. A URL não será mais exibida posteriormente. 
    4. Selecione **OK**. 

        ![Janela Novo Webhook](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. Alterne para a guia **Parâmetros e configurações de execução**. Especifique o nome do grupo de recursos, o nome de data factory e o nome do IR do Azure-SSIS. Para **OPERAÇÃO**, insira **INICIAR**. Clique em **OK**. Em seguida, clique em **Criar**. 

    ![Webhook - parâmetros e configurações de execução](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. Repita as três etapas anteriores para criar outro webhook chamado **StopAzureSsisIR**. Não se esqueça de copiar a URL. Ao especificar os parâmetros e executar configurações, digite **PARAR** para **OPERAÇÃO**. 

Você deverá ter duas URLs, uma para o webhook **StartAzureSsisIR** e outra para o webhook **StopAzureSsisIR**. É possível enviar uma solicitação HTTP POST a essas URLs para iniciar/parar o IR do Azure-SSIS. 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>Cria e agendar um pipeline do Data Factory que inicia/para o IR
Esta seção mostra como usar uma atividade da Web para invocar os webhooks criados na seção anterior.

O pipeline que você criar consistirá em quatro atividades. 

1. A primeira atividade da **Web** invoca o primeiro webhook para iniciar o IR do Azure-SSIS. 
2. A atividade de **Espera** aguarda 30 minutos (1800 segundos) para que o IR do Azure-SSIS inicie. 
3. A atividade de **Procedimento Armazenado** executa um script SQL que executa o pacote SSIS. A segunda atividade da **Web** para o IR do Azure-SSIS. Para obter mais informações sobre como invocar um pacote SSIS de um pipeline do Data Factory usando a atividade de Procedimento Armazenado, consulte [Invocar um pacote SSIS](how-to-invoke-ssis-package-stored-procedure-activity.md). 
4. A segunda atividade da **Web** invoca o webhook para parar o IR do Azure-SSIS. 

Após criar e testar o pipeline, você cria um gatilho de agendamento e associa-se ao pipeline. O gatilho de agendamento define um agendamento para o pipeline. Suponha que você crie um gatilho programado para ser executado diariamente às 11 horas. O gatilho executa no pipeline às 11 horas diariamente. O pipeline inicia o IR do Azure-SSIS, executa o pacote SSIS e, em seguida, para o IR do Azure-SSIS. 

### <a name="create-a-data-factory"></a>Criar uma data factory

1. Faça logon no [Portal do Azure](https://portal.azure.com/).    
2. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Na página **Novo data factory**, insira **MyAzureSsisDataFactory** como o **nome**. 
      
     ![Página de novo data factory](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você receber o erro a seguir, altere o nome do data factory (por exemplo, yournameMyAzureSsisDataFactory) e tente criar novamente. Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
      Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2 (Versão Prévia)** para a **versão**.
5. Selecione o **local** do data factory. Somente os locais com suporte para a criação de data factories são mostrados na lista.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.
8. No painel, você vê o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
   ![Página inicial da data factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Clique em **Criar e Monitorar** para iniciar a interface do usuário (IU) do Azure Data Factory em uma guia separada.

### <a name="create-a-pipeline"></a>Criar uma pipeline

1. Na página **Introdução**, selecione **Criar pipeline**. 

   ![Página Introdução](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. Na caixa de ferramentas **Atividades**, expanda **Geral**, arraste e solte a atividade da **Web** na superfície do designer de pipeline. Na guia **Geral** da janela **Propriedades** altere o nome da atividade para **StartIR**.

   ![Primeira atividade da Web - guia geral](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. Alterne para a guia **Configurações** na janela **Propriedades** e execute as seguintes ações: 

    1. Para **URL**, cole a URL para o webhook que inicia o IR do Azure-SSIS. 
    2. Para **Método**, selecione **POSTAR**. 
    3. Para **Corpo**, insira `{"message":"hello world"}`. 
   
        ![Primeira atividade da Web - guia de configurações](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)
4. Na caixa de ferramentas **Atividades**, expanda **Iteração e condições** e arraste e solte a atividade de **Espera** para a superfície de designer do pipeline. Na guia **Geral**, altere o nome da atividade para **WaitFor30Minutes**. 
5. Alterne para a guia **Configurações** na janela **Propriedades**. Para **Tempo de espera em segundos**, digite **1800**. 
6. Conecte a atividade da **Web** e a atividade de **Espera**. Para conectá-los, comece a arrastar na caixa quadrada verde anexada à atividade da Web para a atividade de Espera. 

    ![Conectar Web e Espera](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-wait.png)
5. Arraste e solte a atividade de Procedimento Armazenado da seção **Geral** da caixa de ferramentas **Atividades**. Defina o nome da atividade para **RunSSISPackage**. 
6. Alterne para a guia **SQL Account** na janela **Propriedades**. 
7. Para **Serviço vinculado**, clique em **+ Novo**.
8. Na janela **Novo Serviço Vinculado**, execute as seguintes ações: 

    1. Selecione **Banco de Dados SQL do Azure** para **Tipo**.
    2. Selecione o Azure SQL Server que hospeda o banco de dados **SSISDB** para o campo **Nome do servidor**. O processo de provisionamento do IR do Azure-SSIS cria um Catálogo SSIS (banco de dados SSISDB) no Azure SQL Server que você especificou.
    3. Selecione **SSISDB** para **Nome do Banco de Dados**.
    4. Para **Nome de usuário**, insira o nome do usuário que tem acesso ao banco de dados.
    5. Para **Senha**, insira a senha do usuário. 
    6. Teste a conexão com o banco de dados, clicando no botão **Testar conexão**.
    7. Salve o serviço vinculado. clicando no botão **Salvar**.
1. Na janela **Propriedades**, alterne para a guia **Procedimento Armazenado** da guia **Conta SQL** e execute as seguintes etapas: 

    1. Para **Nome do procedimento armazenado**, selecione a opção **Editar** e digite **sp_executesql**. 
    2. Clique em **+ Novo** na seção **Parâmetros do procedimento armazenado**. 
    3. Para o **nome** do parâmetro, insira **stmt**. 
    4. Para o **tipo** do parâmetro, insira **Cadeia de caracteres**. 
    5. Para o **valor** do parâmetro, insira a consulta SQL a seguir:

        Na consulta SQL, especifique os valores certos para os parâmetros **nom_da_pasta**, **nome_do_projeto** e **nome_do_pacote**. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END   
        ```
10. Conecte a atividade de **Espera** à atividade de **Procedimento Armazenado**. 

    ![Conecte as atividades de Espera e Procedimento Armazenado](./media/how-to-schedule-azure-ssis-integration-runtime/connect-wait-sproc.png)
11. Arraste e solte a atividade da **Web** à direita da atividade de **Procedimento Armazenado**. Defina o nome da atividade para **StopIR**. 
12. Alterne para a guia **Configurações** na janela **Propriedades** e execute as seguintes ações: 

    1. Para a **URL**, cole a URL para o webhook que para o IR do Azure-SSIS. 
    2. Para **Método**, selecione **POSTAR**. 
    3. Para **Corpo**, insira `{"message":"hello world"}`.  
4. Conecte a atividade de **Procedimento Armazenado** para a última atividade da **Web**.

    ![Pipeline completo](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
5. Valide as configurações do pipeline clicando em **Validar** na barra de ferramentas. Feche o **Relatório de Validação do Pipeline** clicando em **>>**. 

    ![Validar o pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>Executar teste do pipeline

1. Selecione **Execução de Teste** na barra de ferramentas do pipeline. Você vê a saída na janela **Saída** no painel inferior. 

    ![Execução de teste](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. Na página do **Runbook** da sua conta de Automação do Azure, é possível verificar se as tarefas executaram para iniciar e parar o IR do Azure-SSIS. 

    ![Trabalhos do runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. Inicialização do SQL Server Management Studio. Na janela **Conectar ao Servidor**, execute as seguintes ações: 

    1. Para **Nome do servidor**, especifique o **&lt;Banco de Dados SQL do Azure &gt;.database.windows.net**.
    2. Selecione **Opções >>**.
    3. Para **Conectar um banco de dados**, selecione **SSISDB**.
    4. Selecione **Conectar**. 
    5. Expanda **Catálogo do Integration Services** -> **SSISDB** -> Sua pasta -> **Projetos** -> Seu projeto SSIS -> **Pacotes**. 
    6. Clique com o botão direito do mouse no pacote SSIS e selecione **Relatórios** -> **Relatórios Padrão** -> **Todas as Execuções**. 
    7. Verifique se o pacote do SSIS executou. 

        ![Verificar a execução do pacote SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verfiy-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>Agendamento do pipeline 
Como agora o pipeline está funcionando conforme o esperado, você poderá criar um gatilho para executar esse pipeline em uma cadência especificada. Para obter detalhes sobre como associar um gatilho de agendamento com um pipeline, consulte [Disparar o pipeline em um agendamento](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. Na barra de ferramentas do pipeline, selecione **Disparar** e selecione **Novo/Editar**. 

    ![Disparar -> Novo/Editar](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. Na janela **Adicionar Gatilhos**, selecione **+ Novo**.

    ![Adicionar Gatilhos - Novo](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. Em **Novo Gatilho**, execute as seguintes ações: 

    1. Para **Nome**, especifique um nome para o gatilho. No exemplo a seguir, **Executar diariamente** é o nome do gatilho. 
    2. Para **Tipo**, selecione **Agendamento**. 
    3. Para **Data de início**, selecione uma data e hora de início. 
    4. Para **Recorrência**, especifique a cadência do gatilho. No exemplo a seguir, é uma vez por dia. 
    5. Para **Término**, você pode especificar a data e hora, selecionando a opção **Na Data**. 
    6. Selecione **Ativado**. O gatilho será ativado imediatamente após publicar a solução para o Data Factory. 
    7. Selecione **Avançar**.

        ![Disparar -> Novo/Editar](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. Na página **Parâmetros Execução de Gatilho**, revise o aviso e selecione **Concluir**. 
5. Publique a solução o Data Factory, selecionando **Publicar Tudo** no painel esquerdo. 

    ![Publicar Tudo](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)
6. Para monitorar execuções do gatilho e execuções do pipeline, use a guia **Monitor** à esquerda. Para obter etapas detalhadas, consulte [Monitorar o pipeline](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

    ![Execuções de pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
7. Para visualizar as atividades executadas associadas a uma execução do pipeline, selecione o primeiro link (**Exibir as Execuções da atividade**) na coluna **Ações**. É possível visualizar as quatro atividades executadas associadas a cada atividade no pipeline (a primeira atividade da Web, atividade de Espera, atividade de Procedimento Armazenado e a segunda atividade da Web). Para alternar de volta para a exibição das execuções do pipeline, selecione o link **Pipelines** na parte superior.

    ![Execuções de atividade](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
8. Também é possível visualizar as execuções do gatilho, selecionando **Execuções do gatilho** na lista suspensa próxima às **Execuções do pipeline** na parte superior. 

    ![Execuções de gatilho](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

## <a name="next-steps"></a>Próximas etapas
Consulte os artigos a seguir na documentação do SSIS: 

- [Implantar, executar e monitorar um pacote do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Conecte-se ao catálogo do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Agendar a execução do pacote no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Conecte-se a fontes de dados locais com a autenticação do Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)