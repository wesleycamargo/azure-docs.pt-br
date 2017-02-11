---
title: "Recursos de automação em soluções do OMS | Microsoft Docs"
description: "Soluções em OMS normalmente incluirão runbooks na automação do Azure para automatizar processos, como a coleta e o processamento de dados de monitoramento.  Este artigo descreve como incluir runbooks e seus recursos relacionados em uma solução."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e115fd3b5f29dbcbfd9f85ebb215d950539cc1e3


---
# <a name="automation-resources-in-oms-solutions-preview"></a>Recursos de automação em soluções do OMS (Preview)
> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento no OMS, que estão atualmente em visualização. Os esquemas descritos a seguir estão sujeitos a alterações.   
> 
> 

[Soluções de gerenciamento em OMS](operations-management-suite-solutions.md) normalmente incluirão runbooks na automação do Azure para automatizar processos, como a coleta e o processamento de dados de monitoramento.  Além de runbooks, contas de automação incluem ativos, como variáveis e agendas que dão suporte a runbooks usados na solução.  Este artigo descreve como incluir runbooks e seus recursos relacionados em uma solução.

> [!NOTE]
> Os exemplos neste artigo usam parâmetros e variáveis que são necessários ou comuns para as soluções de gerenciamento e estão descritos em [Creating management solutions in Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md) (Criando soluções de gerenciamento no OMS (Operations Management Suite)) 
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já esteja familiarizado com o modo para [criar uma solução de gerenciamento](operations-management-suite-solutions-creating.md) e com a estrutura de um arquivo de solução.

## <a name="samples"></a>Exemplos
Você pode obter modelos do Resource Manager de exemplo para recursos de automação do [modelos de início rápido no GitHub](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates).

