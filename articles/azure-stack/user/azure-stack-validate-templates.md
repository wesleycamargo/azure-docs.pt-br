---
title: Usar o validador de modelo para verificar modelos de pilha do Azure | Microsoft Docs
description: "Modelos de verificação para implantação no Azure pilha"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: c99e5ebc2612e10f42bddbbd2f1c17d7404305d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="check-your-templates-for-azure-stack-with-template-validator"></a>Verifique seus modelos de pilha do Azure com o validador de modelo

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode usar a ferramenta de validação de modelo para verificar se o Gerenciador de recursos do Azure [modelos](azure-stack-arm-templates.md) estão prontos para a pilha do Azure. A ferramenta de validação de modelo está disponível como parte das ferramentas de pilha do Azure. Baixar as ferramentas de pilha do Azure usando as etapas descritas no [baixar as ferramentas do GitHub](azure-stack-powershell-download.md) artigo. 

Para validar modelos, você use os seguintes módulos do PowerShell e o arquivo JSON localizado em **TemplateValidator** e **CloudCapabilities** pastas: 

 - AzureRM.CloudCapabilities.psm1 cria um arquivo JSON de recursos de nuvem que representa os serviços e as versões em uma nuvem como pilha do Azure.
 - AzureRM.TemplateValidator.psm1 usa um arquivo JSON de recursos de nuvem para testar modelos de implantação na pilha do Azure.
 - AzureStackCloudCapabilities_with_AddOns_20170627.json é um arquivo de recursos de nuvem do padrão.  Você pode criar seus próprios ou usar esse arquivo para começar. 

Neste tópico, você executar a validação em relação a seus modelos e, opcionalmente, crie um arquivo de recursos de nuvem.

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

Quaisquer erros ou avisos de validação de modelo são conectados ao console do PowerShell e também são registrados em um arquivo HTML no diretório de origem. Um exemplo de saída do relatório de validação tem esta aparência:

![relatório de validação de exemplo](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>parâmetros

| Parâmetro | Descrição | Obrigatório |
| ----- | -----| ----- |
| TemplatePath | Especifica o caminho para recursivamente encontrar modelos do Gerenciador de recursos | Sim | 
| TemplatePattern | Especifica o nome dos arquivos de modelo para fazer a correspondência. | Não |
| CapabilitiesPath | Especifica o caminho para o arquivo JSON de recursos de nuvem | Sim | 
| IncludeComputeCapabilities | Inclui a avaliação dos recursos de IaaS como tamanhos de VM e extensões de VM | Não |
| IncludeStorageCapabilities | Inclui a avaliação dos recursos de armazenamento como tipos SKU | Não |
| Relatório | Especifica o nome do relatório HTML gerado | Não |
| Detalhado | Registra erros e avisos para o console | Não|


### <a name="examples"></a>Exemplos
Este exemplo valida todos o [modelos de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates) baixado localmente e também valida as extensões contra recursos do Kit de desenvolvimento de pilha do Azure e tamanhos de VM.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="build-cloud-capabilities-file"></a>Criar arquivo de recursos de nuvem
Os arquivos baixados incluem um padrão *AzureStackCloudCapabilities_with_AddOns_20170627.json* arquivo, que descreve as versões de serviço disponíveis no Kit de desenvolvimento de pilha do Azure com os serviços de PaaS instalados.  Se você instalar mais provedores de recursos, você pode usar o módulo do AzureRM.CloudCapabilities PowerShell para criar um arquivo JSON, incluindo os novos serviços.  

1.  Verifique se que você tem conectividade com a pilha do Azure.  Essas etapas podem ser executadas do host do kit de desenvolvimento de pilha do Azure, ou você pode usar [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) para se conectar de sua estação de trabalho. 
2.  Importe o módulo do AzureRM.CloudCapabilities PowerShell:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ``` 

3.  Use o cmdlet Get-CloudCapabilities para recuperar versões de serviço e criar um arquivo JSON de recursos de nuvem:

    ```PowerShell
    Get-AzureRMCloudCapabilities -Location 'local' -Verbose
    ```             


## <a name="next-steps"></a>Próximas etapas
 - [Implantar modelos na pilha do Azure](azure-stack-arm-templates.md)
 - [Desenvolver modelos para o Azure Stack](azure-stack-develop-templates.md)

