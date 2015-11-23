<properties 
	pageTitle="Usar dados de referência | Microsoft Azure" 
	description="Usando dados de referência como um fluxo de entrada" 
	keywords="análise de big data, serviço de nuvem, internet das coisas, serviço gerenciado, processamento de fluxo, streaming analytics, dados de streaming"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="11/09/2015" 
	ms.author="jeffstok"/>

# Usando dados de referência como uma entrada

Dados de referência são um conjunto finito de dados estáticos ou com alteração lenta por natureza, usados para executar uma pesquisa ou para correlacionar com seu fluxo de dados. Para usar dados de referência no seu trabalho do Stream Analytics do Azure geralmente é preciso usar uma [União de dados de referência](https://msdn.microsoft.com/library/azure/dn949258.aspx) em sua consulta. O Stream Analytics usa o armazenamento de Blob do Azure como a camada de armazenamento para dados de referência e com os dados de referência da fábrica de dados do Azure podem ser transformados e/ou copiados para o armazenamento de Blob do Azure, para uso como dados de referência de [qualquer número armazenamentos de dados de nuvem e locais](./articles/data-factory-data-movement-activities.md). Dados de referência são modelados como uma sequência de blobs (definidos na configuração de entrada) em ordem crescente segundo a data/hora especificada no nome do blob. Ele **somente** dá suporte à adição ao final da sequência usando um valor de data/hora **maior** que aquele especificado pelo último blob na sequência.

## Configurando os dados de referência

Para configurar seus dados de referência, você precisa primeiro criar uma entrada de tipo de **dados de referência**. A tabela a seguir explica cada propriedade que você precisará fornecer ao criar os entrada de dados de referência com sua descrição:

<table>
<tbody>
<tr>
<td>Nome da Propriedade</td>
<td>Descrição</td>
</tr>
<tr>
<td>Alias de entrada</td>
<td>Um nome amigável que será usado na consulta de trabalho para fazer referência a essa entrada.</td>
</tr>
<tr>
<td>Conta de armazenamento</td>
<td>O nome da conta de armazenamento onde estão localizados os arquivos de blob. Se estiver na mesma assinatura que o trabalho do Stream Analytics, você pode selecioná-lo na lista suspensa.</td>
</tr>
<tr>
<td>Chave da conta de armazenamento</td>
<td>A chave secreta associada à conta de armazenamento. É preenchida automaticamente se a conta de armazenamento estiver na mesma assinatura que o trabalho do Stream Analytics.</td>
</tr>
<tr>
<td>Contêiner de armazenamento</td>
<td>Os contêineres fornecem um agrupamento lógico de blobs armazenados no serviço Blob do Microsoft Azure. Quando você carrega um blob no serviço Blob, você deve especificar um contêiner para aquele blob.</td>
</tr>
<tr>
<td>Padrão de caminho</td>
<td>O caminho do arquivo usado para localizar seus blobs no contêiner especificado. No caminho, você pode optar por especificar uma ou mais instâncias das duas variáveis a seguir:<BR>{data}, {hora}<BR>Exemplo 1: products/{data}/{hora}/product-list.csv<BR>Exemplo 2: products/{data}/product-list.csv
</tr>
<tr>
<td>Formato de data [opcional]</td>
<td>Se você tiver usado {data} no padrão de caminho que você especificou, você pode selecionar o formato de data no qual os arquivos são organizados na lista suspensa de formatos com suporte. Exemplo: AAAA/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Se você tiver usado {hora} no padrão de caminho que você especificou, você pode selecionar o formato de hora no qual os arquivos são organizados na lista suspensa de formatos com suporte. Exemplo: HH</td>
</tr>
<tr>
<td>Formato de serialização do evento</td>
<td>Para verificar se as consultas funcionam da maneira esperada, o Stream Analytics precisa saber qual formato de serialização você está usando para os fluxos de dados de entrada. Para dados de referência, os formatos com suporte são CSV e JSON.</td>
</tr>
<tr>
<td>Codificação</td>
<td>UTF-8 é o único formato de codificação com suporte no momento.</td>
</tr>
</tbody>
</table>

## Gerar dados de referência em uma agenda

Se os seus dados de referência são um conjunto de dados de alteração lenta, o suporte para atualização de dados de referência é habilitado especificando, na configuração de entrada, um padrão de caminho usando os tokens {date} e {time}. O Stream Analytics pegará as definições de dados de referência baseadas nesse padrão de caminho. Por exemplo, um padrão de ````"/sample/{date}/{time}/products.csv"```` com um formato de data "AAAA-MM-DD" e uma hora de formato "HH:mm" informa ao Stream Analytics para coletar o blob atualizado ````"/sample/2015-04-16/17:30/products.csv"```` às 17:30 do dia 16 de abril de 2015, no fuso horário UTC.

> [AZURE.NOTE]Atualmente os trabalhos do Stream Analytics procuram pela atualização de blob somente quando a hora do computador coincide com a hora codificada no nome do blob. Por exemplo o trabalho procurará /sample/2015-04-16/17:30/products.csv entre 17:30 hora e 17:30:59.9 em 16 de abril de 2015 no fuso horário UTC. Quando o relógio chega em 17:31, ele para de procurar por /sample/2015-04-16/17:30/products.csv e começa a procurar por /sample/2015-04-16/17:31/products.csv. Uma exceção a isso é iniciada quando o trabalho precisa reprocessar dados de volta no tempo ou quando o trabalho é o primeiro a iniciar. Na hora de início, o trabalho está procurando o blob mais recente produzido antes da hora de início especificada para o trabalho. Isso é feito para assegurar que exista um conjunto de dados de referência não vazio no início do trabalho. Se um não for encontrado, o trabalho falhará e exibirá um aviso de diagnóstico para o usuário:

A [Fábrica de dados do Azure](http://azure.microsoft.com/documentation/services/data-factory/) pode ser utilizada para orquestrar a tarefa de criar os blobs atualizados exigidos pela análise do Stream Analytics para atualizar as definições de dados de referência. O Data Factory é um serviço de integração de dados baseado em nuvem que automatiza a movimentação e a transformação dos dados. A Fábrica de dados oferece suporte [conectando-se a um grande número de armazenamentos de dados de nuvem e locais](./articles/data-factory-data-movement-activities.md) e movendo os dados facilmente em um cronograma regular que você especificar. Para obter mais informações e orientações passo a passo sobre como configurar um pipeline de Fábrica de dados para gerar dados de referência para o Stream Analytics que é atualizado em um cronograma predefinido, verifique este [exemplo de GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## Dicas sobre como atualizar seus dados de referência ##

1. Substituir blobs de dados de referência não fará com que o Stream Analytics recarregue o blob e, em alguns casos, isso pode fazer com que o trabalho falhe. A maneira recomendada de alterar os dados de referência é adicionar um novo blob usando o mesmo padrão de caminho e contêiner definidos na entrada de trabalho e usar um valor de data/hora **maior** que o especificado pelo último blob na sequência.
2.	Blobs de dados de referência não são ordenados pela hora da "Última modificação" do blob, mas apenas pela hora e data especificadas no nome do blob usando as substituições {date} e {time}.
3.	Em algumas ocasiões um trabalho deve voltar no tempo, portanto, blobs de dados de referência não devem ser alterados ou excluídos.

## Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureStreamAnalytics)

## Próximas etapas
Você foi apresentado ao Stream Analytics, um serviço gerenciado para análise de streaming em dados da Internet das coisas. Para saber mais sobre esse serviço, consulte:

- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=Nov15_HO3-->