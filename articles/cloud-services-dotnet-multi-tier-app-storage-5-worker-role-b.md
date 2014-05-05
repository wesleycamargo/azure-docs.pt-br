<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" urlDisplayName="Etapa 5: Função de Trabalho B" pageTitle="Aplicativo da web multicamada ASP.NET com o Azure - Etapa 5: Função de Trabalho B" metaKeywords="tutorial do Azure, adicionando função de trabalho no serviço de nuvem, função de trabalho C#" description="O quinto tutorial em uma série que ensina como configurar seu computador para desenvolvimento e implantação do aplicativo de Serviço de Email do Azure" metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Compilando a função de trabalho B (remetente de email) para o aplicativo de Serviço de Email do Azure - 5 de 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

# Criar a função de trabalho B (remetente de email) para o aplicativo de Serviço de Email do Azure - 5 de 5. 

Este é o quinto de uma série de cinco tutoriais que mostram como criar e implantar o aplicativo de exemplo do Serviço de Email do Azure.  Para obter informações sobre o aplicativo e a série de tutoriais, consulte [o primeiro tutorial da série][firsttutorial].

Neste tutorial, você aprenderá:

* Como adicionar uma função de trabalho para um projeto de serviço de nuvem.
* Como pesquisar uma fila e processar os itens de trabalho da fila.
* Como enviar emails usando o SendGrid.
* Como lidar com desligamentos planejados substituindo o método `OnStop`.
* Como lidar com desligamentos não planejados certificando-se de que nenhum email duplicado seja enviado.
 
<h2><a name="addworkerrole"></a><span class="short-header">Adicionar a função de trabalho B</span>Adicionar o projeto da função de trabalho B na solução</h2>

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto de serviço de nuvem e escolha **Novo Projeto de Função de Trabalho**.

	![Menu Novo projeto de função de trabalho][mtas-new-worker-role-project]

3. Na caixa de diálogo **Adicionar Novo Projeto de Função**, selecione **C#**, selecione **Função de Trabalho**, nomeie o projeto WorkerRoleB e clique em **Adicionar**. 

	![Caixa de diálogo Novo projeto de função][mtas-add-new-role-project-dialog]

<h2><a name="addreference"></a><span class="short-header">Adicionar referência</span>Adicionar uma referência ao projeto web</h2>

É necessário uma referência ao projeto web, porque é lá que as classes de entidade são definidas. Você usará as classes de entidade da função de trabalho B para ler e gravar dados nas tabelas do Azure que o aplicativo usa.

4. Clique com o botão direito do mouse no projeto WorkerRoleB e escolha **Adicionar Referência**.

	![Adicionar referência ao projeto WorkerRoleB][mtas-worker-b-add-reference-menu]

4. Em **Gerenciador de Referências**, adicione uma referência ao projeto MvcWebRole (ou ao projeto de aplicativo web se você estiver executando a interface do usuário da web em um Site do Azure).

	![Adicionar referência à MvcWebRole][mtas-worker-b-reference-manager]



<h2><a name="sclpackage"></a><span class="short-header">Adicionar o pacote SCL 2.0</span>Adicionar o pacote NuGet da Biblioteca de Cliente do Armazenamento 2.0 ao projeto</h2>

>[WACOM.NOTE] Com o Visual Studio 2013, você pode ignorar esta seção porque o pacote atual do Armazenamento do Azure está instalado no novo projeto de função de trabalho.

Quando você adicionou o projeto, ele não obteve automaticamente a versão atualizada do pacote NuGet da Biblioteca de Cliente do Armazenamento. Em vez disso, ele obteve a versão antiga 1.7 do pacote uma vez que essa é a que está incluída no modelo do projeto. Agora, a solução tem duas versões do pacote NuGet de Armazenamento do Azure: a versão 2.0 nos projetos MvcWebRole e WorkerRoleA, e a versão 1.7 no projeto WorkerRoleB. Você precisa desinstalar a versão 1.7 e instalar a versão 2.0 no projeto WorkerRoleB.

1. No menu **Ferramentas**, escolha **Gerenciador de Pacotes da Biblioteca** e, em seguida, **Gerenciar Pacotes NuGet para Solução**.

2. Com **Pacotes Instalados** selecionado no painel esquerdo, role para baixo até chegar ao pacote de Armazenamento do Azure.

	Você verá o pacote listado duas vezes, uma vez para a versão 1.7 e uma vez para a versão 2.0.

