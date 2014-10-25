<properties linkid="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande"></tags>

# Criando a função de trabalho A (agendador de email) para o aplicativo de Serviço de Email do Azure - 4 de 5.

Este é o quarto de uma série de cinco tutoriais que mostram como criar e implantar o aplicativo de exemplo do Serviço de Email do Azure. Para obter informações sobre o aplicativo e a série de tutoriais, consulte [o primeiro tutorial da série][o primeiro tutorial da série].

Neste tutorial, você aprenderá:

-   Como consultar e atualizar tabelas do Armazenamento do Azure.
-   Como adicionar itens de trabalho a uma fila para processamento por outra função de trabalho.
-   Como lidar com desligamentos planejados substituindo o método `OnStop`.
-   Como tratar desligamentos não planejados certificando-se de que nenhum email seja perdido e nenhum email duplicado seja enviado.
-   Como testar uma função de trabalho que usa tabelas de Armazenamento do Azure usando o Gerenciador de Armazenamento.

Você já criou o projeto da função de trabalho A quando criou o projeto do Serviço de Nuvem. Portanto, tudo o que você precisa fazer agora é programar a função de trabalho.

## Segmentos deste tutorial

-   [Adicionar uma referência ao projeto web][Adicionar uma referência ao projeto web]
-   [Adicionar o modelo SendEmail][Adicionar o modelo SendEmail]
-   [Adicionar o código que é executado quando a função de trabalho é iniciada.][Adicionar o código que é executado quando a função de trabalho é iniciada.]
-   [Teste a função de trabalho A][Teste a função de trabalho A]
-   [Próximas etapas][Próximas etapas]

## <a name="addref"></a><span class="short-header">Adicionar referência ao projeto</span>Adicionar uma referência ao projeto web

É necessário uma referência ao projeto web, porque é lá que as classes de entidade são definidas. Você usará as mesmas classes de entidade da função de trabalho B para ler e gravar dados nas tabelas do Azure que o aplicativo usa.

**Observação:** Em um aplicativo de produção você não definiria uma referência a um projeto web de um projeto de função de trabalho, porque isso resultaria em referência a vários assemblies dependentes que você não deseja ou precisa na função de trabalho. Normalmente você manteria as classes de modelo compartilhadas em um projeto de biblioteca de classes e os projetos de função web e de trabalho fariam referência ao projeto de biblioteca de classes. Para simplificar a estrutura da solução, as classes de modelo são armazenadas no projeto web neste tutorial.

1.  Clique com o botão direito do mouse no projeto WorkerRoleA e escolha **Adicionar** e, em seguida, escolha **Referência**.

2.  No **Gerenciador de Referências**, adicione uma referência ao projeto MvcWebRole e, em seguida, clique em **OK**.

    ![Adicionar referência à MvcWebRole][Adicionar referência à MvcWebRole]

## <a name="addmodel"></a>Adicionar o modelo SendEmail

A função de trabalho A cria as linhas `SendEmail` na tabela `Message` e a função de trabalho B lê essas linhas para obter as informações necessárias para o envio de email. A imagem a seguir mostra um subconjunto de propriedades para duas linhas de `Message` e três linhas de `SendEmail` na tabela `Message`.

![tabela de mensagem com sendmail][tabela de mensagem com sendmail]

As linhas da tabela `Message` servem para vários propósitos:

-   Fornecem todas as informações que a função de trabalho B precisa para enviar um único email.
-   Rastreiam para verificar se um email foi enviado, para evitar que duplicatas sejam enviadas em caso de uma função de trabalho ser reiniciada após uma falha.
-   Possibilitam que a função de trabalho A determine quando todos os emails para uma mensagem foram enviados, para que possa ser marcada como `Complete`.

Para ler e gravar as linhas de `SendEmail`, uma classe de modelo é necessária. Como deve ser acessível para a função de trabalho A e para a função de trabalho B, e como todas as outras classes de modelo são definidas no projeto web, faz sentido definir essa classe no projeto web também.

