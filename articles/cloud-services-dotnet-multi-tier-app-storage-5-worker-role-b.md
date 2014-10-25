<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande"></tags>

# Criar a função de trabalho B (remetente de email) para o aplicativo de Serviço de Email do Windows Azure - 5 de 5.

Este é o quinto de uma série de cinco tutoriais que mostram como criar e implantar o aplicativo de exemplo do Serviço de Email do Windows Azure. Para obter informações sobre o aplicativo e a série de tutoriais, consulte [o primeiro tutorial da série][o primeiro tutorial da série].

Neste tutorial, você aprenderá:

-   Como adicionar uma função de trabalho para um projeto de serviço de nuvem.
-   Como pesquisar uma fila e processar os itens de trabalho da fila.
-   Como enviar emails usando o SendGrid.
-   Como lidar com desligamentos planejados substituindo o método `OnStop`.
-   Como lidar com desligamentos não planejados certificando-se de que nenhum email duplicado seja enviado.

## Segmentos deste tutorial

-   [Adicionar o projeto da função de trabalho B na solução][Adicionar o projeto da função de trabalho B na solução]
-   [Adicionar uma referência ao projeto web][Adicionar uma referência ao projeto web]
-   [Adicionar o pacote NuGet do SendGrid ao projeto][Adicionar o pacote NuGet do SendGrid ao projeto]
-   [Adicionar configurações do projeto][Adicionar configurações do projeto]
-   [Adicionar o código que é executado quando a função de trabalho é iniciada.][Adicionar o código que é executado quando a função de trabalho é iniciada.]
-   [Teste a função de trabalho B][Teste a função de trabalho B]
-   [Próximas etapas][Próximas etapas]

## <a name="addworkerrole"></a><span class="short-header">Adicionar a função de trabalho B</span>Adicionar o projeto da função de trabalho B na solução

1.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto de serviço de nuvem e escolha **Novo Projeto de Função de Trabalho**.

    ![Menu Novo projeto de função de trabalho][Menu Novo projeto de função de trabalho]

2.  Na caixa de diálogo **Adicionar Novo Projeto de Função**, selecione **C#**, selecione **Função de Trabalho**, nomeie o projeto WorkerRoleB e clique em **Adicionar**.

    ![Caixa de diálogo Novo projeto de função][Caixa de diálogo Novo projeto de função]

## <a name="addreference"></a>Adicionar uma referência ao projeto web

É necessário uma referência ao projeto web, porque é lá que as classes de entidade são definidas. Você usará as classes de entidade da função de trabalho B para ler e gravar dados nas tabelas do Azure que o aplicativo usa.

1.  Clique com o botão direito do mouse no projeto WorkerRoleB e escolha **Adicionar Referência**.

2.  No **Gerenciador de Referências**, adicione uma referência ao projeto MvcWebRole.

    ![Adicionar referência à MvcWebRole][Adicionar referência à MvcWebRole]

## <a name="addsendgrid"></a>Adicionar o pacote NuGet do SendGrid ao projeto

Para enviar um email usando o SendGrid, você precisa instalar o pacote NuGet do SendGrid.

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto WorkerRoleB e escolha **Gerenciar Pacotes NuGet**.

    ![Gerenciar Pacotes NuGet][Gerenciar Pacotes NuGet]

2.  Na caixa de diálogo **Gerenciar Pacotes NuGet**, selecione a guia **Online**, digite "sendgrid" na caixa de pesquisa e pressione Enter.

3.  Clique em **Instalar** no pacote **SendGrid**.

    ![Instalar o pacote SendGrid][Instalar o pacote SendGrid]

4.  Feche a caixa de diálogo.

## <a name="addsettings"></a>Adicionar configurações do projeto

