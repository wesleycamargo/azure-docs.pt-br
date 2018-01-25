---
title: Executar Azure Functions com trabalhos do Azure Stream Analytics | Microsoft Docs
description: "Saiba como configurar o Azure Functions como um coletor de saída para trabalhos do Stream Analytics."
keywords: "saída de dados, transmissão de dados, Azure Functions"
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/19/2017
ms.author: sngun
ms.openlocfilehash: f2f4a8d8cda752dc6ed197b8402119f7cbcaf58f
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2018
---
# <a name="run-azure-functions-with-azure-stream-analytics-jobs"></a>Executar Azure Functions com trabalhos do Azure Stream Analytics 

É possível executar o Azure Functions com o Azure Stream Analytics configurando o Azure Functions como um dos coletores de saída do trabalho do Stream Analytics. O Azure Functions é uma experiência de computação sob demanda voltada para eventos que permite que você implemente código que é disparado por eventos que ocorrem no Azure ou em serviços de terceiros. Essa capacidade do Azure Functions de responder a gatilhos faz dele uma saída natural para trabalhos do Stream Analytics.

O Stream Analytics invoca o Azure Functions por meio de gatilhos HTTP. O adaptador de saída do Azure Functions permite que os usuários o conectem ao Stream Analytics, de modo que os eventos possam ser disparados com base em consultas do Stream Analytics. 

Este tutorial demonstra como conectar o Stream Analytics ao [Cache Redis do Azure](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md) usando o [Azure Functions](../azure-functions/functions-overview.md). 

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Configurar um trabalho do Stream Analytics para executar uma função 

Esta seção demonstra como configurar um trabalho do Stream Analytics para executar uma função que grava dados no Cache Redis do Azure. O trabalho do Stream Analytics lê eventos dos Hubs de Eventos do Azure e executa uma consulta que invoca a função. Esta função lê dados do trabalho do Stream Analytics e os grava no Cache Redis do Azure.

![Diagrama mostrando as relações entre os serviços do Azure](./media/stream-analytics-with-azure-functions/image1.png)