1.  Na pasta *Modelos* no projeto MVC, inclua o arquivo *SendEmail.cs* a partir do projeto transferido por download.

O código aqui é semelhante ao de outras classes de modelo, exceto que nenhum atributo DataAnnotations está incluído porque não há nenhuma interface do usuário associada a esse modelo – não é usado em um controlador MVC.

        public class SendEmail : TableEntity
        {
            public long MessageRef { get; set; }
            public string EmailAddress { get; set; }
            public DateTime? ScheduledDate { get; set; }
            public String FromEmailAddress { get; set; }
            public string SubjectLine { get; set; }
            public bool? EmailSent { get; set; }
            public string SubscriberGUID { get; set; }
            public string ListName { get; set; }
        }

## <a name="addcode"></a><span class="short-header">Adicionar o código de função de trabalho</span>Adicionar o código que é executado quando a função de trabalho é iniciada

No projeto WorkerRoleA project, o modelo do projeto criado *WorkerRole.cs* com o seguinte código:

        public class WorkerRole : RoleEntryPoint
        {
            public override void Run()
            {
                // This is a sample worker implementation. Replace with your logic.
                Trace.WriteLine("WorkerRole1 entry point called", "Information");

                while (true)
                {
                    Thread.Sleep(10000);
                    Trace.WriteLine("Working", "Information");
                }
            }

            public override bool OnStart()
            {
                // Set the maximum number of concurrent connections 
                ServicePointManager.DefaultConnectionLimit = 12;

                // For information on handling configuration changes
                // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

                return base.OnStart();
            }
        }

Esse é o código de modelo padrão para a função de trabalho. Há um método `OnStart` no qual você pode colocar código de inicialização que é executado apenas quando uma instância da função de trabalho é iniciada, e um método `Run` que é chamado após o método `OnStart` ser concluído. Você irá substituir esse código por sua própria inicialização e executar o código.

1.  Exclua *WorkerRole.cs* no projeto WorkerRoleA e, em seguida, adicione o arquivo *WorkerRoleA.cs* do projeto baixado.

### O método OnStart

O método `OnStart` inicializa os objetos de contexto que você precisa para trabalhar com as entidades de Armazenamento do Azure. Também garante que todas as tabelas, filas e contêineres de blob que você usará no método `Run` existam. O código que executa essas tarefas é semelhante ao que você viu anteriormente nos construtores do controlador MVC. Você irá configurar a cadeia de conexão que este método usará mais tarde.

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;

            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in WorkerA");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient(); 
            sendEmailQueue = queueClient.GetQueueReference("azuremailqueue"); 
            var tableClient = storageAccount.CreateCloudTableClient(); 
            mailingListTable = tableClient.GetTableReference("mailinglist"); 
            messageTable = tableClient.GetTableReference("message"); 
            messagearchiveTable = tableClient.GetTableReference("messagearchive"); 

            // Create if not exists for queue, blob container, SentEmail table. 
            sendEmailQueue.CreateIfNotExists(); 
            messageTable.CreateIfNotExists(); 
            mailingListTable.CreateIfNotExists(); 
            messagearchiveTable.CreateIfNotExists(); 

            return base.OnStart();
        }

Você pode ter visto uma documentação anterior sobre como trabalhar com o Armazenamento do Azure que mostra o código de inicialização em um loop que verifica se há erros de transporte. Isso não é mais necessário porque a API agora tem um mecanismo de repetição interno que absorve falhas de rede transitórias para até três tentativas adicionais.

O método `ConfigureDiagnostics` em que as chamadas do método `OnStart` definem o rastreamento para que você possa ver a saída dos métodos `Trace.Information` e `Trace.Error`. Esse método é explicado no [segundo tutorial][segundo tutorial].

