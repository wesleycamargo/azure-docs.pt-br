---
title: Criar um tempo de execução da integração auto-hospedada compartilhado no Azure Data Factory com o PowerShell | Microsoft Docs
description: Aprenda como criar um tempo de execução de integração auto-hospedado compartilhado no Azure Data Factory, para que vários data factories possam acessar o tempo de execução de integração.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: abnarain
ms.openlocfilehash: 22dbe9b29059ff351cd1937f72f6dc742b24c690
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993874"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory-with-powershell"></a>Criar um tempo de execução da integração auto-hospedada compartilhado no Azure Data Factory com o PowerShell

Este guia passo a passo mostra como criar um tempo de execução de integração auto-hospedado compartilhado no Azure Data Factory usando o Azure PowerShell. Em seguida, você pode usar o tempo de execução da integração auto-hospedada compartilhado em outro data factory. Neste tutorial, você deve executar as seguintes etapas: 

1. Criar um data factory. 
1. Criar um tempo de execução de integração auto-hospedado.
1. Compartilhe o tempo de execução da integração auto-hospedada com outros data factories.
1. Crie um tempo de execução de integração vinculado.
1. Revogue o compartilhamento.

## <a name="prerequisites"></a>Pré-requisitos 

- **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

- **Azure PowerShell**. Siga as instruções em [Instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.11.0). Você usa o PowerShell para executar um script para criar um tempo de execução da integração auto-hospedada que pode ser compartilhado com outros data factories. 

> [!NOTE]  
> Para obter uma lista de regiões do Azure nas quais o Data Factory está atualmente disponível, selecione as regiões que lhe interessam em [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Inicie o ISE (ambiente de script integrado) do Windows PowerShell.

1. Crie variáveis. Copie e cole o seguinte script. Substitua as variáveis, como **SubscriptionName** e **ResourceGroupName**, com valores reais: 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Entre e selecione uma assinatura. Adicione o código a seguir ao script para entrar e selecionar sua assinatura do Azure:

    ```powershell
    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

1. Crie um grupo de recursos e um Data Factory.

    > [!NOTE]  
    > Esta etapa é opcional. Se você já tiver um data factory, ignore esta etapa. 

    Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usando o comando [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-6.11.0). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` na localização WestEurope: 

    ```powershell
    New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Execute o comando a seguir para criar um data factory: 

    ```powershell
    Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

## <a name="create-a-self-hosted-integration-runtime"></a>Criar um Integration Runtime auto-hospedado

> [!NOTE]  
> Esta etapa é opcional. Se você já possui o tempo de execução de integração auto-hospedado que deseja compartilhar com outros data factories, pule esta etapa.

Execute o comando a seguir para criar um tempo de execução da integração auto-hospedada:

```powershell
$SharedIR = Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Obter a chave de autenticação do tempo de execução de integração e registrar um nó

Execute o comando a seguir para obter a chave de autenticação para o tempo de execução da integração auto-hospedada:

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

A resposta contém a chave de autenticação para esse tempo de execução da integração auto-hospedada. Use essa chave ao registrar o nó do tempo de execução de integração.

### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Instale e registre o tempo de execução da integração auto-hospedada

1. Baixe o instalador do tempo de execução da integração auto-hospedada de [Tempo de execução de integração do Azure Data Factory](https://aka.ms/dmg).

2. Execute o instalador para instalar a integração auto-hospedada em um computador local.

3. Registre a nova integração auto-hospedada com a chave de autenticação que você recuperou em uma etapa anterior.

## <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Compartilhar o tempo de execução da integração auto-hospedada com outro data factory

### <a name="create-another-data-factory"></a>Criar outro data factory

> [!NOTE]  
> Esta etapa é opcional. Se você já possui o data factory com o qual deseja compartilhar, pule esta etapa.

```powershell
$factory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
### <a name="grant-permission"></a>Conceder permissão

Conceda permissão para o data factory que precisa acessar o tempo de execução de integração auto-hospedado que você criou e registrou.

> [!IMPORTANT]  
> Não ignore esta etapa!

```powershell
New-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

## <a name="create-a-linked-self-hosted-integration-runtime"></a>Criar um tempo de execução da integração auto-hospedada vinculado

Execute o comando a seguir para criar um tempo de execução da integração auto-hospedada vinculado:

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Agora você pode usar esse tempo de execução de integração vinculado em qualquer serviço vinculado. O tempo de execução de integração vinculado usa o tempo de execução de integração compartilhada para executar atividades.

## <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Revogar o compartilhamento do tempo de execução de integração de um data factory

Para revogar o acesso de um data factory a partir do tempo de execução de integração compartilhada, execute o seguinte comando:

```powershell
Remove-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

Para remover o tempo de execução de integração vinculado existente, execute o seguinte comando no tempo de execução de integração compartilhada:

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

## <a name="next-steps"></a>Próximas etapas

- Revise os [conceitos de tempo de execução de integração no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).

- Saiba como [criar um tempo de execução de integração auto-hospedado no portal do Azure](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).
