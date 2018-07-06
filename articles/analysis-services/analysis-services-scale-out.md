---
title: Escala horizontal do Azure Analysis Services | Microsoft Docs
description: Replicar os servidores do Azure Analysis Services com escala horizontal
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4cb7b165311f57fadd63770646907ddfc0378844
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445013"
---
# <a name="azure-analysis-services-scale-out"></a>Escala horizontal do Azure Analysis Services

Com a escala horizontal, as consultas de cliente podem ser distribuídas entre várias *réplicas de consulta* em um pool de consulta, reduzindo os tempos de resposta durante cargas de trabalho de consulta altas. Você também pode separar o processamento do pool de consultas, garantindo que as consultas não são afetadas negativamente pelas operações de processamento. A escala horizontal pode ser configurada no Portal do Azure ou usando a API REST do Azure Analysis Services.

## <a name="how-it-works"></a>Como ele funciona

Em uma implantação típica do servidor, um servidor funciona como o servidor de processamento e o servidor de consulta. Se o número de consultas de cliente em modelos em seu servidor exceder as QPUs (Unidades de Processamento de Consulta) do plano do servidor ou o processamento de modelo ocorrer ao mesmo tempo que cargas de trabalho de consulta altas, o desempenho poderá diminuir. 

Com a escala horizontal, você pode criar um pool de consultas com até sete réplicas de consulta adicionais (total de oito, incluindo o servidor). Você pode dimensionar o número de réplicas de consulta para atender às demandas de QPU em momentos críticos e você pode separar um servidor de processamento do pool de consultas a qualquer momento. Todas as réplicas de consulta são criadas na mesma região que o servidor.

Independentemente do número de réplicas de consulta que você tem em um pool de consulta, as cargas de trabalho de processamento não são distribuídas entre as réplicas de consulta. Um único servidor atua como o servidor de processamento. Réplicas de consulta servem apenas as consultas em relação aos modelos sincronizados entre cada réplica no pool de consulta. 

Quando as operações de processamento são concluídas, a sincronização deve ser executada entre o servidor de processamento e os servidores de réplica de consulta. Para automatizar as operações de processamento, é importante configurar uma operação de sincronização após a conclusão bem-sucedida de operações de processamento. A sincronização pode ser executada manualmente no portal ou usando o PowerShell ou a API REST.

> [!NOTE]
> A escala horizontal está disponível para servidores no tipo de preço Standard. Cada réplica de consulta é cobrada com a mesma taxa de seu servidor.

> [!NOTE]
> A escala horizontal não aumenta a quantidade de memória disponível para o servidor. Para aumentar a memória, você precisa atualizar seu plano.

## <a name="region-limits"></a>Limites de região

O número de réplicas de consulta que você pode configurar é limitado pela região em que o seu servidor está. Os limites a seguir se aplicam:

|Região  |Número máximo de réplicas  |
|---------|---------|
|Leste dos EUA 2    |    7     |
|Centro-Oeste dos EUA     |    7     |
|Europa Ocidental     |    7     |
|Oeste dos EUA     |     7    |
|Centro dos EUA     |     3    |
|Sudeste Asiático    |     3    |
|Todas as outras regiões  |   1    |



## <a name="monitor-qpu-usage"></a>Monitorar o uso de QPU

 Para determinar se a escala horizontal para seu servidor é necessária, monitore o servidor no Portal do Azure usando Métricas. Se a QPU for maximizada regularmente, isso significa que o número de consultas em relação aos modelos está excedendo o limite de QPU do plano. A métrica de comprimento da fila do trabalho do pool de consulta também aumenta quando o número de consultas na fila do pool de thread de consulta excede a QPU disponível. Para obter mais informações, consulte [Monitorar métricas do servidor](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Configurar a escala horizontal

### <a name="in-azure-portal"></a>No Portal do Azure

1. No portal, clique em **Escalar horizontalmente**. Use o controle deslizante para selecionar o número de servidores de réplica de consulta. O número de réplicas escolhido é além dos servidores existentes.

2. Em **Separar o servidor de processamento do pool de consulta**, selecione Sim para excluir o servidor de processamento dos servidores de consulta.

   ![Controle deslizante da escala horizontal](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Clique em **Salvar** para provisionar os novos servidores de réplica de consulta. 

Modelos tabulares no servidor primário são sincronizados com os servidores de réplica. Quando a sincronização é concluída, o pool de consulta começa a distribuir as consultas de entrada entre os servidores de réplica. 


## <a name="synchronization"></a>Sincronização 

Quando você provisiona novas réplicas de consulta, o Azure Analysis Services replica automaticamente seus modelos em todas as réplicas. Também execute uma sincronização manual usando o portal ou a API REST. Ao processar seus modelos, execute uma sincronização, de modo que as atualizações sejam sincronizadas entre as réplicas de consulta.

### <a name="in-azure-portal"></a>No Portal do Azure

Em **Visão Geral** > modelo > **Sincronizar modelo**.

![Controle deslizante da escala horizontal](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>API REST
Use a operação de **sincronização**.

#### <a name="synchronize-a-model"></a>Sincronizar um modelo   
`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Obter o status de sincronização  
`GET https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
Antes de usar o PowerShell, [instale ou atualize o último módulo do AzureRM](https://github.com/Azure/azure-powershell/releases). 

Para definir o número de réplicas de consulta, use [Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver). Especifique o parâmetro `-ReadonlyReplicaCount` opcional.

Para executar a sincronização, use [Sync-AzureAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).



## <a name="connections"></a>conexões

Na página Visão Geral do servidor, há dois nomes de servidor. Se você ainda não tiver configurado a escala horizontal para um servidor, os dois nomes de servidor funcionam da mesma forma. Depois de configurar a expansão para um servidor, você precisará especificar o nome do servidor adequado dependendo do tipo de conexão. 

Para conexões de cliente do usuário final como o Power BI Desktop, Excel e aplicativos personalizados, use **Nome do servidor**. 

Para SSMS, SSDT e cadeias de conexão no PowerShell, aplicativos do Azure Functions e AMO, use o **Nome do servidor de gerenciamento**. O nome do servidor de gerenciamento inclui um qualificador especial `:rw` (leitura-gravação). Todas as operações de processamento ocorrerem no servidor de gerenciamento.

![nomes do servidor](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>Informações relacionadas

[Monitorar métricas do servidor](analysis-services-monitor.md)   
[Gerenciar Azure Analysis Services](analysis-services-manage.md) 