4. Selecione a versão 1.7 do pacote e clique em **Gerenciar**.

	As caixas de seleção para MvcWebRole e WorkerRoleB são desmarcadas, e a caixa de seleção para WorkerRoleB é selecionada.

5. Desmarque a caixa de seleção para WorkerRoleB e, em seguida, clique em **OK**.

6. Quando for perguntado se você deseja desinstalar pacotes dependentes, clique em **Não**.

	Quando a desinstalação for concluída, você terá apenas a versão 2.0 do pacote na caixa de diálogo NuGet.

7. Clique em **Gerenciar** para a versão 2.0 do pacote.

	As caixas de seleção para MvcWebRole e WorkerRoleA são selecionadas, e a caixa de seleção para WorkerRoleA é desmarcada.

8. Selecione a caixa de seleção para WorkerRoleA e, em seguida, clique em **OK**.





<h2><a name="addref2"></a><span class="short-header">Adicionar referência à SCL 1.7</span>Adicionar uma referência a um assembly SCL 1.7</h2>

>[WACOM.NOTE] Ignore esta seção se você instalou o SDK mais recente e estiver usando o Visual Studio 2013

A versão 2.0 da SCL (Biblioteca de Cliente do Armazenamento) não tem tudo o que é necessário para diagnóstico, portanto, você precisa adicionar uma referência a um dos assemblies 1.7, como fez anteriormente para os outros dois projetos.

4. Clique com o botão direito do mouse no projeto WorkerRoleB e escolha **Adicionar Referência**.

5. Clique no botão **Procurar...** na parte inferior da caixa de diálogo.

6. Navegue até a seguinte pasta:

        C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\2012-10\ref

7. Selecione *Microsoft.WindowsAzure.StorageClient.dll* e clique em **Adicionar**.

8. Na caixa de diálogo **Gerenciador de Referência**, clique em **OK**.





<h2><a name="addsendgrid"></a><span class="short-header">Adicionar pacote SendGrid</span>Adicionar o pacote NuGet do SendGrid ao projeto</h2>

Para enviar um email usando o SendGrid, você precisa instalar o pacote NuGet do SendGrid.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto WorkerRoleB e escolha **Gerenciar Pacotes NuGet**.

	![Gerenciar Pacotes NuGet][mtas-worker-b-manage-nuget]

2. Na caixa de diálogo **Gerenciar Pacotes NuGet**, selecione a guia **Online**, digite "sendgrid" na caixa de pesquisa e pressione Enter.

3. Clique em **Instalar** no pacote **SendGrid**.

	![Instalar o pacote SendGrid][mtas-worker-b-install-sendgrid]

4. Feche a caixa de diálogo.








<h2><a name="addsettings"></a><span class="short-header">Adicionar configurações do projeto</span>Adicionar configurações do projeto</h2>

Como a função de trabalho A, a função de trabalho B precisa das credenciais da conta de armazenamento para trabalhar com tabelas, filas e blobs. Além disso, para enviar um email, a função de trabalho precisa ter credenciais para inserir em chamadas para o serviço SendGrid. E, para construir um link de cancelamento de assinatura para incluir nos emails que envia, a função de trabalho precisa saber a URL do aplicativo. Esses valores são armazenados nas configurações do projeto.

Para as credenciais da conta de armazenamento, o procedimento é o mesmo que você viu no [terceiro tutorial][tut3configstorage].

1. No **Gerenciador de Soluções**, sob **Funções** no projeto em nuvem, clique com o botão direito do mouse em **WorkerRoleB** e escolha **Propriedades**.
 
2. Selecione a guia **Configurações**.

2. Verifique se a opção **Todas as Configurações** está selecionada na lista suspensa **Configuração de Serviço**.

2. Selecione a guia **Configurações** e, em seguida, clique em **Adicionar Configuração**.

3. Digite "StorageConnectionString" na coluna **Nome**.

4. Selecione **Cadeia de Conexão** na lista suspensa **Tipo**.  

6. Clique no botão de reticências (**...**) na extremidade direita da linha para abrir a caixa de diálogo **Cadeia de Conexão da Conta de Armazenamento**.

7. Na caixa de diálogo **Criar Cadeia de Conexão de Armazenamento**, clique no botão de opção **Sua Assinatura**.

8. Escolha a mesma **Assinatura** e o mesmo **Nome da Conta** que você escolheu para a função Web e para a função de trabalho A.

1. Siga o mesmo procedimento para definir configurações para a cadeia de conexão **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**.

	Em seguida, você cria e configura as três novas configurações que são usadas apenas pela função de trabalho B.

