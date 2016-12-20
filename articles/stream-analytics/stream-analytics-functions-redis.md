---
title: "Saída para um Cache Redis do Azure, usando o Azure Functions, do Azure Stream Analytics | Microsoft Docs"
description: "Saiba como usar uma função do Azure conectada a uma Fila do Barramento de Serviço para preencher um Cache Redis do Azure da saída de um trabalho do Stream Analytics."
keywords: "transmissão de dados, cache redis, fila do barramento de serviço"
services: stream-analytics
author: ryancrawcour
manager: jhubbard
documentationcenter: 
ms.assetid: d428bb33-4244-4001-b93d-c77bed816527
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2016
ms.author: ryancraw
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 26808d4d73e5b6256b56dd3c5d5ae5d075eaac5a


---
# <a name="how-to-store-data-from-azure-stream-analytics-in-an-azure-redis-cache-using-azure-functions"></a>Como armazenar dados do Stream Analytics do Azure em um Cache Redis do Azure usando o Azure Functions
O Stream Analytics do Azure permite que você desenvolva e implante soluções econômicas com rapidez a fim de descobrir análises em tempo real de dispositivos, sensores, infraestrutura, aplicativos ou qualquer transmissão de dados. Ele habilita vários casos de uso, como o gerenciamento e monitoramento em tempo real, o comando e controle, a detecção de fraudes, carros conectados e muito mais. Em muitos cenários, talvez você queira armazenar dados produzidos pelo Stream Analytics do Azure em um armazenamento de dados distribuído como um Cache Redis do Azure.

Suponha que você faça parte de uma empresa de telecomunicações. Você está tentando detectar uma fraude SIM, na qual várias chamadas vêm da mesma identidade ao mesmo tempo, mas em locais diferentes geograficamente. Você fica encarregado de armazenar todas as possíveis chamadas fraudulentas em um Cache Redis do Azure. Neste blog, fornecemos diretrizes sobre como você pode concluir sua tarefa facilmente. 

## <a name="prerequisites"></a>Pré-requisitos
Conclua o passo a passo de [Detecção de fraudes em tempo real][fraud-detection] para ASA

## <a name="architecture-overview"></a>Visão geral da arquitetura
![Captura de tela da arquitetura](./media/stream-analytics-functions-redis/architecture-overview.png)

Conforme mostrado na figura anterior, o Stream Analytics permite que a transmissão de dados de entrada seja consultada e enviada para uma saída. Com base na saída, o Azure Functions pode disparar algum tipo de evento. 

Neste blog, nos concentramos na parte do Azure Functions deste pipeline, ou mais especificamente no disparo de um evento que armazena dados fraudulentos no cache.
Depois de concluir o tutorial sobre [Detecção de fraudes em tempo real][fraud-detection], você terá uma entrada (um hub de eventos), uma consulta e uma saída (Armazenamento de Blobs) já configurados e em execução. Neste blog, como alternativa, alteramos a saída para usar uma fila do Barramento de Serviço. Depois disso, conectamos uma função do Azure nesta fila. 

## <a name="create-and-connect-a-service-bus-queue-output"></a>Criar e conectar-se a uma saída de Fila do Barramento de Serviço
Para criar uma Fila do Barramento de Serviço, siga as etapas 1 e 2 da seção .NET em [Introdução às Filas do Barramento de Serviço][servicebus-getstarted].
Agora, vamos conectar a fila no trabalho do Stream Analytics que foi criado no passo a passo anterior sobre detecção de fraudes.

1. No Portal do Azure, acesse a folha **Saídas** do seu trabalho e selecione **Adicionar** na parte superior da página.
   
    ![Adição de saídas](./media/stream-analytics-functions-redis/adding-outputs.png)
2. Escolha a **Fila do Barramento de Serviço** como o **Coletor** e siga as instruções na tela. Verifique se você escolheu o namespace da Fila do Barramento de Serviço criado na [Introdução às Filas do Barramento de Serviço][servicebus-getstarted]. Clique no botão "direito" quando tiver terminado.
3. Especifique os seguintes valores:
   
   * **Formato do Serializador de Evento**: JSON
   * **Codificação**: UTF8
   * **FORMATO**: linha separada
4. Clique no botão **Criar** para adicionar essa fonte e verificar se o Stream Analytics pode se conectar com êxito à conta de armazenamento.
5. Na guia **Consulta** , substitua a consulta atual pela seguinte. Substitua *[SEU NOME DO BARRAMENTO DE SERVIÇO] * pelo nome de saída que você criou na etapa 3. 
   
    ```    
   
        SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
   
        INTO [YOUR SERVICE BUS NAME]
   
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   
        WHERE CS1.SwitchNum != CS2.SwitchNum
   
    ```