## <a name="automation-account"></a>Conta de automação
Todos os recursos na Automação do Azure estão contidos em um [Conta de automação](../automation/automation-security-overview.md#automation-account-overview).  Conforme descrito no [espaço de trabalho OMS e conta de automação](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account), a conta de automação não está incluída na solução de gerenciamento, mas deve existir antes que a solução seja instalada.  Se ela não estiver disponível, a instalação da solução falhará.

O nome da sua conta de automação é o nome de cada recurso de automação.  Isso é feito na solução com o parâmetro **accountName**, conforme descrito no exemplo a seguir de um recurso de runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Os recursos do [Runbook de automação do Azure](../automation/automation-runbook-types.md) têm um tipo de **Microsoft.Automation/automationAccounts/runbooks** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| runbookType |Especifica os tipos de runbook. <br><br> Script – Script do PowerShell <br>PowerShell – Fluxo de trabalho do PowerShell <br> GraphPowerShell – Runbook de script do PowerShell gráfico <br> GraphPowerShellWorkflow – Runbook de fluxo de trabalho do PowerShell gráfico |
| logProgress |Especifica se [registros de progresso](../automation/automation-runbook-output-and-messages.md) devem ser gerados para o runbook. |
| logVerbose |Especifica se [registros detalhados](../automation/automation-runbook-output-and-messages.md) devem ser gerados para o runbook. |
| Descrição |Descrição opcional para o runbook. |
| publishContentLink |Especifica o conteúdo do runbook. <br><br>uri – URI do conteúdo do runbook.  Ele será um arquivo .ps1 para runbooks do PowerShell e Script e um arquivo de runbook gráfico exportado para um runbook gráfico.  <br> versão – a versão do runbook para seu próprio acompanhamento. |

Um exemplo de um recurso de runbook pode ser encontrado abaixo.  Nesse caso, ele recupera o runbook na [Galeria do PowerShell](https://www.powershellgallery.com).

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>Iniciando um runbook
Há dois métodos para iniciar um runbook em uma solução de gerenciamento.

* Para iniciar o runbook quando a solução é instalada, crie um [recurso de trabalho](#automation-jobs) conforme descrito abaixo.
* Para iniciar o runbook em um agendamento, crie um [recurso de agendamento](#schedules) conforme descrito abaixo. 

## <a name="automation-jobs"></a>Trabalhos de automação
Para iniciar um runbook quando a solução de gerenciamento estiver instalada, crie um recurso de **trabalho**.  Recursos de trabalho têm um tipo de **Microsoft.Automation/automationAccounts/jobs** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| runbook |Uma entidade com **nome** único com o nome do runbook. |
| parâmetros |A entidade para cada parâmetro exigido pelo runbook. |

O trabalho inclui o nome do runbook e valores de parâmetro a ser enviado para o runbook.  O trabalho deve [depender](operations-management-suite-solutions-creating.md#resources) se o runbook está sendo iniciado, pois o runbook deve ser criado antes do trabalho.  Você também pode criar dependências em outros trabalhos de runbooks que devem ser concluídos antes do atual.

O nome de um recurso de trabalho deve conter um GUID que normalmente é atribuído por um parâmetro.  Você pode ler mais sobre os parâmetros GUID em [Criando soluções no OMS (Operations Management Suite)](operations-management-suite-solutions-creating.md#parameters).  

A seguir está um exemplo de um recurso de trabalho que inicia um runbook quando a solução de gerenciamento estiver instalada.  Um outro runbook deve ser concluído antes deste iniciar, para que ele possua dependências nos trabalhos para esse runbook.  Os detalhes do trabalho são fornecidos por meio de parâmetros e variáveis em vez de ser especificado diretamente.

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>Certificados
Os [certificados de Automação do Azure](../automation/automation-certificates.md) têm um tipo de **Microsoft.Automation/automationAccounts/certificates** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| base64Value |O valor de base 64 do certificado. |
| impressão digital |A impressão digital do certificado. |

Um exemplo de um recurso de certificado pode ser encontrado abaixo.

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>Credenciais
As [credenciais de Automação do Azure](../automation/automation-credentials.md) têm um tipo de **Microsoft.Automation/automationAccounts/credentials** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| userName |Nome de usuário da credencial. |
| Senha |Senha da credencial. |

Um exemplo de um recurso de credencial pode ser encontrado abaixo.

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>Agendas
Os [agendamentos de Automação do Azure](../automation/automation-schedules.md) têm um tipo de **Microsoft.Automation/automationAccounts/schedules** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Descrição |Descrição opcional para o agendamento. |
| startTime |Especifica a hora de início de uma agenda como um objeto DateTime. Uma cadeia de caracteres pode ser fornecida se ele for convertido em um DateTime válido. |
| isEnabled |Especifica se o agendamento está habilitado. |
| intervalo |O tipo de intervalo para o agendamento.<br><br>dia<br>hora |
| frequência |Frequência em que o agendamento deve ser disparado em número de dias ou horas. |

Um exemplo de um recurso de agendamento pode ser encontrado abaixo.

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>Variáveis
As [variáveis de Automação do Azure](../automation/automation-variables.md) têm um tipo de **Microsoft.Automation/automationAccounts/variables** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Descrição |Descrição opcional para a variável. |
| isEncrypted |Especifica se a variável deve ser criptografada. |
| type |Tipo de dados para a variável. |
| value |Valor da variável. |

Um exemplo de um recurso de variável pode ser encontrado abaixo.

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>Módulos
Sua solução de gerenciamento não precisa definir [módulos globais](../automation/automation-integration-modules.md) usados pelos seus runbooks porque eles sempre estarão disponíveis.  Você precisa incluir um recurso para qualquer outro módulo usado pelos seus runbooks e o runbook deve depender do recurso de módulo para garantir que ele seja criado antes do runbook.

[Módulos de integração](../automation/automation-integration-modules.md) têm um tipo de **Microsoft.Automation/automationAccounts/modules** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| contentLink |Especifica o conteúdo do módulo. <br><br>uri – URI do conteúdo do runbook.  Ele será um arquivo .ps1 para runbooks do PowerShell e Script e um arquivo de runbook gráfico exportado para um runbook gráfico.  <br> versão – a versão do runbook para seu próprio acompanhamento. |

Um exemplo de um recurso de módulo pode ser encontrado abaixo.

    {        
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>Atualizando módulos
Se você atualizar uma solução de gerenciamento que inclui um runbook que usa um agendamento e a nova versão da solução tiver um novo módulo usado pelo runbook, o runbook poderá usar a versão antiga do módulo.  Você deve incluir os seguintes runbooks em sua solução e criar um trabalho para executá-los antes de quaisquer outros runbooks.  Isso garantirá que todos os módulos estejam atualizados como necessário antes que os runbooks sejam carregados.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) garantirá que todos os módulos usados por runbooks em sua solução tenham a versão mais recente.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) registrará novamente todos os recursos de agendamento para garantir que os runbooks vinculados a eles usem os módulos mais recentes.

A seguir está um exemplo dos elementos necessários de uma solução para dar suporte à atualização de módulo.

    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>Próximas etapas
* [Adicione um modo de exibição à sua solução](operations-management-suite-solutions-resources-views.md) para visualizar os dados coletados.




<!--HONumber=Nov16_HO3-->


