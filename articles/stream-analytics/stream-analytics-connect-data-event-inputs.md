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
	ms.date="08/13/2015" 
	ms.author="jeffstok"/>

# Criar entradas do Stream Analytics

## Noções básicas sobre entradas do Stream Analytics
---
As entradas do Stream Analytics são definidas como uma conexão a uma fonte de dados. O Stream Analytics tem integração excelente com o Hub de Eventos de fontes do Azure e armazenamento de Blob de dentro e de fora da assinatura de trabalho. Como os dados são enviados a essa fonte de dados, eles são consumidos pelo trabalho do Stream Analytics e processados em tempo real. As entradas são divididas em dois tipos distintos: entradas de fluxo de dados e entradas de dados de referência.

## Entradas de fluxo de dados
---
Os trabalhos do Stream Analytics devem conter pelo menos uma fonte de entrada de fluxo de dados para ser consumida e transformada pelo trabalho. O Armazenamento de Blob do Azure e os Hubs de Eventos do Azure têm suporte como fontes de entrada de fluxo de dados. Os Hubs de Eventos do Azure são usados para coletar fluxos de eventos de vários dispositivos e serviços, como feeds de atividade de mídias sociais, informações sobre o mercado de ações ou dados de sensores. Como alternativa, o Armazenamento de Blob do Azure pode ser usado como uma fonte de entrada para a ingestão de dados em massa.

## Entradas de dados de referência
---
O Stream Analytics dá suporte a um segundo tipo de fonte de entrada conhecido como dados de referência. São dados auxiliares usados tipicamente para executar a correlação e pesquisas de dados auxiliares e os dados aqui são geralmente estáticos ou raramente alterados. O Armazenamento de Blob do Azure é a única fonte de entrada com suporte para dados de referência. Os blobs de fonte de dados de referência estão limitados a 50MB de tamanho.

