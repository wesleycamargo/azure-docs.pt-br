<properties 
	pageTitle="Monitorar uma conta dos Serviços de Mídia" 
	description="Descreve como configurar o monitoramento de sua conta de Serviços de Mídia no Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>





#<a id="monitormediaservicesaccount"></a>Como monitorar uma conta dos Serviços de Mídia

Este artigo faz parte das séries de [Vídeo de serviços de mídia no fluxo de trabalho sob demanda](media-services-video-on-demand-workflow.md) e [Fluxo de trabalho da transmissão ao vivo dos serviços de mídia](media-services-live-streaming-workflow.md).

O painel Serviços de Mídia do Azure apresenta métricas de uso e informações sobre a conta que você pode usar para gerenciar sua conta de Serviços de Mídia.

Você pode monitorar o número de trabalhos de codificação na fila, as tarefas de codificação com falha, os trabalhos de codificação ativos representados pelos dados de entrada e saída do codificador, bem como o uso do armazenamento de blob associado à conta dos Serviços de Mídia. Além disso, se estiver transmitindo conteúdo a clientes, você também pode recuperar várias métricas de streaming. Você pode optar por monitorar os dados das últimas 6 horas, 24 horas ou 7 dias.
 
>[AZURE.NOTE]Custos adicionais estão associados ao monitoramento dos dados do armazenamento no Portal de Gerenciamento do Azure. Para obter mais informações, consulte [Análise de armazenamento e cobrança](http://go.microsoft.com/fwlink/?LinkId=256667).

##<a id="configuremonitoring"></a>Como monitorar uma conta dos Serviços de Mídia

1. No [Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkID=256666), clique em **Serviços de Mídia** e no nome da conta dos Serviços de Mídia para abrir o painel. 

	![MediaServices_Dashboard][dashboard]

2. Para monitorar seus trabalhos ou dados de codificação, basta começar a enviar trabalhos de codificação aos Serviços de Mídia ou iniciar a transmissão de conteúdo aos clientes usando o Streaming de Mídia por Demanda do Azure. Você deve começar a ver os dados de monitoramento no painel em cerca de uma hora.

##<a id="configuringstorage"></a>Como monitorar o uso do seu armazenamento de blob (opcional)
1. Clique no nome da **CONTA DE ARMAZENAMENTO** na seção **Visão Rápida**.
2. Na página da conta de armazenamento, clique no link **configurar página** e role para baixo até as configurações de **monitoramento** dos serviços de Blob, Tabela e Fila, mostrados a seguir.

	>[AZURE.NOTE]Os blobs são o único tipo de armazenamento com suporte nos Serviços de Mídia.

	![StorageOptions][storage_options_scoped]

3. Em **monitoramento**, defina o nível de monitoramento e a política de retenção de dados para Blobs:

-  Para definir o nível de monitoramento, selecione uma das seguintes opções:

      **Mínimo** - Coleta métricas como entrada/saída, disponibilidade, latência e porcentagens de êxitos, que são agregadas aos serviços de Blob, Tabela e Fila.

      **Detalhado** - Além das métricas mínimas, coleta o mesmo conjunto de métricas para cada operação de armazenamento na API do Serviço de Armazenamento do Azure. As métricas no modo detalhado permitem uma análise mais próxima dos problemas que ocorrem durante operações de aplicativo.

      **Desativar** - Desativa o monitoramento. Os dados de monitoramento existentes são mantidos até o final do período de retenção.

- Para definir a política de retenção de dados, em **Retenção (em dias)**, digite o número de dias que os dados devem ser retidos, de 1 a 365 dias. Se não desejar definir uma política de retenção, digite zero. Se não houver nenhuma política de retenção, cabe a você excluir os dados de monitoramento. É recomendável configurar uma política de retenção com base em quanto tempo você deseja manter os dados de análise do armazenamento de sua conta para que os dados de análise antigos e não usados possam ser excluídos pelo sistema sem custo adicional.

4. Ao concluir a configuração do monitoramento, clique em **Salvar**. De maneira semelhante às métricas dos Serviços de Mídia, você deve começar a ver os dados de monitoramento no painel em cerca de uma hora. As métricas são armazenadas na conta de armazenamento em quatro tabelas intituladas $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue e $MetricsCapacityBlob. Para obter mais informações, consulte [Métricas da análise de armazenamento](http://go.microsoft.com/fwlink/?LinkId=256668).


<!-- Images -->
[dashboard]: ./media/media-services-monitor-services-account/media-services-dashboard.png
[storage_options_scoped]: ./media/media-services-monitor-services-account/storagemonitoringoptions_scoped.png

 

<!---HONumber=62-->