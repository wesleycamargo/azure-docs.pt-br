---
title: Usar o validador de modelo para verificar modelos de pilha do Azure | Microsoft Docs
description: "Modelos de verificação para implantação no Azure pilha"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 6a77efb3ef4236048ff08b14346175b592493982
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="check-your-templates-for-azure-stack-with-template-validator"></a>Verifique seus modelos de pilha do Azure com o validador de modelo

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode usar a ferramenta de validação de modelo para verificar se o Gerenciador de recursos do Azure [modelos](azure-stack-arm-templates.md) estão prontos para a pilha do Azure. A ferramenta de validação de modelo está disponível como parte das ferramentas de pilha do Azure. Baixar as ferramentas de pilha do Azure usando as etapas descritas no [baixar as ferramentas do GitHub](azure-stack-powershell-download.md) artigo. 

Para validar modelos, você use os seguintes módulos do PowerShell no **TemplateValidator** e **CloudCapabilities** pastas: 

 - AzureRM.CloudCapabilities.psm1 cria um arquivo JSON de recursos de nuvem que representa os serviços e as versões em uma nuvem como pilha do Azure.
 - AzureRM.TemplateValidator.psm1 usa um arquivo JSON de recursos de nuvem para testar modelos de implantação na pilha do Azure.
 
Neste artigo, você cria um arquivo de recursos de nuvem e, em seguida, execute a ferramenta de validação.

## <a name="build-cloud-capabilities-file"></a>Criar arquivo de recursos de nuvem
Antes de usar o validador de modelo, execute o módulo AzureRM.CloudCapabilities PowerShell para criar um arquivo JSON. Se você atualizar seu sistema, ou adicione novos serviços ou extensões de VM também deve executar esse módulo novamente.

1.  Verifique se que você tem conectividade com a pilha do Azure. Essas etapas podem ser executadas do host do kit de desenvolvimento de pilha do Azure, ou você pode usar um [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) para se conectar de sua estação de trabalho. 
2.  Importe o módulo do AzureRM.CloudCapabilities PowerShell:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ``` 

3.  Use o cmdlet Get-CloudCapabilities para recuperar versões de serviço e criar um arquivo JSON de recursos de nuvem. Se você não especificar - OutputPath, o arquivo AzureCloudCapabilities.Json é criado no diretório atual. Use o local real:

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```             

## <a name="validate-templates"></a>Validar modelos
Nestas etapas, você pode validar modelos usando o módulo AzureRM.TemplateValidator PowerShell. Você pode usar seus próprios modelos ou validar a [modelos de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates).

1.  Importe o módulo do PowerShell AzureRM.TemplateValidator.psm1:
    
    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2.  Execute o validador do modelo:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Quaisquer erros ou avisos de validação do modelo estão conectados ao console do PowerShell e um arquivo HTML no diretório de origem. Aqui está um exemplo de relatório de validação:

![relatório de validação de exemplo](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>parâmetros

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
Este exemplo valida todos o [modelos de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates) baixado localmente e também valida as extensões contra recursos do Kit de desenvolvimento de pilha do Azure e tamanhos de VM.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```


## <a name="next-steps"></a>Próximas etapas
 - [Implantar modelos na pilha do Azure](azure-stack-arm-templates.md)
 - [Desenvolver modelos para o Azure Stack](azure-stack-develop-templates.md)

