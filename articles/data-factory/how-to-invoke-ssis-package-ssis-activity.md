---
title: Executar o pacotes SSIS com a atividade de Executar Pacote do SSIS – Azure | Microsoft Docs
description: Este artigo descreve como executar um pacote SSIS (SQL Server Integration Services) em um pipeline do Azure Data Factory usando a Atividade de Executar Pacote do SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: db5941528eedd10cf252607dbe2160bd498a70de
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951960"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Executar um pacote do SSIS com a atividade Executar Pacote do SSIS no Azure Data Factory
Este artigo descreve como executar um pacote SSIS em um pipeline do Azure Data Factory usando uma atividade de Executar Pacote do SSIS. 

## <a name="prerequisites"></a>pré-requisitos

### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure 
Este artigo passo a passo usa um banco de dados SQL do Azure que hospeda o catálogo do SSIS. Também é possível usar uma Instância Gerenciada do Azure SQL (versão prévia).

## <a name="create-an-azure-ssis-integration-runtime"></a>Criar um Integration Runtime do Azure-SSIS
Crie um Integration Runtime do Azure-SSIS, caso você não tenha um, seguindo as instruções passo a passo no [Tutorial: Implantar pacotes do SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Interface do usuário do Data Factory no Portal do Azure
Nesta seção, você usa a interface do usuário do Data Factory para criar um pipeline do Data Factory com uma atividade de Executar Pacote do SSIS que executa um pacote SSIS.

### <a name="create-a-data-factory"></a>Criar uma data factory
A primeira etapa é criar uma data factory usando o Portal do Azure. 

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
2. Navegue até o [Portal do Azure](https://portal.azure.com). 
3. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, insira **ADFTutorialDataFactory** no campo **nome**. 
      
     ![Página de novo data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se a seguinte mensagem de erro for exibida para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory). Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
     ![Erro - nome não disponível](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
    Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2** para a **versão**.
5. Selecione o **local** do data factory. Apenas os locais com suporte do Data Factory são mostrados na lista suspensa. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outros locais.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.
8. No painel, você vê o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
    ![Página inicial do data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Clique no bloco **Criar e Monitorar** para iniciar o aplicativo de interface do usuário (IU) do Azure Data Factory em uma guia separada. 

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Criar um pipeline com uma atividade Executar Pacote do SSIS
Nesta etapa, você usa a interface do usuário do Data Factory para criar um pipeline. Você adiciona uma atividade de Executar Pacote do SSIS ao pipeline e a configura para executar o pacote SSIS. 

1. Na página de introdução, clique em **Criar pipeline**: 

    ![Página Introdução](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e solte a atividade **Executar Pacote SSIS** para a superfície do designer de pipeline. 

   ![Arraste a Atividade do SSIS para a superfície do designer](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Na guia **Geral** de propriedades para a atividade de Executar Pacote do SSIS, forneça um nome e uma descrição para a atividade. Defina o tempo limite e os valores de repetição opcionais.

    ![Definir propriedades na guia Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Na guia **Configurações** de propriedades para a atividade de Executar Pacote do SSIS, selecione o tempo de execução de integração do Azure-SSIS associado ao banco de dados `SSISDB`, em que o pacote é implantado. Forneça o caminho do pacote no banco de dados `SSISDB` no formato `<folder name>/<project name>/<package name>.dtsx`. Opcionalmente, especifique a execução de 32 bits e um nível de log predefinido ou personalizado e forneça um caminho de ambiente no formato `<folder name>/<environment name>`.

    ![Definir propriedades na guia Configurações](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. Para validar a configuração do pipeline, clique em **Validar** na barra de ferramentas. Para fechar o **Relatório de validação do pipeline** clique em **>>**.

6. Publique o pipeline para Data Factory, clicando no botão **Publicar Tudo**. 

### <a name="optionally-parameterize-the-activity"></a>Opcionalmente, crie parâmetros para a atividade

Opcionalmente, atribua valores, expressões ou funções, que podem se referir a variáveis do sistema do Data Factory, aos parâmetros de projeto ou de pacote no formato JSON usando o botão **Exibir Código-fonte** na parte inferior da caixa da atividade Executar Pacote SSIS ou o botão **Código** no canto superior direito da área do pipeline. Por exemplo, você pode atribuir parâmetros de pipeline do Data Factory ao seu projeto do SSIS ou parâmetros de pacote, conforme mostrado nas seguintes capturas de tela:

![Editar o script JSON para a atividade Executar Pacote SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters.png)

![Adicionar parâmetros à atividade Executar Pacote do SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters2.png)

### <a name="run-and-monitor-the-pipeline"></a>Executar e monitorar o pipeline
Nesta seção, você dispara uma execução do pipeline e, em seguida, faz o monitoramento. 

1. Para disparar uma execução de pipeline, clique em **Disparar** na barra de ferramentas e clique em **Disparar agora**. 

    ![Disparar agora](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Na janela **Execução de Pipeline**, selecione **Concluir**. 

3. Alterne para a guia **Monitorar** à esquerda. Você verá o pipeline de execução e seu status junto com outras informações (como a Hora de início da execução). Para atualizar o modo de exibição, clique em **Atualizar**.

    ![Execuções de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

4. Clique no link **Exibir Execuções da atividade** na coluna **Ações**. Você verá apenas uma execução de atividade, pois o pipeline tem apenas uma atividade (Executar Pacote do SSIS).

    ![Execuções de atividade](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

5. É possível executar a seguinte **consulta** no banco de dados SSISDB em seu servidor SQL do Azure para verificar se o pacote foi executado. 

    ```sql
    select * from catalog.executions
    ```

    ![Verificar as execuções do pacote](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

6. Você também pode obter a ID de execução do SSISDB da saída da execução de atividade do pipeline e usá-la para verificar os logs de execução mais abrangentes e mensagens de erro no SSMS.

    ![Obter a ID de execução.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

> [!NOTE]
> Também é possível criar um gatilho agendado para o pipeline, de modo que o pipeline seja executado em um agendamento (por hora, diariamente etc.). Para um exemplo, consulte [Criar uma data factory - Interface do Usuário do Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell
Nesta seção, você usa o Azure PowerShell para criar um pipeline do Data Factory com uma atividade do SSIS que invoca um pacote SSIS. 

Instale os módulos mais recentes do Azure PowerShell seguindo as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps). 

### <a name="create-a-data-factory"></a>Criar uma data factory
Você pode usar a mesma fábrica de dados que contém o IR do Azure-SSIS ou criar uma fábrica de dados separada. O procedimento a seguir fornece as etapas para criar uma fábrica de dados. Você cria um pipeline com uma atividade SSIS neste data factory. A atividade SSIS executa seu pacote SSIS. 

1. Defina uma variável para o nome do grupo de recursos que você usa nos comandos do PowerShell posteriormente. Copie o seguinte texto de comando para o PowerShell, especifique um nome para o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) entre aspas duplas e, em seguida, execute o comando. Por exemplo: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$ResourceGroupName` e execute o comando novamente
2. Para criar o grupo de recursos do Azure, execute o seguinte comando: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$ResourceGroupName` e execute o comando novamente. 
3. Defina uma variável para o nome do data factory. 

    > [!IMPORTANT]
    >  Atualize o Nome do data factory para ser globalmente exclusivo. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Para criar o data factory, execute o cmdlet **Set-AzureRmDataFactoryV2** a seguir usando a propriedade Location e ResourceGroupName da variável $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
                                            -Location $ResGrp.Location `
                                            -Name $dataFactoryName 
    ```

Observe os seguintes pontos:

* O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para criar instâncias de Data Factory, a conta de usuário usada para fazer logon no Azure deve ser um membro das funções **colaborador** ou **proprietário**, ou um **administrador** da assinatura do Azure.
* Para obter uma lista de regiões do Azure no qual o Data Factory está disponível no momento, selecione as regiões que relevantes para você na página a seguir e, em seguida, expanda **Análise** para localizar **Data Factory**: [ Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.

### <a name="create-a-pipeline-with-an-ssis-activity"></a>Criar um pipeline com uma atividade do SSIS 
Nesta etapa, você cria um pipeline com a atividade do SSIS. A atividade é executada em seu pacote SSIS. 

1. Crie um arquivo JSON denominado **RunSSISPackagePipeline.json** na pasta **C:\ADF\RunSSISPackage** com o conteúdo semelhante ao deste exemplo:

    > [!IMPORTANT]
    > Substitua os nomes de objeto, as descrições e os caminhos, os valores de propriedade e de parâmetro, as senhas e outros valores variáveis antes de salvar o arquivo. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [{
                "name": "mySSISActivity",
                "description": "My SSIS package/activity description",
                "type": "ExecuteSSISPackage",
                "typeProperties": {
                    "connectVia": {
                        "referenceName": "myAzureSSISIR",
                        "type": "IntegrationRuntimeReference"
                    },
                    "runtime": "x64",
                    "loggingLevel": "Basic",
                    "packageLocation": {
                        "packagePath": "FolderName/ProjectName/PackageName.dtsx"            
                    },
                    "environmentPath":   "FolderName/EnvironmentName",
                    "projectParameters": {
                        "project_param_1": {
                            "value": "123"
                        }
                    },
                    "packageParameters": {
                        "package_param_1": {
                            "value": "345"
                        }
                    },
                    "projectConnectionManagers": {
                        "MyAdonetCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "abc"
                                }
                            }
                        }
                    },
                    "packageConnectionManagers": {
                        "MyOledbCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "def"
                                }
                            }
                        }
                    },
                    "propertyOverrides": {
                        "\\PackageName.dtsx\\MaxConcurrentExecutables ": {
                            "value": 8,
                            "isSensitive": false
                        }
                    }
                },
                "policy": {
                    "timeout": "0.01:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30
                }
            }]
        }
    }
    ```

2.  No Azure PowerShell, mude para a pasta `C:\ADF\RunSSISPackage`.

3. Para criar o pipeline **RunSSISPackagePipeline**, execute o cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                   -ResourceGroupName $ResGrp.ResourceGroupName `
                                                   -Name "RunSSISPackagePipeline"
                                                   -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Veja o exemplo de saída:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline
Use o cmdlet **AzureRmDataFactoryV2Pipeline Invoke** para executar o pipeline. O cmdlet retorna a ID da execução de pipeline para monitoramento futuro.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

Execute o script do PowerShell a seguir para verificar continuamente o status da execução de pipeline até que ela termine de copiar os dados. Copie/cole o script a seguir na janela do PowerShell e pressione ENTER. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Você também pode monitorar o pipeline usando o Portal do Azure. Para obter instruções passo a passo, consulte [Monitorar o pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="create-a-trigger"></a>Escolha um gatilho
Na etapa anterior, você executou o pipeline sob demanda. Você também pode criar um gatilho de agendamento para a agendar a execução do pipeline (por hora, diariamente, etc.).

1. Crie um arquivo JSON denominado **MyTrigger.json** na pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. No **Azure PowerShell**, mude para a pasta **C:\ADF\RunSSISPackage**.
3. Execute o cmdlet **Set-AzureRmDataFactoryV2Trigger**, que cria o gatilho. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                    -DataFactoryName $DataFactory.DataFactoryName `
                                    -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Por padrão, o gatilho está no estado interrompido. Inicie o gatilho usando o cmdlet **Start-AzureRmDataFactoryV2Trigger**. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                      -DataFactoryName $DataFactory.DataFactoryName `
                                      -Name "MyTrigger" 
    ```
5. Verifique se o gatilho foi iniciado executando o cmdlet **AzureRmDataFactoryV2Trigger**. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                    -DataFactoryName $DataFactoryName `
                                    -Name "MyTrigger"     
    ```    
6. Execute o seguinte comando após a próxima hora. Por exemplo, se a hora atual for 15:25 UTC, execute o comando às 16:00 UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                       -DataFactoryName $DataFactoryName `
                                       -TriggerName "MyTrigger" `
                                       -TriggerRunStartedAfter "2017-12-06" `
                                       -TriggerRunStartedBefore "2017-12-09"
    ```

    Você pode executar a consulta a seguir no banco de dados SSISDB no seu servidor do SQL Azure para verificar se o pacote foi executado. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Próximas etapas
Consulte a postagem blog a seguir:
-   [Modernizar e estender seus fluxos de trabalho ETL/ELT com atividades do SSIS nos pipelines do ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