Como a função de trabalho A, a função de trabalho B precisa das credenciais da conta de armazenamento para trabalhar com tabelas, filas e blobs. Além disso, para enviar um email, a função de trabalho precisa ter credenciais para inserir em chamadas para o serviço SendGrid. E, para construir um link de cancelamento de assinatura para incluir nos emails que envia, a função de trabalho precisa saber a URL do aplicativo. Esses valores são armazenados nas configurações do projeto.

Para as credenciais da conta de armazenamento, o procedimento é o mesmo que você viu no [terceiro tutorial][terceiro tutorial].

1.  No **Gerenciador de Soluções**, sob **Funções** no projeto em nuvem, clique com o botão direito do mouse em **WorkerRoleB** e escolha **Propriedades**.

2.  Selecione a guia **Configurações**.

3.  Verifique se a opção **Todas as Configurações** está selecionada na lista suspensa **Configuração do Serviço**.

4.  Selecione a guia **Configurações** e clique no botão **Adicionar Configuração**.

5.  Digite "StorageConnectionString" na coluna **Nome**.

6.  Selecione **Cadeia de Conexão** na lista suspensa **Tipo**.

7.  Clique no botão de reticências (**...**) na extremidade direita da linha para abrir a caixa de diálogo **Cadeia de Conexão da Conta de Armazenamento**.

8.  No diálogo **Criar Cadeia de Conexão de Armazenamento**, clique no botão de opção **Emulador de Armazenamento do Azure** e clique em **OK**.

Em seguida, você cria e configura as três novas configurações que são usadas apenas pela função de trabalho B.

1.  Na guia **Configurações** na janela **Propriedades**, clique em **Adicionar Configuração** e, em seguida, adicione três novas configurações do tipo **Cadeia de Caracteres**:

    -   **Nome**: SendGridUserName, **Valor**: o nome de usuário do SendGrid que você estabeleceu no [segundo tutorial][segundo tutorial].

    -   **Nome**: SendGridPassword, **Valor**: a senha SendGrid.

    -   **Nome**: AzureMailServiceURL, **Valor**: a URL base que o aplicativo terá quando você implantá-lo, por exemplo: <http://sampleurl.cloudapp.net>.

    ![Novas configurações no projeto WorkerRoleB][Novas configurações no projeto WorkerRoleB]

## <a name="addcode"></a>Adicionar o código que é executado quando a função de trabalho é iniciada.

1.  No projeto WorkerRoleB, exclua WorkerRole.cs.

2.  No projeto WorkerRoleB, inclua o arquivo WorkerRoleB.cs do projeto transferido por download.

3.  Compilar a solução.

    Se você obter um erro de compilação, você deve encerrar com várias versões do pacote NuGet de armazenamento do Azure. Neste caso, para resolver o problema, vá para o Gerenciador de Pacotes do NuGet para a solução, selecione pacotes Instalados e role para baixo para ver se existem duas instâncias do pacote de Armazenamento do Azure. Selecione a entrada para o Azure Storage 4.0.0, e exclua-o dos projetos que está instalado. Em seguida, selecione a entrada para o Azure Storage 3.0.x e instale-o nos projetos que estão faltando. Feche e reinicie o Visual Studio e, em seguida, compile a solução novamente.

4.  Certifique-se de que o projeto de serviço de nuvem ainda está no projeto de inicialização para a solução.

### O método Onstart

Como você já viu na função de trabalho A, o método `OnStart` inicializa as classes de contexto de que você precisa para trabalhar com as entidades de armazenamento do Azure. Também garante que todas as tabelas, filas e contêineres de blob de que você precisa no método `Run` existam.

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;

            // Read storage account configuration settings
            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in worker role B");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            // Initialize queue storage 
            Trace.TraceInformation("Creating queue client.");
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            this.sendEmailQueue = queueClient.GetQueueReference("azuremailqueue");
            this.subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");

            // Initialize blob storage
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
            this.blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

            // Initialize table storage
            var tableClient = storageAccount.CreateCloudTableClient();
            tableServiceContext = tableClient.GetDataServiceContext();

            Trace.TraceInformation("WorkerB: Creating blob container, queue, tables, if they don't exist.");
            this.blobContainer.CreateIfNotExists();
            this.sendEmailQueue.CreateIfNotExists();
            this.subscribeQueue.CreateIfNotExists();
            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            return base.OnStart();
        }