## Criando um fluxo de entrada de dados de Hub de Eventos
---
### Visão geral dos Hubs de Eventos
Os Hubs de Eventos são ingestores de eventos altamente escalonáveis e são a maneira mais comum de entrada de dados para um trabalho do Stream Analytics. Juntos, os Hubs de Eventos e o Stream Analytics oferecem aos clientes uma solução ponta a ponta para análise em tempo real - os Hubs de Eventos permitem que os clientes alimentem eventos no Azure em tempo real, e os trabalhos do Stream Analytics podem processá-los em tempo real. Por exemplo, os clientes podem enviar cliques da Web, leituras de sensor, eventos de log online para Hubs de Eventos, e criar trabalhos do Stream Analytics que usem os Hubs de Eventos como os fluxos de dados de entrada para filtragem, agregação e junção em tempo real. Os Hubs de Eventos também podem ser usados para egresso de dados. Para saber mais detalhes sobre os Hubs de Eventos, confira a documentação de [Hubs de Eventos](https://azure.microsoft.com/services/event-hubs/ "Hubs de Evento").

### Grupos de consumidores
Cada entrada do trabalho do Hub de Eventos do Stream Analytics deve ser configurada para ter seu próprio grupo de consumidores. Quando um trabalho contiver uma autojunção ou várias entradas, algumas entradas poderão ser lidas por mais de um downstream de leitor, causando impacto no número de leitores de um único grupo de consumidores. Para evitar exceder o limite de Hub de Eventos dos 5 leitores por grupo de consumidores por partição, recomenda-se designar um grupo de consumidores para cada trabalho do Stream Analytics. Observe que também há um limite de 20 grupos de consumidores por Hub de Eventos. Para saber detalhes, confira o [Guia de programação de Hubs de Eventos](https://msdn.microsoft.com/library/azure/dn789972.aspx "Guia de Programação de Hubs de Eventos").

## Como criar um fluxo de dados de entrada do Hub de Eventos
---
### Como adicionar um Hub de Eventos como um fluxo de dados de entrada  ###

1. Na guia de entradas do trabalho do Stream Analytics, clique em **ADICIONAR ENTRADA** e, em seguida, selecione a opção padrão, **Fluxo de dados** e clique no botão à direita.

    ![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. Em seguida, selecione **Hub de eventos**.

    ![image6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

3. Digite ou selecione os campos a seguir e clique no botão à direita:

    - **Alias de entrada**: um nome amigável que será usado na consulta de trabalho para fazer referência a essa entrada  
    - **Namespace do Barramento de serviço**: um Namespace do Barramento de Serviço é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo Hub de Eventos, você também criou um namespace Barramento de Serviço.  
    - **Hub de eventos**: o nome da sua entrada de Hub de Eventos  
    - **Nome de política do Hub de Eventos**: a política de acesso compartilhada, que pode ser criada na guia Configurar o Hub de Eventos. Cada política de acesso compartilhado terá um nome, as permissões definidas por você e as chaves de acesso.  
    - **Grupo de Consumidores do Hub de Eventos**(opcional): o grupo de consumidores que receberão os dados do Hub de Eventos. Se não for especificado, os trabalhos do Stream Analytics usarão o Grupo de Consumidores Padrão para ingerir dados do Hub de Eventos. É recomendável usar um Grupo de consumidores distinto para cada trabalho do Stream Analytics.  

    ![image7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

4. Especifique as configurações a seguir:

    - **Formato de serialização de evento**: para verificar se as consultas funcionam da maneira esperada, o Stream Analytics precisa saber qual formato de serialização (JSON, CSV ou Avro) você está usando para os fluxos de dados de entrada.  
    - **Codificação**: UTF-8 é o único formato de codificação com suporte no momento.  

    ![image8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

5. Clique no botão de seleção para concluir o assistente e verificar se o Stream Analytics pode ser conectado com êxito ao Hub de Eventos.

## Como criar uma entrada do fluxo de dados do armazenamento de Blob
---
Para cenários com grandes quantidades de dados não estruturados para armazenamento na nuvem, o Armazenamento de Blob oferece uma solução econômica e escalonável. Os dados no armazenamento de Blob são geralmente considerados como dados "em repouso", mas podem ser processados como um fluxo de dados pelo Stream Analytics. Um cenário comum para entradas do armazenamento de Blob com o Stream Analytics é o processamento de log, no qual a telemetria é capturada de um sistema e precisa ser analisada e processada para extrair dados significativos. É importante observar que o carimbo de data/hora padrão de eventos de armazenamento de Blob no Stream Analytics é o carimbo de data/hora que o blob foi criado. Para processar os dados como um fluxo usando um carimbo de data/hora na carga útil do evento, use a palavra-chave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx). Para obter mais informações sobre o Armazenamento de Blob, confira a documentação [Armazenamento de Blob](http://azure.microsoft.com/services/storage/blobs/).

### Como adicionar o armazenamento de Blob como um fluxo de dados de entrada  ###

1. Na guia de entradas do trabalho do Stream Analytics, clique em **ADICIONAR ENTRADA** e, em seguida, selecione a opção padrão, **Fluxo de dados** e clique no botão à direita.

    ![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. Selecione **Armazenamento de Blob** e clique no botão à direita.

    ![image2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

3. Digite ou selecione os campos a seguir:

    - **Alias de entrada**: um nome amigável que será usado na consulta de trabalho para fazer referência a essa entrada  
    - **Conta de armazenamento**: se a conta de armazenamento estiver em uma assinatura diferente que o trabalho de streaming, o Nome da Conta de Armazenamento e a Chave de Conta de Armazenamento serão solicitados.  
    - **Contêiner de armazenamento**: os contêineres fornecem um agrupamento lógico de blobs armazenados no serviço Blob do Microsoft Azure. Quando você carrega um blob no serviço Blob, você deve especificar um contêiner para aquele blob.  

    ![image3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

4. Clique na caixa **Definir Configurações Avançadas** para a opção de configurar o Padrão de Prefixo de Caminho para blobs de leituras em um caminho personalizado. Se esse campo não for especificado, o Stream Analytics lerá todos os blobs no contêiner.

    ![image4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

5. Escolha as configurações a seguir:

    - **Formato de serialização de evento**: para verificar se as consultas funcionam da maneira esperada, o Stream Analytics precisa saber qual formato de serialização (JSON, CSV ou Avro) você está usando para os fluxos de dados de entrada.  
    - **Codificação**: UTF-8 é o único formato de codificação com suporte no momento.  


    ![image5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

6. Clique no botão de seleção para concluir o assistente e verificar se o Stream Analytics pode ser conectado com êxito na conta de armazenamento do Blob.

## Como criar dados de referência de armazenamento de Blob
---
O armazenamento de blob pode ser usado para definir dados de referência para um trabalho do Stream Analytics. São dados estáticos ou de alteração lenta que são usados para a realização de pesquisas ou correlacionar dados. O suporte para atualização de dados de referência pode ser habilitado especificando, na configuração de entrada, um padrão de caminho usando os tokens {date} e {time}. O Stream Analytics atualizará as definições de dados de referência baseadas nesse padrão de caminho. Por exemplo, um padrão de `"/sample/{date}/{time}/products.csv"` com um formato de data "AAAA-MM-DD" e uma hora de formato "HH:mm" informa ao Stream Analytics para coletar o blob atualizado `"/sample/2015-04-16/17:30/products.csv"` às 17:30 do dia 16 de abril de 2015, no fuso horário UTC.

> [AZURE.NOTE]Atualmente, trabalhos do Stream Analytics buscam dados atualizados do blob de referência somente quando o horário coincide com o horário codificado no nome do blob: por exemplo, trabalhos procuram por /sample/2015-04-16/17:30/products.csv entre 17:30 e 17:30:59,999999999 em 16 de abril de 2015, no fuso horário UTC. Quando o relógio chega a 17:31, ele para de procurar por /sample/2015-04-16/17:30/products.csv e começa a procurar por /sample/2015-04-16/17:31/products.csv.

A única vez em que são blobs de dados de referência anterior são considerados é quando o trabalho é iniciado. Nesse momento, o trabalho está procurando o blob que tem uma data/hora mais recente codificada em seu nome com um valor anterior ao horário de início do trabalho (os mais recentes dados de referência de blob anteriores ao horário de início do trabalho). Isso é feito para assegurar que exista um conjunto de dados de referência não vazio no início do trabalho. Se um não for encontrado, o trabalho falhará e exibirá um aviso de diagnóstico para o usuário:

### Como adicionar o armazenamento de Blob como dados de referência  ###

1. Na guia entradas do trabalho do Stream Analytics, clique em **ADICIONAR ENTRADA** e, em seguida, selecione **Dados de referência** e clique no botão à direita.

    ![image9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

2.	Digite ou selecione os campos a seguir:

    - **Alias de entrada**: um nome amigável que será usado na consulta de trabalho para fazer referência a essa entrada  
    - **Conta de armazenamento**: se a conta de armazenamento estiver em uma assinatura diferente que o trabalho de streaming, o Nome da Conta de Armazenamento e a Chave de Conta de Armazenamento serão solicitados.  
    - **Contêiner de armazenamento**: os contêineres fornecem um agrupamento lógico de blobs armazenados no serviço Blob do Microsoft Azure. Quando você carrega um blob no serviço Blob, você deve especificar um contêiner para aquele blob.  
    - **Padrão de caminho**: o caminho do arquivo usado para localizar seus blobs no contêiner especificado. No caminho, você pode optar por especificar uma ou mais instâncias das duas variáveis a seguir: {date} e {time}  

    ![image10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

3. Escolha as configurações a seguir:

    - **Formato de serialização de evento**: para verificar se as consultas funcionam da maneira esperada, o Stream Analytics precisa saber qual formato de serialização (JSON, CSV ou Avro) você está usando para os fluxos de dados de entrada.  
    - **Codificação**: UTF-8 é o único formato de codificação com suporte no momento.  

    ![image12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

4.	Clique no botão de seleção para concluir o assistente e verificar se o Stream Analytics pode ser conectado com êxito na conta de armazenamento do Blob.

    ![image11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)


## Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=August15_HO7-->