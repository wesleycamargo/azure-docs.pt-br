---
title: Use uma ferramenta de validação de modelo para verificar os modelos para o Azure Stack | Microsoft Docs
description: Verificar modelos para implantação no Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/27/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 4ec617fe49a3b55ba0608b77a8d1226fae025e6c
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246442"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>Verifique seus modelos para o Azure Stack com a ferramenta de validação de modelo

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode usar a ferramenta de validação de modelo para verificar se seu Gerenciador de recursos do Azure [modelos](azure-stack-arm-templates.md) está pronto para implantação no Azure Stack. A ferramenta de validação de modelo está disponível como parte das ferramentas do Azure Stack. Baixe as ferramentas do Azure Stack, usando as etapas descritas a [Baixe as ferramentas do GitHub](azure-stack-powershell-download.md) artigo.

## <a name="overview"></a>Visão geral

Para validar um modelo, você precisa criar uma nuvem de recursos de arquivo primeiro e, em seguida, execute a ferramenta de validação. Você usar os seguintes módulos do PowerShell de ferramentas do Azure Stack:

- No **CloudCapabilities** pasta:<br>         `AzureRM.CloudCapabilities.psm1` cria um arquivo JSON de recursos de nuvem que representam os serviços e as versões em uma nuvem do Azure Stack.
- No **TemplateValidator** pasta:<br>
`AzureRM.TemplateValidator.psm1` usa um arquivo JSON de recursos de nuvem para testar modelos para implantação no Azure Stack.

## <a name="build-the-cloud-capabilities-file"></a>Compile o arquivo de recursos de nuvem

Antes de usar o validador de modelo, execute as **AzureRM.CloudCapabilities** módulo do PowerShell para criar um arquivo JSON.

>[!NOTE]
>Se você atualizar seu sistema integrado, ou adiciona novos serviços ou extensões virtual, você deve executar este módulo novamente.

1. Verifique se que você tem conectividade com o Azure Stack. Essas etapas podem ser executadas do host do kit de desenvolvimento do Azure Stack, ou você pode usar um [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) para se conectar de sua estação de trabalho.
2. Importar o **AzureRM.CloudCapabilities** módulo do PowerShell:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. Use o `Get-CloudCapabilities` cmdlet para recuperar versões de serviço e crie um arquivo JSON de recursos de nuvem. Se você não especificar **- OutputPath**, o arquivo AzureCloudCapabilities.Json é criado no diretório atual. Use o seu local real:

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Validar modelos

Use estas etapas para validar modelos usando o **AzureRM.TemplateValidator** módulo do PowerShell. Você pode usar seus próprios modelos ou validar a [modelos de início rápido do Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Importar o **AzureRM.TemplateValidator.psm1** módulo do PowerShell:

    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. Execute o validador de modelo:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Erros ou avisos de validação de modelo estão conectados ao console do PowerShell e um arquivo HTML no diretório de origem. A captura de tela a seguir mostra um exemplo de um relatório de validação:

![Relatório de validação de modelo](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>parâmetros

O validador de modelo suporta os seguintes parâmetros.

| Parâmetro | DESCRIÇÃO | Obrigatório |
| ----- | -----| ----- |
| TemplatePath | Especifica o caminho para recursivamente encontrar modelos do Azure Resource Manager | Sim | 
| TemplatePattern | Especifica o nome dos arquivos de modelo para corresponder. | Não  |
| CapabilitiesPath | Especifica o caminho para o arquivo JSON de recursos de nuvem | Sim | 
| IncludeComputeCapabilities | Inclui avaliação de recursos de IaaS, como tamanhos de VM e extensões de VM | Não  |
| IncludeStorageCapabilities | Inclui avaliação dos recursos de armazenamento assim como os tipos SKU | Não  |
| Relatório | Especifica o nome do relatório HTML gerado | Não  |
| Detalhado | Registra erros e avisos para o console | Não |

### <a name="examples"></a>Exemplos

Este exemplo valida que todos os [modelos de início rápido do Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) baixado para o armazenamento local. O exemplo também valida tamanhos de máquinas virtuais e extensões em relação aos recursos do Kit de desenvolvimento do Azure Stack:

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>Próximas etapas

- [Implantar modelos do Azure Stack](azure-stack-arm-templates.md)
- [Desenvolver modelos para o Azure Stack](azure-stack-develop-templates.md)