A diferença em comparação com a função de trabalho A é a adição do contêiner de blob e a fila de inscrição entre os recursos a serem criados, caso ainda não existam. Você usará o contêiner de blob para obter os arquivos que contêm o HTML e o texto sem formatação para o corpo do email. A fila de inscrição é usada para enviar emails de confirmação de assinatura.

### O método Run

O método `Run` processa os itens de trabalho de duas filas: a fila usada para mensagens enviadas a listas de email (itens de trabalho criados pela função de trabalho A), e a fila usada para confirmação de assinatura de emails (itens de trabalho criados pelo método API de inscrição no projeto MvcWebRole).

        public override void Run()
        {
            CloudQueueMessage msg = null;

            Trace.TraceInformation("WorkerRoleB start of Run()");
            while (true)
            {
                try
                {
                    bool messageFound = false;

                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    if (msg != null)
                    {
                        err += " Last queue message retrieved: " + msg.AsString;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in either process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

Esse código é executado em um loop infinito até que a função de trabalho seja encerrada. Se um item de trabalho for localizado na fila principal, o código o processará e, em seguida, verificará a fila de inscrição.

                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = this.sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = this.subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

Se nada estiver aguardando na fila, o código é suspenso por 60 segundos antes de continuar com o loop.

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }

O objetivo do tempo de suspensão é minimizar os custos de transação de Armazenamento do Azure, conforme explicado no [tutorial anterior][tutorial anterior].

Quando é efetuado pull de um item da fila pelo método [GetMessage][GetMessage], esse item da fila torna-se invisível por 30 segundos para todas as outras funções Web e de trabalho acessarem a fila. Isso é o que garante que apenas uma instância da função de trabalho irá selecionar qualquer mensagem de determinada fila para processamento. Você pode definir explicitamente esse tempo de *concessão exclusiva* (o tempo que o item de fila está invisível), passando um parâmetro [visibility timeout][visibility timeout] para o método `GetMessage`. Se a função de trabalho puder levar mais de 30 segundos para processar uma mensagem da fila, você deverá aumentar o tempo de concessão exclusiva para prevenir que outras instâncias da função processem a mesma mensagem.

Por outro lado, você não deseja definir o tempo de concessão exclusiva para um valor excessivamente grande. Por exemplo, se o tempo de concessão exclusiva for definido para 48 horas, e a função de trabalho terminar inesperadamente depois do desenfileiramento de uma mensagem, outra função de trabalhado não poderá processar a mensagem por 48 horas. A concessão exclusiva máxima é de sete dias.

O método [GetMessages][GetMessages] (observe o "s" no final do nome) pode ser usado para efetuar pull de até 32 mensagens da fila em uma chamada. Cada acesso de fila incorre em um custo de transação pequeno, e o custo de transação será o mesmo se 32 mensagens forem retornadas ou se zero mensagem for retornada. O código a seguir busca até 32 mensagens em uma chamada e, em seguida, as processa.

        foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))
        {
            ProcessQueueMessage(msg);
            messageFound = true;
        }

Ao usar o `GetMessages` para remover várias mensagens, verifique se o tempo limite da visibilidade dá ao seu aplicativo tempo suficiente para processar todas as mensagens. Quando o tempo limite de visibilidade expirar, outras instâncias da função poderão acessar a mensagem e quando o fizerem, a primeira instância não poderá excluir a mensagem quando terminar de processar o item de trabalho.

### O método ProcessQueueMessage

O método `Run` chama o `ProcessQueueMessage` quando localiza um item de trabalho na fila principal:

        private void ProcessQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.           
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison message:    message {0} Role Instance {1}.",
                    msg.ToString(), GetRoleInstance());
                sendEmailQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue.
            // Example:  2012-01-01,0123456789email@domain.com,0
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];
            Trace.TraceInformation("ProcessQueueMessage start:  partitionKey {0} rowKey {1} Role Instance {2}.",
                partitionKey, rowKey, GetRoleInstance());
            // If this is a restart, verify that the email hasn't already been sent.
            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    // SendEmail row is in archive, so email is already sent. 
                    // If there's a SendEmail Row in message table, delete it,
                    // and delete the queue message.
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }
                        // Get the row in the Message table that has data we need to send the email.
            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }
            // Derive blob names from the MessageRef.
            var htmlMessageBodyRef = emailRowInMessageTable.MessageRef + ".htm";
            var textMessageBodyRef = emailRowInMessageTable.MessageRef + ".txt";
            // If the email hasn't already been sent, send email and archive the table row.
            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

            // Delete the queue message.
            sendEmailQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessQueueMessage complete:  partitionKey {0} rowKey {1} Role Instance {2}.",
               partitionKey, rowKey, GetRoleInstance());
        }