3. Na guia **Configurações** na janela **Propriedades**, clique em **Adicionar Configuração** e, em seguida, adicione três novas configurações do tipo **Cadeia de Caracteres**:

	* **Nome**: SendGridUserName, **Valor**: o nome de usuário do SendGrid que você estabeleceu no [segundo tutorial][tut2].

	* **Nome**: SendGridPassword, **Valor**: a senha do SendGrid.

	* **Nome**: AzureMailServiceURL, **Valor**: a URL base que o aplicativo terá quando você implantá-lo, por exemplo: http://sampleurl.cloudapp.net.

	![Novas configurações no projeto WorkerRoleB][mtas-worker-b-settings]

### Adicionar o código que é executado quando a função de trabalho é iniciada.

4. No projeto WorkerRoleB, exclua WorkerRole.cs.

5. Clique com o botão direito do mouse no projeto WorkerRoleB e escolha **Adicionar Item Existente**.

	![Adicionar um item existente à Função de Trabalho B][mtas-worker-b-add-existing]

2. Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione o arquivo WorkerRoleB.cs no projeto WorkerRoleB e clique em **Adicionar**.

>[WACOM.NOTE] Para o Visual Studio 2013 com o SDK mais recente e o pacote NuGet do SendGrid mais recente, abra *WorkerRoleB.cs* e faça as seguintes alterações no código: (1) Exclua a instrução `using` para `SendGridMail.Transport`. (2) Altere as duas instâncias de `SendGrid.GenerateInstance` para `SendGrid.GetInstance`. (3) Altere as duas instâncias de `REST.GetInstance` para `Web.GetInstance`.