## <a name="create-an-azure-redis-cache"></a>Criar um Cache Redis do Azure
Crie um Cache Redis do Azure seguindo a seção .NET em [Como utilizar o cache Redis do Azure][use-rediscache] até a seção chamada ***Configurar os clientes de cache***.
Uma vez concluído, você terá um novo Cache Redis. Em **Todas as configurações**, selecione **Teclas de acesso** e anote a ***Cadeia de conexão primária***.

![Captura de tela da arquitetura](./media/stream-analytics-functions-redis/redis-cache-keys.png)

## <a name="create-an-azure-function"></a>Criar uma Função do Azure
Siga o tutorial [Criar sua primeira Função do Azure][functions-getstarted] para começar a usar o Azure Functions. Se você já tiver uma função do Azure que deseja usar, avance para [Writing to Redis Cache](#Writing-to-Redis-Cache)

1. No portal, selecione Serviços de Aplicativos da navegação à esquerda, clique no seu nome do Aplicativo de funções do Azure para chegar ao site do Aplicativo de funções.
    ![Captura de tela da lista de função dos Serviços de Aplicativos](./media/stream-analytics-functions-redis/app-services-function-list.png)
2. Clique em **Nova função > ServiceBusQueueTrigger – C#**. Para os seguintes campos, siga estas instruções:
   
   * **Nome da fila**: o mesmo nome que aquele que você inseriu ao criar a fila em [Introdução às Filas do Barramento de Serviço][servicebus-getstarted] (não o nome do barramento de serviço). Certifique-se de usar a fila que está conectada à saída do Stream Analytics.
   * **Conexão do Barramento de Serviço**: selecione **Adicionar uma cadeia de conexão**. Para localizar a cadeia de conexão, acesse o portal clássico, selecione **Barramento de Serviço**, o barramento de serviço que você criou e **INFORMAÇÕES DE CONEXÃO** na parte inferior da tela. Verifique se você está na tela principal nesta página. Copie e cole a cadeia de conexão. Fique à vontade para inserir qualquer nome de conexão.
     
       ![Captura de tela da conexão do Barramento de Serviço](./media/stream-analytics-functions-redis/servicebus-connection.png)
   * **AccessRights**: escolha **Gerenciar**
3. Clique em **Criar**

## <a name="writing-to-redis-cache"></a>Writing to Redis Cache
Agora, criamos uma função do Azure que lê de uma Fila do Barramento de Serviço. Tudo o que resta para fazer é usar a Função para gravar esses dados no Cache Redis. 

1. Selecione o **ServiceBusQueueTrigger** recém-criado e clique em **Configurações do Aplicativo de Funções** no canto superior direito. Selecione **Ir para as Configurações do Serviço de Aplicativo > Configurações > Configurações do Aplicativo**
2. Na seção Cadeias de Conexão, crie um nome na seção **Nome** . Cole a cadeia de conexão primária encontrada na etapa **Criar um Cache Redis** na seção **Valor**. Selecione **Personalizado**, no lugar indicado como **Banco de Dados SQL**.
3. Clique em **Salvar** na parte superior.
   
    ![Captura de tela da conexão do Barramento de Serviço](./media/stream-analytics-functions-redis/function-connection-string.png)
4. Agora, volte para as Configurações de Serviço de Aplicativo e selecione **Ferramentas > Editor do Serviço de Aplicativo (Visualização) > Ativar > Ir**.
   
    ![Captura de tela da conexão do Barramento de Serviço](./media/stream-analytics-functions-redis/app-service-editor.png)
5. Em um editor de sua escolha, crie um arquivo JSON chamado **project.json** com o seguinte e salve-o em seu disco local.
   
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
6. Carregue esse arquivo no diretório raiz de sua função (não WWWROOT). Um arquivo chamado **project.lock.json** deverá aparecer automaticamente, confirmando que os pacotes do Nuget “StackExchange.Redis” e “Newtonsoft.Json” foram importados.
7. No arquivo **run.csx** , substitua o código pré-gerado pelo código a seguir. Na função lazyConnection, substitua “CONN NAME” pelo nome que você criou na etapa 2 da seção **Armazenar dados no cache do Redis**.

````

    using System;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;

    public static void Run(string myQueueItem, TraceWriter log)
    {
        log.Info($"Function processed message: {myQueueItem}");

        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = Connection.GetDatabase();
        log.Info($"Created database {db}");

        // Parse JSON and extract the time
        var message = JsonConvert.DeserializeObject<dynamic>(myQueueItem);
        string time = message.time;
        string callingnum1 = message.callingnum1;

        // Perform cache operations using the cache object...
        // Simple put of integral data types into the cache
        string key = time + " - " + callingnum1;
        db.StringSet(key, myQueueItem);
        log.Info($"Object put in database. Key is {key} and value is {myQueueItem}");

        // Simple get of data types from the cache
        string value = db.StringGet(key);
        log.Info($"Database got: {value}"); 
    }

    // Connect to the Service Bus
    private static Lazy<ConnectionMultiplexer> lazyConnection = 
        new Lazy<ConnectionMultiplexer>(() =>
            {
                var cnn = ConfigurationManager.ConnectionStrings["CONN NAME"].ConnectionString
                return ConnectionMultiplexer.Connect();
            });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

````

## <a name="start-the-stream-analytics-job"></a>Iniciar o trabalho do Stream Analytics
1. Inicie o aplicativo telcodatagen.exe. A utilização é a seguinte: ````telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]````
2. Da folha do trabalho do Stream Analytics no portal, clique em **Iniciar** na parte superior da página.
   
    ![Captura de tela de início do trabalho](./media/stream-analytics-functions-redis/starting-job.png)
