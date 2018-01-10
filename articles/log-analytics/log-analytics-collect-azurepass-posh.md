---
title: "Coletar métricas de recursos de PaaS do Azure com Log Analytics | Microsoft Docs"
description: "Saiba como habilitar a coleta de métricas de recurso de PaaS do Azure usando o PowerShell para retenção e análise no Log Analytics."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: magoedte
ms.openlocfilehash: 83491c4902dabc6bab1e222551298cfaffbaecf4
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>Coletar métricas de recursos de PaaS do Azure com Log Analytics

Plataforma do Azure como um recurso de serviço (PaaS), como SQL Azure e Sites da Web (aplicativos da Web), pode emitir os dados de métricas de desempenho de forma nativa para o Log Analytics. Esse script permite aos usuários habilitarem as métricas de registro em log para recursos de PaaS já implantados em um grupo de recursos específicos ou em uma assinatura inteira. 

Hoje, não há nenhuma maneira de habilitar registro de métricas para recursos de PaaS através do portal do Azure. Portanto, você precisa usar um script do PowerShell. Esse recurso de registro de métricas nativo junto com monitoramento de Log Analytics permitem monitorar recursos do Azure em grande escala. 

## <a name="prerequisites"></a>pré-requisitos
Verifique se você tem os seguintes módulos do Azure Resource Manager instalados no seu computador antes de continuar:

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>É recomendável que todos os seus módulos do Azure Resource Manager estejam na mesma versão para garantir a compatibilidade quando você executar comandos do Gerenciador de Recursos do Azure do PowerShell.
>
Para instalar a versão mais recente dos módulos do Azure Resource Manager em seu computador, consulte [Instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps).  

## <a name="enable-azure-diagnostics"></a>Habilitar o Diagnóstico do Azure  
Configurar o Diagnóstico do Azure para recursos de PaaS é feito por meio da execução do script, **Enable-AzureRMDiagnostics.ps1**, que está disponível na [Galeria do PowerShell](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52/DisplayScript).  O script oferece suporte para os seguintes cenários:
  
* Especificar um recurso relacionado a um ou mais grupos de recursos em uma assinatura  
* Especificar um recurso relacionado a um grupo de recursos em uma assinatura  
* Reconfigurar um recurso para encaminhar para outro espaço de trabalho

Há suporte para apenas os recursos que oferecem suporte a coletar métricas com o diagnóstico do Azure e enviam diretamente para o Log Analytics.  Para uma lista mais detalhada, reveja [Coletar logs e métricas do serviço do Azure para uso no Log Analytics](log-analytics-azure-storage.md) 

Execute as etapas a seguir para fazer o download e executar o script.

1.  Na tela inicial do Windows, digite **PowerShell** e com o botão direito no PowerShell nos resultados da pesquisa.  Selecione no menu **Executar como Administrador**.   
2. Salve o arquivo de script **Enable-AzureRMDiagnostics.ps1** localmente, executando o seguinte comando e fornecendo um caminho para armazenar o script.    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. Execute `Login-AzureRmAccount` para criar uma conexão com o Azure.   
4. Execute o seguinte script `.\Enable-AzureRmDiagnostics.ps1` sem parâmetros para habilitar a coleta de dados de um recurso específico em sua assinatura ou com o parâmetro `-ResourceGroup <myResourceGroup>` para especificar um recurso em um grupo de recursos específicos.   
5. Selecione a assinatura apropriada na lista se você tiver mais de um, digitando o valor correto.<br><br> ![Selecione a assinatura retornada pelo script](./media/log-analytics-collect-azurepass-posh/script-select-subscription.png)<br> Caso contrário, ele seleciona automaticamente a assinatura única disponível.
6. Em seguida, o script retorna uma lista de espaços de trabalho de Log Analytics registrados na assinatura.  Selecione um apropriado da lista.<br><br> ![Selecione o espaço de trabalho retornado pelo script](./media/log-analytics-collect-azurepass-posh/script-select-workspace.png)<br> 
7. Selecione os recursos do Azure dos quais você gostaria de habilitar a coleta. Por exemplo, se você digitar 5, você habilita coleta de dados para bancos de dados do SQL Azure.<br><br> ![Tipo de recurso selecione retornado pelo script](./media/log-analytics-collect-azurepass-posh/script-select-resource.png)<br>
   Há suporte para apenas os recursos que oferecem suporte para coletar métricas com o Diagnóstico do Azure e enviar diretamente para o Log Analytics.  O script mostrará um valor de **Verdadeiro** sob a coluna **Métricas** para a lista de recursos detectados na sua assinatura ou grupo de recursos especificado.    
8. Será solicitado que você confirme a seleção.  Digite **Y** para habilitar o log de métricas para todos os recursos selecionados para o escopo definido, que, em nosso exemplo, são todos os bancos de dados do SQL na assinatura.  

O script será executado em todos os recursos selecionados de critérios de correspondência e habilitada a coleta de métricas para eles. Depois que ele for concluído, você verá uma mensagem indicando que a configuração está concluída.  

Logo após a conclusão, você começará a ver os dados do recurso PaaS do Azure no seu repositório de Log Analytics.  Um registro com tipo `AzureMetrics` é criado e analisando esses registros são compatíveis com o [Análise de SQL do Azure](log-analytics-azure-sql.md) e soluções de gerenciamento [Análise de Aplicativos Web do Azure](log-analytics-azure-web-apps-analytics.md).   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>Atualizar um recurso para enviar dados para outro espaço de trabalho
Se você tiver um recurso que já está enviando dados para um espaço de trabalho de Log Analytics e posteriormente decidir reconfigurá-lo para fazer referência a outro espaço de trabalho, você pode executar o script com o parâmetro `-Update`.  

**Exemplo:** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

Você será solicitado a responder as mesmas informações de que quando você executou o script para executar a configuração inicial.  

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções. 

* Use [Campos Personalizados](log-analytics-custom-fields.md) (para analisar os registros de eventos em campos individuais.

* Revise [Criar um painel personalizado para uso de Log Analytics](log-analytics-dashboards.md) para entender como visualizar o log de pesquisas de maneiras significativas para a organização.