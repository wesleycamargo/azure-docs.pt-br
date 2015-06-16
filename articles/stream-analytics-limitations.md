<properties 
	pageTitle="Limitações da Análise de fluxo na versão de teste | Azure" 
	description="Aprenda as limitações na versão de visualização pública dos trabalhos da análise de fluxo do Azure" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/05/2015"
	ms.author="jgao"/>

# Limitações e problemas conhecidos da Versão de Teste da Análise de fluxo do Azure

Este documento descreve as limitações e problemas conhecidos da [Stream Analytics do Azure][stream.analytics.documentation] durante a versão de teste. Na maioria dos casos, esses limites existem com uma tentativa de receber seus comentários iniciais ou com base nas restrições de capacidade atual. 
<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## Limitações

### Disponibilidade regional
Os trabalhos de Stream Analytics podem ser provisionados apenas nas regiões Central dos Estados Unidos e na Europa Ocidental na versão de visualização.

### Escala 
**Cota de unidade de transmissão**

Devido às restrições de capacidade atual, é aplicada uma cota de 12 unidades de Streaming por região por assinatura. Para obter mais informações, consulte [Escalar trabalhos de Stream Analytics do Azure][stream.analytics.scale.jobs]. Se você tem uma necessidade comercial de reduzir estes limites, entre em contato com o [Suporte da Microsoft][microsoft.support] e faremos o possível para acomodar a sua necessidade dentro das restrições da oferta pública.

**Utilização de unidade de transmissão**

Nesta versão de visualização, o número de unidades de Streaming fornecido a um trabalho, às vezes, pode ser maior que o valor selecionado ou cobrado. Além disso, as unidades de streaming não serão limitadas, indicando que o desempenho observado pode estar maior que o garantido dependendo da disponibilidade de recursos computacionais.

**Chave de partição**

Ao escalar horizontalmente a sua consulta com **Partição por**, o campo para particionar deve ser **PartitionId**. Particionamento em outros campos definidos pelo usuário será habilitado em uma versão futura. Para obter detalhes sobre a escala do seu trabalho, consulte [Escalar os trabalhos da Stream Analytics do Azure][stream.analytics.scale.jobs].

### Entradas



**Codificação de caracteres**

Para as fontes de entrada CSV e JSON, UTF-8 é o único formato de codificação com suporte.


### Complexidade da consulta
O número máximo de funções de agregação com suporte em uma definição de consulta de trabalho de Stream Analytics é sete (7).

### Número de consultas
O número máximo de consultas com suporte para uma determinada fonte de entrada é cinco (5).


### Gerenciamento do ciclo de vida

**Atualização de trabalho**

Atualmente, o Stream Analytics não dá suporte a edições dinâmicas para a definição ou a configuração de um trabalho em execução. Para alterar a entrada, saída, consulta, escala ou configuração de um trabalho em execução, você deve primeiro interromper o trabalho.

**Reinício e interrupção de trabalho**

Interromper um trabalho não preserva qualquer estado sobre o andamento do trabalho, significando que não há atualmente nenhuma maneira de configurar um trabalho reiniciado para continuar de onde ele foi parado por último. Esta é uma limitação que será corrigida em uma versão futura. Para obter as práticas recomendadas sobre como iniciar e parar trabalhos, consulte o [Guia do desenvolvedor de Stream Analytics do Azure][stream.analytics.developer.guide].

### Monitoramento
Algumas métricas relacionadas ao uso de trabalho e desempenho, como latência, não estão disponíveis na versão de visualização. A versão de teste também expõe somente a taxa de transferência do trabalho em termos de contagem de evento, não de tamanho.

## Notas de versão/problemas conhecidos

Essa seção contém uma lista de problemas conhecidos para o Stream Analytics do Azure. Esta seção será alterada ao longo do tempo, podemos remover itens da lista, encontrarmos novos problemas ou saber mais sobre os problemas existentes.


### Permissões elevadas e necessárias do hub de eventos
Neste momento, o Stream Analytics requer uma política de acesso compartilhado com permissões de Gerente para fontes de entrada de hub de eventos e destinos de saída.

### Atraso na configuração da conta de Armazenamento do Azure
Ao criar um trabalho do Stream Analytics em uma região pela primeira vez, você precisará criar uma nova conta de Armazenamento ou especifique uma conta existente para monitorar trabalhos do Stream Analytics nessa região. Devido à latência na configuração de monitoramento, criar outro trabalho do Stream Analytics na mesma região dentro de 30 minutos solicitará a especificação de uma segunda conta de armazenamento em vez de mostrar aquela configurada recentemente no menu suspenso de **Monitoramento de Conta de Armazenamento**. Para evitar a criação de uma conta de Armazenamento desnecessária, aguarde 30 minutos após a criação de um trabalho em uma região pela primeira vez antes de provisionar trabalhos adicionais nessa região.