A configuração [ServicePointManager.DefaultConnectionLimit][ServicePointManager.DefaultConnectionLimit] especifica o número máximo de conexões TCP concorrentes que podem ser abertas no .NET. A documentação para a classe [ServicePointManager][ServicePointManager] explica que o único host que a instância de função de trabalho conecta é uma conexão separada. Por exemplo, esta função de trabalho deveria ter três conexões concorrentes, uma para tabelas, uma para blobs e uma para filas. Para algumas conexões, tal como SQL Server, o software do cliente faz pool de conexões, que pode reduzir o número de conexões concorrentes gerenciadas pelo `ServicePointManager`. O melhor número para `DefaultConnectionLimit` depende, em parte, do serviço de back-end que você conectou. Para alguns serviços, pode ser bom ter 500 conexões abertas, outro serviço pode ser sobrecarregado com apenas 5 conexões. O limite de 12 conexões para cada processador é uma orientação geral que funcionará bem em vários cenários.

### O método OnStop

O método `OnStop` define a variável global `onStopCalled` como verdadeira e aguarda o método `Run` definir a variável global `returnedFromRunMethod` como verdadeira, que sinaliza que está pronto para realizar um desligamento normal.

        public override void OnStop()
        {
            onStopCalled = true;
            while (returnedFromRunMethod == false)
            {
                System.Threading.Thread.Sleep(1000);
            }
        }

O método `OnStop` é chamado quando a função de trabalho está sendo desligada devido a um dos seguintes motivos:

-   O Azure precisa reinicializar a máquina virtual (a instância da função web ou da função de trabalho) ou o computador físico que hospeda a máquina virtual.
-   Você parou seu Serviço de Nuvem usando o botão **Parar** no Portal de Gerenciamento do Azure.
-   Você implantou uma atualização em seu projeto de Serviço de Nuvem.

O método `Run` monitora a variável `onStopCalled` e para de efetuar pull em qualquer novo item de trabalho a ser processado quando essa variável for alterada para `true`. Essa coordenação entre os métodos `OnStop` e `Run` permite um desligamento normal do processo de trabalho.

O Azure periodicamente instala atualizações do sistema operacional para garantir que a plataforma esteja segura e confiável e execute bem. Essas atualizações normalmente requerem as máquinas que hospedam o Serviço de Nuvem sejam desligadas e reiniciadas. Para obter mais informações, consulte [A instância da função é reinicializada devido as atualizações do sistema operacional (a página pode estar em inglês)][A instância da função é reinicializada devido as atualizações do sistema operacional (a página pode estar em inglês)].

### O método Run

O método `Run` executa duas funções:

-   Examina a tabela `message` procurando por mensagens agendadas a serem enviadas hoje ou anteriormente, para as quais os itens de trabalho da fila não foram criados ainda.

-   Examina a tabela `message` procurando por mensagens que tenham um status indicando que todos os itens de trabalho da fila foram criados, mas nem todos os emails foram enviados. Caso encontre um, ele examina as linhas de `SendEmail` dessa mensagem para ver se todos os emails foram enviados, e se foram, atualiza o status para `Completed` e arquiva a linha `message`.

