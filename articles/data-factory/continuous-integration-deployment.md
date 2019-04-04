---
title: Integração e entrega contínuas no Azure Data Factory | Microsoft Docs
description: Aprenda a usar integração e entrega contínuas para mover os pipelines do Data Factory de um ambiente (desenvolvimento, teste, produção) para outro.
services: data-factory
documentationcenter: ''
author: gauravmalhot
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: gamal
manager: craigg
ms.openlocfilehash: 2edd4e28a0dd67be3c06159bce2e968d681b7f70
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905249"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Integração e entrega contínua (CI / CD) no Azure Data Factory

Integração Contínua é a prática de testar cada alteração feita em sua base de código automaticamente e o mais cedo possível. A Entrega Contínua segue o teste que acontece durante a Integração Contínua e envia as alterações para um sistema de preparação ou de produção.

Para o Azure Data Factory, integração e entrega contínuas significa mover pipelines do Data Factory de um ambiente (desenvolvimento, teste, produção) para outro. Para fazer integração e entrega contínuas, você pode usar a integração da interface do usuário do Data Factory com os modelos do Azure Resource Manager. A interface de usuário do Data Factory pode gerar um modelo do Resource Manager quando você seleciona as opções de **modelo ARM**. Quando você seleciona **Exportar modelo ARM**, o portal gera o modelo do Resource Manager para o data factory e um arquivo de configuração que inclui todas as suas cadeias de conexão e outros parâmetros. Em seguida, você precisa criar um arquivo de configuração para cada ambiente (desenvolvimento, teste, produção). O arquivo de modelo do Resource Manager principal permanece o mesmo em todos os ambientes.

Para ver uma introdução de nove minutos e uma demonstração desse recurso, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-manager-template-for-each-environment"></a>Criar um modelo do Resource Manager para cada ambiente
Selecione **Exportar modelo ARM** para exportar o modelo do Resource Manager para seu data factory no ambiente de desenvolvimento.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Em seguida, vá para o data factory de teste e o data factory de produção e selecione **Importar modelo ARM**.

![](media/continuous-integration-deployment/continuous-integration-image2.png)

Essa ação leva você até o Portal do Azure, onde você pode importar o modelo exportado. Selecione **Criar seu próprio modelo no editor** e, em seguida, **Carregar arquivo** e selecione o modelo do Resource Manager gerado. Forneça as configurações e o data factory e todo o pipeline inteiro será importado para seu ambiente de produção.

![](media/continuous-integration-deployment/continuous-integration-image3.png)

![](media/continuous-integration-deployment/continuous-integration-image4.png)

Selecione **Carregar arquivo** para selecionar o modelo do Resource Manager exportado e forneça todos os valores de configuração (por exemplo, serviços vinculados).

![](media/continuous-integration-deployment/continuous-integration-image5.png)

**Cadeias de caracteres de Conexão**. Você pode encontrar as informações necessárias para criar strings de conexão nos artigos sobre os conectores individuais. Por exemplo, para o Banco de Dados SQL do Azure, consulte [Copiar dados para ou do Banco de Dados SQL do Azure usando o Azure Data Factory](connector-azure-sql-database.md). Para verificar a cadeia de conexão correta – para um serviço vinculado, por exemplo, você também pode abrir a exibição de código para o recurso na IU do Data Factory. No modo de exibição de código, no entanto, a parte da chave de senha ou conta da seqüência de conexão é removida. Para abrir a visualização de código, selecione o ícone destacado na captura de tela a seguir.

