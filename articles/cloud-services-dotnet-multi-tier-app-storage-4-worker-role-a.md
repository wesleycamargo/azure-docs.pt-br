<properties linkid="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" urlDisplayName="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" pageTitle="Aplicativo Web multicamadas do ASP.NET com o Azure -  Etapa 4: Função de trabalho A" metaKeywords="tutorial do Azure, aplicativo multicamadas .NET, arquitetura multicamadas" description="O quarto tutorial de uma série que ensina como configurar seu computador para desenvolvimento e implantação do aplicativo de Serviço de Email do Azure." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Criando a função de trabalho A (agendador de email) para o aplicativo de Serviço de Email do Azure - 4 de 5" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />.

# Criando a função de trabalho A (agendador de email) para o aplicativo de Serviço de Email do Azure - 4 de 5. 

Este é o quarto de uma série de cinco tutoriais que mostram como criar e implantar o aplicativo de exemplo do Serviço de Email do Azure.  Para obter informações sobre o aplicativo e a série de tutoriais, consulte [o primeiro tutorial da série][firsttutorial].

Neste tutorial, você aprenderá:

* Como consultar e atualizar tabelas do Armazenamento do Azure.
* Como adicionar itens de trabalho a uma fila para processamento por outra função de trabalho.
* Como tratar desligamentos planejados substituindo o método `OnStop`.
* Como tratar desligamentos não planejados certificando-se de que nenhum email seja perdido e nenhum email duplicado seja enviado.
* Como testar uma função de trabalho que usa tabelas de Armazenamento do Azure usando o Gerenciador de Armazenamento do Azure.
 
Você já criou o projeto da função de trabalho A quando criou o projeto do Serviço de Nuvem. Portanto, tudo o que você precisa fazer agora é programar a função de trabalho e configurá-la para usar sua conta de Armazenamento do Azure.




<h2><a name="addref"></a><span class="short-header">Adicionar referência ao projeto</span>Adicionar uma referência ao projeto web</h2>

É necessário uma referência ao projeto web, porque é lá que as classes de entidade são definidas. Você usará as mesmas classes de entidade da função de trabalho B para ler e gravar dados nas tabelas do Azure que o aplicativo usa.

**Observação:** em um aplicativo de produção você não definiria uma referência a um projeto web de um projeto de função de trabalho, porque isso resultaria em referência a vários assemblies dependentes que você não deseja ou precisa na função de trabalho. Normalmente você manteria as classes de modelo compartilhadas em um projeto de biblioteca de classes e os projetos de função web e de trabalho fariam referência ao projeto de biblioteca de classes. Para simplificar a estrutura da solução, as classes de modelo são armazenadas no projeto web neste tutorial.

4. Clique com o botão direito do mouse no projeto WorkerRoleA e escolha **Adicionar Referência**.

	![Adicionar referência ao projeto WorkerRoleA][mtas-worker-a-add-reference-menu]

4. No **Gerenciador de Referências**, adicione uma referência ao projeto MvcWebRole (ou ao projeto de aplicativo web se você estiver executando a interface do usuário da web em um Site do Azure) e clique em **OK**.

	![Adicionar referência à MvcWebRole][mtas-worker-a-reference-manager]




<h2><a name="addref2"></a><span class="short-header">Adicionar referência à SCL 1.7</span>Adicionar uma referência a um assembly SCL 1.7</h2>

>[WACOM.NOTE] Ignore esta etapa se você tiver instalado o SDK 2.3 ou posterior.

A versão 2.0 da Storage Client Library (SCL) 2.0 não tem tudo o que é necessário para diagnóstico, portanto, você precisa adicionar uma referência a um dos assemblies 1.7. Você já fez isso se tiver seguido as etapas do tutorial anterior, mas as instruções estão incluídas aqui caso tenha ignorado essa etapa.

4. Clique com o botão direito do mouse no projeto WorkerRoleA e escolha **Adicionar Referência**.

5. Clique no botão **Procurar...** na parte inferior da caixa de diálogo.

6. Navegue até a seguinte pasta:

        C:\Arquivos de Programas\Microsoft SDKs\Windows Azure\.NET SDK\2012-10\ref