Mensagens suspeitas são aquelas que fazem com que o aplicativo gere uma exceção quando são processadas. Se for efetuado pull de uma mensagem mais de cinco vezes, pressuporemos que ela não possa ser processada e a removemos da fila para que não continuemos a tentar processá-la. Os aplicativos de produção devem considerar mover a mensagem suspeita para uma fila de "mensagens mortas" para análise, em vez de excluir a mensagem.

O código analisa a mensagem da fila na chave de partição e na chave de linha necessárias para recuperar a linha SendEmail e um sinalizador de reinicialização.

            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];

Se o processamento dessa mensagem for reiniciado após um desligamento inesperado, o código verificará a tabela `messagearchive` para determinar se este email já foi enviado. Se já tiver sido enviado, o código excluirá a linha `SendEmail` se ela existir e excluirá a mensagem da fila.

            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }

Em seguida, obtemos a linha `SendEmail` da tabela `message`. Essa linha tem todas as informações necessárias para enviar o email, exceto os blobs que contêm o HTML e o corpo do texto sem formatação do email.

            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }

Em seguida, o código envia o email e arquiva a linha `SendEmail`.

            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

A movimentação da linha para a tabela messagearchive não pode ser feita em uma transação, pois afeta várias tabelas.

Finalmente, se todo o resto for bem-sucedido, a mensagem da fila será excluída.

            sendEmailQueue.DeleteMessage(msg);

### O método SendEmailToList

O trabalho real de envio de email usando o SendGrid é feito pelo método `SendEmailToList`. Se você desejar usar um serviço diferente do SendGrid, tudo o que você precisa fazer é alterar o código nesse método.

**Observação:** Se você tiver credenciais inválidas nas configurações do projeto, a chamada para o SendGrid falhará, mas o aplicativo não receberá nenhuma indicação da falha. Se você usar SendGrid em um aplicativo de produção, será recomendável configurar credenciais separadas para a API web para evitar causar falhas silenciosas quando um administrador alterar a sua senha da conta de usuário do SendGrid. Para obter mais informações, consulte [SendGrid MultiAuth - Várias credenciais da conta][SendGrid MultiAuth - Várias credenciais da conta]. Você pode configurar as credenciais em [https://sendgrid.com/credentials](https://sendgrid.com/credentials) (a página pode estar em inglês)</a>.

        private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
            string htmlMessageBodyRef, string textMessageBodyRef)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(fromEmailAddress);
            email.AddTo(emailAddress);
            email.Html = GetBlobText(htmlMessageBodyRef);
            email.Text = GetBlobText(textMessageBodyRef);
            email.Subject = subjectLine;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

        private string GetBlobText(string blogRef)
        {
            var blob = blobContainer.GetBlockBlobReference(blogRef);
            blob.FetchAttributes();
            var blobSize = blob.Properties.Length;
            using (var memoryStream = new MemoryStream((int)blobSize))
            {
                blob.DownloadToStream(memoryStream);
                return System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }
        }

