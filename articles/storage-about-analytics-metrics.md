<properties 
	pageTitle="Sobre as métricas de análise de armazenamento" 
	description="Saiba como usar a análise de armazenamento, incluindo métricas de transação e métricas de capacidade, como métricas são armazenados e como acessar dados de métrica." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Sobre as métricas de análise de armazenamento

## Visão geral

A análise de armazenamento pode armazenar métricas que incluem estatísticas de transação agregadas e os dados de capacidade sobre solicitações para um serviço de armazenamento. As transações são relatadas no nível de operação da API, bem como no nível de serviço de armazenamento, e a capacidade é relatada no nível de serviço de armazenamento. Os dados de métricas podem ser usados para analisar o uso do serviço de armazenamento, diagnosticar problemas com solicitações feitas no serviço de armazenamento e melhorar o desempenho de aplicativos que usam um serviço.

Para usar a análise de armazenamento, você deve habilitá-la separadamente para cada serviço que você deseja monitorar. Você pode habilitá-la no [Portal de gerenciamento do Azure](https://manage.windowsazure.com/). Para obter detalhes, consulte [Como monitorar uma conta de armazenamento](../how-to-monitor-a-storage-account). Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. [Use as operações Obter propriedades do serviço Blob, Obter propriedades do serviço de fila](https://msdn.microsoft.com/library/hh452239.aspx), e [Obter propriedades do serviço de tabela para habilitar a análise de armazenamento](https://msdn.microsoft.com/library/hh452238.aspx) para cada serviço.

## Métricas de transação

Um conjunto robusto de dados é registrado em intervalos de horas ou minutos para cada serviço de armazenamento e operações de API solicitadas, incluindo ingresso/egresso, disponibilidade, erros e percentuais da solicitação categorizados. Você pode ver uma lista completa dos detalhes da transação no tópico [Esquema da tabela de métricas da análise de armazenamento](https://msdn.microsoft.com/library/hh343264.aspx).

Os dados de transação são registrados em dois níveis: nível de serviço e nível de operação da API. No nível de serviço, as estatísticas que resumem todas as operações de API solicitadas são gravadas em uma entidade de tabela a cada hora, mesmo que nenhuma solicitação seja feita ao serviço. No nível de operação da API, as estatísticas serão gravadas somente em uma entidade se a operação foi solicitada nessa hora.

Por exemplo, se você executar uma operação **GetBlob** no serviço Blob, as métricas de análise de armazenamento registrarão a solicitação e as incluirá nos dados agregados para o serviço Blob, bem como a operação **GetBlob**. No entanto, se nenhuma operação **GetBlob** é solicitada durante a hora, uma entidade não será gravada para `$MetricsTransactionsBlob` para essa operação.

As métricas de transações são registradas para solicitações de usuários e solicitações feitas pela própria análise de armazenamento. Por exemplo, solicitações pela análise de armazenamento para gravar logs e entidades de tabela são registradas. Para obter mais informações sobre como essas solicitações são cobradas, consulte [Análise de armazenamento e cobrança](https://msdn.microsoft.com/library/hh360997.aspx)

## Métricas de capacidade

>[AZURE.NOTE] Atualmente, as métricas de capacidade somente estão disponíveis para o serviço Blob. As métricas de capacidade para os serviços de tabela e fila estarão disponíveis em versões futuras da análise de armazenamento.

Os dados de capacidade são gravados diariamente para serviço de Blob de uma conta de armazenamento e duas entidades de tabela são gravadas. Uma entidade fornece estatísticas para dados de usuário e a outra fornece estatísticas sobre o contêiner de blob `$logs` usado pela análise de armazenamento. A tabela `$MetricsCapacityBlob` inclui as seguintes estatísticas:

- **Capacidade**: A quantidade de armazenamento usado pelo serviço Blob da conta de armazenamento, em bytes.

- **ContainerCount**: O número de contêineres de blob no serviço Blob da conta de armazenamento.

- **ObjectCount**: O número de blobs de blocos ou páginas confirmados e não confirmados no serviço Blob da conta de armazenamento.

Para obter mais informações sobre as métricas de capacidade, consulte o esquema da tabela de métricas da análise de armazenamento.

## Como as métricas são armazenadas

Todos os dados de métricas para cada um dos serviços de armazenamento são armazenados em três tabelas reservadas para esse serviço: uma tabela para informações de transação, uma tabela de informações de transações de minutos e outra tabela para informações de capacidade. Informações de transações de transação de minuto consistem em dados de solicitação e resposta, e informações de capacidade consistem em dados de uso de armazenamento. As métricas de horas, minutos e capacidade para um serviço Blob da conta de armazenamento podem ser acessados nas tabelas que são nomeadas conforme descrito na tabela a seguir.

| Nível de métricas                      	| Nomes da tabela                                                                                                                 	| Suporte para versões                                                                                                                       	|
|------------------------------------	|-----------------------------------------------------------------------------------------------------------------------------	|----------------------------------------------------------------------------------------------------------------------------------------------	|
| Métricas por hora, local principal   	|  $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue                                              	| Versões anteriores a 15-08-2013 apenas. Embora esses nomes ainda ter suporte, é recomendável que você alterne para usar as tabelas listadas abaixo. 	|
| Métricas por hora, local principal   	| $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue             	| Todas as versões, incluindo 15-08-2013                                                                                                           	|
| Métricas por minuto, local principal   	| $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue       	| Todas as versões, incluindo 15-08-2013                                                                                                           	|
| Métricas por hora, local secundário 	| $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue       	| Todas as versões, incluindo 15-08-2013. A replicação de redundância geográfica com acesso de leitura deve estar habilitada.                                                   	|
| Métricas por minuto, local secundário 	| $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue 	| Todas as versões, incluindo 15-08-2013. A replicação de redundância geográfica com acesso de leitura deve estar habilitada.                                                   	|
| Capacidade (apenas serviço Blob)       	| $MetricsCapacityBlob                                                                                                        	| Todas as versões, incluindo 15-08-2013                                                                                                           	|



Essas tabelas são criadas automaticamente quando a análise de armazenamento é habilitada para uma conta de armazenamento. Elas são acessadas por meio do namespace da conta de armazenamento, por exemplo: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`


## Acessando dados de métrica

Todos os dados nas tabelas de métricas podem ser acessados usando as APIs do serviço de tabela, incluindo as APIs do .NET fornecidas pela biblioteca gerenciada do Azure. O administrador da conta de armazenamento pode ler e excluir entidades de tabela, mas não pode criá-las ou atualizá-las.

## Próximas etapas
### Conceitos
[Habilitar e configurar a análise de armazenamento](https://msdn.microsoft.com/library/hh360996.aspx)

[Esquema da tabela de métricas da análise de armazenamento](https://msdn.microsoft.com/library/hh343264.aspx)

[Operações registradas de análise de armazenamento e mensagens de Status](https://msdn.microsoft.com/library/hh343260.aspx)

[Sobre o registro em log da análise de armazenamento](https://msdn.microsoft.com/library/hh343262.aspx)

### Outros recursos

[Como monitorar uma conta de armazenamento](../how-to-monitor-a-storage-account) 
<!--HONumber=47-->
