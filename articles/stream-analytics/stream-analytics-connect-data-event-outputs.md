<properties 
	pageTitle="Criar saídas do Stream Analytics | Microsoft Azure" 
	description="Saiba como se conectar aos destinos de saída e como configurá-los para soluções do Stream Analytics." 
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
	ms.date="11/06/2015" 
	ms.author="jeffstok"/>

# Criar saídas do Stream Analytics

## Noções básicas sobre saídas do Stream Analytics ##
---
Ao criar um trabalho do Stream Analytics, um dos itens a considerar é como a saída do trabalho é consumida. Como os consumidores da transformação de dados estão visualizando os resultados do trabalho do Stream Analytics? Que ferramenta(s) eles usarão para analisar a saída? A retenção ou armazenamento de dados é um requisito?

O Stream Analytics do Azure fornece sete métodos diferentes para armazenar e exibir saídas de trabalho. Banco de dados SQL, Armazenamento de Blob, Hubs de Eventos, filas de barramento de serviço, tópicos de barramento de serviço, Power BI e armazenamento de tabela. Isso proporciona tanto a facilidade de exibição da saída de trabalho quanto a flexibilidade no consumo e armazenamento de saída do trabalho para armazenamento de dados e outros fins.

## Usando um banco de dados SQL como uma saída ##
---
Um banco de dados SQL pode ser usado como saída para os dados que sejam relacionais por natureza ou para aplicativos que dependam de o conteúdo ser hospedado em um banco de dados relacional. Para obter mais informações sobre bancos de dados SQL do Azure, consulte [Bancos de Dados SQL do Azure](http://azure.microsoft.com/services/sql-database/).

### Parâmetros ###

Para começar a usar um Banco de Dados SQL, serão necessárias as seguintes informações sobre ele:

1. Nome do Servidor
2. Nome do Banco de Dados
3. Uma combinação válida de nome de usuário/senha.
4. Nome da tabela de saída. Esta tabela já deve existir, o trabalho não vai criá-la.

### Adicionando um Banco de Dados SQL como uma saída ###

![elementográfico1][graphic1]

Vá para a guia de saídas do trabalho, clique no comando **ADICIONAR SAÍDA** e clique em Avançar.

![elementográfico2][graphic2]

Escolha **Banco de dados SQL** como a saída.

![elementográfico3][graphic3]

Insira as informações do banco de dados na próxima página. O alias de saída é um nome amigável utilizado em consultas para direcionar a saída da consulta para esse banco de dados. Se houver uma única saída presente, o alias padrão será "saída".

![elementográfico4][graphic4]

Se o banco de dados existir dentro da mesma assinatura que o trabalho do Stream Analytics, a opção de selecionar "Usar Banco de Dados SQL da assinatura atual" estará disponível. Selecione o banco de dados na lista suspensa.

![elementográfico5][graphic5]

Clique em Avançar para adicionar essa saída. Duas operações deverão iniciar, a primeira delas para adicionar a saída.

![elementográfico6][graphic6]

A segunda operação destina-se a testar a conexão. O Azure Stream Analytics tentará se conectar ao Banco de Dados SQL e verificar se as credenciais inseridas estão corretas e se a tabela pode ser acessada.

## Usando o Armazenamento de Blob como uma saída ##
---
Para obter uma introdução sobre o Armazenamento de Blob do Azure e seu uso, consulte a documentação em [Como usar Blobs](./articles/storage/storage-dotnet-how-to-use-blobs.md).

### Parâmetros ###

Para começar a usar uma saída do Armazenamento de Blob, serão necessárias as seguintes informações:

1. Se a conta de armazenamento está em uma assinatura diferente que o trabalho de streaming, aparecerão campos para que você forneça o Nome da Conta de Armazenamento e a Chave de Conta de Armazenamento.
2. O nome do contêiner.
3. O prefixo do nome do arquivo.
4. Qual formato de serialização é utilizado para os dados (Avro, CSV, JSON).

### Adicionando o Armazenamento de Blob como uma saída ###

Selecione a saída para o Armazenamento de Blob.

![elementográfico20][graphic20]

Em seguida, forneça os detalhes conforme mostrado abaixo:

![elementográfico21][graphic21]

## Usando um Hub de Eventos como uma saída ##
---
### Visão geral ###
 
Os Hubs de Eventos são ingestores de eventos altamente escalonáveis e normalmente são a maneira mais comum de entrada de dados do Stream Analytics. Seu tratamento robusto de um número elevado de eventos também torna-os perfeitos para saída de trabalho. Uma utilização de um Hub de Eventos como saída é quando a saída de um trabalho do Stream Analytics será a entrada de outro trabalho de streaming. Para mais detalhes sobre os Hubs de Eventos, visite o portal em [Hubs de Eventos](https://azure.microsoft.com/services/event-hubs/ "Hubs de Evento").
 
### Parâmetros ###

Há alguns parâmetros que são necessários para configurar fluxos de dados de Hub de Eventos.

1. Namespace do Barramento de Serviço: Namespace do Barramento de Serviço do Hub de Eventos. Um namespace Barramento de Serviço é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo Hub de Eventos, um namespace de Barramento de Serviço também é criado. 
2. Nome do Hub de Eventos: o nome do Hub de Eventos. É o nome especificado ao criar um novo Hub de Eventos. 
3. Nome da Política de Hub de Eventos: o nome da política de acesso compartilhado para acessar o Hub de Eventos. As políticas de acesso compartilhado podem ser configuradas para um Hub de Eventos na guia Configurar. Cada política de acesso compartilhado terá um nome, as permissões definidas e as chaves de acesso geradas.
4. Chave da Política do Hub de Eventos: a chave primária ou secundária da política de acesso compartilhado para acessar o Hub de Eventos.  
5. Coluna Chave de Partição: parâmetro opcional para as saídas do Hub de Eventos. Esta coluna contém a chave de partição para saída do Hub de Eventos.

### Adicionando um Hub de Eventos como uma saída ###

1. Clique em **Saída** na parte superior da página e então clique em **Adicionar Saída**. Selecione o Hub de Eventos como a opção de saída e clique no botão direito na parte inferior da janela.

    ![elementográfico38][graphic38]

2. Forneça as informações relevantes nos campos para a saída e, após terminar, clique no botão direito na parte inferior da janela para continuar.

    ![elementográfico36][graphic36]

3. Confirme que o formato de serialização de evento, o tipo de codificação e formato estejam definidos com os valores apropriados e clique na caixa de seleção para concluir a ação.

    ![elementográfico37][graphic37]

## Usando o Power BI como uma saída ##
---
### Visão geral ###
O Power BI pode ser utilizado como uma saída de um trabalho do Stream Analytics para fornecer uma experiência rica de visualização para usuários do Stream Analytics. Essa funcionalidade pode ser utilizada para painéis operacionais, geração de relatórios e relatórios orientados por métricas. Para obter mais informações sobre o Power BI, visite o site do [Power BI](https://powerbi.microsoft.com/).

### Parâmetros ###

Há alguns parâmetros que são necessários para configurar uma saída do Power BI.

1. Alias - qualquer alias de saída com nome amigável à qual é fácil se referir. Esse alias de saída será particularmente útil se você optar por ter várias saídas para um determinado trabalho. Nesse caso, esse alias será referenciado em sua consulta. Por exemplo, use o valor do alias de saída = “OutPbi”.
2. Nome do Conjunto de Dados - forneça um nome de conjunto de dados que você deseja como nome da saída do Power BI. Por exemplo, use “pbidemo”.
2. Nome da Tabela - forneça um nome de tabela sob o conjunto de dados da saída do Power BI. Por exemplo, use “pbidemo”. **Atualmente, a saída do Power BI de trabalhos do Stream Analytics só podem ter uma tabela em um conjunto de dados.**

### Adicionando o Power BI como uma saída ###

1.  Clique em **Saída** na parte superior da página e então clique em **Adicionar Saída**. Selecione o Power BI como a opção de saída.

    ![elementográfico22][graphic22]

2.  Uma tela como essa será apresentada.

    ![elementográfico23][graphic23]

3.  Nesta etapa, forneça a conta corporativa ou de estudante para autorizar a saída do Power BI. Se você não se inscreveu ainda no Power BI, escolha a opção **Inscreva-se agora**.
4.  Depois disso, uma tela como a mostrada a seguir será apresentada.

    ![elementográfico24][graphic24]


>	[AZURE.NOTE] One should not explicitly create the dataset and table in the Power BI dashboard. The dataset and table will be automatically populated when the job is started and the job starts pumping output into Power BI. Note that if the job query doesn’t return any results, the dataset and table will not be created. Also be aware that if Power BI already had a dataset and table with the same name as the one provided in this Stream Analytics job, the existing data will be overwritten.

*	Clique em **OK**, **Testar Conexão** e agora a configuração de saída está concluída.


## Usando o Armazenamento de tabela do Azure para uma saída ##
---
O armazenamento de tabela oferece armazenamento altamente disponível e altamente escalonável, para que um aplicativo possa ser dimensionado automaticamente para atender à demanda dos usuários. O Armazenamento de tabela é um repositório de chave/atributo NoSQL da Microsoft que pode ser utilizado por alguém com dados estruturados, com menos restrições no esquema. O armazenamento de Tabela do Azure pode ser usado para armazenar dados de persistência e para recuperação eficiente. Para obter mais informações sobre o Armazenamento de Tabela do Azure, visite [Armazenamento de Tabela do Azure](./articles/storage/storage-introduction.md).

### Parâmetros ###

Para começar a usar o Armazenamento de Tabela do Azure, as informações a seguir são necessárias:

1. Nome de conta de armazenamento (se esse armazenamento estiver em uma assinatura diferente do trabalho de streaming).
2. Chave de conta de armazenamento (se esse armazenamento estiver em uma assinatura diferente do trabalho de streaming).
3. Nome da tabela de saída (será criado se não existir).
4. Chave de Partição (obrigatório).
5. Chave de linha

Para obter um design melhor da Chave de partição e da Chave de linha, consulte o artigo abaixo [Criando uma estratégia de particionamento escalonável para o armazenamento de Tabela do Azure](https://msdn.microsoft.com/library/azure/hh508997.aspx).

### Adicionando o Armazenamento de tabela do Azure como uma saída ###

![elementográfico11][graphic11]

Vá para a guia de saídas do trabalho, clique no comando **ADICIONAR SAÍDA** e clique em Avançar.

![elementográfico12][graphic12]

Escolha **Armazenamento de tabela** como a saída.

![elementográfico13][graphic13]

Insira as informações da Tabela do Azure na próxima página. O alias de saída é o nome que você pode usar em sua consulta para direcionar a saída da consulta para essa tabela.

![elementográfico14][graphic14]

![elementográfico15][graphic15]

Tamanho do Lote é o número de registros para uma operação em lote. Normalmente, o padrão é suficiente para a maioria dos trabalhos; consulte a [Especificação da operação de lote de tabela](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) para obter mais detalhes sobre como modificar essa configuração.

Se uma conta de Armazenamento do Azure existe na mesma assinatura sendo usada para criar o trabalho, selecione “Usar a conta de armazenamento da assinatura atual” e selecione a Conta de Armazenamento na lista suspensa.

Clique em Avançar para adicionar essa saída. Duas operações deverão iniciar, a primeira delas para adicionar a saída.

![elementográfico16][graphic16]

A segunda destina-se a testar a conexão. O Azure Stream Analytics tentará se conectar a essa Conta de Armazenamento e verificar se as credenciais inseridas estão corretas.

## Filas de barramento de serviço ##
---
### Introdução aos conceitos de filas de barramento de serviço ###
As filas de barramento de serviço oferecem entrega de mensagem do tipo PEPS (primeiro a entrar, primeiro a sair) para um ou mais consumidores concorrentes. Normalmente, espera-se que as mensagens sejam recebidas e processadas pelos receptores na ordem cronológica em que foram adicionadas à fila, sendo que cada mensagem é recebida e processada por apenas um consumidor de mensagem.

Para obter mais informações sobre filas de barramento de serviço, consulte [Filas, tópicos e assinaturas de barramento de serviço](https://msdn.microsoft.com/library/azure/hh367516.aspx "Filas, tópicos e assinaturas de barramento de serviço") e [Uma introdução a filas de barramento de serviço](http://blogs.msdn.com/b/appfabric/archive/2011/05/17/an-introduction-to-service-bus-queues.aspx "Uma introdução a filas de barramento de serviço").

### Parâmetros ###

Para começar a usar uma saída de Filas de Barramento de Serviço, serão necessárias as seguintes informações:

1. Alias - qualquer alias de saída com nome amigável à qual é fácil se referir. Esse alias de saída será particularmente útil se você optar por ter várias saídas para um determinado trabalho. Nesse caso, este alias será referenciado na consulta do trabalho.
2. O namespace e nome do barramento de serviço.
3. Nome de fila - filas são entidades de envio de mensagens, semelhantes a tópicos e hubs de eventos. Eles são projetados para coletar fluxos de eventos de alguns dispositivos e serviços diferentes. Quando uma fila é criada, ela também recebe um nome específico.
4. Qual formato de serialização é utilizado para os dados (Avro, CSV, JSON).

### Adição de filas de barramento de serviço como uma saída ###

![elementográfico31][graphic31]

Em seguida, forneça os detalhes conforme mostrado abaixo e selecione avançar.

![elementográfico32][graphic32]

Verifique se o formato e a serialização dos seus dados estão corretos.

![elementográfico33][graphic33]

## Tópicos do Service Bus ##
---
### Introdução aos conceitos de tópicos de barramento de serviço ###
Enquanto as filas de barramento de serviço fornecem um método de comunicação de um-para-um do remetente para o receptor, os tópicos de barramento de serviço fornecem uma forma de comunicação um-para-muitos.

Para obter mais informações sobre tópicos de barramento de serviço, consulte [Filas, tópicos e assinaturas de barramento de serviço](https://msdn.microsoft.com/library/azure/hh367516.aspx "Filas, tópicos e assinaturas de barramento de serviço") e [Uma introdução a tópicos de barramento de serviço](http://blogs.msdn.com/b/appfabric/archive/2011/05/25/an-introduction-to-service-bus-topics.aspx "Uma introdução a tópicos de barramento de serviço").

### Parâmetros ###

Para começar a usar uma saída de tópicos de barramento de serviço, serão necessárias as seguintes informações:

1. Alias - qualquer alias de saída com nome amigável à qual é fácil se referir. Esse alias de saída será particularmente útil se você optar por ter várias saídas para um determinado trabalho. Nesse caso, esse alias será referenciado em sua consulta.
2. O namespace e nome do barramento de serviço.
3. Nome do tópico - tópicos são entidades de envio de mensagens, semelhantes a filas e hubs de eventos. Eles são projetados para coletar fluxos de eventos de alguns dispositivos e serviços diferentes. Quando um tópico é criado, ele também recebe um nome específico. As mensagens enviadas para um tópico não estarão disponíveis a menos que uma assinatura seja criada, portanto, certifique-se de que há uma ou mais assinaturas sob o tópico.
4. Qual formato de serialização é utilizado para os dados (Avro, CSV, JSON).

### Adicionando tópicos de barramento de serviço como uma saída ###

Selecione a saída para os tópicos de barramento de serviço.

![elementográfico34][graphic34]

Em seguida, forneça os detalhes conforme mostrado abaixo e selecione avançar.

![elementográfico35][graphic35]

Verifique se o formato e a serialização dos seus dados estão corretos.

![elementográfico33][graphic33]


## Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureStreamAnalytics)

## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-outputs/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-outputs/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-outputs/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-outputs/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-outputs/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-outputs/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-outputs/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-outputs/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-outputs/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-outputs/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-outputs/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-outputs/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-outputs/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-outputs/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-outputs/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-outputs/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-outputs/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-outputs/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-outputs/19-stream-analytics-connect-data-event-input-output.png
[graphic20]: ./media/stream-analytics-connect-data-event-outputs/20-stream-analytics-connect-data-event-input-output.png
[graphic21]: ./media/stream-analytics-connect-data-event-outputs/21-stream-analytics-connect-data-event-input-output.png
[graphic22]: ./media/stream-analytics-connect-data-event-outputs/22-stream-analytics-connect-data-event-input-output.png
[graphic23]: ./media/stream-analytics-connect-data-event-outputs/23-stream-analytics-connect-data-event-input-output.png
[graphic24]: ./media/stream-analytics-connect-data-event-outputs/24-stream-analytics-connect-data-event-input-output.png
[graphic25]: ./media/stream-analytics-connect-data-event-outputs/25-stream-analytics-connect-data-event-input-output.png
[graphic26]: ./media/stream-analytics-connect-data-event-outputs/26-stream-analytics-connect-data-event-input-output.png
[graphic27]: ./media/stream-analytics-connect-data-event-outputs/27-stream-analytics-connect-data-event-input-output.png
[graphic28]: ./media/stream-analytics-connect-data-event-outputs/28-stream-analytics-connect-data-event-input-output.png
[graphic29]: ./media/stream-analytics-connect-data-event-outputs/29-stream-analytics-connect-data-event-input-output.png
[graphic30]: ./media/stream-analytics-connect-data-event-outputs/30-stream-analytics-connect-data-event-input-output.png
[graphic31]: ./media/stream-analytics-connect-data-event-outputs/31-stream-analytics-connect-data-event-input-output.png
[graphic32]: ./media/stream-analytics-connect-data-event-outputs/32-stream-analytics-connect-data-event-input-output.png
[graphic33]: ./media/stream-analytics-connect-data-event-outputs/33-stream-analytics-connect-data-event-input-output.png
[graphic34]: ./media/stream-analytics-connect-data-event-outputs/34-stream-analytics-connect-data-event-input-output.png
[graphic35]: ./media/stream-analytics-connect-data-event-outputs/35-stream-analytics-connect-data-event-input-output.png
[graphic36]: ./media/stream-analytics-connect-data-event-outputs/36-stream-analytics-connect-data-event-input-output.png
[graphic37]: ./media/stream-analytics-connect-data-event-outputs/37-stream-analytics-connect-data-event-input-output.png
[graphic38]: ./media/stream-analytics-connect-data-event-outputs/38-stream-analytics-connect-data-event-input-output.png

<!---HONumber=Nov15_HO3-->