7. Selecione *Microsoft.WindowsAzure.StorageClient.dll* e clique em **Adicionar**.

8. Na caixa de diálogo **Gerenciador de Referência**, clique em **OK**.




<h2><a name="addmodel"></a><span class="short-header">Adicionar modelo SendEmail</span>Adicionar o modelo SendEmail</h2>

A função de trabalho A cria as linhas `SendEmail` na tabela `Message` e a função de trabalho B lê essas linhas para obter as informações necessárias para o envio de email. A imagem a seguir mostra um subconjunto de propriedades para duas linhas de `Message` e três linhas de `SendEmail` na tabela `Message`.

	![tabela de mensagem com sendmail][mtas-sendMailTbl]

As linhas da tabela `Message` servem para vários propósitos:

* Fornecem todas as informações que a função de trabalho B precisa para enviar um único email.
* Rastreiam para verificar se um email foi enviado, para evitar que duplicatas sejam enviadas em caso de uma função de trabalho ser reiniciada após uma falha.
* Possibilitam que a função de trabalho A determine quando todos os emails para uma mensagem foram enviados, para que possa ser marcada como `Concluída`.

Para ler e gravar as linhas de `SendEmail`, uma classe de modelo é necessária.  Como deve ser acessível para a função de trabalho A e para a função de trabalho B, e como todas as outras classes de modelo são definidas no projeto web, faz sentido definir essa classe no projeto web também. 

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Models e escolha **Adicionar Item Existente**.

	![Adicionar um item existente à pasta Models no projeto web][mtas-add-existing-for-sendemail-model]

3. Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione o arquivo *SendEmail.cs* na pasta Models do projeto web e clique em Adicionar.

4. Abra o *SendEmail.cs* e examine o código.

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
	
	O código aqui é semelhante ao de outras classes de modelo, exceto que nenhum atributo DataAnnotations está incluído porque não há nenhuma interface do usuário associada a esse modelo – não é usado em um controlador MVC. 

<h2><a name="addcode"></a><span class="short-header">Adicionar o código de função de trabalho</span>Adicionar o código que é executado quando a função de trabalho é iniciada</h2>

4. No projeto WorkerRoleA, abra *WorkerRole.cs* e examine o código.
	
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

5. Exclua o *WorkerRole.cs* e clique com o botão direito do mouse no projeto WorkerRoleA e escolha **Adicionar Item Existente**.

	![Adicionar um item existente à Função de Trabalho A][mtas-worker-a-add-existing]

2. Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione o arquivo *WorkerRoleA.cs* no projeto WorkerRoleA e clique em **Adicionar**.