O método também verifica a variável global `onStopCalled`. Quando a variável for `true`, o método para de efetuar pull de novos itens de trabalho a serem processados e retorna quando as tarefas já iniciadas forem concluídas.

        public override void Run()
        {
            Trace.TraceInformation("WorkerRoleA entering Run()");
            while (true)
            {
                try
                {
                    var tomorrow = DateTime.Today.AddDays(1.0).ToString("yyyy-MM-dd");
                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve all messages that are scheduled for tomorrow or earlier
                    // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(
                        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                    var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                    var messagesToProcess = messageTable.ExecuteQuery(query).ToList();
                    TableOperation replaceOperation;
                    // Process each message (queue emails to be sent).
                    foreach (Message messageToProcess in messagesToProcess)
                    {
                        string restartFlag = "0";
                        // If the message is already in Queuing status,
                        // set flag to indicate this is a restart.
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";
                        }

                        // If the message is in Pending status, change
                        // it to Queuing.
                        if (messageToProcess.Status == "Pending")
                        {
                            messageToProcess.Status = "Queuing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }

                        // If the message is in Queuing status, 
                        // process it and change it to Processing status;
                        // otherwise it's already in processing status, and 
                        // in that case check if processing is complete.
                        if (messageToProcess.Status == "Queuing")
                        {
                            ProcessMessage(messageToProcess, restartFlag);

                            messageToProcess.Status = "Processing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }
                        else
                        {
                            CheckAndArchiveIfComplete(messageToProcess);
                        }
                    }

                    // Sleep for one minute to minimize query costs. 
                    System.Threading.Thread.Sleep(1000 * 60);
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in queue process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

Observe que todo o trabalho é feito em um loop infinito em um bloco `while` e todo o código no bloco `while` é disposto em um bloco `try`-`catch` para prevenir uma exceção sem tratamento. Se ocorrer uma exceção sem tratamento, o Azure acionará o evento [UnhandledException][UnhandledException], o processo de trabalho será terminado e a função é colocada offline. A função de trabalho será reiniciada pelo Azure, mas isso levará alguns minutos. O bloco `try` chama `TraceError` para registrar os erros e, em seguida, entra em modo de suspensão por 60 segundos de forma que, se o erro for persistente, a mensagem de erro não será repetida muitas vezes. Em um aplicativo de produção, você pode enviar um email a um administrador no bloco `try`.

O método `Run` processa uma consulta para linhas de `message` na tabela `message` que agendou a data antes de amanhã:

                    // Retrieve all messages that are scheduled for tomorrow or earlier
                    // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(
                        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                    var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                    var messagesToProcess = messageTable.ExecuteQuery(query).ToList();

**Observação:** Um dos benefícios de mover as linhas da mensagem para a tabela `messagearchive` depois de serem processadas é que essa consulta precisa especificar apenas `PartitionKey` e `RowKey` como critérios de pesquisa. Se não arquivamos as linhas processadas, a consulta também precisaria especificar um campo não chave (`Status`) e procurar em mais linhas. O tamanho da tabela aumentaria, a consulta levaria mais tempo e poderia começar a obter tokens de continuação.

Se uma mensagem estiver em status `Pending`, o processamento ainda não foi iniciado. Se estiver em status `Queuing`, o processamento terá começado mais cedo, mas foi interrompido antes que todas as mensagens da fila fossem criadas. Nesse caso, uma verificação adicional precisa ser feita na função de trabalho B quando está enviando cada email para verificar se o email já não foi enviado. Esse é o objetivo da variável `restartFlag`.

                        string restartFlag = "0";
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";
                        }

Em seguida, o código define as linhas de `message` que estão com o status `Pending` como `Queuing`. Em seguida, para aquelas linhas e mais qualquer linha que já estava com o status `Queuing`, ele chama o método `ProcessMessage` para criar os itens de trabalho da fila para enviar emails para a mensagem.

                        if (messageToProcess.Status == "Pending")
                        {
                            messageToProcess.Status = "Queuing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }

                        if (messageToProcess.Status == "Queuing")
                        {
                            ProcessMessage(messageToProcess, restartFlag);

                            messageToProcess.Status = "Processing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }
                        else
                        {
                            CheckAndArchiveIfComplete(messageToProcess);
                        }

Após processar uma mensagem no status `Queuing`, o código define o status da linha da `Message` como `Processing`. As linhas na tabela `message` que não estão com o status `Pending` ou `Queuing` já estão com o status `Processing`, e, para essas linhas, o código chama o método que verifica se todos os emails para a mensagem foram enviados. Se todos os emails foram enviados, a linha `message` será arquivada.

Após o processamento de todos os registros recuperados pela consulta, o código é suspenso por um minuto.

                // Sleep for one minute to minimize query costs.
                System.Threading.Thread.Sleep(1000*60);

Há um custo mínimo para todas as consultas de Armazenamento do Azure, mesmo que a consulta não retorne nenhum dado, portanto, verificar novamente continuamente aumentaria desnecessariamente suas despesas do Azure. Na data em que este tutorial está sendo escrito, o custo é de US$ 0,10 por milhões de transações (uma consulta conta como uma transação), portanto, o tempo de suspensão pode ser transformado em muito menos que um minuto e o custo de verificação de mensagens nas tabelas a serem enviadas seria mínimo. Para obter mais informações sobre preços, consulte o [primeiro tutorial][o primeiro tutorial da série].

**Observação sobre threading e o uso ideal da CPU:** Há duas tarefas no método `Run` (enfileiramento de emails e verificação de mensagens concluídas), e elas são executadas sequencialmente em um único thread. Uma VM (máquina virtual) pequena tem 1,75 GB de RAM e uma única CPU, portanto, provavelmente está OK executar essas tarefas sequencialmente com um único thread. Suponha que seu aplicativo precise de mais memória do que a VM pequena fornecida para executar de forma eficiente. Uma VM média fornece 3,5 GB de RAM e duas CPUs, mas esse aplicativo só usaria uma CPU, porque é single threaded. Para aproveitar todas as CPUs, você precisaria criar um thread de trabalho para cada CPU. Mesmo assim, uma única CPU não é completamente utilizada por um thread. Quando um thread faz chamadas de rede ou de E/S, o thread deve esperar que a chamada de rede ou E/S seja concluída e, enquanto espera, não está fazendo trabalho útil. Se o método `Run` foi implementado usando dois threads, quando um thread estiver esperando que a operação de rede ou de E/S seja concluída, o outro thread poderá estar fazendo trabalho útil.

### O método ProcessMessage

O método `ProcessMessage` obtém todos os endereços de email da lista de email de destino e cria um item de fila de trabalho para cada endereço de email. À medida que cria itens de trabalho da fila, também cria linhas de `SendEmail` na tabela `Message`. Essas linhas fornecem à função de trabalho B as informações necessárias para enviar emails e incluem uma propriedade `EmailSent` que rastreia se cada email foi enviado.

        private void ProcessMessage(Message messageToProcess, string restartFlag)
        {
            // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                return;
            }
            // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(
               TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
               TableOperators.And,
               TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();

            foreach (Subscriber subscriber in subscribers)
            {
                // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)
                {
                    Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                    continue;
                }

                // Create a SendEmail entity for this email.              
                var sendEmailRow = new SendEmail
                {
                    PartitionKey = messageToProcess.PartitionKey,
                    RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                    EmailAddress = subscriber.EmailAddress,
                    EmailSent = false,
                    MessageRef = messageToProcess.MessageRef,
                    ScheduledDate = messageToProcess.ScheduledDate,
                    FromEmailAddress = mailingList.FromEmailAddress,
                    SubjectLine = messageToProcess.SubjectLine,
                    SubscriberGUID = subscriber.SubscriberGUID,
                    ListName = mailingList.ListName
                };

                // When we try to add the entity to the SendEmail table, 
                // an exception might happen if this worker role went 
                // down after processing some of the email addresses and then restarted.
                // In that case the row might already be present, so we do an Upsert operation.
                try
                {
                    var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                    messageTable.Execute(upsertOperation);
                }
                catch (Exception ex)
                {
                    string err = "Error creating SendEmail row:  " + ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException;
                    }
                    Trace.TraceError(err);
                }

                // Create the queue message.
                string queueMessageString =
                    sendEmailRow.PartitionKey + "," +
                    sendEmailRow.RowKey + "," +
                    restartFlag;
                var queueMessage = new CloudQueueMessage(queueMessageString);
                sendEmailQueue.AddMessage(queueMessage);
            }

            Trace.TraceInformation("ProcessMessage end PK: "
                + messageToProcess.PartitionKey);
        }

Primeiro, o código obtém a linha da lista de endereçamento da tabela `mailinglist` para a lista de endereçamento de destino. Essa linha tem o endereço de email "de" que precisa ser fornecido para a função de trabalho B para o envio de emails.

            // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                return;
            }

Em seguida, ele consulta todas as linhas de assinantes da tabela `mailinglist` para obter a lista de endereçamento de destino.

            // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(
               TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
               TableOperators.And,
               TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();

No loop que processa os resultados da consulta, o código começa verificando se o endereço de email do assinante foi verificado e, se não, nenhum email será enfileirado.

                // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)
                {
                    Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                    continue;
                }

Em seguida, o código cria uma linha `SendEmail` na tabela `message`. Esta linha contém as informações que a função de trabalho B usará para enviar um email. A linha é criada com a propriedade `EmailSent` definida como `false`.

                // Create a SendEmail entity for this email.              
                var sendEmailRow = new SendEmail
                {
                    PartitionKey = messageToProcess.PartitionKey,
                    RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                    EmailAddress = subscriber.EmailAddress,
                    EmailSent = false,
                    MessageRef = messageToProcess.MessageRef,
                    ScheduledDate = messageToProcess.ScheduledDate,
                    FromEmailAddress = mailingList.FromEmailAddress,
                    SubjectLine = messageToProcess.SubjectLine,
                    SubscriberGUID = subscriber.SubscriberGUID,
                    ListName = mailingList.ListName
                };
                try
                {
                    var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                    messageTable.Execute(upsertOperation);
                }
                catch (Exception ex)
                {
                    string err = "Error creating SendEmail row:  " + ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException;
                    }
                    Trace.TraceError(err);
                }

O código usa uma operação "upsert" porque a linha poderá já existir se a função de trabalho A estiver sendo reiniciada após uma falha.

A última tarefa a ser executada para cada endereço de email é criar o item da fila de trabalho que acionará a função de trabalho B para enviar um email. O item da fila de trabalho contém o valor da chave de partição e da chave da linha `SendEmail` que acabou de ser criada e o sinalizador de reinicialização que foi definido anteriormente. A linha `SendEmail` contém todas as informações que a função de trabalho B precisa para enviar um email.

                // Create the queue message.
                string queueMessageString =
                    sendEmailRow.PartitionKey + "," +
                    sendEmailRow.RowKey + "," +
                    restartFlag;
                var queueMessage = new CloudQueueMessage(queueMessageString);
                sendEmailQueue.AddMessage(queueMessage);

### O método CheckAndUpdateStatusIfComplete

O método `CheckAndUpdateStatusIfComplete` verifica as mensagens que estão no status Processando para ver se todos os emails foram enviados. Se nenhum email não enviado for encontrado, ele atualizará o status da linha para `Completed` e arquivará a linha.

        private void CheckAndArchiveIfComplete(Message messageToCheck)
        {
            // Get the list of emails to be sent for this message: all SendEmail rows
            // for this message.  
            string pkrkFilter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToCheck.PartitionKey),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "message"));
            var query = new TableQuery<SendEmail>().Where(pkrkFilter);
            var emailToBeSent = messageTable.ExecuteQuery(query).FirstOrDefault();

            if (emailToBeSent != null)
            {
                return;
            }

            // All emails have been sent; copy the message row to the archive table.

            // Insert the message row in the messagearchive table
            var messageToDelete = new Message { PartitionKey = messageToCheck.PartitionKey, RowKey = messageToCheck.RowKey, ETag = "*" };
            messageToCheck.Status = "Complete";
            var insertOrReplaceOperation = TableOperation.InsertOrReplace(messageToCheck);
            messagearchiveTable.Execute(insertOrReplaceOperation);

            // Delete the message row from the message table.
            var deleteOperation = TableOperation.Delete(messageToDelete);
            messageTable.Execute(deleteOperation);
        }

