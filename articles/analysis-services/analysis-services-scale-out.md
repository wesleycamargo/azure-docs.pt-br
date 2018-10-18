---
title: Escala horizontal do Azure Analysis Services | Microsoft Docs
description: Replicar os servidores do Azure Analysis Services com escala horizontal
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7c0aa2d43001100a392f8882316b7998838d90b9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121897"
---
# <a name="azure-analysis-services-scale-out"></a>Escala horizontal do Azure Analysis Services

Com a escala horizontal, as consultas de cliente podem ser distribuídas entre várias *réplicas de consulta* em um pool de consulta, reduzindo os tempos de resposta durante cargas de trabalho de consulta altas. Você também pode separar o processamento do pool de consultas, garantindo que as consultas não são afetadas negativamente pelas operações de processamento. A escala horizontal pode ser configurada no Portal do Azure ou usando a API REST do Azure Analysis Services.

## <a name="how-it-works"></a>Como ele funciona

Em uma implantação típica do servidor, um servidor funciona como o servidor de processamento e o servidor de consulta. Se o número de consultas de cliente em modelos em seu servidor exceder as QPUs (Unidades de Processamento de Consulta) do plano do servidor ou o processamento de modelo ocorrer ao mesmo tempo que cargas de trabalho de consulta altas, o desempenho poderá diminuir. 

Com o scale-out, você pode criar um pool de consulta com até sete recursos adicionais de réplica de consulta (oito no total, incluindo seu servidor). Você pode dimensionar o número de réplicas de consulta para atender às demandas de QPU em momentos críticos e você pode separar um servidor de processamento do pool de consultas a qualquer momento. Todas as réplicas de consulta são criadas na mesma região que o servidor.

Independentemente do número de réplicas de consulta que você tem em um pool de consulta, as cargas de trabalho de processamento não são distribuídas entre as réplicas de consulta. Um único servidor atua como o servidor de processamento. As réplicas de consulta veiculam apenas consultas nos modelos sincronizados entre cada réplica de consulta no conjunto de consultas. 

Ao dimensionar, novas réplicas de consulta são adicionadas ao pool de consultas de forma incremental. Pode levar até cinco minutos para que novos recursos de réplica de consulta sejam incluídos no pool de consulta. Quando todas as novas réplicas de consulta estiverem ativadas e em execução, novas conexões de cliente serão balanceadas por carga em todos os recursos do pool de consulta. As conexões de clientes existentes não são alteradas a partir do recurso ao qual elas estão conectadas atualmente.  Ao dimensionar, quaisquer conexões de cliente existentes para um recurso de pool de consulta que está sendo removido do pool de consulta são finalizadas. Eles são reconectados a um recurso de pool de consulta restante quando a escala em operação é concluída, que pode levar até cinco minutos.

Ao processar modelos, depois que as operações de processamento são concluídas, uma sincronização deve ser executada entre o servidor de processamento e as réplicas de consulta. Para automatizar as operações de processamento, é importante configurar uma operação de sincronização após a conclusão bem-sucedida de operações de processamento. A sincronização pode ser executada manualmente no portal ou usando o PowerShell ou a API REST. 

### <a name="separate-processing-from-query-pool"></a>Separe o processamento do pool de consulta

Para desempenho máximo para operações de processamento e consulta, você pode optar por separar seu servidor de processamento do pool de consulta. Quando separadas, as conexões existentes e novas do cliente são atribuídas a réplicas de consulta apenas no pool de consulta. Se as operações de processamento ocuparem apenas um curto período de tempo, você poderá optar por separar seu servidor de processamento do pool de consulta apenas pelo tempo necessário para executar operações de processamento e sincronização e, em seguida, incluí-lo novamente no pool de consultas. 

> [!NOTE]
> A escala horizontal está disponível para servidores no tipo de preço Standard. Cada réplica de consulta é cobrada com a mesma taxa de seu servidor.

> [!NOTE]
> A escala horizontal não aumenta a quantidade de memória disponível para o servidor. Para aumentar a memória, você precisa atualizar seu plano.

## <a name="region-limits"></a>Limites de região

O número de réplicas de consulta que você pode configurar é limitado pela região em que o seu servidor está. Para obter mais informações, veja [Disponibilidade por região](analysis-services-overview.md#availability-by-region).

## <a name="monitor-qpu-usage"></a>Monitorar o uso de QPU

 Para determinar se a escala horizontal para seu servidor é necessária, monitore o servidor no Portal do Azure usando Métricas. Se a QPU for maximizada regularmente, isso significa que o número de consultas em relação aos modelos está excedendo o limite de QPU do plano. A métrica de comprimento da fila do trabalho do pool de consulta também aumenta quando o número de consultas na fila do pool de thread de consulta excede a QPU disponível. Para obter mais informações, consulte [Monitorar métricas do servidor](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Configurar a escala horizontal

### <a name="in-azure-portal"></a>No Portal do Azure

1. No portal, clique em **Escalar horizontalmente**. Use o controle deslizante para selecionar o número de servidores de réplica de consulta. O número de réplicas escolhido é além dos servidores existentes.

2. Em **Separar o servidor de processamento do pool de consulta**, selecione Sim para excluir o servidor de processamento dos servidores de consulta. Conexões de cliente usando a cadeia de caracteres de conexão padrão (sem: rw) são redirecionadas para as réplicas no pool de consulta. 

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

## <a name="troubleshoot"></a>Solucionar problemas

**Problema:** usuários obtêm o erro **não é possível localizar o servidor '\<nome do servidor >' instância no modo de conexão 'ReadOnly'.**

**Solução:** ao selecionar o **separar o servidor de processamento do pool de consulta** opção conexões de cliente usando a cadeia de caracteres de conexão padrão (sem: rw) são redirecionadas para réplicas de pool de consulta. Se as réplicas no pool de consulta estiverem ainda online porque a sincronização ainda não foi concluída, as conexões de cliente redirecionada podem falhar. Para evitar conexões com falha, escolha não separar o servidor de processamento do pool de consulta até que uma operação de expansão e sincronização sejam concluídas. Você pode usar as métricas de memória e QPU para monitorar o status de sincronização.

## <a name="related-information"></a>Informações relacionadas

[Monitorar métricas do servidor](analysis-services-monitor.md)   
[Gerenciar Azure Analysis Services](analysis-services-manage.md) 

