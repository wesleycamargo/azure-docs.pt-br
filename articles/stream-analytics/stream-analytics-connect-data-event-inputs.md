<properties 
	pageTitle="Criar entradas do Stream Analytics | Microsoft Azure" 
	description="Saiba como se conectar às fontes de entrada e como configurá-las para soluções do Stream Analytics."
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
	ms.date="08/05/2015" 
	ms.author="jeffstok"/>

# Criar entradas do Stream Analytics

## Noções básicas sobre entradas do Stream Analytics
---
As entradas do Stream Analytics são definidas como uma conexão a uma fonte de dados. Como os dados são enviados a essa fonte de dados, eles são consumidos pelo trabalho do Stream Analytics e processados em tempo real. As entradas são divididas em dois grupos distintos, entradas de fluxo de dados e entradas de dados de referência. O Stream Analytics tem integração de primeira classe com Hub de Eventos de fontes de entrada e Armazenamento de Blob de dentro e de fora da assinatura de trabalho. Os formatos de dados com suporte são Avro, CSV e JSON.

## Entradas de fluxo de dados
---
Em um nível básico, as definições de trabalho do Stream Analytics deve conter pelo menos uma fonte entrada de fluxo de dados para ser consumida e transformada pelo trabalho. O Armazenamento de Blob do Azure e os Hubs de Eventos do Azure têm suporte como fontes de entrada de fluxo de dados. Fontes de entrada de Hubs de eventos do Azure são usadas para coletar fluxos de eventos de vários dispositivos e serviços. Exemplos de fluxos de dados podem ser itens como feeds de atividade de mídia social, informações sobre o mercado de ações ou dados de sensores.

Como alternativa, o Armazenamento de Blob do Azure pode ser usado como uma fonte de entrada para a ingestão de dados em massa. É importante observar que, ao usar os Blobs do Azure, os dados estão em repouso e, portanto, o Stream Analytics interpretará todos os dados contidos em um blob como tendo o carimbo de data/hora do carimbo de data/hora de criação do próprio blob. Ou seja, a menos que os registros no blob contenham carimbos de data/hora e a palavra-chave TIMESTAMP BY seja usada.

## Entradas de dados de referência
---
A Stream Analytics também dá suporte a um segundo tipo de fonte de entrada conhecido como dados de referência. São dados auxiliares usados tipicamente para executar a correlação e pesquisas de dados auxiliares e os dados aqui são geralmente estáticos ou raramente alterados. O Armazenamento de Blob do Azure é a única fonte de entrada com suporte para dados de referência. Os blobs de fonte de dados de referência estão limitados a 50MB de tamanho.

O suporte para atualização de dados de referência pode ser habilitado especificando, na configuração de entrada, um padrão de caminho usando os tokens {date} e {time}. O trabalho carregará o blob correspondente com base na data e na hora codificadas nos nomes de blob usando o fuso horário UTC. Essa sequência de blobs de dados de referência com data e hora codificadas é necessária para garantir a consistência dos resultados. Por exemplo, se há um atraso no processamento e temos de recarregar um arquivo de blob, esperamos que o arquivo exista no mesmo local, sem ser modificado; caso contrário, é possível que vejamos uma saída diferente. O único cenário para o qual há suporte é adicionar novos blobs com data e hora futuras codificados no padrão de caminho.

Por exemplo, se o trabalho tem uma entrada de referência configurada no portal com o padrão de caminho, como: /sample/{date}/{time}/products.csv em que o formato de data é "AAAA-MM-DD" e a hora é em formato "HH:mm", o trabalho pegará um arquivo nomeado /sample/2015-04-16/17:30/products.csv em 17h30 em 16 de abril de 2015, fuso horário UTC.


> [AZURE.NOTE]Atualmente, trabalhos do Stream Analytics buscam dados atualizados do blob de referência somente quando o horário coincide com o horário codificado no nome do blob: por exemplo, trabalhos procuram por /sample/2015-04-16/17:30/products.csv entre 17:30 e 17:30:59,999999999 em 16 de abril de 2015, no fuso horário UTC. Quando o relógio chega a 17:31, ele para de procurar por /sample/2015-04-16/17:30/products.csv e começa a procurar por /sample/2015-04-16/17:31/products.csv.

