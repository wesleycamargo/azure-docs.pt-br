---
title: Criar um espaço de trabalho do Log Analytics usando a CLI do Azure | Microsoft Docs
description: Saiba como criar um espaço de trabalho do Log Analytics para habilitar as soluções de gerenciamento e a coleta de dados da sua nuvem e dos ambientes locais com a CLI do Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptal
ms.date: 09/19/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 47e2db42e21be6b540882a9c316517cfb4fb82e7
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47054423"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Criar um espaço de trabalho do Log Analytics com a CLI do Azure 2.0

A CLI do Azure 2.0 é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este início rápido mostra a você como usar a CLI do Azure 2.0 para implantar um espaço de trabalho do Log Analytics, que é um ambiente exclusivo com seu próprio repositório de dados, fontes de dados e soluções.  As etapas descritas neste artigo serão necessárias se você pretender coletar dados das seguintes fontes:

* Recursos do Azure em sua assinatura  
* Computadores locais monitorados pelo System Center Operations Manager  
* Coleções de dispositivos do System Center Configuration Manager  
* Dados de diagnóstico ou de log do armazenamento do Azure  
 
Para outras fontes, como as VMs do Azure e as VMs do Windows ou do Linux em seu ambiente, veja os tópicos a seguir:

* [Coletar dados de máquinas virtuais do Azure](log-analytics-quick-collect-azurevm.md)
* [Coletar dados de um computador Linux híbrido](log-analytics-quick-collect-linux-computer.md)
* [Coletar dados de um computador Windows híbrido](log-analytics-quick-collect-windows-computer.md)

Se você não tiver uma assinatura do Azure, crie [uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este início rápido exigirá a execução da CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Criar um espaço de trabalho
Criar um espaço de trabalho com [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). O exemplo a seguir cria um espaço de trabalho chamado *TestWorkspace* no grupo de recursos *Laboratório* no local *eastus* usando um modelo do Resource Manager de seu computador local. O modelo JSON está configurado para solicitar apenas o nome do espaço de trabalho e especifica um valor padrão para os outros parâmetros que provavelmente seriam usados como uma configuração padrão em seu ambiente. Ou ainda armazenar o modelo em uma conta de armazenamento do Azure para acesso compartilhado na sua organização. Para obter mais informações sobre como trabalhar com modelos, veja [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)

Os parâmetros a seguir definem um valor padrão:

* local – assume por padrão o valor Leste dos EUA
* SKU – assume por padrão o novo tipo de preço por GB lançado no modelo de preço de abril de 2018

>[!WARNING]
>Se criar ou configurar um espaço de trabalho do Log Analytics em uma assinatura que tiver aceitado o novo modelo de preços de abril de 2018, o único tipo de preço válido do Log Analytics **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Criar e implantar modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Edite o modelo para atender às suas necessidades.  Revisão de referência[Microsoft.OperationalInsights/workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) para saber quais propriedades e os valores são suportados. 
3. Salve esse arquivo como **deploylaworkspacetemplate.json** para uma pasta local.   
4. Você está pronto para implantar o modelo. Use os seguintes comandos na pasta que contém o modelo:

    ```azurecli
    azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json
    ```

A implantação pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem semelhante que inclui o resultado:

![Resultados de exemplo, quando a implantação for concluída](./media/log-analytics-template-workspace-configuration/template-output-01.png)

## <a name="next-steps"></a>Próximas etapas
Agora que já tem um espaço de trabalho disponível, você pode configurar a coleta de monitoramento de telemetria, executar pesquisas de logs para analisar os dados e adicionar uma solução de gerenciamento para fornecer dados adicionais e informações analíticas.  

* Para habilitar a coleta de dados de recursos do Azure com o Diagnóstico do Azure ou com o armazenamento do Azure, consulte [Coletar logs e as métricas do serviço do Azure para uso no Log Analytics](log-analytics-azure-storage.md).  
* Adicione o [System Center Operations Manager como uma fonte de dados](log-analytics-om-agents.md) para coletar dados de agentes que se reportam ao seu grupo de gerenciamento do Operations Manager e armazene-o em seu repositório de espaço de trabalho do Log Analytics.  
* Conecte-se ao [Configuration Manager](log-analytics-sccm.md) para importar computadores que são membros de coleções na hierarquia.  
* Examine as [soluções de gerenciamento](log-analytics-add-solutions.md) disponíveis e como adicionar ou remover uma solução do espaço de trabalho.