## <a name="testing"></a>Teste a função de trabalho A

1.  Execute o aplicativo pressionando F5.

2.  Use as páginas da web do administrador para criar uma lista de endereçamento e criar assinantes para a lista de endereçamento. Defina a propriedade `Verified` como `true` para pelo menos um dos assinantes e defina o endereço de email como um endereço no qual você possa receber email.

    Nenhum email será enviado até que você implemente a função de trabalho B, mas você usará os mesmos dados de teste para testar a função de trabalho B.

3.  Crie uma mensagem a ser enviada para a lista de endereçamento que você criou e defina a data programada para hoje ou uma data no passado.

    ![Nova mensagem no status pendente][Nova mensagem no status pendente]

4.  Em um pouco mais de um minuto (devido ao tempo de suspensão de um minuto no método Run), atualize a página da web Mensagem e você verá o status mudar para Processando. (Você talvez o veja mudando para Em Fila primeiro, mas as alterações irão de Em Fila para Processando tão rapidamente que você não verá Em Fila.

    ![Nova mensagem no status processando][Nova mensagem no status processando]

5.  Abra o **Gerenciador de Servidores** e abra o nó para Development Storage.

6.  Expanda **Filas**, clique com o botão dirito do mouse em **azuremailqueue** e, em seguida, clique em **Visualizar Fila**.

    Você verá uma mensagem da fila para cada assinante verificado na sua lista de emails de destino.

    ![Mensagens da fila no ASE][Mensagens da fila no ASE]

7.  Clique duas vezes em uma mensagem da fila.

    Você verá o conteúdo da mensagem da fila: a chave de partição (a data), a chave da linha (o valor de MessageRef e o endereço de email) e o sinalizador de reinicialização, delimitados por uma vírgula.

    ![Conteúdo da mensagem da fila no ASE][Conteúdo da mensagem da fila no ASE]

8.  Feche a caixa de diálogo **Visualizar Mensagem**.

9.  Expandao nó das **Tabelas**, clique com o botão direito na tabela **Mensagem** e clique em **Visualizar Tabela**.

    Você verá a mensagem agendada, com "Mensagem" na chave de linha, seguida por uma linha para cada assinante verificado, com o endereço de email na chave de linha.

10. Clique duas vezes em uma linha que tenha uma “mensagem” na chave de linha, para ver o conteúdo da linha que a função de trabalho criou.

    ![Linha da mensagem:][Linha da mensagem:]

11. Clique duas vezes em uma linha que tenha um endereço de email na chave de linha, para ver o conteúdo da linha `SendEmail` que a função de trabalho A criou.

    ![Linha de SendEmail na tabela de Message][Linha de SendEmail na tabela de Message]

## <a name="nextsteps"></a>Próximas etapas

Agora você criou a função de trabalho A e verificou que ela cria as mensagens da fila e as filas da tabela que a função de trabalho B precisa para enviar emails. No [próximo tutorial][próximo tutorial], você irá compilar e testar a função de trabalho B.

Para obter links para recursos adicionais a fim de trabalhar com tabelas, filas e blobs do Armazenamento do Azure, consulte [o último tutorial nesta série][próximo tutorial].

<div><a href="/pt-br/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/" class="site-arrowboxcta download-cta">Tutorial 5</a></div>

  [o primeiro tutorial da série]: /pt-br/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Adicionar uma referência ao projeto web]: #addref
  [Adicionar o modelo SendEmail]: #addmodel
  [Adicionar o código que é executado quando a função de trabalho é iniciada.]: #addcode
  [Teste a função de trabalho A]: #testing
  [Próximas etapas]: #nextsteps
  [Adicionar referência à MvcWebRole]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-reference-manager.png
  [tabela de mensagem com sendmail]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-sendMailTbl.png
  [segundo tutorial]: /pt-br/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [ServicePointManager.DefaultConnectionLimit]: http://msdn.microsoft.com/pt-br/library/system.net.servicepointmanager.defaultconnectionlimit.aspx
  [ServicePointManager]: http://msdn.microsoft.com/pt-br/library/system.net.servicepointmanager.aspx
  [A instância da função é reinicializada devido as atualizações do sistema operacional (a página pode estar em inglês)]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [UnhandledException]: http://msdn.microsoft.com/pt-br/library/system.appdomain.unhandledexception.aspx
  [Nova mensagem no status pendente]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-pending.png
  [Nova mensagem no status processando]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-processing.png
  [Mensagens da fila no ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue.png
  [Conteúdo da mensagem da fila no ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue-detail.png
  [Linha da mensagem:]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-message-table.png
  [Linha de SendEmail na tabela de Message]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-sendemail-row.png
  [próximo tutorial]: /pt-br/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
