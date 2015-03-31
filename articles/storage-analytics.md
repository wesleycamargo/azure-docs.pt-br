<properties 
	pageTitle="Análise de Armazenamento" 
	description="Como gerenciar a simultaneidade para os serviços Blob, Fila, Tabela e Arquivo" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/08/2014" 
	ms.author="tamram"/>

# Análise de Armazenamento

A análise de armazenamento do Azure executa registro em log e fornece dados de métrica para uma conta de armazenamento. Você pode usar esses dados para rastrear solicitações, analisar tendências de uso e diagnosticar problemas com sua conta de armazenamento.

Para usar a análise de armazenamento, você deve habilitá-la separadamente para cada serviço que você deseja monitorar. Você pode habilitá-la no [Portal de gerenciamento do Azure](https://manage.windowsazure.com/). Para obter detalhes, consulte [Como monitorar uma conta de armazenamento](http://www.azure.com/manage/services/storage/how-to-monitor-a-storage-account/). Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use as operações [Obter propriedades do serviço Blob](https://msdn.microsoft.com/pt-br/library/hh452239.aspx), [Obter propriedades do serviço de fila](https://msdn.microsoft.com/pt-br/library/hh452243.aspx), e [Obter propriedades do serviço de tabela](https://msdn.microsoft.com/pt-br/library/hh452238.aspx) para habilitar a análise de armazenamento para cada serviço.

Os dados agregados são armazenados em um blob conhecido (para registro em log) e em tabelas conhecidas (para métricas), que podem ser acessados usando os serviços de Blob e APIs do serviço de tabela

A análise de armazenamento tem um limite de 20TB na quantidade de dados armazenados são independentes do limite total para sua conta de armazenamento. Para obter mais informações sobre cobrança e políticas de retenção de dados, consulte [análise de armazenamento e cobrança](https://msdn.microsoft.com/library/hh360997.aspx). Para obter mais informações sobre os limites da conta de armazenamento, consulte [Metas de desempenho e escalabilidade do armazenamento do Azure](https://msdn.microsoft.com/library/dn249410.aspx).

Para um guia aprofundado sobre como usar a Análise de Armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao Armazenamento do Azure, consulte [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](http://azure.microsoft.com/documentation/articles/storage-monitoring-diagnosing-troubleshooting/).



<!--HONumber=47-->