![Abra a visualização de código para ver a string de conexão](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="continuous-integration-lifecycle"></a>Ciclo de vida da integração contínua
Aqui está todo o ciclo de vida para integração e entrega contínuas que você pode usar depois de ativar a integração do Azure Repos Git na interface do Data Factory:

1.  Configure um data factory de desenvolvimento com o Azure Repos, no qual todos os desenvolvedores podem criar recursos do Data Factory, como pipelines, conjuntos de dados e assim por diante.

1.  Em seguida, os desenvolvedores podem modificar recursos como pipelines. À medida que fazem as modificações, eles podem selecionar **Depurar** para ver como o pipeline é executado com as alterações mais recentes.

1.  Depois que os desenvolvedores estiverem satisfeitos com as alterações, eles poderão criar uma solicitação de pull em seu branch para o branch mestre (ou branch de colaboração) para que as alterações sejam revisadas por colegas.

1.  Depois que as alterações estiverem no branch mestre, eles poderão publicar o factory de desenvolvimento selecionando **Publicar**.

1.  Quando a equipe estiver pronta para promover alterações ao factory de teste e ao factory de produção, ela poderá exportar o modelo do Resource Manager do branch mestre ou de qualquer outro branch caso o branch mestre aceite o Data Factory de desenvolvimento dinâmico.

1.  O modelo do Resource Manager exportado pode ser implantado com arquivos de parâmetro diferentes para o factory de teste e o factory de produção.

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Automatize a integração contínua com os lançamentos do Azure Pipelines

Aqui estão as etapas para configurar uma versão do Azure Pipelines para que você possa automatizar a implantação de um data factory em vários ambientes.

![Diagrama de integração contínua com os pipelines do Azure](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Requisitos

-   Uma assinatura do Azure vinculada ao Team Foundation Server ou ao Azure Repos usando o ponto de extremidade do serviço do [*Gerenciador do Azure Resource Manager*](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   Uma integração do Data Factory com o Azure Repos Git configurada.

-   Um [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) contendo os segredos.

### <a name="set-up-an-azure-pipelines-release"></a>Configurar um lançamento do Azure Pipelines

1.  Vá para a página do Repositório do Azure no mesmo projeto que o configurado com o Data Factory.

1.  Clique no menu superior **Pipelines do Azure**  &gt;  **Lançamentos** &gt;  **Crie uma definição de versão**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selecione o modelo **Processo vazio**.

1.  Insira o nome do seu ambiente.

1.  Adicione um artefato de Git e selecione o mesmo repositório configurado com o Data Factory. Escolha `adf_publish` como a branch padrão com a versão padrão mais recente.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Adicione uma tarefa de Implantação do Azure Resource Manager:

     a.  Crie a nova tarefa, procure **Implantação de Grupo de Recursos do Azure**e adicione-o.

    b.  Na tarefa de Implantação, escolha a assinatura, o grupo de recursos e o local para o Data Factory de destino e forneça credenciais se necessário.

    c.  Selecione a ação **Criar ou atualizar grupo de recursos**.

    d.  Selecione **...** no campo **Modelo**. Procure o modelo do Gerenciador de Recursos (*ARMTemplateForFactory.json*) que foi criado pela ação de publicação no portal. Procure esse arquivo na pasta `<FactoryName>` do `adf_publish` branch.

    e.  Faça o mesmo para o arquivo de parâmetros. Escolha o arquivo correto, dependendo de se você criou uma cópia ou se está usando o arquivo padrão *ARMTemplateParametersForFactory.json*.

    f.  Selecione **...** ao lado do campo **Substituir parâmetros de modelo** e preencha as informações do Data Factory de destino. Para as credenciais que vêm do cofre de chaves, use o mesmo nome para o segredo no seguinte formato: supondo que o nome do segredo seja `cred1`, digite `"$(cred1)"` (entre aspas).

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    g. Selecione o modo de Implantação **Incremental**.

    > [!WARNING]
    > Se você selecionar o modo de implementação **Concluído**, os recursos existentes poderão ser excluídos, incluindo todos os recursos no grupo de recursos de destino que não estiverem definidos no modelo do Resource Manager.

1.  Salve o pipeline de lançamento.

1.  Crie uma nova versão esse pipeline de lançamento.Crie uma nova versão esse pipeline de lançamento.

    ![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="optional---get-the-secrets-from-azure-key-vault"></a>Opcional - Obtenha os segredos do Azure Key Vault

Se você tiver segredos para passar em um modelo do Azure Resource Manager, recomendamos o uso do Cofre de Chaves do Azure com a versão do Azure Pipelines.

Há duas maneiras de lidar cos segredos:

1.  Adicione os segredos ao arquivo de parâmetros. Para obter mais informações, consulte [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](../azure-resource-manager/resource-manager-keyvault-parameter.md).

    -   Crie uma cópia do arquivo de parâmetros que é carregado para o branch de publicação e defina os valores dos parâmetros que você deseja obter do cofre de chaves com o seguinte formato:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    -   Quando você usa esse método, o segredo é extraído do cofre de chaves automaticamente.

    -   O arquivo de parâmetros também deve estar no branch de publicação.

1.  Adicione uma [tarefa do Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) antes da Implantação do Azure Resource Manager descrita na seção anterior:

    -   Selecione a guia **Tarefas** guia, crie uma nova tarefa, procure **Azure Key Vault** e adicione-o.

    -   Na tarefa do Key Vault, escolha a assinatura na qual você criou o cofre de chaves, forneça as credenciais se necessário e, em seguida, escolha o cofre de chaves.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Conceder permissões ao agente do Azure Pipelines
A tarefa do Azure Key Vault pode falhar a hora de tempo de execução fIntegration com um erro acesso negado. Baixe os logs da versão e localize o arquivo `.ps1` com o comando para conceder permissões ao agente do Azure Pipelines. Você pode executar o comando diretamente ou pode copiar a ID de entidade de segurança do arquivo e adicionar a política de acesso manualmente no Portal do Azure. (*Obter* e *Listar* são as permissões mínimas necessárias).

### <a name="update-active-triggers"></a>Atualizar gatilhos ativos
A implantação poderá falhar se você tentar atualizar gatilhos ativos. Para atualizar os gatilhos ativos, você precisa interrompê-los manualmente e iniciá-los após a implantação. Você pode adicionar uma tarefa do Azure Powershell para essa finalidade, conforme mostrado no exemplo a seguir:

1.  Na guia Tarefas da versão, pesquise o **Azure Powershell** e adicione-o.

1.  Escolha **Azure Resource Manager** como tipo de conexão e selecione sua assinatura.

1.  Escolha **Script Embutido** como tipo de script e, em seguida, forneça seu código. O exemplo abaixo interrompe os gatilhos:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Você pode seguir etapas semelhantes e usar um código semelhante (com a função `Start-AzDataFactoryV2Trigger`) para reiniciar os gatilhos depois da implantação.

> [!IMPORTANT]
> Em cenários de integração e implementação contínuos, o tipo de tempo de execução de integração em diferentes ambientes deve ser o mesmo. Por exemplo, se você tiver um *Runtime de integração (IR) auto-hospedado* no ambiente de desenvolvimento, o mesmo IR deve ser do tipo *Auto-Hospedado* em outros ambientes, como teste e produção Além disso. Da mesma forma, se você estiver compartilhando tempos de execução de integração em vários estágios, será necessário configurar os Integration Runtimes como *Linked Self-Hosted* em todos os ambientes, como desenvolvimento, teste e produção.

## <a name="sample-deployment-template"></a>Modelo de implantação de exemplo

Aqui está uma amostra de modelo de implantação que você pode importar em Pipelines do Azure.

```json
{
    "source": 2,
    "id": 1,
    "revision": 51,
    "name": "Data Factory Prod Deployment",
    "description": null,
    "createdBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "createdOn": "2018-03-01T22:57:25.660Z",
    "modifiedBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "modifiedOn": "2018-03-14T17:58:11.643Z",
    "isDeleted": false,
    "path": "\\",
    "variables": {},
    "variableGroups": [],
    "environments": [{
        "id": 1,
        "name": "Prod",
        "rank": 1,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserprod"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 1
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 2
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 3
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param\n(\n    [parameter(Mandatory = $false)] [String] $rootFolder=\"C:\\Users\\sampleuser\\Downloads\\arm_template\",\n    [parameter(Mandatory = $false)] [String] $armTemplate=\"$rootFolder\\arm_template.json\",\n    [parameter(Mandatory = $false)] [String] $armTemplateParameters=\"$rootFolder\\arm_template_parameters.json\",\n    [parameter(Mandatory = $false)] [String] $domain=\"microsoft.onmicrosoft.com\",\n    [parameter(Mandatory = $false)] [String] $TenantId=\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\n    [parame",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": "5.*"
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "secret1",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/1"
    }, {
        "id": 2,
        "name": "Staging",
        "rank": 2,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserstaging"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 4
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 5
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 6
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "16a37943-8b58-4c2f-a3d6-052d6f032a07",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param(\n$x,\n$y,\n$z)\nwrite-host \"----------\"\nwrite-host $x\nwrite-host $y\nwrite-host $z | ConvertTo-SecureString\nwrite-host \"----------\"",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/2"
    }],
    "artifacts": [{
        "sourceId": "19749ef3-2f42-49b5-9696-f28b49faebcb:a6c88f30-5e1f-4de8-b24d-279bb209d85f",
        "type": "Git",
        "alias": "Dev",
        "definitionReference": {
            "branches": {
                "id": "adf_publish",
                "name": "adf_publish"
            },
            "checkoutSubmodules": {
                "id": "",
                "name": ""
            },
            "defaultVersionSpecific": {
                "id": "",
                "name": ""
            },
            "defaultVersionType": {
                "id": "latestFromBranchType",
                "name": "Latest from default branch"
            },
            "definition": {
                "id": "a6c88f30-5e1f-4de8-b24d-279bb209d85f",
                "name": "Dev"
            },
            "fetchDepth": {
                "id": "",
                "name": ""
            },
            "gitLfsSupport": {
                "id": "",
                "name": ""
            },
            "project": {
                "id": "19749ef3-2f42-49b5-9696-f28b49faebcb",
                "name": "Prod"
            }
        },
        "isPrimary": true
    }],
    "triggers": [{
        "schedule": {
            "jobId": "b5ef09b6-8dfd-4b91-8b48-0709e3e67b2d",
            "timeZoneId": "UTC",
            "startHours": 3,
            "startMinutes": 0,
            "daysToRelease": 31
        },
        "triggerType": 2
    }],
    "releaseNameFormat": "Release-$(rev:r)",
    "url": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1",
    "_links": {
        "self": {
            "href": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1"
        },
        "web": {
            "href": "https://sampleuser.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_release?definitionId=1"
        }
    },
    "tags": [],
    "properties": {
        "DefinitionCreationSource": {
            "$type": "System.String",
            "$value": "ReleaseNew"
        }
    }
}
```

## <a name="sample-script-to-stop-and-restart-triggers-and-clean-up"></a>Script de amostra para parar e reiniciar gatilhos e limpar

Aqui está um exemplo de script para parar os gatilhos antes da implantação e reiniciar os gatilhos posteriormente. O script também inclui código para excluir recursos que foram removidos. Para instalar a versão mais recente do Azure PowerShell, consulte [Instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Usar parâmetros personalizados com o modelo do Resource Manager

Se você estiver no modo GIT, você pode substituir as propriedades padrão em seu modelo do Resource Manager para definir propriedades que são parametrizadas no modelo e as propriedades que são embutidos. Talvez você queira substituir o modelo de parametrização padrão nesses cenários:

* Usar o CI/CD automatizado e você deseja alterar algumas propriedades durante a implantação do Resource Manager, mas as propriedades não são parametrizadas por padrão.
* Sua fábrica é tão grande que o modelo do Gerenciador de recursos padrão é inválido porque ele tem mais do que o máximo permitido de parâmetros (256).

Sob essas condições, para substituir o modelo de parametrização padrão, crie um arquivo chamado *arm-modelo parâmetros definition.json* na pasta raiz do repositório. O nome do arquivo deve corresponder exatamente. Fábrica de dados tenta ler esse arquivo de qualquer branch que você está atualmente no portal do Azure Data Factory, não apenas a ramificação de colaboração. Você pode criar ou editar o arquivo de uma ramificação particular, em que você pode testar suas alterações usando o **modelo de ARM exportar** na interface do usuário. Em seguida, você pode mesclar o arquivo com a ramificação de colaboração. Se nenhum arquivo for encontrado, o modelo padrão será usado.


### <a name="syntax-of-a-custom-parameters-file"></a>Sintaxe de um arquivo de parâmetros personalizados

Aqui estão algumas diretrizes para usar ao criar o arquivo de parâmetros personalizados. O arquivo consiste em uma seção para cada tipo de entidade: disparador, pipeline, linkedservice, dataset, integrationruntime e assim por diante.
* Insira o caminho da propriedade com o tipo de entidade relevantes.
* Quando você define um nome de propriedade como '\*', você indica que você deseja parametrizar todas as propriedades nele (somente até o primeiro nível, não recursivamente). Você também pode fornecer todas as exceções a isso.
* Quando você define o valor de uma propriedade como uma string, você indica que deseja parametrizar a propriedade. Use o formato `<action>:<name>:<stype>`.
   *  `<action>` pode ser um dos seguintes caracteres:
      * `=` significa manter o valor atual como o valor padrão para o parâmetro.
      * `-` significa que não mantenha o valor padrão para o parâmetro.
      * `|` é um caso especial para segredos do Azure Key Vault para cadeias de caracteres de conexão ou chaves.
   * `<name>` É o nome do parâmetro. Se ele estiver em branco, ele usa o nome da propriedade. Se o valor começa com um `-` caractere, o nome é reduzido. Por exemplo, `AzureStorage1_properties_typeProperties_connectionString` seriam reduzidos a `AzureStorage1_connectionString`.
   * `<stype>` é o tipo de parâmetro. Se `<stype>` está em branco, o tipo padrão é `string`. Valores com suporte: `string`, `bool`, `number`, `object`, e `securestring`.
* Quando você especifica uma matriz no arquivo de definição, você indicar que a propriedade correspondente no modelo é uma matriz. Data Factory itera em todos os objetos na matriz, usando a definição que é especificada no objeto de tempo de execução de integração da matriz. O segundo objeto, uma cadeia de caracteres, torna-se o nome da propriedade, que é usada como o nome do parâmetro para cada iteração.
* Não é possível ter uma definição que é específica para uma instância do recurso. Qualquer definição se aplica a todos os recursos desse tipo.
* Por padrão, todas as cadeias de seguras, como segredos do Cofre de chaves e cadeias de caracteres seguras, como cadeias de caracteres de conexão, chaves e tokens, são parametrizadas.
 
## <a name="sample-parameterization-template"></a>Exemplo de modelo de parametrização

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

### <a name="explanation"></a>Explicação:

#### <a name="pipelines"></a>Pipelines
    
* Qualquer propriedade no caminho atividades/typeProperties/waitTimeInSeconds é parametrizada. Isso significa que qualquer atividade em um pipeline que tem uma propriedade de nível de código chamada `waitTimeInSeconds` (por exemplo, o `Wait` atividade) é parametrizado como um número, com um nome padrão. Mas, ele não terá um valor padrão no modelo do Resource Manager. É uma entrada obrigatória durante a implantação do Resource Manager.
* Da mesma forma, uma propriedade chamada `headers` (por exemplo, em um `Web` atividade) é parametrizada com tipo `object` (JObject). Ele tem um valor padrão, que é o mesmo valor da fábrica de origem.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Somente propriedades e todas as propriedades, no caminho `typeProperties` são parametrizadas com seus valores padrão respectiva. Por exemplo, a partir do esquema de hoje, há duas propriedades sob **IntegrationRuntimes** propriedades do tipo: `computeProperties` e `ssisProperties`. Ambos os tipos de propriedade são criados com seus respectivos valores e tipos padrão (objeto).

#### <a name="triggers"></a>Gatilhos

* Em `typeProperties`, duas propriedades são parametrizadas. É o primeiro deles `maxConcurrency`, que é especificado para ter um valor padrão e o tipo seria `string`. Ele tem o nome do parâmetro padrão de `<entityName>_properties_typeProperties_maxConcurrency`.
* O `recurrence` propriedade também é parametrizada. Aqui, todas as propriedades nesse nível são especificadas sejam parametrizados como cadeias de caracteres, com valores padrão e nomes de parâmetro. Uma exceção é o `interval` propriedade, que é parametrizada como tipo numérico e com o nome do parâmetro com o sufixo `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Da mesma forma, o `freq` propriedade é uma cadeia de caracteres e é parametrizada como uma cadeia de caracteres. No entanto, o `freq` propriedade é parametrizada sem um valor padrão. O nome é reduzido e o sufixo. Por exemplo, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Serviços vinculados é exclusivo. Como serviços vinculados e conjuntos de dados podem ser de vários tipos, você pode fornecer personalização de tipo específico. Por exemplo, você pode dizer que para todos os serviços vinculados do tipo `AzureDataLakeStore`, um modelo específico serão aplicados e para todas as outras (por meio de \*) será aplicado a um modelo diferente.
* No exemplo anterior, o `connectionString` propriedade será parametrizada como uma `securestring` valor, ele não terá um valor padrão, e ele terá um nome de parâmetro abreviada é sufixado com `connectionString`.
* A propriedade `secretAccessKey`, no entanto, acontece ser um `AzureKeyVaultSecret` (por exemplo, um `AmazonS3` serviço vinculado). Assim, é automaticamente parametrizado como um segredo do Cofre de chaves do Azure, e ele é buscado do Cofre de chaves que é configurado com na fábrica de origem. Também é possível parametrizar o Cofre de chaves em si.

#### <a name="datasets"></a>Conjunto de dados

* Mesmo que o tipo específico personalização está disponível para conjuntos de dados, a configuração pode ser fornecida sem a necessidade de explicitamente um \*-configuração de nível. No exemplo anterior, todas as propriedades de conjunto de dados sob `typeProperties` são parametrizadas.

O modelo de parametrização padrão pode alterar, mas este é o modelo atual. Isso será útil se você precisar apenas adicionar uma propriedade adicional como um parâmetro, mas também se você não quiser perder as parametrizações existentes e precisará recriá-los.


```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

**Exemplo**: Adicione uma ID de cluster Databricks interativo (de um serviço vinculado do Databricks) para o arquivo de parâmetros:

```
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```


## <a name="linked-resource-manager-templates"></a>Modelos Vinculados do Resource Manager

Se você configurou a integração e implantação contínua (CI/CD) para as Fábricas de Dados, é possível observar que, à medida que sua fábrica cresce, atinge os limites de modelo do Resource Manager, como o número máximo de recursos ou o conteúdo máximo em um modelo do Resource Manager. Para cenários como esses, juntamente com a geração de modelo completo do Resource Manager completo para uma fábrica, o Data Factory agora também gera modelos vinculados do Resource Manager. Como resultado, você tem a carga de fábrica inteira dividida em vários arquivos, assim você não atinge os limites mencionados.

Se você tiver o Git configurado, os modelos vinculados são gerados e salvos juntamente com os modelos completos do Resource Manager, na ramificação `adf_publish`, em uma nova pasta chamada `linkedTemplates`.

![Pasta de modelos vinculados do Resource Manager](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Os modelos vinculados do Resource Manager geralmente têm um modelo mestre e um conjunto de modelos filho vinculados ao mestre. O modelo pai é chamado `ArmTemplate_master.json`, e os modelos filho são nomeados com o padrão `ArmTemplate_0.json`, `ArmTemplate_1.json` e assim por diante. Para mudar o uso do modelo completo do Resource Manager completo para o uso dos modelos vinculados, atualize sua tarefa de CI/CD para indicar `ArmTemplate_master.json` em vez de indicar `ArmTemplateForFactory.json` (ou seja, o modelo completo do Resource Manager). O Resource Manager também requer que você carregue os modelos vinculados em uma conta de armazenamento para que eles possam ser acessados pelo Azure durante a implantação. Para obter mais informações, consulte [Implantar Modelos ARM Vinculados com VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Lembre-se de adicionar os scripts do Data Factory no pipeline de CI/CD antes e depois a tarefa de implantação.

Se você não tiver o Git configurado, os modelos vinculados são acessíveis por meio do gesto **Exportar modelo ARM**.

## <a name="best-practices-for-cicd"></a>Práticas recomendadas para CI/CD

Se você estiver usando a integração de Git com seu data factory e tiver um pipeline de CI/CD que mova as alterações de Desenvolvimento para Teste e então para Produção, recomendamos as seguintes melhores práticas:

-   **Integração do Git**. Você precisa apenas configurar seu data factory de Desenvolvimento com a integração do Git. Alterações para Teste e Produção são implantadas por meio de CI/CD e não precisam ter integração do Git.

-   **Script de CI/CD do Data Factory**. Antes da etapa de implantação do Resource Manager no CI/CD, você deve cuidar de elementos como parar os gatilhos e diferentes tipos de limpeza de alocador. É recomendável usar [este script](#sample-script-to-stop-and-restart-triggers-and-clean-up), uma vez que ele cuida de tudo isso. Execute o script uma vez antes da implantação e uma vez depois usando os sinalizadores adequados.

-   **Integration Runtimes e compartilhamento**. Integration Runtimes são um dos componentes de infraestrutura no seu data factory, que passam por alterações com menos frequência, e são semelhantes entre todos os estágios em seu CI/CD. Como resultado, o Data Factory espera que você tenha o mesmo nome e o mesmo tipo de Integration Runtimes em todos os estágios de CI/CD. Se você estiver buscando compartilhar Integration Runtimes em todos os estágios, por exemplo, Integration Runtimes Auto-hospedados, uma maneira de compartilhar é hospedando o IR auto-hospedado em um alocador ternário, apenas para conter os Integration Runtimes compartilhados. Em seguida, você pode usá-los em Desenvolvimento/Teste/Produção como um tipo de IR Vinculado.

-   **Key Vault**. Quando você usa os serviços vinculados baseados em Azure Key Vault recomendados, pode elevar sua vantagens a um novo patamar potencialmente mantendo cofres de chaves separados para Desenvolvimento/Teste/Produção. Você também pode configurar níveis de permissão separados para cada um deles. Talvez você não queira que os membros da sua equipe tenham permissões para os segredos de Produção. Também recomendamos que você mantenha os mesmos nomes secretos em todos os estágios. Se você mantiver os mesmos nomes, não precisará alterar os modelos do Resource Manager entre CI/CD, uma vez que a única alteração necessária será no nome do cofre de chaves, que é um dos parâmetros de modelo do Resource Manager.

## <a name="unsupported-features"></a>Recursos sem suporte

-   Você não pode publicar recursos individuais, pois as entidades do data factory dependem entre si. Por exemplo, os gatilhos dependem de pipelines, pipelines dependem de conjuntos de dados e de outros pipelines etc. É difícil acompanhar dependências inconstantes. Se fosse possível selecionar os recursos para publicar manualmente, seria possível selecionar apenas um subconjunto de todo o conjunto de alterações, o que levaria a um comportamento inesperado dos itens após a publicação.

-   Não é possível publicar de branches particulares.

-   Você não pode hospedar projetos no Bitbucket.