No método `GetBlobText`, o código obtém o tamanho do blob e, em seguida, usa esse valor para inicializar o objeto `MemoryStream` por motivos de desempenho. Se você não fornecer o tamanho, o que o `MemoryStream` faz é alocar 256 bytes, e em seguida, quando o download ultrapassar esse tamanho, aloca mais 512 bytes e assim por diante, dobrando o valor alocado a cada vez. Para um blob grande, esse processo seria ineficiente em comparação com a alocação da quantidade correta no início do download.

### O método ProcessSubscribeQueueMessage

O método `Run` chama o `ProcessSubscribeQueueMessage` quando localiza um item de trabalho na fila de assinatura:

        private void ProcessSubscribeQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.  
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison subscribe message:    message {0}.",
                    msg.AsString, GetRoleInstance());
                subscribeQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue. Message consists of
            // subscriber GUID and list name.
            // Example:  57ab4c4b-d564-40e3-9a3f-81835b3e102e,contoso1
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var subscriberGUID = messageParts[0];
            var listName = messageParts[1];
            Trace.TraceInformation("ProcessSubscribeQueueMessage start:    subscriber GUID {0} listName {1} Role Instance {2}.",
                subscriberGUID, listName, GetRoleInstance());
            // Get subscriber info. 
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
            // Get mailing list info.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(subscriber.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;

            SendSubscribeEmail(subscriberGUID, subscriber, mailingList);

            subscribeQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessSubscribeQueueMessage complete: subscriber GUID {0} Role Instance {1}.",
                subscriberGUID, GetRoleInstance());
        }

Esse método executa as seguintes tarefas:

-   Se a mensagem for uma mensagem "suspeita", ele a registra em log e a exclui.
-   Obtém o GUID do assinante da mensagem da fila.
-   Usa o GUID para obter informações do assinante da tabela MailingList.
-   Envia um email de confirmação para o novo assinante.
-   Exclui a mensagem da fila.

Como com emails enviados para listas, o envio real do email está em um método separado, facilitando a alteração para um serviço de email diferente se você desejar fazer isso.

        private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(mailingList.FromEmailAddress);
            email.AddTo(subscriber.EmailAddress);
            string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                "/subscribe?id=" + subscriberGUID + "&listName=" + subscriber.ListName;
            email.Html = String.Format("<p>Click the link below to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.</p>" +
                "<a href=\"{1}\">Confirm Subscription</a>", mailingList.Description, subscribeURL);
            email.Text = String.Format("Copy and paste the following URL into your browser in order to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.\n" +
                "{1}", mailingList.Description, subscribeURL);
            email.Subject = "Subscribe to " + mailingList.Description;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

## <a name="testing"></a>Teste a função de trabalho B

1.  Execute o aplicativo pressionando F5.

2.  Vá para a página **Mensagens** para ver a mensagem que você criou para testar a função de trabalho A. Após um minuto ou mais, atualize a página da web e verá que a linha desapareceu da lista porque foi arquivada.

3.  Verifique a caixa de entrada de email onde você espera receber o email. Observe que pode haver atrasos no envio ou entrega de emails pelo SendGrid para seu cliente de email, portanto, talvez seja necessário esperar um pouco para ver o email. Talvez seja necessário que você verifique sua pasta de lixo eletrônico também.

## <a name="nextsteps"></a>Próximas etapas

Você criou o aplicativo de Serviço de Email do Azure a partir do zero, e o que você tem é o mesmo que o projeto concluído que você baixou. Para implantar na nuvem, testar na nuvem e promover para produção, você pode usar os mesmos procedimentos que viu no [segundo tutorial][segundo tutorial].

Para obter um exemplo de código que mostre como usar o LINQ, consulte [PhluffyFotos][PhluffyFotos].