### Os trabalhos devem usar seu próprio grupo de consumidores do hub de eventos
Cada entrada do trabalho do Stream Analytics deve ser configurada para ter seu próprio grupo de consumidores do hub de eventos. Quando um trabalho contiver autojunção ou várias saídas, alguma entrada poderá ser lida por mais de um leitor, fazendo com que o número total de leitores de um único grupo de consumidores exceda o limite de 5 leitores por grupo de consumidores do hub de eventos. Nesse caso, a consulta precisará ser dividida em várias consultas e resultados intermediários roteados por meio de hubs de eventos adicionais. Observe que também há um limite de 20 grupos de consumidores por hub de eventos. Para obter detalhes, consulte o [Guia do desenvolvedor de Hubs de Eventos][azure.event.hubs.developer.guide].

### Adicionar uma ﻿Entrada/Saída - Hub de eventos 
A terceira página das caixas de diálogo **Adicionar entrada** e **Adicionar saída** para fontes de Hub de evento tem uma lista suspensa intitulada **Hub de Evento** que contém uma lista de namespaces do Barramento de Serviço na assinatura atual e uma opção para se conectar a um Hub de eventos em uma assinatura diferente. Se você quiser se conectar a um hub de eventos na mesma assinatura, selecione seu namespace do Barramento de Serviço aqui. Se você quiser se conectar a um Hub de eventos fora da assinatura, selecione **Usar Hub de eventos de outra assinatura**.


### Não é possível fazer referência a mesma etapa de consulta mais de uma vez
Nesta versão de visualização, uma determinada etapa de subconsulta definida usando a palavra-chave **WITH** não pode ser referenciada mais de uma vez. Um cenário comum que isso pode impactar é uma autojunção usando aliases da mesma etapa. Para contornar esse comportamento, crie duas etapas separadas com a mesma subconsulta e nomes diferentes.

### Conversões de tipo sem suporte resultam em valores nulos
Quaisquer valores de evento com conversões de tipo que não tenham suporte na seção “Tipos de dados” de [Referência de linguagem de consulta da Stream Analytics do Azure][stream.analytics.query.language.reference] resultarão em um valor nulo. Nesta versão de visualização, nenhum log de erros está em vigor para essas exceções de conversão.

### Problema de memória insuficiente
Trabalhos do Stream Analytics com uma grande tolerância para eventos de problemas verificação e/ou consultas complexas com uma grande quantidade de estado podem causar insuficiência de memória para o trabalho, resultando no reinício do trabalho. As operações de iniciar e parar ficarão visíveis nos logs de operação do trabalho. Para evitar esse comportamento, dimensione a consulta por várias partições. Em uma versão futura, essa limitação será tratada por meio da redução do desempenho nos trabalhos afetados em vez de reiniciá-los.

### Um fragmento de Hub de eventos vazio na consulta particionada não resulta em nenhuma saída
Ao executar uma consulta particionada com uma subconsulta não particionada como segunda etapa, se uma das partições do Hub de eventos na entrada estiver completamente vazia, a consulta não gerará resultados. Um erro para isso será refletido nos Logs de operação para o trabalho. Verifique se todas as partições do Hub de evento têm eventos de entrada em todos os momentos para evitar esse problema.

### Limitação de volume de eventos de Banco de Dados SQL
Quando o Banco de Dados SQL for usado como um destino de saída, volumes muito grandes de dados de saída poderão fazer com que o trabalho do Stream Analytics atinja o tempo limite. Para resolver esse problema, reduza o volume de saída usando agregações ou operadores de junção, ou escolha o armazenamento Blob ou Eventos de Hubs do Azure como um destino de saída.

### Entradas de blob grande não possuem suporte
Consumir grandes arquivos do Armazenamento de Blob pode provocar uma falha no trabalho de Stream Analytics. Para evitar esse problema, não permita que os blobs tenham mais de 10 MB de tamanho.

### O espaço em branco nos cabeçalhos de coluna faz com que as entradas de saída sejam nulas
A Stream Analytics não corta espaços em branco nos cabeçalhos de coluna. Incluir espaços em branco no início ou no final de um nome de coluna resultará em entradas nulas na saída do trabalho.


## Obtenha suporte
Para obter suporte adicional, consulte o [Fórum de Stream Analytics do Azure](stream-analytics-forum.md).


## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](stream-analytics-query-language-reference.md)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](stream-analytics-rest-api-reference.md) 

<!--Anchors-->
[Limitations]: #Limitations
[Release notes and known issues]: #Release-notes-and-known-issues
[Next steps]: #next-steps

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->

[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.limitations]: stream-analytics-limitations.md


[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


[microsoft.support]: http://support.microsoft.com/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!--HONumber=54-->