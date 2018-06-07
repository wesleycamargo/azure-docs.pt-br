---
title: Use uma ferramenta de validação de modelo para procurar modelos a pilha do Azure | Microsoft Docs
description: Modelos de verificação para implantação no Azure pilha
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 1800db8cd25aa37bffb76adf00901e11c75d49fe
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604073"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>Verifique seus modelos de pilha do Azure com a ferramenta de validação de modelo

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode usar a ferramenta de validação de modelo para verificar se o Gerenciador de recursos do Azure [modelos](azure-stack-arm-templates.md) está pronto para implantar a pilha do Azure. A ferramenta de validação de modelo está disponível como parte das ferramentas de pilha do Azure. Baixar as ferramentas de pilha do Azure usando as etapas descritas no [baixar as ferramentas do GitHub](azure-stack-powershell-download.md) artigo.

## <a name="overview"></a>Visão geral

Para validar um modelo, você precisa criar uma nuvem de recursos de arquivo primeiro e, em seguida, execute a ferramenta de validação. Você usa os seguintes módulos do PowerShell de ferramentas do Azure pilha:

- No **TemplateValidator** pasta:<br>         AzureRM.CloudCapabilities.psm1 cria um arquivo JSON de recursos de nuvem que representa os serviços e as versões em uma nuvem de pilha do Azure.
- No **CloudCapabilities** pasta:<br>
AzureRM.TemplateValidator.psm1 usa um arquivo JSON de recursos de nuvem para testar modelos de implantação na pilha do Azure.

## <a name="build-the-cloud-capabilities-file"></a>Criar o arquivo de recursos de nuvem

Antes de usar o validador de modelo, execute o módulo AzureRM.CloudCapabilities PowerShell para criar um arquivo JSON.

>[!NOTE]
>Se você atualizar seu sistema, ou adicione novos serviços ou extensões virtual, você deve executar este módulo novamente.

1. Verifique se que você tem conectividade com a pilha do Azure. Essas etapas podem ser executadas do host do kit de desenvolvimento de pilha do Azure, ou você pode usar um [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) para se conectar de sua estação de trabalho.
2. Importe o módulo do AzureRM.CloudCapabilities PowerShell:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. Use o cmdlet Get-CloudCapabilities para recuperar versões de serviço e criar um arquivo JSON de recursos de nuvem. Se você não especificar **- OutputPath**, o arquivo AzureCloudCapabilities.Json é criado no diretório atual. Use o local real:

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Validar modelos

Siga estas etapas para validar modelos usando o módulo AzureRM.TemplateValidator PowerShell. Você pode usar seus próprios modelos ou validar a [modelos de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Importe o módulo do PowerShell AzureRM.TemplateValidator.psm1:

    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. Execute o validador do modelo:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Erros ou avisos de validação do modelo estão conectados ao console do PowerShell e um arquivo HTML no diretório de origem. A captura de tela a seguir mostra um exemplo de um relatório de validação:

![Relatório de validação de modelo](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>parâmetros

O validador de modelo suporta os seguintes parâmetros.

| Parâmetro | DESCRIÇÃO | Obrigatório |
| ----- | -----| ----- |
| TemplatePath | Especifica o caminho para recursivamente encontrar modelos do Gerenciador de recursos do Azure | Sim | 
| TemplatePattern | Especifica o nome dos arquivos de modelo para fazer a correspondência. | Não  |
| CapabilitiesPath | Especifica o caminho para o arquivo JSON de recursos de nuvem | Sim | 
| IncludeComputeCapabilities | Inclui a avaliação dos recursos de IaaS como tamanhos de VM e extensões de VM | Não  |
| IncludeStorageCapabilities | Inclui a avaliação dos recursos de armazenamento como tipos SKU | Não  |
| Relatório | Especifica o nome do relatório HTML gerado | Não  |
| Detalhado | Registra erros e avisos para o console | Não |

### <a name="examples"></a>Exemplos

Este exemplo valida todos os [modelos de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates) baixado para o armazenamento local. O exemplo também valida tamanhos de máquina virtual e extensões contra recursos do Kit de desenvolvimento de pilha do Azure.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="next-steps"></a>Próximas etapas

- [Implantar modelos na pilha do Azure](azure-stack-arm-templates.md)
- [Desenvolver modelos para o Azure Stack](azure-stack-develop-templates.md)
