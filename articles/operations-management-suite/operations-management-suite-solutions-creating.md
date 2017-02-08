---
title: "Como criar soluções de gerenciamento no OMS (Operations Management Suite) | Microsoft Docs"
description: "As soluções de gerenciamento estendem a funcionalidade do OMS (Operations Management Suite), fornecendo cenários de gerenciamento empacotados que os clientes podem adicionar ao seu espaço de trabalho do OMS.  Este artigo fornece detalhes sobre como criar soluções de gerenciamento para usar em seu próprio ambiente ou disponibilizar para os clientes."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: fc8b76bf996060e226ac3f508a1ecffca6fc3c98
ms.openlocfilehash: caa2f96d452174ebb13c5cbf67737f20e2a2134d


---
# <a name="creating-management-solutions-in-operations-management-suite-oms-preview"></a>Criando soluções de gerenciamento no OMS (Operations Management Suite) (Preview)
> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento no OMS, que estão atualmente em visualização. Os esquemas descritos a seguir estão sujeitos a alterações.  
>
>

As soluções de gerenciamento estendem a funcionalidade do OMS (Operations Management Suite), fornecendo cenários de gerenciamento empacotados que os clientes podem adicionar ao seu espaço de trabalho do OMS.  Este artigo fornece detalhes sobre como criar suas próprias soluções de gerenciamento que você pode usar em seu próprio ambiente ou disponibilizar aos clientes por meio da comunidade.

## <a name="planning-your-management-solution"></a>Planejando a solução de gerenciamento
Soluções de gerenciamento em OMS incluem vários recursos dando suporte a um cenário de gerenciamento específico.  Ao planejar sua solução, você deve se concentrar no cenário que você está tentando obter e todos os recursos necessários para dar suporte a ele.  Cada solução deverá ser autocontida e definir cada recurso que requer, mesmo se um ou mais recursos também forem definidos por outras soluções.  Quando uma solução de gerenciamento é instalada, cada recurso é criado, a menos que já exista e você pode definir o que acontece com os recursos quando uma solução é removida.  

Por exemplo, uma solução de gerenciamento pode incluir um [Runbook de automação do Azure](../automation/automation-intro.md) que coleta dados para o repositório do Log Analytics usando uma [agenda](../automation/automation-schedules.md) e uma [exibição](../log-analytics/log-analytics-view-designer.md) que fornece várias visualizações dos dados coletados.  A mesma agenda pode ser usada por outra solução.  Como autor da solução de gerenciamento, você deve definir todos os três recursos mas especificar que o runbook e o modo de exibição deverão ser removidos automaticamente quando a solução for removida.    Você também definiria a agenda mas especificaria que ela deve permanecer aplicada se a solução for removida, caso essa agenda ainda esteja em uso por outra solução.

## <a name="management-solution-files"></a>Arquivos da solução de gerenciamento
Soluções de gerenciamento são implementadas como [modelos do Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).  A principal tarefa ao aprender a criar soluções personalizadas é aprender como [criar um modelo](../azure-resource-manager/resource-group-authoring-templates.md).  Este artigo fornece detalhes exclusivos sobre os modelos usados para soluções e como definir recursos de soluções típicas.