3. Na folha **Iniciar trabalho** que aparece, selecione **Agora** e clique no botão **Iniciar** na parte inferior da tela. O status do trabalho é alterado para Inicial e, depois de algumas alterações, para Em Execução.
   
    ![Captura de tela de seleção de início do horário de trabalho](./media/stream-analytics-functions-redis/start-job-time.png)

## <a name="run-solution-and-check-results"></a>Executar a solução e os resultados da verificação
Voltando à página **ServiceBusQueueTrigger** , agora você verá as instruções de log. Esses logs mostram que você recebeu algo da Fila de Barramento de Serviço, o colocou no banco de dados e o buscou usando o tempo como a chave!

Para verificar se os dados estão em seu cache Redis, acesse a página de cache Redis no novo portal (conforme mostrado anteriormente na etapa [Criar um Cache Redis do Azure](#Create-an-Azure-Redis-Cache) ) e selecione o Console.

Agora, você pode criar comandos do Redis para confirmar se os dados estão, de fato, no cache.

![Captura de tela do Console Redis](./media/stream-analytics-functions-redis/redis-console.png)

## <a name="next-steps"></a>Próximas etapas
Estamos empolgados sobre as novas coisas que o Azure Functions e o Stream Analytics podem fazer juntos e esperamos que isso crie novas possibilidades para você. Se tiver comentários sobre o que deseja ver depois, fique à vontade para usar o [site UserVoice do Azure](https://feedback.azure.com/forums/270577-stream-analytics).

Se for novo no Microsoft Azure, convidamos você para testá-lo se inscrevendo com uma [conta de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). Se for novo no Stream Analytics, convidamos você para [criar seu primeiro trabalho do Stream Analytics](stream-analytics-create-a-job.md).

Caso precise de ajuda ou se tiver dúvidas, poste-as nos fóruns do [MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) ou do [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-stream-analytics). 

Você também pode ver os seguintes recursos:

* [Referência do desenvolvedor do Azure Functions](../azure-functions/functions-reference.md)
* [Referência do desenvolvedor de C# do Azure Functions](../azure-functions/functions-reference-csharp.md)
* [Referência do desenvolvedor em F# do Azure Functions](../azure-functions/functions-reference-fsharp.md)
* [Referência do desenvolvedor de NodeJS do Azure Functions](../azure-functions/functions-reference.md)
* [Gatilhos e de associações do Azure Functions](../azure-functions/functions-triggers-bindings.md)
* [Como monitorar o Cache Redis do Azure](../redis-cache/cache-how-to-monitor.md)

Para se manter atualizado sobre todas as notícias e recursos mais recentes, siga [@AzureStreaming](https://twitter.com/AzureStreaming) no Twitter.

[fraud-detection]: stream-analytics-real-time-fraud-detection.md
[servicebus-getstarted]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[use-rediscache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md
[functions-getstarted]: ../azure-functions/functions-create-first-azure-function.md



<!--HONumber=Nov16_HO3-->