As etapas a seguir são necessárias para executar esta tarefa:
* [Criar um trabalho do Stream Analytics com os Hubs de Eventos como entrada](#create-stream-analytics-job-with-event-hub-as-input)  
* [Criar uma instância do Cache Redis do Azure](#create-an-azure-redis-cache)  
* [Criar uma função no Azure Functions que pode gravar dados no Cache Redis do Azure](#create-an-azure-function-that-can-write-data-to-the-redis-cache)    
* [Atualizar o trabalho do Stream Analytics com a função como saída](#update-the-stream-analytic-job-with-azure-function-as-output)  
* [Verificar o Cache Redis do Azure quanto aos resultados](#check-redis-cache-for-results)  

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Criar um trabalho do Stream Analytics com os Hubs de Eventos como entrada

Siga o tutorial [Detecção de fraudes em tempo real](stream-analytics-real-time-fraud-detection.md) para criar um hub de eventos, iniciar o aplicativo gerador de evento e criar um trabalho do Stream Analytics. (Ignore as etapas para criar a consulta e a saída. Em vez disso, consulte as seções a seguir para configurar a saída do Azure Functions).

## <a name="create-an-azure-redis-cache-instance"></a>Criar uma instância do Cache Redis do Azure

1. Crie um cache no Cache Redis do Azure usando as etapas descritas em [Criar um cache](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).  

2. Depois de criar o cache, em **Configurações**, selecione **Chaves de Acesso**. Anote a **Chave da cadeia de conexão primária**.

   ![Captura de tela da cadeia de conexão do Cache Redis do Azure](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-redis-cache"></a>Criar uma função no Azure Functions que pode gravar dados no Cache Redis do Azure

1. Consulte a seção [Criar um aplicativo de funções](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) da documentação do Azure Functions. Ela detalha como criar um aplicativo de funções e uma [Função disparada por HTTP no Azure Functions](../azure-functions/functions-create-first-azure-function.md#create-function) usando a linguagem CSharp.  

2. Navegue até a função **run.csx**. Atualize-a com o código a seguir. (Certifique-se de substituir "\<sua cadeia de conexão do redis cache aqui\>" com a cadeia de conexão primária do Cache Redis do Azure que você recuperou na seção anterior.)  

   ```csharp
   using System;
   using System.Net;
   using System.Threading.Tasks;
   using StackExchange.Redis;
   using Newtonsoft.Json;
   using System.Configuration;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
      // Get the request body
      dynamic dataArray = await req.Content.ReadAsAsync<object>();

      // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

      if (dataArray.ToString().Length > 262144)
      {        
         return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
      var connection = ConnectionMultiplexer.Connect("<your redis cache connection string goes here>");
      log.Info($"Connection string.. {connection}");
    
      // Connection refers to a property that returns a ConnectionMultiplexer
      IDatabase db = connection.GetDatabase();
      log.Info($"Created database {db}");
    
      log.Info($"Message Count {dataArray.Count}");

      // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
      for (var i = 0; i < dataArray.Count; i++)
      {
        string time = dataArray[i].time;
        string callingnum1 = dataArray[i].callingnum1;
        string key = time + " - " + callingnum1;
        db.StringSet(key, dataArray[i].ToString());
        log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
      // Simple get of data types from the cache
      string value = db.StringGet(key);
      log.Info($"Database got: {value}");
      }

      return req.CreateResponse(HttpStatusCode.OK, "Got");
    }    

   ```

   Quando o Stream Analytics recebe a exceção "Entidade de Solicitação HTTP muito grande" da função, ele reduz o tamanho dos lotes que envia para o Azure Functions. Em sua função, use o código a seguir para verificar se o Stream Analytics não envia lotes muito grandes. Certifique-se de que os valores de contagem e tamanho máximo do lote usados na função sejam consistentes com os valores inseridos no portal do Stream Analytics.

   ```csharp
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. Em um editor de texto de sua escolha, crie um arquivo JSON chamado **project.json**. Use o seguinte código e salve-o no computador local. Esse arquivo contém as dependências do pacote NuGet exigidas pela função C#.  
   
   ```json
       {
         "frameworks": {
             "net46": {
                 "dependencies": {
                     "StackExchange.Redis":"1.1.603",
                     "Newtonsoft.Json": "9.0.1"
                 }
             }
         }
     }

   ```
 
4. Retorne ao portal do Azure. Na guia **Recursos da plataforma**, navegue até a função. Em **Ferramentas de Desenvolvimento**, selecione **Editor do Serviço de Aplicativo**. 
 
   ![Captura de tela Editor do Serviço de Aplicativo](./media/stream-analytics-with-azure-functions/image3.png)

5. No Editor do Serviço de Aplicativo, clique com o botão direito do mouse na pasta raiz e carregue o arquivo **project.json**. Após o upload bem-sucedido, atualize a página. Agora, você verá um arquivo gerado automaticamente chamado **project.lock.json**. O arquivo gerado automaticamente contém referências aos arquivos .dll que são especificados no arquivo project.json.  

   ![Captura de tela Editor do Serviço de Aplicativo](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>Atualizar o trabalho do Stream Analytics com a função como saída

1. Abra seu trabalho do Stream Analytics no portal do Azure.  

2. Navegue até a função e selecione **Visão Geral** > **Saídas** > **Adicionar**. Para adicionar uma nova saída, selecione **Azure Functions** como a opção de coletor. O novo adaptador de saída do Azure Functions está disponível com as seguintes propriedades:  

   |**Nome da propriedade**|**Descrição**|
   |---|---|
   |Alias de saída| Um nome amigável que você usa na consulta do trabalho para fazer referência à saída. |
   |Importar opção| Você poderá usar a função da assinatura atual ou fornecer as configurações manualmente se a função estiver localizada em outra assinatura. |
   |Aplicativo de Funções| Nome de seu aplicativo de funções. |
   |Função| Nome da função em seu aplicativo de funções (nome da função run.csx).|
   |Tamanho Máximo do Lote|Define o tamanho máximo de cada lote de saída, que é enviado para a função. Por padrão, esse valor é definido como 256 KB.|
   |Contagem Máxima do Lote|Especifica o número máximo de eventos em cada lote enviado para a função. O valor padrão é 100. Essa propriedade é opcional.|
   |Chave|Permite que você use uma função de outra assinatura. Forneça o valor da chave para acessar sua função. Essa propriedade é opcional.|

3. Forneça um nome para o alias de saída. Neste tutorial, o chamamos de **saop1** (você pode usar qualquer nome de sua escolha). Preencha outros detalhes.  

4. Abra seu trabalho do Stream Analytics e atualize a consulta para o seguinte. (Certifique-se de substituir o texto "saop1" se você tiver nomeado o coletor de saída de outra forma.)  

   ```sql
    SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. Inicie o aplicativo telcodatagen.exe executando o seguinte comando na linha de comando (use o formato `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`):  
   
   **telcodatagen.exe 1000 .2 2**
    
6.  Inicie o trabalho do Stream Analytics.

## <a name="check-azure-redis-cache-for-results"></a>Verificar o Cache Redis do Azure quanto aos resultados

1. Navegue até o portal do Azure e localize o Cache Redis do Azure. Selecione **Console**.  

2. Use [comandos de cache Redis](https://redis.io/commands) para verificar se seus dados no cache Redis. (O comando usa o formato Get {chave}.) Por exemplo: 

   **Get "12/19/2017 21:32:24 - 123414732"**

   Este comando deve imprimir o valor para a chave especificada:

   ![Captura de tela da saída do Cache Redis do Azure](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>Problemas conhecidos

No portal do Azure, quando você tenta redefinir o valor de Tamanho máximo do lote/Contagem máxima de lotes como vazio (padrão), o valor é alterado para o valor inserido anteriormente ao salvar. Nesse caso, insira manualmente os valores padrão para esses campos.