A única vez em que são blobs de dados de referência anterior são considerados é quando o trabalho é iniciado. Nesse momento, o trabalho está procurando o blob que tem uma data/hora mais recente codificada em seu nome com um valor anterior ao horário de início do trabalho (os mais recentes dados de referência de blob anteriores ao horário de início do trabalho). Isso é feito para assegurar que exista um conjunto de dados de referência não vazio no início do trabalho. Se um não for encontrado, o trabalho falhará e exibirá um aviso de diagnóstico para o usuário:

    “Initializing input without a valid reference data blob for UTC time <job start time>.”

 
## Criando uma entrada de fluxo de dados
---
Para criar uma entrada de fluxo de dados, vá para a guia **Entradas** do trabalho do Stream Analytics e clique em **Adicionar Entrada** na parte inferior da página.

![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

 Criar uma entrada de fluxo de dados apresentará duas opções ao usuário, [**Hub de Eventos**](Creating-an-Event-hub-input-data-stream) ou [**Armazenamento de Blob**] (Creating-a Blob-storage-input-data-stream). Selecione a opção apropriada para o tipo de fluxo a ser processado.

![image2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

## Criando um fluxo de entrada de dados de Hub de Eventos
---
### Visão geral dos Hubs de Eventos
Os Hubs de Eventos são ingestores de eventos altamente escalonáveis e normalmente são a maneira mais comum de entrada de dados do Stream Analytics. Eles são projetados para coletar fluxos de eventos de alguns dispositivos e serviços diferentes. Juntos, os Hubs de Eventos e o Stream Analytics oferecem aos clientes uma solução ponta a ponta para análise em tempo real - os Hubs de Eventos permitem que os clientes alimentem eventos no Azure em tempo real, e os trabalhos do Stream Analytics podem processá-los em tempo real. Por exemplo, os clientes podem enviar cliques da Web, leituras de sensor, eventos de log online para Hubs de Eventos, e criar trabalhos do Stream Analytics que usem os Hubs de Eventos como os fluxos de dados de entrada para filtragem, agregação e junção em tempo real. Os Hubs de Eventos também podem ser usados para egresso de dados. Um uso em potencial dos Hubs de Eventos como saída é quando a saída de um trabalho do Stream Analytics será a entrada de outro trabalho de streaming. Para obter mais detalhes sobre os Hubs de Eventos, consulte a documentação de [Hubs de Eventos](https://azure.microsoft.com/services/event-hubs/ "Hubs de Evento").

### Grupos de consumidores
Cada entrada do trabalho do Stream Analytics deve ser configurada para ter seu próprio grupo de consumidores do Hub de Eventos. Quando um trabalho contiver autojunção ou várias entradas, alguma entrada poderá ser lida por mais de um leitor downstream, fazendo com que o número total de leitores de um único grupo de consumidores exceda o limite de 5 leitores por grupo de consumidores do Hub de Eventos. Nesse caso, a consulta precisará ser dividida em várias consultas e resultados intermediários roteados por meio de Hubs de Eventos adicionais. Observe que também há um limite de 20 grupos de consumidores por Hub de Eventos. Para obter detalhes, consulte o [Guia de programação de Hubs de Eventos](https://msdn.microsoft.com/library/azure/dn789972.aspx "Guia de Programação de Hubs de Eventos").

## Exemplo de como criar uma entrada de Hub de Eventos no Portal do Azure
---
Abaixo está um passo a passo para configurar um Hub de Eventos como entrada. Para começar a usar uma entrada de Hub de Eventos, o usuário deve ter as seguintes informações sobre o Hub de Eventos coletadas:

1. Alias de entrada – um alias de entrada com nome amigável que será referenciado na consulta de trabalho.
2. O nome do namespace do Barramento de Serviço. 
3. O nome do Hub de Eventos.
3. O nome da política do Hub de Eventos.
4. Opcional: nome do grupo de consumidores de Hub de Eventos.
	- O Grupo de Consumidores para ingerir dados do Hub de Eventos. Se não for especificado, os trabalhos do Stream Analytics usarão o Grupo de Consumidores Padrão para ingerir dados do Hub de Eventos. É recomendável usar um Grupo de consumidores distinto para cada trabalho do Stream Analytics.
5. O formato de serialização é utilizado para o fluxo de dados (Avro, CSV, JSON).

Primeiro, selecione **ADICIONAR ENTRADA** na página Entradas do trabalho do Stream Analytics.

![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

Selecione o Hub de Eventos como a entrada.

![image6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

Em seguida, insira as informações nos campos conforme mostrado abaixo para o Hub de Eventos.

![image7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

Em seguida, valide formato de serialização do evento como correto para o fluxo de dados.

![image8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

Então, selecione **Concluir**; a entrada de fluxo de dados de Hub de Eventos terá sido criada.

## Criando um fluxo de dados de entrada do Armazenamento de Blob
---
Para cenários com grandes quantidades de dados não estruturados para armazenamento na nuvem, o Armazenamento de Blob oferece uma solução econômica e escalonável. Esses dados geralmente são considerados dados "em repouso". Um cenário em que um Blob poderia ser útil para um fluxo de dados de entrada seria análise de log, na qual logs já foram capturados de sistemas; esses dados devem ser analisados e dados significativos devem ser extraídos deles. Outro cenário pode ser análise do armazenamento de dados em repouso. Para obter mais informações sobre o Armazenamento de Blob, consulte a documentação [Armazenamento de Blob](http://azure.microsoft.com/services/storage/blobs/).

Abaixo está um passo a passo para configurar um Armazenamento de Blob como entrada. Para começar a usar uma entrada de Armazenamento de Blob do Azure, as informações a seguir são necessárias:

1. Alias de entrada – um alias de entrada com nome amigável que será referenciado na consulta de trabalho.
2. Se a conta de armazenamento está em uma assinatura diferente que o trabalho de streaming, o Nome da Conta de Armazenamento e a Chave de Conta de Armazenamento serão solicitados.
3. O nome do contêiner.
4. O prefixo do nome do arquivo.
5. Qual formato de serialização é utilizado para os dados (Avro, CSV, JSON).

Na guia entradas do trabalho do Stream Analytics, clique em **ADICIONAR ENTRADA** e, em seguida, selecione a opção padrão, **Fluxo de dados**. ![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

Em seguida, selecione **Armazenamento de Blob**

![image2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

Depois, insira as informações nos campos conforme mostrado abaixo para a conta de armazenamento.

![image3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

> [AZURE.NOTE]Clicar na caixa Definir Configurações Avançadas permite o uso de uma opção para especificar o Padrão de Prefixo de Caminho para blobs de leituras em um caminho personalizado. Se esse campo não for especificado, o Stream Analytics lerá todos os blobs no contêiner.

![image4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

Em seguida, escolha a configuração de serialização correta para os dados. As opções são JSON, CSV e Avro.

![image5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

Então, selecione **Concluir**; a entrada de fluxo de dados de Armazenamento de Blob terá sido criada.

## Criando uma entrada de dados de referência de Armazenamento de Blob
---
Para utilizar recursos de dados de referência, pode-se aproveitar o Armazenamento de Blob.

Abaixo, um passo a passo para configuração do Armazenamento de Blob como uma entrada de dados de referência. Para começar, são necessárias as seguintes informações:

1. Alias de entrada – um alias de entrada com nome amigável que será referenciado na consulta de trabalho.
2. Se a conta de armazenamento está em uma assinatura diferente que o trabalho de streaming, o Nome da Conta de Armazenamento e a Chave de Conta de Armazenamento serão solicitados.
3. O nome do contêiner.
4. O prefixo do nome do arquivo.
5. Qual formato de serialização é utilizado para os dados (CSV, JSON).
6. O Padrão de Caminho. O caminho do arquivo usado para localizar seus blobs no contêiner especificado. No caminho, você pode optar por especificar uma ou mais instâncias das duas variáveis a seguir: {date} e {time}.


Na guia entradas do trabalho do Stream Analytics, clique em **ADICIONAR ENTRADA** e, em seguida, selecione a opção padrão, **Dados de referência**.

![image9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

Depois, insira as informações nos campos conforme mostrado abaixo para o Armazenamento de Blob e a conta de armazenamento.

![image10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

Certifique-se de rolar para baixo para especificar o padrão de prefixo para a hierarquia de caminho que contém o blob, bem como o formato dos campos de data e hora.

![image12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

Agora, escolha a configuração de serialização correta para os dados. As opções são JSON, CSV e Avro.

![image11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)

Então, selecione **Concluir**; a entrada de dados de referência do Armazenamento de Blob terá sido criada.


## Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=06-->