<properties title="Azure Stream Analytics limitations in the preview release" pageTitle=" Limitações da Análise de fluxo na versão de teste | Azure" description="Aprenda as limitações na versão de visualização pública dos trabalhos da análise de fluxo do Azure" metaKeywords="" services="stream analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />

#Limitações e problemas conhecidos da Versão de Teste da Análise de fluxo do Azure

Este documento descreve as limitações e problemas conhecidos da [Análise de fluxo do Azure][stream.analytics.documentation] durante a versão de teste.  Na maioria dos casos, esses limites existem com uma tentativa de receber seus comentários iniciais ou com base nas restrições de capacidade atual. 
<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

##Neste artigo
+ [Limitações](#limitations) 
+ [Notas de versão e problemas conhecidos](#knownissues)
+ [Próximas etapas]

##<a name="limitations"></a> Limitações

###Disponibilidade regional
Trabalhos de análise de fluxo só podem ser provisionados nas regiões Central dos Estados Unidos e na Europa Ocidental na versão de visualização.

###Escala 
**Cota de unidade de transmissão**

Devido a restrições de capacidade atual, uma cota de 12 unidades de Streaming por região por assinatura é aplicada. Para obter mais informações, consulte [trabalhos de Análise de fluxo da escala do Azure][stream.analytics.scale.jobs]. Se você tem a necessidade comercial de reduzir estes limites, entre em contato com o [Suporte da Microsoft][microsoft.support] e faremos o possível para acomodá-la dentro das restrições da oferta pública. 

**Utilização de unidade de transmissão**

Nesta versão de visualização, o número de unidades de Streaming fornecido a um trabalho, às vezes, pode ser maior que o valor selecionado ou cobrado.  Além disso, Unidades de streaming será não limitadas, indicando que o desempenho observado pode estar maior garantia dependendo da disponibilidade de recursos computacionais.

**Chave de partição**

Ao escalar horizontalmente consulta com PARTITION BY, o campo para particionar deve ser PartitionId.  Particionamento em outros campos definidos pelo usuário será habilitado em uma versão futura.
Para obter detalhas sobre a escala do seu trabalho, consulte [trabalhos da Análise de fluxo de escala do Azure][stream.analytics.scale.jobs].

###Entradas 



**Codificação de caracteres**

Para as fontes de entrada CSV e JSON, UTF-8 é o único formato de codificação com suporte.


###Complexidade da consulta
O número máximo de funções de agregação com suporte em uma definição de consulta de trabalho de análise de fluxo é sete.

###Gerenciamento do ciclo de vida

**Atualização de trabalho**

Neste momento a Análise de fluxo não dá suporte a edições ao vivo para a definição ou a configuração de um trabalho em execução.  Para alterar a entrada, saída, consulta, escala ou configuração de um trabalho em execução, você deve primeiro interromper o trabalho.

**Reinício e interrupção de trabalho**

Interromper um trabalho não preserva qualquer estado sobre o andamento do trabalho, significando que não há atualmente nenhuma maneira de configurar um trabalho reiniciado para continuar de onde ele foi parado por último.  Esta é uma limitação que será corrigida em uma versão futura.  Para práticas recomendadas sobre como iniciar e parar trabalhos, consulte o [Guia do desenvolvedor de Análise de fluxo do Azure][stream.analytics.developer.guide]. 

###Monitoramento
Algumas métricas relacionadas ao uso de trabalho e desempenho, como latência, não estão disponíveis na versão de visualização.  A versão de teste também só expõe a taxa de transferência do trabalho em termos de contagem de evento, não de tamanho.

##<a name="knownissues"></a>Notas de versão / problemas conhecidos

Ela contém uma lista de problemas conhecidos para Análise de fluxo do Azure. Esta seção será alterada ao longo do tempo, podemos remover itens da lista, encontrarmos novos problemas ou saber mais sobre os problemas existentes.


###Permissões elevadas do Hub de eventos necessárias
Neste momento, que análise de fluxo requer uma política de acesso compartilhado com permissões Gerenciar para o Hub de eventos de entrada e saída de fontes.

###Atraso na configuração da conta de Armazenamento do Azure
Ao criar um trabalho de Análise de fluxo em uma região pela primeira vez, você precisará criar uma nova conta de armazenamento ou especifique uma conta existente para monitorar trabalhos de Análise de fluxo nessa região.  Devido à latência na configuração de monitoramento, criar outro trabalho de Análise de fluxo na mesma região dentro de 30 minutos solicitará a especificação de uma segunda conta de armazenamento em vez de mostrar aquela recentemente configurada no Menu suspenso de conta de armazenamento de monitoramento.  Para evitar a criação de uma conta de armazenamento desnecessárias, aguarde 30 minutos após a criação de um trabalho em uma região pela primeira vez antes de provisionar trabalhos adicionais nessa região. 

###Trabalhos usam Grupo padrão do consumidor para o Hub de eventos
Quando um Hub de eventos é especificado como uma entrada, trabalhos de Análise de fluxo usará o Grupo de consumidores padrão para receber dados de Hub de eventos.  Isso, sem configuração adicional, significa que nenhum outro receptor pode acessar o Hub de eventos.  Para habilitar um Hub de eventos com mais de um destinatário, grupos de consumidor adicionais devem ser configurados.  Para obter detalhes, consulte o [Guia do desenvolvedor de Hubs de eventos][azure.event.hubs.developer.guide].

###Adicionar uma ﻿Entrada/Saída - Hub de eventos 
A terceira página das caixas de diálogo Adicionar entrada e Adicionar saída para fontes de Hub de evento tem uma lista suspensa intitulada Hub de evento que contém uma lista de namespaces do barramento de serviço na assinatura atual e uma opção para se conectar a um Hub de eventos em uma assinatura diferente.  Se você quiser se conectar a um Hub de eventos na mesma assinatura, selecione seu namespace do barramento de serviço aqui.  Se você quiser se conectar a um Hub de eventos fora da assinatura, selecione "Usar Hub de eventos de outra assinatura".  


###Não é possível fazer referência a mesma etapa de consulta mais de uma vez
Nesta versão de visualização uma etapa de subconsulta de determinada definida usando a palavra-chave WITH não pode ser referenciada mais de uma vez.  Um cenário comum que isso pode impactar é uma autojunção usando aliases da mesma etapa.  Para contornar esse comportamento, crie duas etapas separadas com a mesma subconsulta e nomes diferentes.

###Conversões de tipo sem suporte resultam em valores NULL
Quaisquer valores de evento com conversões de tipo não têm suporte na seção Tipos de dados de [Referência de linguagem de consulta da Análise de fluxo do Azure][stream.analytics.query.language.reference] resultará em um valor NULL.  Nesta versão de visualização nenhum log de erros está em vigor para essas exceções de conversão. 

###Sem o problema de memória
Reinicie os trabalhos de Análise de fluxo com uma janela de tolerância grande para eventos fora de ordem e/ou consultas complexas, mantendo uma grande quantidade de estado podem fazer com que o trabalho seja executado sem memória, resultando em um trabalho. As operações de iniciar e parar ficará visíveis nos Logs de operação do trabalho.  Para evitar esse comportamento, dimensione a consulta por várias partições.  Em uma versão futura tratará essa limitação degradando o desempenho em trabalhos afetados em vez de reiniciá-los.

###um fragmento de Hub de eventos vazio na consulta particionada resulta em nenhuma saída
Ao executar uma consulta particionada com uma subconsulta não particionada como segunda etapa, se uma das partições do Hub de eventos na entrada está completamente vazia, a consulta não gerará resultados. Um erro para isso será refletido nos Logs de operação para o trabalho.  Verifique se que todas as partições do Hub de evento têm eventos de entrada em todos os momentos para evitar esse problema.

###Limitação de volume de eventos de Banco de Dados SQL
Ao usar o Banco de Dados SQL como uma fonte de saída, volumes muito grandes de dados de saída podem fazer com que o trabalho de análise de fluxo atinja o tempo limite. Para resolver esse problema, reduzir o volume de saída usando agregações ou operadores de junção ou escolha o Armazenamento de Blob ou Hub de eventos como uma fonte de saída.

###Entradas de blob grande não possuem suporte
Consumir grandes arquivos do Armazenamento de Blob, podem provocar uma falha na Análise de fluxo.  Para evitar esse problema, mantenha cada blob abaixo de 10 MB de tamanho.

###LEFT OUTER JOIN não tem suporte
A operação LEFT OUTER JOIN é habilitada na linguagem de consulta de análise de fluxo, mas não tem suporte.  Executar uma consulta com um LEFT OUTER JOIN por mais de alguns minutos resultará em problemas de consumo de memória.  Não recomendamos usar essa operação para os cenários além experimentação de consulta de curta duração.  Essa limitação será corrigida em uma versão futura.


##<a name="nextsteps"></a>Próximas etapas

- [Introdução à análise de fluxo do Azure][stream.analytics.introduction]
- [Introdução ao uso da Análise de fluxo do Azure][stream.analytics.get.started]
- [Escalonar trabalhos de Análise de fluxo do Azure][stream.analytics.scale.jobs]
- [Limitações e problemas conhecidos da Análise de fluxo do Azure][stream.analytics.limitations]
- [Referência da linguagem de consulta da Análise de fluxo do Azure][stream.analytics.query.language.reference]
- [Referência da API REST do gerenciamento da Análise de fluxo do Azure][stream.analytics.rest.api.reference] 

<!--Anchors-->
[Limitações]: #Limitations
[Notas de versão e problemas conhecidos]: #Release-notes-and-known-issues
[Próximas etapas]: #next-steps

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->

[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.limitations]: ../stream-analytics-limitations/


[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


[microsoft.support]: http://support.microsoft.com/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/pt-br/library/azure/dn789972.aspx

[Link de 1 para outro tópico de documentação azure.microsoft.com]: ../virtual-machines-windows-tutorial/
[Link de 2 para outro tópico de documentação azure.microsoft.com]: ../web-sites-custom-domain-name/
[Link de 3 para outro tópico de documentação azure.microsoft.com]: ../storage-whatis-account/
