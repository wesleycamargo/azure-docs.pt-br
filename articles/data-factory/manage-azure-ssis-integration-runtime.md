---
title: "Reconfigurar o tempo de execução de integração do Azure-SSIS | Microsoft Docs"
description: "Saiba como reconfigurar o tempo de execução de integração do Azure-SSIS no Azure Data Factory depois de já tê-lo provisionado."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: spelluru
ms.openlocfilehash: f1745df7bafc4b5fa6f04cbfbc81a7a70a233d23
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="manage-an-azure-ssis-integration-runtime"></a>Gerenciar um tempo de execução de integração do Azure-SSIS
O artigo [Criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md) mostra como criar um IR (tempo de execução de integração) do Azure-SSIS usando o Azure Data Factory. Este artigo fornece informações sobre a reconfiguração de um tempo de execução de integração do Azure-SSIS existente.  

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Documentação do Data Factory versão 1](v1/data-factory-introduction.md).


## <a name="data-factory-ui"></a>IU do Data Factory 
Use a interface do usuário do Data Factory para parar, editar/reconfigurar ou excluir um IR do Azure SSIS. 

1. Na **Interface do usuário do Data Factory**, mude para a guia **Editar**. Para iniciar a interface do usuário do Data Factory, clique em **Criar e Monitorar** na home page do data factory.
2. No painel esquerdo, clique em **Conexões**.
3. No painel direito, mude para os **Tempos de Execução de Integração**. 
4. Use os botões na coluna Ações para **parar**, **editar** ou **excluir** o tempo de execução de integração. O botão **Código** na coluna **Ações** permite que você exiba a definição de JSON associada ao tempo de execução de integração.  
    
    ![Ações para IR do Azure SSIS](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Para reconfigurar um IR do Azure SSIS
1. Pare o tempo de execução de integração clicando em **Parar** na coluna **Ações**. Para atualizar a exibição de lista, clique em **Atualizar** na barra de ferramentas. Depois que o IR for interrompido, você verá que a primeira ação permite iniciar o IR. 

    ![Ações para IR do Azure SSIS – após a interrupção](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Edite/reconfigure o IR clicando no botão **Editar** na coluna **Ações**. Na janela **Configuração do Tempo de Execução de Integração**, altere as configurações (por exemplo, tamanho do nó, número de nós ou número máximo de execuções paralelas por nó). 
3. Para reiniciar o IR, clique no botão **Iniciar** na coluna **Ações**.     

## <a name="azure-powershell"></a>Azure PowerShell
Depois de provisionar e iniciar uma instância do tempo de execução de integração do Azure-SSIS, você pode reconfigurá-lo executando uma sequência de cmdlets do PowerShell `Stop` - `Set` - `Start` consecutivamente. Por exemplo, o script do PowerShell a seguir altera o número de nós alocados para a instância de tempo de execução de integração do Azure-SSIS para cinco.

### <a name="reconfigure-an-azure-ssis-ir"></a>Reconfigurar um IR do Azure-SSIS

1. Primeiro, interrompa o tempo de execução de integração do Azure-SSIS usando o [Stop-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/stop-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) cmdlet. Este comando libera todos os seus nós e interrompe a cobrança.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Em seguida, reconfigure o IR do Azure-SSIS usando o cmdlet [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1). O comando de exemplo a seguir escala horizontalmente um tempo de execução de integração do Azure-SSIS para cinco nós.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Em seguida, inicialize o tempo de execução de integração do Azure-SSIS usando o cmdlet [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1). Esse comando aloca todos os seus nós para executar pacotes do SSIS.   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Excluir um IR do Azure-SSIS
1. Primeiro, liste todos os IRs do Azure SSIS em seu data factory.

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Em seguida, pare todos os IRs do Azure SSIS em seu data factory.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Em seguida, remova todos os IRs do Azure SSIS de seu data factory um por um.

    ```powershell
    Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Por fim, remova o data factory.

    ```powershell
    Remove-AzureRmDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Se tiver criado um novo grupo de recursos, remova-o.

    ```powershell
    Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Próximas etapas
Confira estes tópicos para obter mais informações sobre o tempo de execução do Azure-SSIS: 

- [Tempo de execução de integração do Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre tempos de execução de integração em geral, incluindo o IR do Azure-SSIS. 
- [Tutorial: implantar pacotes do SSIS para o Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo fornece instruções passo a passo para criar um IR do Azure-SSIS e usa um banco de dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Como: Criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar a Instância Gerenciada do SQL do Azure (visualização particular) e unir o IR para uma VNet. 
- [Unir um IR do Azure-SSIS a uma VNet](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece informações conceituais sobre como unir um IR do Azure-SSIS a uma rede virtual (VNet) do Azure. Ele também apresenta as etapas para usar o portal do Azure para configurar a VNet para que o IR do Azure-SSIS possa unir-se à VNet. 
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar informações sobre um IR do Azure-SSIS e descrições de status nas informações retornadas. 
 