3. Abra WorkerRoleB.cs e examine o código.

	Como você já viu na função de trabalho A, o método `OnStart` inicializa as classes de contexto de que você precisa para trabalhar com as entidades de armazenamento do Azure. Também garante que todas as tabelas, filas e contêineres de blob de que você precisa no método `Run` existam.  

	A diferença em comparação com a função de trabalho A é a adição do contêiner de blob e a fila de inscrição entre os recursos a serem criados, caso ainda não existam. Você usará o contêiner de blob para obter os arquivos que contêm o HTML e o texto sem formatação para o corpo do email. A fila de inscrição é usada para enviar emails de confirmação de assinatura.

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount;

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

	O método `Executar` processa itens de trabalho de duas filas: a fila usada para mensagens enviadas a listas de email (itens de trabalho criados pela função de trabalho A), e a fila usada para confirmação de assinatura de emails (itens de trabalho criados pelo método API de inscrição no projeto MvcWebRole).

       
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

	O objetivo do tempo de suspensão é minimizar os custos de transação de Armazenamento do Azure, conforme explicado no [tutorial anterior][tut4]. 

	Quando é efetuado pull de um item da fila pelo método [GetMessage][], esse item da fila torna-se invisível por 30 segundos para todas as outras funções Web e de trabalho acessarem a fila. Isso é o que garante que apenas uma instância da função de trabalho irá selecionar qualquer mensagem de determinada fila para processamento. Você pode definir explicitamente esse tempo de *concessão exclusiva* (o tempo que o item de fila está invisível), passando um parâmetro [visibility timeout](http://msdn.microsoft.com/pt-br/library/windowsazure/ee758454.aspx) para o método `GetMessage`. Se a função de trabalho puder levar mais de 30 segundos para processar uma mensagem da fila, você deverá aumentar o tempo de concessão exclusiva para prevenir que outras instâncias da função processem a mesma mensagem. 

	Por outro lado, você não deseja definir o tempo de concessão exclusiva para um valor excessivamente grande. Por exemplo, se o tempo de concessão exclusiva for definido para 48 horas, e a função de trabalho terminar inesperadamente depois do desenfileiramento de uma mensagem, outra função de trabalhado não poderá processar a mensagem por 48 horas. A concessão exclusiva máxima é de sete dias.

	O método [GetMessages](http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx) (observe o "s" no final do nome) pode ser usado para efetuar pull de até 32 mensagens da fila em uma chamada. Cada acesso de fila incorre em um custo de transação pequeno, e o custo de transação será o mesmo se 32 mensagens forem retornadas ou se zero mensagem for retornada. O código a seguir busca até 32 mensagens em uma chamada e, em seguida, as processa.

    	foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))
	    {
	        ProcessQueueMessage(msg);
	        messageFound = true;
	    }

	Ao usar o `GetMessages` para remover várias mensagens, verifique se o tempo limite da visibilidade dá ao seu aplicativo tempo suficiente para processar todas as mensagens. Quando o tempo limite de visibilidade expirar, outras instâncias da função poderão acessar a mensagem e quando o fizerem, a primeira instância não poderá excluir a mensagem quando terminar de processar o item de trabalho.

	O método `Run` chama o `ProcessQueueMessage` quando localiza um item de trabalho na fila principal.

       
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

	Mensagens suspeitas são aquelas que fazem com que o aplicativo gere uma exceção quando são processadas.  Se for efetuado pull de uma mensagem mais de cinco vezes, pressuporemos que ela não possa ser processada e a removemos da fila para que não continuemos a tentar processá-la. Os aplicativos de produção devem considerar mover a mensagem suspeita para uma fila de "mensagens mortas" para análise, em vez de excluir a mensagem.

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

	O trabalho real de envio de email usando o SendGrid é feito pelo método `SendEmailToList`. Se você desejar usar um serviço diferente do SendGrid, tudo o que você precisa fazer é alterar o código nesse método.

	**Observação:** se você tiver credenciais inválidas nas configurações do projeto, a chamada para o SendGrid falhará, mas o aplicativo não receberá nenhuma indicação da falha.  Se você usar SendGrid em um aplicativo de produção, será recomendável configurar credenciais separadas para a API web para evitar causar falhas silenciosas quando um administrador alterar a sua senha da conta de usuário do SendGrid. Para obter mais informações, consulte [SendGrid MultiAuth - Várias credenciais da conta](http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials). Você pode configurar as credenciais em [https://sendgrid.com/credentials (a página pode estar em inglês)](https://sendgrid.com/credentials) 

        private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
            string htmlMessageBodyRef, string textMessageBodyRef)
        {
            var email = SendGrid.GenerateInstance();
            email.From = new MailAddress(fromEmailAddress);
            email.AddTo(emailAddress);
            email.Html = GetBlobText(htmlMessageBodyRef);
            email.Text = GetBlobText(textMessageBodyRef);
            email.Subject = subjectLine;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = REST.GetInstance(credentials);
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

	O método `Run` chama o `ProcessSubscribeQueueMessage` quando localiza um item de trabalho na fila de inscrição:
 
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

	* Se a mensagem for uma mensagem "suspeita", ele a registra em log e a exclui.
	* Obtém o GUID do assinante da mensagem da fila.
	* Usa o GUID para obter informações do assinante da tabela MailingList.
	* Envia um email de confirmação para o novo assinante.
	* Exclui a mensagem da fila.

	Como com emails enviados para listas, o envio real do email está em um método separado, facilitando a alteração para um serviço de email diferente se você desejar fazer isso.

        private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
        {
            var email = SendGrid.GenerateInstance();
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
            var transportREST = REST.GetInstance(credentials);
            transportREST.Deliver(email);
        }





<h2><a name="testing"></a><span class="short-header">Testando</span>Testando a função de trabalho B</h2>

1. Execute o aplicativo pressionando F5.

2. Vá para a página **Mensagens** para ver a mensagem que você criou para testar a função de trabalho A. Após um minuto ou mais, atualize a página da web e verá que a linha desapareceu da lista porque foi arquivada.

4. Verifique a caixa de entrada de email onde você espera receber o email. Observe que pode haver atrasos no envio ou entrega de emails pelo SendGrid para seu cliente de email, portanto, talvez seja necessário esperar um pouco para ver o email. Talvez seja necessário que você verifique sua pasta de lixo eletrônico também.




<h2><a name="nextsteps"></a><span class="short-header">Próximas etapas</span>Próximas etapas</h2>

Você criou o aplicativo de Serviço de Email do Azure a partir do zero, e o que você tem é o mesmo que o projeto concluído que você baixou.  Para implantar na nuvem, testar na nuvem e promover para produção, você pode usar os mesmos procedimentos que viu no [segundo tutorial][tut2].  Se você optar por criar a arquitetura alternativa, consulte [o tutorial Introdução aos Sites do Azure][getstartedtutorial] para obter informações sobre como implantar o projeto MVC em um Site do Azure.

Para saber mais sobre o armazenamento do Azure, consulte o seguinte recurso:

* [Conhecimento essencial para o Armazenamento do Windows Azure (a página pode estar em inglês)](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx) (blog de Bruno Terkaly)

Para saber mais sobre o serviço Tabela do Azure, consulte os seguintes recursos:

* [Conhecimento essencial para o Armazenamento de Tabela do Windows Azure (a página pode estar em inglês)](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx) (blog de Bruno Terkaly)
* [Como obter o máximo das Tabelas do Azure (a página pode estar em inglês)](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx) (blog da equipe de Armazenamento do Azure)
* [Como usar o Serviço de Armazenamento de Tabela no .NET (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/develop/net/how-to-guides/table-services/) 
* [Windows Azure Storage Client Library 2.0 Tables Deep Dive (a página pode estar em inglês)](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx) (blog da equipe de Armazenamento do Azure)
* [Mundo real: criando uma Estratégia de Particionamento Escalonável para Armazenamento de Tabela do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/hh508997.aspx)

Para saber mais sobre o serviço Fila do Azure e filas do Service Bus do Azure, consulte os seguintes recursos:

* [Padrão de trabalho centrado em fila (Criando aplicativos em nuvem do mundo real com o Windows Azure) ](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern)
* [Filas do Azure e Filas do Service Bus do Azure - Semelhanças e diferenças][sbqueuecomparison]
* [Como usar o Serviço de Armazenamento de Fila no .NET][queuehowto]

Para saber mais sobre o serviço Blob do Azure, consulte os seguintes recursos:

* [Armazenamento de Blob não estruturado (Criando aplicativos de nuvem do mundo real no Windows Azure) ](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)
* [Como usar o Serviço de Armazenamento de Blob no .NET][blobhowto]

Para saber mais sobre o dimensionamento automático das funções do Serviço de Nuvem do Azure, consulte os seguintes recursos:

* [Como usar o Bloco de Aplicativos de Dimensionamento Automático][autoscalingappblock]
* [Dimensionamento automático e o Azure][autoscaling-and-windows-azure]
* [Criando soluções elásticas e autoescalonáveis com o Azure](http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04) (vídeo do canal 9 do MSDN)


<h2><a name="Acknowledgments"></a><span class="short-header">Agradecimentos</span>Agradecimentos</h2>

Esses tutoriais e o aplicativo de exemplo foram escritos por [Rick Anderson](http://blogs.msdn.com/b/rickandy/) e Tom Dykstra. Gostaríamos de agradecer às pessoas a seguir por sua assistência:

* Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)) 
* [Cory Fowler](http://blog.syntaxc4.net/) (Twitter [@SyntaxC4](https://twitter.com/SyntaxC4) ) 
* [Joe Giardino](http://blogs.msdn.com/b/windowsazurestorage/)
* Don Glover 
* Jai Haridas
* [Scott Hunter](http://blogs.msdn.com/b/scothu/) (Twitter: [@coolcsh](http://twitter.com/coolcsh)
* [Brian Swan](http://blogs.msdn.com/b/brian_swan/)
* [Daniel Wang](http://blogs.msdn.com/b/daniwang/)
* Os membros do Conselho de Consultoria de Desenvolvedores que forneceram comentários:
   * Damir Arh
   * Jean-Luc Boucho
   * Carlos dos Santos
   * Mike Douglas
   * Robert Fite
   * Gianni Rosa Gallina
   * Fabien Lavocat
   * Karl Ots
   * Carlos Alejandro Perez
   * Sunao Tomita
   * Perez Jones Tsisah
   * Michiel van Otegem

 







[firsttutorial]: /pt-br/develop/net/tutorials/multi-tier-web-site/1-overview/
[tut2]: /pt-br/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
[tut3configstorage]: /pt-br/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage
[tut4]: /pt-br/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
[queuehowto]: /pt-br/develop/net/how-to-guides/queue-service/

[blobhowto]: /pt-br/develop/net/how-to-guides/blob-storage/
[GetMessage]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee741827.aspx
[getstartedtutorial]: /pt-br/develop/net/tutorials/get-started
[sbqueuecomparison]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh767287.aspx
[autoscalingappblock]: /pt-br/develop/net/how-to-guides/autoscaling/


[mtas-new-worker-role-project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png
[mtas-add-new-role-project-dialog]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png
[mtas-worker-b-add-existing]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-existing.png
[mtas-worker-b-add-reference-menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-reference-menu.png
[mtas-worker-b-reference-manager]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png
[mtas-worker-b-manage-nuget]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png
[mtas-worker-b-install-sendgrid]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png


[mtas-worker-b-settings]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png
[autoscaling-and-windows-azure]: http://msdn.microsoft.com/pt-br/library/hh680945(v=PandP.50).aspx