3. Abra *WorkerRoleA.cs* e examine o código.

	O método `OnStart` inicializa os objetos de contexto que você precisa para trabalhar com as entidades de Armazenamento do Azure. Também garante que todas as tabelas, filas e contêineres de blob que você usará no método `Run` existam. O código que executa essas tarefas é semelhante ao que você viu anteriormente nos construtores do controlador MVC. Você irá configurar a cadeia de conexão que este método usará mais tarde.

   
        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount;

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
   
	O método `ConfigureDiagnostics` em que as chamadas do método `OnStart` definem o rastreamento para que você possa ver a saída dos métodos `Trace.Information` e `Trace.Error`.  Esse método é explicado no [segundo tutorial][tut2].

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

	* O Azure precisa reinicializar a máquina virtual (a instância da função web ou da função de trabalho) ou o computador físico que hospeda a máquina virtual.
	* Você parou seu Serviço de Nuvem usando o botão **Parar** no Portal de Gerenciamento do Azure.
	* Você implantou uma atualização em seu projeto de Serviço de Nuvem.

	O método `Run` monitora a variável `onStopCalled` e para de efetuar pull em qualquer novo item de trabalho a ser processado quando essa variável for alterada para `true`. Essa coordenação entre os métodos `OnStop` e `Run` permite um desligamento normal do processo de trabalho.

	O Azure periodicamente instala atualizações do sistema operacional para garantir que a plataforma esteja segura e confiável e execute bem. Essas atualizações normalmente requerem as máquinas que hospedam o Serviço de Nuvem sejam desligadas e reiniciadas. Para obter mais informações, consulte [A instância da função é reinicializada devido as atualizações do sistema operacional (a página pode estar em inglês)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

	O método `Run` executa duas funções:

	* Examina a tabela `message` procurando por mensagens agendadas a serem enviadas hoje ou anteriormente, para as quais os itens de trabalho da fila não foram criados ainda.

	* Examina a tabela `message` procurando por mensagens que tenham um status indicando que todos os itens de trabalho da fila foram criados, mas nem todos os emails foram enviados. Caso encontre um, ele examina as linhas de `SendEmail` dessa mensagem para ver se todos os emails foram enviados, e se foram, atualiza o status para `Completed` e arquiva a linha `message`.

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


	Observe que todo o trabalho é feito em um loop infinito em um bloco `while` e todo o código no bloco `while` é disposto em um bloco `try`-`catch` para prevenir uma exceção sem tratamento. Se ocorrer uma exceção sem tratamento, o Azure acionará o evento [UnhandledException](http://msdn.microsoft.com/pt-br/library/system.appdomain.unhandledexception.aspx), o processo de trabalho será terminado e a função é colocada offline. A função de trabalho será reiniciada pelo Azure, mas isso levará alguns minutos. O bloco `try` chama `TraceError` para registrar os erros e, em seguida, entra em modo de suspensão por 60 segundos de forma que, se o erro for persistente, a mensagem de erro não será repetida muitas vezes.  Em um aplicativo de produção, você pode enviar um email a um administrador no bloco `try`.

	O método `Run` processa uma consulta para linhas de `message` na tabela `message` que agendou a data antes de amanhã:

                    // Retrieve all messages that are scheduled for tomorrow or earlier
                    // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(
                        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                    var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                    var messagesToProcess = messageTable.ExecuteQuery(query).ToList();

	**Observação:** um dos benefícios de mover as linhas da mensagem para a tabela `messagearchive` depois de serem processadas é que essa consulta precisa especificar apenas `PartitionKey` e `RowKey` como critérios de pesquisa. Se não arquivamos as linhas processadas, a consulta também precisaria especificar um campo não chave (`Status`) e procurar em mais linhas.  O tamanho da tabela aumentaria, a consulta levaria mais tempo e poderia começar a obter tokens de continuação.

	Se uma mensagem estiver em status `Pendente`, o processamento ainda não foi iniciado. Se estiver em status `Em Fila`, o processamento terá começado mais cedo, mas foi interrompido antes que todas as mensagens da fila fossem criadas. Nesse caso, uma verificação adicional precisa ser feita na função de trabalho B quando está enviando cada email para verificar se o email já não foi enviado. Esse é o objetivo da variável `restartFlag`.

                        string restartFlag = "0";
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";
                        }

	Em seguida, o código define as linhas de `message` que estão com o status `Pendente` como `Em Fila`.  Em seguida, para aquelas linhas e mais qualquer linha que já estava com o status `Em Fila`, ele chama o método `ProcessMessage` para criar os itens de trabalho da fila para enviar emails para a mensagem. 

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


	Após processar uma mensagem no status `Em Fila`, o código define o status da linha da `Mensagem` como `Processando`. As linhas na tabela `message` que não estão com o status `Pendente` ou `Em Fila` já estão com o status `Processando` e, para essas linhas, o código chama um método que verifica se todos os emails para a mensagem foram enviados.  Se todos os emails foram enviados, a linha `message` será arquivada.

	Após o processamento de todos os registros recuperados pela consulta, o código é suspenso por um minuto.  

                // Sleep for one minute to minimize query costs.
                System.Threading.Thread.Sleep(1000*60);

	Há um custo mínimo para todas as consultas de Armazenamento do Azure, mesmo que a consulta não retorne nenhum dado, portanto, verificar novamente continuamente aumentaria desnecessariamente suas despesas do Azure. Na data em que este tutorial está sendo escrito, o custo é de US$ 0,10 por milhões de transações (uma consulta conta como uma transação), portanto, o tempo de suspensão pode ser transformado em muito menos que um minuto e o custo de verificação de mensagens nas tabelas a serem enviadas seria mínimo. Para obter mais informações sobre preços, consulte o [primeiro tutorial][firsttutorial].

	**Observação sobre threading e utilização ideal da CPU:** há duas tarefas no método `Run` (enfileiramento de emails e verificação de mensagens concluídas), e elas são executadas sequencialmente em um único thread. Uma VM (máquina virtual) pequena tem 1,75 GB de RAM e uma única CPU, portanto, provavelmente está OK executar essas tarefas sequencialmente com um único thread. Suponha que seu aplicativo precise de mais memória do que a VM pequena fornecida para executar de forma eficiente. Uma VM média fornece 3,5 GB de RAM e duas CPUs, mas esse aplicativo só usaria uma CPU, porque é single threaded. Para aproveitar todas as CPUs, você precisaria criar um thread de trabalho para cada CPU. Mesmo assim, uma única CPU não é completamente utilizada por um thread. Quando um thread faz chamadas de rede ou de E/S, o thread deve esperar que a chamada de rede ou E/S seja concluída e, enquanto espera, não está fazendo trabalho útil. Se o método `Run` foi implementado usando dois threads, quando um thread estiver esperando que a operação de rede ou de E/S seja concluída, o outro thread poderá estar fazendo trabalho útil.

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

	Primeiro, o código obtém a linha da lista de endereçamento da tabela `mailinglist` para a lista de endereçamento de destino.  Essa linha tem o endereço de email "de" que precisa ser fornecido para a função de trabalho B para o envio de emails.

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

	A última tarefa a ser executada para cada endereço de email é criar o item da fila de trabalho que acionará a função de trabalho B para enviar um email. O item da fila de trabalho contém o valor da chave de partição e da chave da linha `SendEmail` que acabou de ser criada e o sinalizador de reinicialização que foi definido anteriormente. A linha `SendEmail` contêm todas as informações que a função de trabalho B precisa para enviar um email.

                // Create the queue message.
                string queueMessageString =
                    sendEmailRow.PartitionKey + "," +
                    sendEmailRow.RowKey + "," +
                    restartFlag;
                var queueMessage = new CloudQueueMessage(queueMessageString);
                sendEmailQueue.AddMessage(queueMessage);

	O método `CheckAndUpdateStatusIfComplete` verifica as mensagens que estão no status Processando para ver se todos os emails foram enviados. Se nenhum email não enviado for encontrado, ele atualizará o status da linha para `Concluída` e arquivará a linha. 

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





<h2><a name="configure"></a><span class="short-header">Configurar o armazenamento</span>Configurar a cadeia de conexão de armazenamento</h2>

Se você ainda não configurou as credenciais da conta de armazenamento para função de trabalho A quando fez isso para a função web, faça isso agora. 

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **WorkerRoleA** sob **Funções** no projeto em nuvem **AzureEmailService** e, em seguida, escolha **Propriedades**.

2. Verifique se a opção **Todas as Configurações** está selecionada na lista suspensa **Configuração de Serviço**.

2. Selecione a guia **Configurações** e, em seguida, clique em **Adicionar Configuração**.

3. Digite *StorageConnectionString* na coluna **Nome**.

4. Selecione **Cadeia de Conexão** na lista suspensa **Tipo**.  
  
5. Clique nas reticências (**...**) na extremidade direita da linha para criar uma nova cadeia de conexão.

6. Na caixa de diálogo **Conta de Armazenamento da Cadeia de Conexão**, clique em **Sua assinatura**.

7. Escolha a **Assinatura** e o **Nome da Conta** corretos e clique em **OK**.

8. Defina a cadeia de conexão de diagnóstico. Você pode usar a mesma conta de armazenamento para a cadeia de conexão de diagnóstico, mas uma prática recomendada é usar outra conta de armazenamento para as informações de rastreamento (diagnóstico).




<h2><a name="testing"></a><span class="short-header">Testando</span>Testando a função de trabalho A</h2>

1. Execute o aplicativo pressionando F5.

>[WACOM.NOTE] Com o Visual Studio 2013 e o SDK mais recente, você pode obter um erro de "referência ambígua" para a referência ao `LogLevel`. Clique com o botão direito do mouse em `LogLevel`, clique em Resolver e selecione `Microsoft.WindowsAzure.Diagnostics.LogLevel`.

2. Use as páginas da web do administrador para criar uma lista de endereçamento e criar assinantes para a lista de endereçamento. Defina a propriedade `Verified` como `true` para pelo menos um dos assinantes e defina o endereço de email como um endereço no qual você possa receber email.

	Nenhum email será enviado até que você implemente a função de trabalho B, mas você usará os mesmos dados de teste para testar a função de trabalho B.

3. Crie uma mensagem a ser enviada para a lista de endereçamento que você criou e defina a data programada para hoje ou para uma data no passado.

	![Nova mensagem no status pendente][mtas-worker-a-test-pending]

4. Em um pouco mais de um minuto (devido ao tempo de suspensão de um minuto do método Run), atualize a página da web Mensagens e você verá o status mudar para Processando. (Você talvez o veja mudando para Em Fila primeiro, mas as alterações irão de Em Fila para Processando tão rapidamente que você não verá Em Fila.

	![Nova mensagem no status processando][mtas-worker-a-test-processing]

5. Abra o Gerenciador de Armazenamento do Azure e selecione sua conta de armazenamento de teste.

6. No Gerenciador de Armazenamento do Azure sob **Tipo de Armazenamento** selecione **Filas** e selecione **azuremailqueue**.

	Você verá uma mensagem da fila para cada assinante verificado na sua lista de emails de destino.

	![Mensagens da fila no ASE][mtas-worker-a-tst-ase-queue]

7. Clique duas vezes em uma mensagem da fila e, em seguida, na caixa de diálogo **Detalhes da Mensagem**, selecione a guia **Mensagem**.

	Você verá o conteúdo da fila de mensagens: a chave de partição (data de 14-12-2012), a chave da linha (o valor de MessageRef e o endereço de email) e o sinalizador de reinicialização, delimitados por uma vírgula.

	![Conteúdo da mensagem da fila no ASE][mtas-worker-a-tst-ase-queue-detail]

8. Feche a caixa de diálogo **Detalhes da Mensagem**.

9. Sob **Tipo de Armazenamento**, selecione **Tabelas** e, em seguida, selecione a tabela **Message**.

10. Clique em **Consulta** para ver todas as linhas da tabela.

	Você verá a mensagem agendada, com "Mensagem" na chave de linha, seguida por uma linha para cada assinante verificado, com o endereço de email na chave de linha.

	![Linhas da tabela Message no ASE][mtas-worker-a-test-ase-message-table]

11. Clique duas vezes em uma linha que tenha um endereço de email na chave de linha, para ver o conteúdo da linha `SendEmail` que a função de trabalho A criou.

	![Linha de SendEmail na tabela de Message][mtas-worker-a-test-ase-sendemail-row]

<h2><a name="nextsteps"></a><span class="short-header">Próximas etapas</span>Próximas etapas</h2>

Agora você criou a função de trabalho A e verificou que ela cria as mensagens da fila e as filas da tabela que a função de trabalho B precisa para enviar emails. No [próximo tutorial][tut5], você irá compilar e testar a função de trabalho B.

Para obter links para recursos adicionais para trabalhar com tabelas, filas e blobs do Armazenamento do Azure, consulte o final do [último tutorial desta série][tut5].

<div><a href="/pt-br/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/" class="site-arrowboxcta download-cta">Tutorial 5</a></div>








[firsttutorial]: /pt-br/develop/net/tutorials/multi-tier-web-site/1-overview/
[tut5]: /pt-br/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
[tut2]: /pt-br/develop/net/tutorials/multi-tier-web-site/2-download-and-run/




[mtas-worker-a-add-existing]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-add-existing.png
[mtas-worker-a-add-reference-menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-add-reference-menu.png
[mtas-worker-a-reference-manager]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-reference-manager.png
[mtas-add-existing-for-sendemail-model]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-add-existing-for-sendemail-model.png
[mtas-worker-a-test-processing]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-processing.png
[mtas-worker-a-test-pending]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-pending.png
[mtas-worker-a-tst-ase-queue]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue.png
[mtas-worker-a-tst-ase-queue-detail]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue-detail.png
[mtas-worker-a-test-ase-message-table]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-message-table.png
[mtas-worker-a-test-ase-sendemail-row]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-sendemail-row.png
[mtas-sendMailTbl]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-sendMailTbl.png


