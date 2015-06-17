<properties 
	pageTitle="Conectar a entradas e saídas | Azure" 
	description="Saiba como se conectar às fontes de entrada e aos destinos de saída e como configurá-los para soluções do Stream Analytics." 
	documentationCenter="" 
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>

#Conectar a entradas e saídas
Neste documento, você descobrirá os diferentes métodos de configuração de fontes de entrada e de destinos de saída para soluções do Stream Analytics.

##Usando o SQL como saída

Você pode usar bancos de dados SQL do Azure como saída para os dados que sejam relacionais por natureza ou para aplicativos que dependam de o conteúdo ser hospedado em um banco de dados.

[http://azure.microsoft.com/services/sql-database/](http://azure.microsoft.com/services/sql-database/)

Para começar a usar um banco de dados SQL do Azure, você deverá ter as seguintes informações sobre o banco de dados:

1. Nome do Servidor.
2. Nome do Banco de Dados.
3. Uma combinação de nome de usuário/senha válida.
4. Nome da tabela de saída.

![elementográfico1][graphic1]

Vá para a guia de saídas de seu trabalho e clique no comando "ADICIONAR SAÍDA" e clique em Avançar.

![elementográfico2][graphic2]


Escolha "Banco de dados SQL" como sua saída.

![elementográfico3][graphic3]

Insira as informações do banco de dados na próxima página. O alias de saída é o nome que você pode usar em sua consulta para direcionar a saída da consulta para esse banco de dados. O alias padrão se você tiver uma saída será "saída".

![elementográfico4][graphic4]

Se você estiver usando um banco de dados que exista dentro da mesma assinatura usada, será possível selecionar “Usar Banco de Dados SQL da assinatura atual” e selecionar o banco de dados na lista suspensa.

![elementográfico5][graphic5]

Clique em Avançar para adicionar essa saída. Você deverá ver duas operações iniciando, a primeira para adicionar a saída.

![elementográfico6][graphic6]

A segunda destina-se a testar a conexão. O Azure Stream Analytics tentará se conectar a esse Banco de Dados SQL e verificar se as credenciais inseridas estão corretas e se a tabela pode ser acessada. Você deverá ver uma de duas mensagens após a conclusão.

![elementográfico7][graphic7]

![elementográfico8][graphic8]



Se tiver sido a última, clique em “DETALHES” para exibir os detalhes exatos do erro.

![elementográfico9][graphic9]

Neste exemplo, as credenciais fornecidas estavam incorretas. Você pode corrigir as credenciais e tente testar novamente clicando no botão "TESTAR CONEXÃO".

![elementográfico10][graphic10]

##Usando Hubs de Eventos

###Visão geral
Os Hubs de Eventos são ingestores de eventos altamente escalonáveis e normalmente são a maneira mais comum de entrada de dados do Stream Analytics. Eles são projetados para coletar fluxos de eventos de alguns dispositivos e serviços diferentes. Juntos, os Hubs de Eventos e o Stream Analytics oferecem aos clientes uma solução ponta a ponta para análise em tempo real -- os Hubs de Eventos permitem que os clientes alimentem eventos no Azure em tempo real, e os trabalhos do Stream Analytics podem processá-los em tempo real. Por exemplo, os clientes podem publicar cliques da Web, leituras de sensor, eventos de log online para Hubs de Eventos, e criar trabalhos do Stream Analytics que usem os Hubs de Eventos como os fluxos de dados de entrada para filtragem, agregação e junção em tempo real. Os Hubs de Eventos também podem ser usados para egresso de dados. O uso mais comum de Hubs de Eventos como saída é quando a saída de um trabalho do Stream Analytics será a entrada de outro trabalho de streaming.

###Grupos de consumidores
Cada entrada do trabalho do Stream Analytics deve ser configurada para ter seu próprio grupo de consumidores do hub de eventos. Quando um trabalho contiver autojunção ou várias saídas, alguma entrada poderá ser lida por mais de um leitor, fazendo com que o número total de leitores de um único grupo de consumidores exceda o limite de 5 leitores por grupo de consumidores do hub de eventos. Nesse caso, a consulta precisará ser dividida em várias consultas e resultados intermediários roteados por meio de hubs de eventos adicionais. Observe que também há um limite de 20 grupos de consumidores por hub de eventos. Para obter detalhes, consulte o Guia do desenvolvedor de Hubs de eventos.

 
###Parâmetros
Há alguns parâmetros que os clientes precisam configurar para fluxos de dados do Hub de Eventos. Esses parâmetros se aplicam aos fluxos de dados de entrada e de saída do Hub de Eventos, a menos que observado o contrário.

1. Namespace do Barramento de Serviço: Namespace do Barramento de Serviço do Hub de Eventos. Um namespace Barramento de Serviço é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo Hub de Eventos, você também criou um namespace Barramento de Serviço. 
2. Nome do Hub de Eventos: o nome do Hub de Eventos. É o nome que você especificou ao criar um novo Hub de Eventos. 
3. Nome da Política de Hub de Eventos: o nome da política de acesso compartilhado para acessar o Hub de Eventos. As políticas de acesso compartilhado podem ser configuradas para um Hub de Eventos na guia Configurar. Cada política de acesso compartilhado terá um nome, as permissões definidas por você e as chaves de acesso.
4. Chave da Política do Hub de Eventos: a chave primária ou secundária da política de acesso compartilhado para acessar o Hub de Eventos. 
5. Grupo de Consumidores do Hub de Eventos: parâmetro opcional para entradas do Hub de Eventos. O Grupo de Consumidores para ingerir dados do Hub de Eventos. Se não for especificado, os trabalhos do Stream Analytics usarão o Grupo de Consumidores Padrão para ingerir dados do Hub de Eventos. É recomendável usar um Grupo de consumidores distinto para cada trabalho do Stream Analytics.

Coluna Chave de Partição: parâmetro opcional para as saídas do Hub de Eventos. A coluna de atributo de dados usada como a chave de partição para saída do Hub de Eventos.

##Usando a saída de tabela do Azure

Você pode usar a tabela do Azure para dados estruturados com menos restrições no esquema. O armazenamento de Tabela do Azure pode ser usado para armazenar dados de persistência e para recuperação eficiente. Para saber mais, consulte: [Introdução ao Armazenamento do Azure](http://azure.microsoft.com/storage-introduction/)
 
Para começar a usar um Armazenamento de Tabela do Azure, você deverá ter as seguintes informações sobre sua Tabela:

1. Nome de conta de armazenamento (se esse armazenamento estiver em uma assinatura diferente do seu trabalho de streaming).
2. Chave de conta de armazenamento (se esse armazenamento estiver em uma assinatura diferente do seu trabalho de streaming).
3. Nome da tabela de saída (será criado se não existir).
4. Chave de Partição (obrigatório).
5.   Chave de linha (no momento é necessário; de acordo com os comentários dos clientes, estamos planejando torná-la opcional)

Para obter um design melhor da Chave de partição e da Chave de linha, consulte o artigo abaixo [Criando uma estratégia de particionamento escalonável para o armazenamento de Tabela do Azure](https://msdn.microsoft.com/library/azure/hh508997.aspx).


![elementográfico11][graphic11]


Vá para a guia de saídas de seu trabalho e clique no comando "ADICIONAR SAÍDA" e clique em Avançar.


![elementográfico12][graphic12]


Escolha "Armazenamento de tabela" como sua saída.


![elementográfico13][graphic13]


Insira as informações da Tabela do Azure na próxima página. O alias de saída é o nome que você pode usar em sua consulta para direcionar a saída da consulta para essa tabela.


![elementográfico14][graphic14] ![elementográfico15][graphic15]

Tamanho do Lote é o número de registros para uma operação em lote, deixe-o como padrão se não estiver familiarizado com ele ou consulte [https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) para obter mais detalhes


Se você estiver usando um armazenamento do Azure existente na mesma assinatura que está usando, poderá selecionar “Usar a conta de armazenamento da assinatura atual” e selecione a Conta de Armazenamento na lista suspensa.

Clique em Avançar para adicionar essa saída. Você deverá ver duas operações iniciando, a primeira para adicionar a saída.

![elementográfico16][graphic16]

A segunda destina-se a testar a conexão. O Azure Stream Analytics tentará se conectar a essa Conta de Armazenamento e verificar se as credenciais inseridas estão corretas. Você deverá ver uma de duas mensagens após a conclusão.

![elementográfico17][graphic17]

Se tiver sido a última, clique em “DETALHES” para exibir os detalhes exatos do erro.

![elementográfico18][graphic18]

Neste exemplo, as credenciais fornecidas estavam incorretas. Você pode corrigir as credenciais e tente testar novamente clicando no botão "TESTAR CONEXÃO".

![elementográfico19][graphic19]

## Obter ajuda
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Azure Stream Analytics](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Referência da linguagem de consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-input-output/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-input-output/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-input-output/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-input-output/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-input-output/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-input-output/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-input-output/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-input-output/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-input-output/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-input-output/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-input-output/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-input-output/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-input-output/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-input-output/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-input-output/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-input-output/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-input-output/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-input-output/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-input-output/19-stream-analytics-connect-data-event-input-output.png

<!--HONumber=52-->
 