Para saber mais sobre o armazenamento do Azure, consulte o seguinte recurso:

-   [Conhecimento essencial para o Armazenamento do Windows Azure (a página pode estar em inglês)][Conhecimento essencial para o Armazenamento do Windows Azure (a página pode estar em inglês)] (blog de Bruno Terkaly)

Para saber mais sobre o serviço Tabela do Azure, consulte os seguintes recursos:

-   [Conhecimento essencial para o Armazenamento de Tabela do Azure (a página pode estar em inglês)][Conhecimento essencial para o Armazenamento de Tabela do Azure (a página pode estar em inglês)] (blog de Bruno Terkaly)
-   [Como obter o máximo das Tabelas do Windows Azure][Como obter o máximo das Tabelas do Windows Azure] (blog da equipe de Armazenamento do Azure)
-   [Como usar o Serviço de Armazenamento de Tabela no .NET][Como usar o Serviço de Armazenamento de Tabela no .NET]
-   [Windows Azure Storage Client Library 2.0 Tables Deep Dive (a página pode estar em inglês)][Windows Azure Storage Client Library 2.0 Tables Deep Dive (a página pode estar em inglês)] (blog da equipe de Armazenamento do Azure)
-   [Mundi real: Criando uma Estratégia de Particionamento Escalonável para Armazenamento de Tabela do Azure][Mundi real: Criando uma Estratégia de Particionamento Escalonável para Armazenamento de Tabela do Azure]

Para saber mais sobre o serviço Fila do Azure e filas do Service Bus do Azure, consulte os seguintes recursos:

-   [Padrão de trabalho centrado em fila (Criando aplicativos em nuvem do mundo real com o Windows Azure)][Padrão de trabalho centrado em fila (Criando aplicativos em nuvem do mundo real com o Windows Azure)]
-   [Filas do Azure e Filas do Service Bus do Azure Semelhanças e diferenças][Filas do Azure e Filas do Service Bus do Azure Semelhanças e diferenças]
-   [Como usar o Serviço de Armazenamento de Fila no .NET][Como usar o Serviço de Armazenamento de Fila no .NET]

Para saber mais sobre o serviço Blob do Azure, consulte os seguintes recursos:

-   [Armazenamento de Blob não estruturado (Criando aplicativos de nuvem do mundo real no Windows Azure)][Armazenamento de Blob não estruturado (Criando aplicativos de nuvem do mundo real no Windows Azure)]
-   [Como usar o Serviço de Armazenamento de Blob no .NET][Como usar o Serviço de Armazenamento de Blob no .NET]

Para saber mais sobre o dimensionamento automático das funções do Serviço de Nuvem do Azure, consulte os seguintes recursos:

-   [Como usar o Bloco de Aplicativos de Dimensionamento Automático][Como usar o Bloco de Aplicativos de Dimensionamento Automático]
-   [Dimensionamento automático e o Azure][Dimensionamento automático e o Azure]
-   [Criando soluções elásticas e autoescalonáveis com o Azure][Criando soluções elásticas e autoescalonáveis com o Azure] (vídeo do canal 9 do MSDN)

## <a name="Acknowledgments"></a><span class="short-header">Agradecimentos</span>Agradecimentos

Esses tutoriais e o aplicativo de exemplo foram escritos por [Rick Anderson][Rick Anderson] e Tom Dykstra. Gostaríamos de agradecer às pessoas a seguir por sua assistência:

-   Barry Dorrans (Twitter [@blowdart][@blowdart])
-   [Cory Fowler][Cory Fowler] (Twitter [@SyntaxC4][@SyntaxC4] )
-   [Joe Giardino][Joe Giardino]
-   Don Glover
-   Jai Haridas
-   [Scott Hunter][Scott Hunter] (Twitter: [@coolcsh][@coolcsh])
-   [Brian Swan][Brian Swan]
-   [Daniel Wang][Daniel Wang]
-   Os membros do Conselho de Consultoria do Desenvolvedor que forneceram comentários:
   * Damir Arh
   * Jean-Luc Boucho
   * Carlos dos Santos
   * Mike Douglas
   * Robert Fite
   * Gianni Rosa Gallina
   * Fabien Lavocat
   * Karl Ots
   * Carlos-Alejandro Perez
   * Sunao Tomita
   * Perez Jones Tsisah
   * Michiel van Otegem

  [o primeiro tutorial da série]: /pt-br/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Adicionar o projeto da função de trabalho B na solução]: #addworkerrole
  [Adicionar uma referência ao projeto web]: #addreference
  [Adicionar o pacote NuGet do SendGrid ao projeto]: #addsendgrid
  [Adicionar configurações do projeto]: #addsettings
  [Adicionar o código que é executado quando a função de trabalho é iniciada.]: #addcode
  [Teste a função de trabalho B]: #testing
  [Próximas etapas]: #nextsteps
  [Menu Novo projeto de função de trabalho]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png
  [Caixa de diálogo Novo projeto de função]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png
  [Adicionar referência à MvcWebRole]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png
  [Gerenciar Pacotes NuGet]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png
  [Instalar o pacote SendGrid]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png
  [terceiro tutorial]: /pt-br/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage
  [segundo tutorial]: /pt-br/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [Novas configurações no projeto WorkerRoleB]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png
  [tutorial anterior]: /pt-br/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [GetMessage]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee741827.aspx
  [visibility timeout]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee758454.aspx
  [GetMessages]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx
  [SendGrid MultiAuth - Várias credenciais da conta]: http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials
  []: https://sendgrid.com/credentials
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Conhecimento essencial para o Armazenamento do Windows Azure (a página pode estar em inglês)]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx
  [Conhecimento essencial para o Armazenamento de Tabela do Azure (a página pode estar em inglês)]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx
  [Como obter o máximo das Tabelas do Windows Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [Como usar o Serviço de Armazenamento de Tabela no .NET]: http://www.windowsazure.com/pt-br/develop/net/how-to-guides/table-services/
  [Windows Azure Storage Client Library 2.0 Tables Deep Dive (a página pode estar em inglês)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [Mundi real: Criando uma Estratégia de Particionamento Escalonável para Armazenamento de Tabela do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh508997.aspx
  [Padrão de trabalho centrado em fila (Criando aplicativos em nuvem do mundo real com o Windows Azure)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern
  [Filas do Azure e Filas do Service Bus do Azure Semelhanças e diferenças]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh767287.aspx
  [Como usar o Serviço de Armazenamento de Fila no .NET]: /pt-br/develop/net/how-to-guides/queue-service/
  [Armazenamento de Blob não estruturado (Criando aplicativos de nuvem do mundo real no Windows Azure)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage
  [Como usar o Serviço de Armazenamento de Blob no .NET]: /pt-br/develop/net/how-to-guides/blob-storage/
  [Como usar o Bloco de Aplicativos de Dimensionamento Automático]: /pt-br/develop/net/how-to-guides/autoscaling/
  [Dimensionamento automático e o Azure]: http://msdn.microsoft.com/pt-br/library/hh680945(v=PandP.50).aspx
  [Criando soluções elásticas e autoescalonáveis com o Azure]: http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04
  [Rick Anderson]: http://blogs.msdn.com/b/rickandy/
  [@blowdart]: https://twitter.com/blowdart
  [Cory Fowler]: http://blog.syntaxc4.net/
  [@SyntaxC4]: https://twitter.com/SyntaxC4
  [Joe Giardino]: http://blogs.msdn.com/b/windowsazurestorage/
  [Scott Hunter]: http://blogs.msdn.com/b/scothu/
  [@coolcsh]: http://twitter.com/coolcsh
  [Brian Swan]: http://blogs.msdn.com/b/brian_swan/
  [Daniel Wang]: http://blogs.msdn.com/b/daniwang/