A estrutura básica de um arquivo de solução de gerenciamento é a mesma que um [modelo do Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#template-format), que é da maneira demonstrada a seguir.  Cada uma das seções a seguir descreve os elementos de nível superior e seu conteúdo em uma solução.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parâmetros
[Parâmetros](../azure-resource-manager/resource-group-authoring-templates.md#parameters) são valores que exige dos usuários quando eles instalam a solução de gerenciamento.  Eles são parâmetros padrão que todas as soluções terão; além disso, você pode adicionar parâmetros adicionais conforme necessário para sua solução específica.  O modo como os usuários fornecerão valores de parâmetro quando instalarem sua solução dependerá do parâmetro específico e do modo como a solução estiver sendo instalada.

Quando um usuário instala a solução de gerenciamento por meio do [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-management-solutions) ou dos [Modelos de Início Rápido do Azure](operations-management-suite-solutions.md#finding-and-installing-management-solutions), será solicitado que ele selecione uma [Conta de automação e um Espaço de trabalho do OMS](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account).  Eles são usados para preencher os valores de cada um dos parâmetros padrão.  Não é solicitado que o usuário forneça diretamente os valores dos parâmetros padrão, mas será solicitado que ele forneça valores para eventuais parâmetros adicionais.

Quando o usuário instalar a solução por [outro método](operations-management-suite-solutions.md#finding-and-installing-management-solutions), ele deverá fornecer um valor para todos os parâmetros padrão e todos os parâmetros adicionais.

Um parâmetro de exemplo é mostrado abaixo.

    "Daily Start Time": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

A tabela a seguir descreve os atributos de um parâmetro.

| Atributo | Descrição |
|:--- |:--- |
| type |Tipo de dados para o parâmetro. O controle de entrada exibido para o usuário depende do tipo de dados.<br><br>bool – Caixa suspensa<br>cadeia de caracteres – caixa de texto<br>int – Caixa de texto<br>securestring – Campo de senha<br> |
| categoria |Categoria opcional para o parâmetro.  Parâmetros na mesma categoria são agrupados. |
| controle |Funcionalidade adicional para parâmetros de cadeia de caracteres.<br><br>datetime – O controle datetime é exibido.<br>GUID – O valor de GUID é gerado automaticamente e o parâmetro não é exibido. |
| Descrição |Descrição opcional para o parâmetro.  Exibido em um balão de informações ao lado do parâmetro. |

### <a name="standard-parameters"></a>Parâmetros padrão
A tabela a seguir lista os parâmetros padrão para todas as soluções de gerenciamento.  Esses valores são populados para o usuário em vez de solicitados a eles quando a solução é instalada dos modelos do Azure Marketplace ou de Início Rápido.  Se a solução for instalada com outro método, o usuário deverá fornecer valores para eles.

> [!NOTE]
> A interface do usuário nos modelos do Azure Marketplace e de Início Rápido espera os nomes de parâmetro na tabela.  Se você usar nomes de parâmetro diferentes, será solicitado que o usuário os forneça e eles não serão automaticamente populados.
>
>

| Parâmetro | Tipo | Descrição |
|:--- |:--- |:--- |
| accountName |string |Nome da conta de Automação do Azure. |
| pricingTier |string |Tipo de preço do espaço de trabalho do Log Analytics e da conta de Automação do Azure. |
| regionId |string |Região da conta de Automação do Azure. |
| solutionName |string |O nome da solução. |
| workspaceName |string |O nome do espaço de trabalho do Log Analytics. |
| workspaceRegionId |string |A região do espaço de trabalho do Log Analytics. |

### <a name="sample"></a>Amostra
A seguir está uma entidade de parâmetro de exemplo para uma solução.  Isso inclui todos os parâmetros padrão e dois parâmetros adicionais na mesma categoria.

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        },
        "jobIdGuid": {
        "type": "string",
            "metadata": {
                "description": "GUID for a runbook job",
                "control": "guid",
                "category": "Schedule"
            }
        },
        "startTime": {
            "type": "string",
            "metadata": {
                "description": "Time for starting the runbook.",
                "control": "datetime",
                "category": "Schedule"
            }
        }


Consulte os valores de parâmetro em outros elementos da solução com a sintaxe **parameters('nome do parâmetro')**.  Por exemplo, para acessar o nome do espaço de trabalho, você usaria **parameters('workspaceName')**

## <a name="variables"></a>Variáveis
O elemento **Variables** inclui os valores que você usará no restante da solução de gerenciamento.  Esses valores não são expostos ao usuário que instala a solução.  Eles se destinam a fornecer ao autor um único local onde ele pode gerenciar os valores que podem ser usados várias vezes em toda a solução. Você deve colocar todos os valores específicos à sua solução em variáveis em vez de defini-los de modo predeterminado e inalterável no elemento **resources**.  Isso torna o código mais legível e permite que você altere esses valores facilmente em versões posteriores.

A seguir está um exemplo de um elemento **variables** com parâmetros típicos usado em soluções.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Você consulta os valores de variáveis por toda a solução com a sintaxe **variables('nome da variável')**.  Por exemplo, para acessar a variável SolutionName, você usaria **variables('solutionName')**

## <a name="resources"></a>Recursos
O elemento **resources** define os diferentes recursos incluídos em sua solução de gerenciamento.  Essa será a maior e mais complexa parte do modelo.  Você define recursos com a estrutura a seguir.  

    "resources": [
        {
            "name": "<name-of-the-resource>",            
            "apiVersion": "<api-version-of-resource>",
            "type": "<resource-provider-namespace/resource-type-name>",        
            "location": "<location-of-resource>",
            "tags": "<name-value-pairs-for-resource-tagging>",
            "comments": "<your-reference-notes>",
            "dependsOn": [
                "<array-of-related-resource-names>"
            ],
            "properties": "<unique-settings-for-the-resource>",
            "resources": [
                "<array-of-child-resources>"
            ]
        }
    ]

### <a name="dependencies"></a>Dependências
O elemento **dependsOn** especifica uma [dependência](../azure-resource-manager/resource-group-define-dependencies.md) de outro recurso.  Quando a solução é instalada, um recurso não é criado até que todas as suas dependências tenham sido criadas.  Por exemplo, sua solução pode [iniciar um runbook](operations-management-suite-solutions-resources-automation.md#runbooks) quando ele é instalado usando um [recurso de trabalho](operations-management-suite-solutions-resources-automation.md#automation-jobs).  O recurso de trabalho seria dependente do recurso de runbook para assegurar que o runbook fosse criado antes do trabalho.

### <a name="oms-workspace-and-automation-account"></a>Espaço de trabalho do OMS e Conta de automação
Soluções de gerenciamento exigem que um [espaço de trabalho do OMS](../log-analytics/log-analytics-manage-access.md) contenha modos de exibição e que uma [conta de automação](../automation/automation-security-overview.md#automation-account-overview) contenha runbooks e recursos relacionados.  Eles devem estar disponíveis antes que os recursos na solução sejam criados e não devem ser definidos na solução em si.  O usuário [especificará uma conta e espaço de trabalho](operations-management-suite-solutions.md#oms-workspace-and-automation-account) quando implantar a sua solução mas, na condição de autor, você deve considerar os pontos a seguir.

## <a name="solution-resource"></a>Recurso da solução
Cada solução exige uma entrada de recurso no elemento **resources** que define a solução em si.  Isso terá um tipo **Microsoft.OperationsManagement/solutions**.  Veja a seguir o exemplo de um recurso da solução.  Seus diferentes elementos são descritos nas seções a seguir.

    "name": "[concat(variables('SolutionName'), '[ ' ,parameters('workspacename'), ' ]')]",
    "location": "[parameters('workspaceRegionId')]",
    "tags": { },
    "type": "Microsoft.OperationsManagement/solutions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]",
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
    ]
    "properties": {
        "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
        "referencedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]"
        ],
        "containedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
        ]
    },
    "plan": {
        "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
        "Version": "[variables('SolutionVersion')]",
        "product": "AzureSQLAnalyticSolution",
        "publisher": "[variables('SolutionPublisher')]",
        "promotionCode": ""
    }

### <a name="solution-name"></a>Nome da solução
O nome da solução deve ser exclusivo na sua Assinatura do Azure. O valor recomendado para uso é o mostrado a seguir.  Isso usa uma variável chamada **SolutionName** para o nome de base e o parâmetro **workspaceName** para garantir que o nome seja exclusivo.

    [concat(variables('SolutionName'), ' [' ,parameters('workspaceName'), ']')]

Isso deve ser resolvido para um nome semelhante ao mostrado a seguir.

    My Solution Name [MyWorkspace]


### <a name="dependencies"></a>Dependências
O recurso da solução deve ter uma [dependência](../azure-resource-manager/resource-group-define-dependencies.md) em todos os outros recursos da solução, pois precisam existir antes que a solução possa ser criada.  Você pode fazer isso adicionando uma entrada para cada recurso no elemento **dependsOn**.

### <a name="properties"></a>Propriedades
O recurso da solução tem as propriedades na tabela a seguir.  Isso inclui os recursos referenciados e contidos pela solução que define como os recursos são gerenciados após a instalação da solução.  Cada recurso na solução deve ser listado na propriedade **referencedResources** ou **containedResources**.

| Propriedade | Descrição |
|:--- |:--- |
| workspaceResourceId |ID do espaço de trabalho do OMS no formulário *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<Workspace Name\>*. |
| referencedResources |Lista de recursos na solução que não deverão ser removidos quando a solução for removida. |
| containedResources |Lista de recursos na solução que deverão ser removidos quando a solução for removida. |

O exemplo acima é uma solução com um runbook, um cronograma e uma exibição.  O agendamento e o runbook são *referenciados* no elemento **properties** para que eles não sejam removidos quando a solução for removida.  A exibição é *contida* para que ela seja removido quando a solução for removida.

### <a name="plan"></a>Plano
A entidade **plano** do recurso da solução tem as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| name |O nome da solução. |
| version |Versão da solução conforme determinado pelo autor. |
| product |Cadeia de caracteres exclusiva para identificar a solução. |
| publicador |O publicador da solução. |

## <a name="other-resources"></a>Outros recursos
Você pode obter os detalhes e exemplos de recursos que são comuns para soluções de gerenciamento nos artigos a seguir.

* [Modos de exibição e painéis](operations-management-suite-solutions-resources-views.md)
* [Recursos de automação](operations-management-suite-solutions-resources-automation.md)

## <a name="testing-a-management-solution"></a>Testando uma solução de gerenciamento
Antes de implantar sua solução de gerenciamento, é recomendável que você teste-a usando [Test-AzureRmResourceGroupDeployment](../azure-resource-manager/resource-group-template-deploy.md#deploy).  Isso validará o arquivo de solução e ajudará você a identificar eventuais problemas antes de tentar implantá-lo.

## <a name="next-steps"></a>Próximas etapas
* [Adicionar alertas e pesquisas salvas](operations-management-suite-solutions-resources-searches-alerts.md) à sua solução de gerenciamento.
* [Adicionar exibições](operations-management-suite-solutions-resources-views.md) à sua solução de gerenciamento.
* [Adicionar runbooks de automação e outros recursos](operations-management-suite-solutions-resources-automation.md) à sua solução de gerenciamento.
* Aprenda os detalhes da [Criação de modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Pesquise entre os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates) para obter exemplos de diferentes modelos do Resource Manager.



<!--HONumber=Jan17_HO4-->


