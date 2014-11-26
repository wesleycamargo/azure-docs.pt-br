<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" pageTitle="Azure Cloud Service Tutorial: ASP.NET Web Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, ASP.NET MVC tutorial, Azure web role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# Criando a função da web para o aplicativo de Serviço de Email do Azure - 3 de 5.

Este é o terceiro de uma série de cinco tutoriais que mostram como criar e implantar o aplicativo de exemplo do Serviço de Email do Azure. Para obter informações sobre o aplicativo e a série de tutoriais, consulte [o primeiro tutorial da série][o primeiro tutorial da série].

Neste tutorial, você aprenderá:

-   Como criar uma solução que contém um projeto de Serviço de Nuvem com uma função da web ou de trabalho.
-   Como trabalhar com tabelas, blobs e filas do Azure em controlares e visualizações MVC 5.
-   Como tratar conflitos de simultaneidade ao trabalhar com tabelas do Azure.

## Segmentos deste tutorial

-   [Criar a solução do Visual Studio][Criar a solução do Visual Studio]
-   [Atualizar o pacote NuGet da Biblioteca do Cliente de Armazenamento][Atualizar o pacote NuGet da Biblioteca do Cliente de Armazenamento]
-   [Configurar os projetos para usar o emulador de armazenamento][Configurar os projetos para usar o emulador de armazenamento]
-   [Configurar rastreamento e tratar reinicializações][Configurar rastreamento e tratar reinicializações]
-   [Adicionar código para criar tabelas, fila e contêiner de blob no método Application\_Start][Adicionar código para criar tabelas, fila e contêiner de blob no método Application\_Start]
-   [Criar e testar a Lista de Correio][Criar e testar a Lista de Correio]
-   [Criar e testar o controlador e as visualizações do Assinante][Criar e testar o controlador e as visualizações do Assinante]
-   [Criar e testar o controlador e as visualizações da Mensagem][Criar e testar o controlador e as visualizações da Mensagem]
-   [Criar e testar o controlador e as visualizações de Cancelar Assinatura][Criar e testar o controlador e as visualizações de Cancelar Assinatura]
-   [Próximas etapas][Próximas etapas]

## <a name="cloudproject"></a>Criar a solução do Visual Studio

Você começa criando uma solução do Visual Studio com um projeto para o front-end da Web e um projeto para uma das funções de trabalho de back-end do Azure. Mais tarde, você adicionará a segunda função de trabalho.

### Criar um projeto de serviço de nuvem com uma função da web e uma função de trabalho

1.  Inicie o Visual Studio.

2.  No menu **Arquivo**, selecione **Novo Projeto**.

    ![Menu Novo Projeto][Menu Novo Projeto]

3.  Expanda **C\#** e selecione **Nuvem** em **Modelos Instalados** e selecione **Serviço de Nuvem do Azure**.

4.  Nomeie o aplicativo como **AzureEmailService** e clique em **OK**.

    ![Caixa de diálogo Novo Projeto][Caixa de diálogo Novo Projeto]

5.  Na caixa de diálogo **Novo Serviço de Nuvem do Azure**, selecione **Função Web ASP.NET** e clique na seta que aponta para a direita.

    ![Caixa de diálogo Novo Projeto de Nuvem do Azure][Caixa de diálogo Novo Projeto de Nuvem do Azure]

6.  Na coluna à direita, focalize o ponteiro sobre **WebRole1** e clique no ícone de lápis para alterar o nome da função da web.

7.  Digite MvcWebRole como o novo nome e pressione Enter.

    ![Caixa de diálogo Novo Projeto de Nuvem do Azure - renomeando a função da web][Caixa de diálogo Novo Projeto de Nuvem do Azure - renomeando a função da web]

8.  Siga o mesmo procedimento para adicionar uma **Função de Trabalho**, nomeie-a como WorkerRoleA e, em seguida, clique em **OK**.

    ![Caixa de diálogo Novo Projeto de Nuvem do Azure - incluindo uma função de trabalho][Caixa de diálogo Novo Projeto de Nuvem do Azure - incluindo uma função de trabalho]

9.  Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo **MVC**, marque a caixa de seleção **API Web** e clique em **Alterar Autenticação**.

    ![Caixa de diálogo Novo Projeto][1]

10. Na caixa de diálogo **Alterar Autenticação**, clique em **Sem Autenticação** e clique em **OK**.

    ![Sem autenticação][Sem autenticação]

11. Na caixa de diálogo **Novo Projeto ASP .NET**, clique em **OK**.

### Configure o cabeçalho, o menu e o rodapé da página

Nesta seção você atualiza os cabeçalhos, rodapés e itens de menu que são exibidos em cada página da interface da web do administrador. O aplicativo terá três conjuntos de páginas da web do administrador: uma para Listas de Correio, uma para Assinantes de listas de correio e uma para Mensagens.

1.  Se você ainda não tiver feito download da [solução completa][solução completa], faça-o antes de continuar com a próxima etapa.

    No lembrete do tutorial, quando precisar incluir código, você copiará arquivos do projeto transferido por download no novo projeto, em vez de copiar e colar trechos. O tutorial mostrará e explicará as partes principais do código que você está copiando.

    Para incluir um arquivo do projeto transferido por download, clique com o botão direito no projeto em que deseja incluir o arquivo ou a pasta em que deseja incluí-lo e escolha **Incluir - Item Existente** no menu de contexto. Em seguida, navegue para onde baixou o projeto concluído, selecione os arquivos que deseja e clique em **Incluir**. Se aparecer uma caixa de diálogo **Arquivo de Destino Existente**, clique em **Sim**.

2.  No projeto MvcWebRole, inclua o arquivo *Views\\Shared\_Layout.cshtml* do projeto transferido por download (clique com o botão direito na pasta *Shared* em *Views* para incluir o arquivo).

    Isto inclui o cabeçalho, rodapé e entradas de menu para as páginas Lista de Correio, Mensagens e Assinante:

        <ul class="nav navbar-nav">
            <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
            <li>@Html.ActionLink("Messages", "Index", "Message")</li>
            <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
        </ul>

### Executar o aplicativo localmente

1.  Pressione CTRL+F5 para executar o aplicativo.

    Se você estiver acostumado a iniciar projetos da web que não sejam projetos de serviço de nuvem do Azure, você perceberá que demora mais que o normal antes de ver a home page no navegador.

    ![home page][home page]

    O atraso é porque o Visual Studio inicia o emulador de computação do Azure e o emulador de armazenamento do Azure. Você pode ver o ícone do emulador de computação na bandeja do sistema do Windows:

    ![Emulador de computação na bandeja do sistema][Emulador de computação na bandeja do sistema]

2.  Feche o navegador.

## <a name="updatescl"></a>Atualizar o pacote NuGet da Biblioteca do Cliente de Armazenamento

A estrutura da API que você usa para trabalhar com tabelas, filas e blobs do Armazenamento do Azure é a Storage Client Library (SCL). Essa API é incluída em um pacote NuGet no modelo do projeto do Serviço de Nuvem. Entretanto, atualizações no SCL geralmente são liberadas após os modelos do projeto serem criados, portanto, é sempre uma boa ideia verificar se uma atualização está disponível para seu pacote NuGet SCL.

1.  No menu **Ferramentas** do Visual Studio, focalize o **Gerenciador de Pacotes de Biblioteca** e, em seguida, clique em **Gerenciar Pacotes NuGet para Solução**.

    ![Gerenciar Pacotes NuGet para Solução no menu][Gerenciar Pacotes NuGet para Solução no menu]

2.  No painel esquerdo da caixa de diálogo **Gerenciar Pacotes NuGet**, selecione **Atualizações**, em seguida, role para baixo o pacote **Armazenamento do Azure** e clique em **Atualizar**.

    ![Pacote de Armazenamento do Azure na caixa de diálogo Gerenciar Pacotes NuGet][Pacote de Armazenamento do Azure na caixa de diálogo Gerenciar Pacotes NuGet]

3.  Na caixa de diálogo **Selecionar Projetos**, verifique se os dois projetos estão selecionados e, em seguida, clique em **OK**.

    ![Selecionando os dois projetos na caixa de diálogo Selecionar Projetos][Selecionando os dois projetos na caixa de diálogo Selecionar Projetos]

4.  Aceite os termos de licença para concluir a instalação do pacote e, em seguida, feche a caixa de diálogo **Gerenciar Pacotes NuGet**.

## <a name="configurestorage"></a>Configurar os projetos para usar o emulador de armazenamento

O código da função da web e da função de trabalho que você incluirá posteriormente usará uma cadeia de conexão chamada StorageConnectionString para se conectar ao Armazenamento do Azure. Nesta seção, você incluirá a configuração nas propriedades da função e a configurará para usar o emulador de armazenamento. O segundo tutorial na série mostra como configurar a cadeia de conexão para usar uma conta de armazenamento do Azure.

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse em **MvcWebRole** sob **Funções** no projeto em nuvem **AzureEmailService** e, em seguida, escolha **Propriedades**.

    ![Propriedades da função web][Propriedades da função web]

2.  Verifique se a opção **Todas as Configurações** está selecionada na lista suspensa **Configuração do Serviço**.

3.  Selecione a guia **Configurações** e clique no botão **Adicionar Configuração**.

4.  Digite "StorageConnectionString" na coluna **Nome**.

5.  Selecione **Cadeia de Conexão** na lista suspensa **Tipo**.

6.  Clique no botão de reticências (**...**) na extremidade direita da linha para abrir a caixa de diálogo **Cadeia de Conexão da Conta de Armazenamento**.

    ![Clicar com o botão direito do mouse em Propriedades][Clicar com o botão direito do mouse em Propriedades]

7.  No diálogo **Criar Cadeia de Conexão de Armazenamento**, clique no botão de opção **Emulador de Armazenamento do Azure** e clique em **OK**.

    A cadeia de conexão `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` é configurada para o emulador de armazenamento por padrão, portanto, você não tem que alterar isto.

8.  Siga o mesmo procedimento que usou para MvcWebRole para incluir a cadeia de conexão de armazenamento para a função WorkerRoleA.

Quando você adicionou uma nova configuração com o botão **Adicionar Configurações**, a nova configuração foi adicionada ao XML no arquivo *ServiceDefinition.csdf* e em cada um dos dois arquivos de configuração *.cscfg*. O XML a seguir é adicionado pelo Visual Studio ao arquivo *ServiceDefinition.csdf*.

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

O XML a seguir é adicionado a cada arquivo de configuração *.cscfg*.

       <Setting name="StorageConnectionString"
       value="UseDevelopmentStorage=true" />

Você pode adicionar manualmente as configurações ao arquivo *ServiceDefinition.csdf* e aos dois arquivos de configuração *.cscfg*, mas o uso do editor de propriedades tem as seguintes vantagens para cadeias de conexão:

-   Você só inclui a nova configuração em um lugar e a configuração XML correta é incluída em todos os três arquivos.
-   O XML correto é gerado para os três arquivos de configurações. O arquivo *ServiceDefinition.csdf* define as configurações que devem estar em cada arquivo de configuração *.cscfg*. Se o arquivo *ServiceDefinition.csdf* e as duas configurações de arquivos de configuração *.cscfg* estiverem inconsistentes, você poderá obter a mensagem de erro a seguir do Visual Studio: *O modelo de serviço atual está fora de sincronização. Assegure-se de que os arquivos de configuração e definição do serviço sejam válidos.*

    ![Erro dos arquivos de configuração e de definição do serviço inválidos][Erro dos arquivos de configuração e de definição do serviço inválidos]

Se você receber esse erro, o editor de propriedades não irá funcionar até que você resolva o problema de inconsistência manualmente editando os arquivos.

## <a name="tracing"></a>Configurar rastreamento e tratar reinicializações

1.  No projeto MvcWebRole, inclua o arquivo *WebRole.cs* do projeto transferido por download.

Isso inclui um método que configura a criação de log e chama-o a partir do método `OnStart` executado quando a função web inicia. O código no novo método `ConfigureDiagnostics` é explicado no [segundo tutorial][segundo tutorial].

Isso também inclui o código que é executado quando a função da web é notificada de que está prestes a ser encerrada. Os aplicativos do Serviço de Nuvem do Azure são reiniciados aproximadamente duas vezes por mês para atualizações do sistema operacional. (Para obter mais informações sobre as atualizações do SO, consulte [A instância da função é reinicializada devido a atualizações do SO][A instância da função é reinicializada devido a atualizações do SO].) Quando um aplicativo web estiver para ser desligado, é gerado um evento `OnStop`. O clichê da função web criado pelo Visual Studio não substitui o método `OnStop`, portanto, o aplicativo terá apenas alguns segundos para concluir o processamento das solicitações HTTP antes de ser encerrado. Você pode adicionar código para substituir o método `OnStop` para garantir que os desligamentos sejam manipulados normalmente.

O arquivo que acabou de incluir contém a substituição de método `OnStop` a seguir.

        public override void OnStop()
        {
            Trace.TraceInformation("OnStop called from WebRole");
            var rcCounter = new PerformanceCounter("ASP.NET", "Requests Current", "");
            while (rcCounter.NextValue() > 0)
            {
                Trace.TraceInformation("ASP.NET Requests Current = " + rcCounter.NextValue().ToString());
                System.Threading.Thread.Sleep(1000);
            }
        }

O método `OnStop` tem até 5 minutos para sair antes de o aplicativo ser encerrado. Você pode adicionar uma chamada de suspensão por 5 minutos para o método `OnStop` para dar ao aplicativo o máximo de tempo para processar as solicitações atuais, mas se o aplicativo estiver dimensionado corretamente, ele deve ser capaz de processar as solicitações restantes em muito menos de 5 minutos. É aconselhável interromper o mais rápido possível, para que o aplicativo possa reiniciar o mais rápido possível e continuar o processamento de solicitações.

Depois que uma função é desativada pelo Azure, o balanceador de carga interrompe o envio de solicitações à instância da função e, depois disso, o método `OnStop` é chamado. Se você não tiver outra instância de sua função, nenhuma solicitação será processada até que sua função conclua o desligamento e reinicie (o que normalmente leva vários minutos). Esse é um motivo pelo qual o contrato de nível de serviço do Azure requer que você tenha pelo menos duas instâncias de cada função para se beneficiar da garantia de tempo de atividade.

No código mostrado para o método `OnStop`, um contador de desempenho do ASP.NET é criado para `Requests Current`. O valor do contador `Requests Current` contém o número atual de solicitações, incluindo aquelas que são enfileiradas, atualmente em execução ou aguardando serem gravadas no cliente. O valor `Requests Current` é verificado a cada segundo e uma vez que caia para zero, o método `OnStop` é retornado. Depois de `OnStop` ser retornado, a função é encerrada.

Os dados de rastreamento não são salvos quando chamados do método `OnStop` sem realizar uma [Transferência sob demanda][Transferência sob demanda]. Você pode exibir as informações de rastreamento do método `OnStop` em tempo real com o utilitário [dbgview][dbgview] em uma conexão de área de trabalho remota.

## <a name="createifnotexists"></a>Adicionar código para criar tabelas, fila e contêiner de blob no método Application\_Start

O aplicativo web usará as tabelas `MailingList` e `Message`, a fila `azuremailsubscribequeue` e o contêiner de blob `azuremailblobcontainer`. Você pode criá-los manualmente usando uma ferramenta como o Gerenciador de Armazenamento do Azure, mas você precisa fazer isso manualmente toda vez que você começar a usar o aplicativo com uma nova conta de armazenamento. Nesta seção você adicionará um código que é executado quando o aplicativo é iniciado, verificará se as tabelas, filas e contêineres de blob necessários existem e, caso contrário, irá criá-los.

Você pode adicionar esse código de inicialização de uma única vez ao método `OnStart` no arquivo *WebRole.cs* ou ao arquivo *Global.asax*. Para este tutorial, você inicializará o Armazenamento do Azure no arquivo *Global.asax*.

1.  No **Gerenciador de Soluções**, clique com o botão direito no projeto MvcWebRole e inclua o arquivo *Global.asax.cs* a partir do projeto transferido por download.

Você incluiu um novo método que é chamado a partir do método `Application_Start`:

        private static void CreateTablesQueuesBlobContainers()
        {
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            var tableClient = storageAccount.CreateCloudTableClient();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();

            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
            blobContainer.CreateIfNotExists();

            var queueClient = storageAccount.CreateCloudQueueClient();
            var subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");
            subscribeQueue.CreateIfNotExists();
        }

Nas seções a seguir, você cria os componentes do aplicativo da web e pode testá-los com o armazenamento de desenvolvimento ou com sua conta de armazenamento, sem que seja necessário primeiro criar tabelas, filas ou contêiner de blob manualmente.

## <a name="mailinglist"></a>Criar e testar o controlador e visualizações da Lista de Correio

A interface do usuário da web **Lista de Endereçamento** é usada por administradores para criar, editar e exibir listas de endereçamento, como "Contoso University History Department announcements" e "Fabrikam Engineering job postings".

### Inclua a classe da entidade de MailingList na pasta Modelos

A classe de entidade `MailingList` é usada para as linhas da tabela `MailingList` que contêm informações sobre a lista, como a descrição e o endereço de email "De" dos emails enviados para a lista.

1.  Na pasta `Models` no projeto MVC, inclua o arquivo *MailingList.cs* a partir do projeto baixado.

    Você usará a classe da entidade `MailingList` para ler e gravar linhas da lista de endereçamento na tabela mailinglist.

        public class MailingList : TableEntity
        {
            public MailingList()
            {
                this.RowKey = "mailinglist";
            }

            [Required]
            [RegularExpression(@"[\w]+",
             ErrorMessage = @"Only alphanumeric characters and underscore (_) are allowed.")]
            [Display(Name = "List Name")]
            public string ListName
            {
                get
                {
                    return this.PartitionKey;
                }
                set
                {
                    this.PartitionKey = value;
                }
            }

            [Required]
            [Display(Name = "'From' Email Address")]
            public string FromEmailAddress { get; set; }

            public string Description { get; set; }
        }

    A API do Armazenamento do Azure requer que as classes da entidade para as operações de tabela derivem de [TableEntity][TableEntity]. `TableEntity` define os campos `PartitionKey`, `RowKey`, `TimeStamp` e `ETag`. As propriedades `TimeStamp` e `ETag` são usadas pelo sistema. Você verá como a propriedade `ETag` é usada para tratamento de simultaneidade mais adiante no tutorial.

    (Também existe uma classe [DynamicTableEntity][DynamicTableEntity] para uso quando você deseja trabalhar com linhas de tabela como Coleções de dicionários de pares de valor de chaves em vez de usar classes de modelo predefinidos. Para obter mais informações, consulte [Aprofundando-se nas tabelas da Storage Client Library 2.0 do Azure][Aprofundando-se nas tabelas da Storage Client Library 2.0 do Azure].)

    A chave de partição da tabela `mailinglist` é o nome da lista. Nessa classe de entidade, o valor da chave de partição pode ser acessado usando a propriedade `PartitionKey` (definida na classe `TableEntity`) ou a propriedade `ListName` (definida na classe `MailingList`). A propriedade `ListName` usa `PartitionKey` como sua variável de backup. A definição da propriedade `ListName` permite que você use um nome de variável mais descritivo no código e facilita a programação da interface do usuário da Web, uma vez que a formatação e a validação dos atributos de DataAnnotations podem ser adicionados à propriedade `ListName`, mas não podem ser adicionados diretamente à propriedade `PartitionKey`.

    O atributo `RegularExpression` na propriedade `ListName` faz com que o MVC valide a entrada do usuário para garantir que o valor do nome da lista inserido contenha somente caracteres alfanuméricos ou sublinhados. Essa restrição foi implementada para simplificar nomes de lista para que possam ser facilmente usados em cadeias de consulta em URLs.

    > [WACOM.NOTE] Se você queria que o formato do nome da lista fosse menos restritivo, você poderia permitir outros caracteres e nomes de lista de codificação de URL quando usados em cadeias de consultas. No entanto, certos caracteres não são permitidos em chaves de partição ou chaves de linha da Tabela do Azure, e você precisa excluir pelo menos esses caracteres. Para obter informações sobre os caracteres que não são permitidos ou provocam problemas nos campos de chave de partição ou de chave de linha, consulte [Compreendendo o modelo de dados do serviço Tabela][Compreendendo o modelo de dados do serviço Tabela] e [Caractere % em PartitionKey ou RowKey][Caractere % em PartitionKey ou RowKey].

    A classe `MailingList` define um construtor padrão que define `RowKey` como a cadeia de caracteres embutida em código "mailinglist", porque todas as linhas da lista de endereçamento nessa tabela têm esse valor como sua chave de linha. (Para obter uma explicação sobre a estrutura da tabela, consulte o [primeiro tutorial da série][o primeiro tutorial da série].) Qualquer valor constante poderia ter sido escolhido para essa finalidade, desde que nunca fosse igual a um endereço de email, que é a chave de linha das linhas de assinantes nessa tabela.

    O nome da lista e o endereço de email "de" devem sempre ser inseridos quando é criada uma nova entidade `MailingList`, para que tenham atributos `Required`.

    Os atributos `Display` especificam a legenda padrão a ser usada para um campo na interface do usuário do MVC.

### Inclua o controlador MailingList MVC

1.  Na pasta *Controladores* no projeto MVC, inclua o arquivo *MailingListController.cs* a partir do projeto transferido por download.

    O construtor padrão do controlador cria um objeto `CloudTable` para usar para trabalhar com a tabela `mailinglist`.

        public class MailingListController : Controller
        {
            private CloudTable mailingListTable;

            public MailingListController()
            {
                var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

                var tableClient = storageAccount.CreateCloudTableClient();
                mailingListTable = tableClient.GetTableReference("mailinglist");

    O código obtém as credenciais da sua conta de Armazenamento do Azure do arquivo de configurações do projeto do Serviço de Nuvem para estabelecer uma conexão com a conta de armazenamento. (Você configurará esses parâmetros posteriormente neste tutorial, antes de testar o controlador.)

    Em seguida, há um método `FindRowAsync` que é chamado sempre que o controlador precisa procurar uma entrada específica na lista de endereçamento da tabela `MailingList`, por exemplo, para editar uma entrada da lista de endereçamento. O código recupera uma única entidade `MailingList` usando os valores das chaves de partição e das chaves de linha passados para ele. As linhas que esse controlador edita são as que têm "MailingList" como a chave de linha, portanto, "MailingList" pode ter sido embutido no código para a chave de linha, mas a especificação da chave de partição e da chave de linha é um padrão usado para os métodos `FindRow` em todos os controladores.

    > [WACOM.NOTE] O aplicativo usa o código assíncrono do ASP.NET 4.5 para operações de E/S na função da web a fim de usar recursos do servidor de forma eficiente. Para obter informações sobre o código assíncrono no aplicativo Web, consulte [Usar suporte assíncrono do .NET 4.5 para evitar o bloqueio de chamadas][Usar suporte assíncrono do .NET 4.5 para evitar o bloqueio de chamadas].

        private async Task<MailingList> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                throw new Exception("No mailing list found for: " + partitionKey);
            }
            return mailingList;
        }

    O código neste método `FindRow` retorna uma linha da lista de endereçamento. O código no método `FindRow` correspondente no controlador `Subscriber` retorna uma linha de assinante da mesma tabela `mailinglist`. O código nos dois métodos é idêntico, exceto para o tipo de modelo usado com o método [TableOperation.Retrieve][TableOperation.Retrieve].

        private async Task<Subscriber> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var subscriber = retrievedResult.Result as Subscriber;
            if (subscriber == null)
            {
                throw new Exception("No subscriber found for: " + partitionKey + ", " + rowKey);
            }
            return subscriber;
        }

    O objeto `TableOperation` retornado pelo método `TableOperation.Retrieve` especifica o esquema (as propriedades) da linha ou linhas que você espera que a consulta retorne. Uma única tabela pode ter diferentes esquemas em linhas diferentes. Normalmente, você especifica o mesmo tipo de modelo ao ler uma linha que foi usada para criar a linha.

    A página **Índice** exibe todas as linhas da lista de endereçamento, portanto, a consulta no método `Index` retorna todas as entidades de `MailingList` que têm "mailinglist" como a chave de linha (as outras linhas da tabela têm endereço de email como a chave de linha e contêm informações sobre o assinante).

        var query = new TableQuery<MailingList>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.Equal, "mailinglist"));
        TableContinuationToken token = null;
        OperationContext ctx = new OperationContext();
        TableQuerySegment<MailingList> currentSegment = null;
        while (currentSegment == null || currentSegment.ContinuationToken != null)
        {
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync
                (query, token, webUIRetryPolicy, ctx);
            lists.AddRange(currentSegment.Results);
            token = currentSegment.ContinuationToken;
        }

    O método `ExecuteQuerySegmentedAsync` quebra vários conjuntos grandes de resultados em segmentos. Ele retorna até 1.000 linhas. Quando você executa uma consulta que recuperaria mais de 1.000 linhas, você obtém 1.000 linhas e um token de continuação. É possível usar o token de continuação para executar outra consulta que comece onde a anterior parou. O código mostrado é simpliicado para um aplicativo função Web: ele agrega todos os segmentos em uma lista. Para um aplicativo de produção, você implementaria um código de paginação. Para obter mais informações sobre grandes conjuntos de resultados e tokens de continuação, consulte [Como aproveitar ao máximo as Tabelas do Azure][Como aproveitar ao máximo as Tabelas do Azure] e [Tabelas do Azure: Esperar Tokens de Continuação, Sério][Tabelas do Azure: Esperar Tokens de Continuação, Sério].

    Ao criar o objeto `OperationContext`, você pode definir o valor da propriedade `ClientID` a fim de fornecer um identificador exclusivo que será incluído em logs gravados pelo Armazenamento do Azure. É possível usar esse identificador para controlar logs de operação de armazenamento para o código que causou a atividade do serviço de armazenamento. Para obter informações sobre a criação de log de armazenamento do Azure, consulte [Criação de Log do Armazenamento do Azure: Usando Logs para Controlar Solicitações de Armazenamento][Criação de Log do Armazenamento do Azure: Usando Logs para Controlar Solicitações de Armazenamento].

    Com o SCL 2.1 e a API posterior é possível usar também o LINQ para suas consultas de tabela. Para obter um exemplo de código que mostre como usar o LINQ, consulte [PhluffyFotos][PhluffyFotos].

    Se você não especificar uma política de nova tentativa, a API tentará de novo automaticamente três vezes com tempos limite exponencialmente maiores. Para uma interface da web com um usuário que está aguardando uma página aparecer, isso pode resultar em tempos de espera inaceitavelmente longos. Portanto, esse código especifica tentativas lineares (de forma que o tempo limite não aumente a cada vez) e um tempo limite que seja razoável para o usuário a esperar. A política de novas tentativas é especificada no objeto `webUIRetryPolicy` passado ao método `ExecuteQuerySegmentedAsync`. O objeto `webUIRetryPolicy` é definido no construtor do controlador:

        private TableRequestOptions webUIRetryPolicy;

        public MailingListController()
        {
            // Other constructor code not shown.

            webUIRetryPolicy = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
        }

    O método `Index` inclui uma caixa try-catch que está designada a tratar condições de tempo limite.

        try
        {
            // Code not shown for retrieving MailingList rows.
        }
        catch (StorageException se)
        {
            ViewBag.errorMessage = "Timeout error, try again. ";
            Trace.TraceError(se.Message);
            return View("Error");
        }

    Quando o usuário clica no botão **Criar** na página **Criar**, o associador de modelo MVC cria uma entidade `MailingList` da entrada inserida na exibição, e o método `HttpPost Create` adiciona a entidade à tabela.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(MailingList mailingList)
        {
            if (ModelState.IsValid)
            {
                var insertOperation = TableOperation.Insert(mailingList);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }

            return View(mailingList);
        }

    Para a página **Editar**, o método `HttpPost Edit` atualiza a linha.

        var replaceOperation = TableOperation.Replace(mailingList);
        await mailingListTable.ExecuteAsync(replaceOperation);

    No método `HttpPost Edit`, um bloco de captura trata erros de simultaneidade.

        catch (StorageException ex)
        {
            if (ex.RequestInformation.HttpStatusCode == 412)
            {
                // Concurrency error
                var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
                var retrievedResult = mailingListTable.Execute(retrieveOperation);
                var currentMailingList = retrievedResult.Result as MailingList;
                if (currentMailingList == null)
                {
                    ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                        + "was deleted by another user after you got the original value. The "
                        + "edit operation was canceled. Click the Back to List hyperlink.");
                }
                if (currentMailingList.FromEmailAddress != editedMailingList.FromEmailAddress)
                {
                    ModelState.AddModelError("FromEmailAddress", "Current value: " + currentMailingList.FromEmailAddress);
                }
                if (currentMailingList.Description != editedMailingList.Description)
                {
                    ModelState.AddModelError("Description", "Current value: " + currentMailingList.Description);
                }
                ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                    + "was modified by another user after you got the original value. The "
                    + "edit operation was canceled and the current values in the database "
                    + "have been displayed. If you still want to edit this record, click "
                    + "the Save button again. Otherwise click the Back to List hyperlink.");
                 ModelState.SetModelValue("ETag", new ValueProviderResult(currentMailingList.ETag, currentMailingList.ETag, null));
            }
            else
            {
                throw; 
            }
        }

    Uma exceção de simultaneidade será gerada se um usuário selecionar uma lista de endereçamento para edição e, enquanto a página **Editar** é exibida no navegador, outro usuário edita a mesma lista de endereçamento. Quando isso ocorre, o código exibe uma mensagem de aviso e indica quais campos foram alterados pelo outro usuário. A API da TSL usa `ETag` para verificar se há conflitos de simultaneidade. Sempre que uma linha da tabela é atualizada, o valor de `ETag` é alterado. Ao obter uma linha para edição, você salva o valor de `ETag` e, quando executa uma operação de atualização ou de exclusão, passa o valor de `ETag` que salvou. (A exibição `Edit` tem um campo oculto para o valor de ETag.) Se a operação de atualização detectar que o valor de `ETag` no registro que você está atualizando é diferente do valor de `ETag` que é passado para a operação de atualização, ele gerará uma exceção de simultaneidade. Se você não se importar com conflitos de simultaneidade, é possível configurar o campo ETag como um asterisco ("\*") na entidade que você transmite na operação de atualização e os conflitos serão ignorados.

    Observação: O erro HTTP 412 não é exclusivo a erros de simultaneidade. Ele pode ser gerado para outros erros pela API SCL.

    Para a página **Excluir**, o método `HttpPost Delete` exclui a linha `MailingList` juntamente com quaisquer linhas `Subscriber` associadas à tabela `MailingList`.

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> DeleteConfirmed(string partitionKey)
        {
            // Delete all rows for this mailing list, that is, 
            // Subscriber rows as well as MailingList rows.
            // Therefore, no need to specify row key.

            // Get all rows for this mailing list. For a production app where this 
            // could return too many split the work up into segments.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            List<MailingList> listRows = new List<MailingList>();
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                listRows.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }

            // Delete the rows in batches of 100.
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    await mailingListTable.ExecuteBatchAsync(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
            return RedirectToAction("Index");
        }

    Quando um grande número de assinantes precisar ser excluído, o código exclui os registros em lotes. O custo da transação de exclusão de uma linha é o mesmo que o da exclusão de 100 linhas em um lote. O número máximo de operações que podem ser executadas em um lote é 100.

    Embora o loop processe ambas as linhas `MailingList` e `Subscriber`, ele lê todas elas na classe de entidade `MailingList` porque os únicos campos necessários para a operação `Delete` são s campos `PartitionKey`, `RowKey` e `ETag`.

### Incluir as visualizações MailingList MVC

1.  Na pasta *Visualizações* no projeto do MVC, crie uma nova pasta e chame-a de *MailingList*.

2.  Na nova pasta *Views\\MailingList*, inclua todos os quatro arquivos *.cshtml* do projeto transferido por download.

No arquivo *Edit.cshtml*, observe o campo oculto incluído para preservar o valor de `ETag` usado para tratar conflitos de simultaneidade.

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
        
            @Html.HiddenFor(model => model.ETag)

Observe também que o campo `ListName` possui um auxiliar `DisplayFor` em vez de um auxiliar `EditorFor`.

        <div class="form-group">
            @Html.LabelFor(model => model.ListName, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.DisplayFor(model => model.ListName, new { htmlAttributes = new { @class = "form-control" } })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.FromEmailAddress, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.FromEmailAddress, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.FromEmailAddress, "", new { @class = "text-danger" })
            </div>
        </div>

Não habilitamos a página **Editar** para alterar o nome da lista, porque isso exigiria um código complexo no controlador: o método `HttpPost Edit` teria de ter excluído a linha da lista de endereçamento existente e todas as linhas do assinante associadas e reinserí-las todas com o novo valor de chave. Em um aplicativo de produção, você pode decidir se a complexidade adicional vale a pena. Como você verá posteriormente, o controlador `Subscriber` permite alterações do nome da lista, uma vez que apenas uma linha é afetada a cada vez.

No arquivo *Index.cshtml*, os hyperlinks **Editar** e **Excluir** especificam parâmetros de cadeia de caracteres de consulta de chave de partição e de chave de linha a fim de identificar uma linha específica. Para entidades `MailingList`, apenas a chave de partição é realmente necessária, pois a chave de linha é sempre “MailingList”, mas ambas são mantidas para que o código de visualização do MVC seja consistente entre todos os controladores e visualizações.

        <td>
            @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
            @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
        </td>

### Tornar MailingList o controlador padrão

1.  Abra *Route.config.cs* na pasta *App\_Start*.

2.  Na linha que especifica os padrões, altere o controlador padrão de "Home" para "MailingList".

         routes.MapRoute(
             name: "Default",
             url: "{controller}/{action}/{id}",
             defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }

### Testar o aplicativo

1.  Execute o projeto pressionando CTRL+F5.

    ![Página de índice da lista de endereçamento vazia][Página de índice da lista de endereçamento vazia]

2.  Use a função **Criar** para adicionar algumas listas de endereçamento e tente as funções **Edit** e **Delete** para verificar se funcionam.

    ![Página de índice da lista de endereçamento com linhas][Página de índice da lista de endereçamento com linhas]

## <a name="subscriber"></a><span class="short-header">Assinante</span>Criar e testar o controlador e as visualizações do Assinante

A interface do usuário da Web **Assinante** é usada pelos administradores para adicionar novos assinantes a uma lista de endereçamento e para editar, exibir e excluir assinantes existentes.

### Inclua a classe da entidade do Assinante na pasta Modelos

A classe de entidade `Subscriber` é usada para as linhas da tabela `MailingList` que contêm informações sobre os assinantes de uma lista. Essas linhas contêm informações, como o endereço de email da pessoa e se o endereço está verificado.

1.  Na pasta *Modelos* no projeto MVC, inclua o arquivo *Subscriber.cs* a partir do projeto transferido por download.

Como a classe de entidade `MailingList`, a classe de entidade `Subscriber` é usada para ler e gravar linhas na tabela `mailinglist`.

            public class Subscriber : TableEntity
            {
                [Required]
                public string ListName
                {
                    get
                    {
                        return this.PartitionKey;
                    }
                    set
                    {
                        this.PartitionKey = value;
                    }
                }
        
                [Required]
                [Display(Name = "Email Address")]
                public string EmailAddress
                {
                    get
                    {
                        return this.RowKey;
                    }
                    set
                    {
                        this.RowKey = value;
                    }
                }
        
                public string SubscriberGUID { get; set; }
        
                public bool? Verified { get; set; }
            }
        

Linhas de `Subscriber` usam o endereço de email ao invés da constante "mailinglist" para a chave da linha. (Para obter uma explicação sobre a estrutura da tabela, consulte o [primeiro tutorial da série][o primeiro tutorial da série].) Portanto, é definida uma propriedade `EmailAddress` que usa a propriedade `RowKey` como seu campo de suporte, da mesma forma como `ListName` usa `PartitionKey` como seu campo de suporte. Conforme explicado anteriormente, isso permite que você coloque atributos de DataAnnotations de formatação e de validação nas propriedades.

O valor `SubscriberGUID` é gerado quando uma entidade `Subscriber` é criada. Ele é usado nos links para assinar e cancelar assinatura para ajudar a garantir que somente pessoas autorizadas possam assinar ou cancelar a assinatura de endereços de email.

Quando uma linha é criada inicialmente para um novo assinante, o valor `Verified` é `false`. O valor `Verified` é alterado para `true` apenas depois que o novo assinante clica no hiperlink **Confirmar** no email de boas-vindas. Se uma mensagem for enviada a uma lista enquanto um assinante tiver `Verified` = `false`, nenhum email será enviado a esse assinante.

A propriedade `Verified` na entidade `Subscriber` é definida como anulável. Quando você especificar que uma consulta deve retornar entidades `Subscriber`, é possível que algumas das linhas recuperadas não tenham uma propriedade `Verified`. Portanto, a entidade `Subscriber` define sua propriedade `Verified` como anulável para que possa refletir mais precisamente o conteúdo real de uma linha, se linhas de tabela que não possuam uma propriedade *Verified* forem retornadas por uma consulta. Você pode estar acostumado a trabalhar com as tabelas do SQL Server, no qual cada linha de uma tabela tem o mesmo esquema. Em uma tabela de Armazenamento do Azure, cada linha é apenas uma coleção de propriedades, e cada linha pode ter um conjunto diferente de propriedades. Por exemplo, no aplicativo de exemplo do Serviço de Email do Azure, as linhas que têm "MailingList" como a chave da linha não têm uma propriedade `Verified`. Se uma consulta retornar uma linha da tabela que não possua uma propriedade `Verified`, quando a classe de entidade `Subscriber` for instanciada, a propriedade `Verified` no objeto de entidade será nula. Se a propriedade era não anulável, você obterá o mesmo valor de `false` para linhas que têm `Verified` = `false` e para linhas que não têm nenhuma propriedade `Verified`. Portanto, uma prática recomendada para trabalhar com Tabelas do Azure é tornar anulável cada propriedade de uma classe de entidade para ler com precisão as linhas que foram criadas usando classes de entidade diferentes ou versões diferentes da classe da entidade atual.

### Incluir o controlador MVC do Assinante

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Controllers* no projeto MVC e escolha **Adicionar Item Existente**.

2.  Navegue até a pasta em que você baixou o aplicativo função Web, selecione o arquivo *SubscriberController.cs* na pasta *Controladores* e clique em **Incluir**. (Verifique se você obtém *Subscriber.cs* e não *Subscribe.cs*; você adicionará *Subscribe.cs* posteriormente.)

A maioria do código neste controlador é semelhante ao que você viu no controlador `MailingList`. Até o nome da tabela é o mesmo, porque as informações do assinante são mantidas na tabela `MailingList`.

Além do método `FindRowAsync`, há também o método `FindRow`, uma vez que existe uma necessidade de chamá-lo a partir de um bloco catch e não é possível chamar um método assíncrono a partir de um bloco catch.

Depois dos métodos `FindRow`, você verá um método `GetListNamesAsync`. Esse método obtém os dados de uma lista suspensa nas páginas **Criar** e **Editar**, nas quais você pode selecionar a lista de endereçamento na qual inscrever um endereço de email.

        private async Task<List<MailingList>> GetListNamesAsync()
        {
            List<MailingList> lists = new List<MailingList>();
            var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                lists.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }
            return lists;
        }

Esta é a mesma consulta que você viu no controlador `MailingList`. Para a lista suspensa, você deseja linhas que tenham informações sobre listas de endereçamento, portanto, você seleciona apenas as que têm RowKey = "mailinglist".

Para o método que recupera dados para a página **Index**, você deseja linhas que contenham informações do assinante, portanto, você seleciona todas as linhas que não tenham RowKey = "MailingList".

        var query = (new TableQuery<Subscriber>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.NotEqual, "mailinglist")));

Observe que a consulta especifica que os dados serão lidos em objetos `Subscriber` (especificando `<Subscriber>`) mas os dados serão lidos na tabela `mailinglist`.

O número de assinantes poderia crescer muito para tratar isso dessa forma em uma única consulta. Conforme anotado anteriormente, em um aplicativo de produção, você implementaria a paginação usando tokens de continuação.

No método `HttpGet Create`, você configura dados para a lista suspensa; e no método `HttpPost`, você deve definir valores padrão antes de salvar a nova entidade.

        public async Task<ActionResult> Create()
        {
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            var model = new Subscriber() { Verified = false };
            return View(model);
        }
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Subscriber subscriber)
        {
            if (ModelState.IsValid)
            {
                subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                if (subscriber.Verified.HasValue == false)
                {
                    subscriber.Verified = false;
                }
        
                var insertOperation = TableOperation.Insert(subscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);
        
            return View(subscriber);
        }

A página `HttpPost Edit` é mais complexa do que o que você viu no controlador `MailingList`, porque a página `Subscriber` permite que você altere o nome ou o endereço de email da lista, que são campos-chave. Se o usuário alterar um desses campos, você precisará excluir o registro existente e adicionar um novo, em vez de atualizar o registro existente. O código a seguir mostra a parte do método de edição que trata dos diferentes procedimentos para alterações de chave versus não chave:

        UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
        if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
        {
            //Keys didn't change -- Update the row
            var replaceOperation = TableOperation.Replace(editedSubscriber);
            await mailingListTable.ExecuteAsync(replaceOperation);
        }
        else
        {
            // Keys changed, delete the old record and insert the new one.
            if (editedSubscriber.PartitionKey != partitionKey)
            {
                // PartitionKey changed, can't do delete/insert in a batch.
                var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                await mailingListTable.ExecuteAsync(deleteOperation);
                var insertOperation = TableOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
            }
            else
            {
                // RowKey changed, do delete/insert in a batch.
                var batchOperation = new TableBatchOperation();
                batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                batchOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
        }

Os parâmetros que o associador de modelo MVC passa para o método `Edit`' incluem os valores de nome e endereço de email originais da lista (nos parâmetros de `partitionKey` e `rowKey`) e os valores inseridos pelo usuário (nos parâmetros `listName` e `emailAddress`):

        public async Task<ActionResult> Edit(string partitionKey, string rowKey, string listName, string emailAddress, Subscriber editedSubscriber)

Os parâmetros passados para o método `UpdateModel` excluem as propriedades `PartitionKey` e `RowKey` da associação de modelo:

        var excludeProperties = new string[] { "PartitionKey", "RowKey" };
            

O motivo disso é que as propriedades `ListName` e `EmailAddress` usam `PartitionKey` e `RowKey` como suas propriedades de suporte, e o usuário pode ter alterado um desses valores. Quando o associador de modelo atualiza o modelo definindo a propriedade `ListName`, a propriedade `PartitionKey` é atualizada automaticamente. Se o associador de modelo atualizar a propriedade `PartitionKey` com o valor original da propriedade depois de atualizar a propriedade `ListName`, ele substituirá o novo valor definido pela propriedade `ListName`. A propriedade `EmailAddress` atualiza automaticamente a propriedade `RowKey` da mesma maneira.

Depois de atualizar o objeto de modelo `editedSubscriber`, o código determina se a chave de partição ou a chave de linha foi alterado. Se uma das chaves de valor for alterada, a linha de assinante existente precisará ser excluída e um nova inserida. Se apenas a chave de linha for alterada, a exclusão e a inserção podem ser feitas em uma transação em lotes atômica.

Observe que o código cria uma nova entidade para passar na operação `Delete`:

            // RowKey changed, do delete/insert in a batch.
            var batchOperation = new TableBatchOperation();
            batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
            batchOperation.Insert(editedSubscriber);
            await mailingListTable.ExecuteBatchAsync(batchOperation);

As entidades que você passa para operações em um lote devem ser entidades distintas. Por exemplo, você não pode criar uma entidade `Subscriber`, passá-la para uma operação de `Delete` e, em seguida, alterar um valor na mesma entidade `Subscriber` e passá-la para uma operação de `Insert`. Se você fizer isso, o estado da entidade após a alteração da propriedade estará em vigor para a operação de Delete e de Insert.

As operações em um lote devem estar todas na mesma partição. Como uma alteração do nome da lista altera a chave da partição, ela não pode ser feita em uma transação.

### Incluir as visualizações do MVC do Assinante

1.  No **Gerenciador de Soluções**, crie uma nova pasta na pasta *Views* no projeto MVC e dê a ela o nome *Subscriber*.

2.  Clique com o botão direito do mouse na nova pasta *Views\\Subscriber* e escolha **Adicionar Item Existente**.

3.  Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione todos os cinco arquivos .cshtml na pasta *Views/Subscriber* e clique em **Adicionar**.

No campo *Edit.cshtml*, um arquivo oculto está incluído para o valor `SubscriberGUID`, uma vez que esse campo não é mostrado e, portanto, não é fornecido automaticamente em um campo de formulário

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.SubscriberGUID)
            @Html.HiddenFor(model => model.ETag)
                        

### Testar o aplicativo

1.  Execute o projeto pressionando CTRL+F5 e, em seguida, clique em **Subscribers**.

    ![Página de índice de assinante vazia][Página de índice de assinante vazia]

2.  Use a função **Criar** para adicionar algumas listas de endereçamento e tente as funções **Edit** e **Delete** para verificar se funcionam.

    ![Página de índice de assinantes com linhas][Página de índice de assinantes com linhas]

## <a name="message"></a>Criar e testar o controlador e as visualizações da Mensagem

A interface do usuário da web **Message** é usada pelos administradores para criar, editar e exibir informações sobre mensagens que estão agendadas para serem enviadas a listas de endereçamento.

### Incluir a classe da entidade Mensagem na pasta Modelos

1.  Na pasta *Modelos* no projeto MVC, inclua o arquivo *Message.cs* a partir do projeto transferido por download.

A classe de entidade `Message` é usada para as linhas da tabela `Message` que contém informações sobre uma mensagem que está agendada para ser enviada a uma lista. Essas linhas incluem informações, como a linha de assunto, a lista para envio de uma mensagem e a data agendada para o envio.

        public class Message : TableEntity
        {
            private DateTime? _scheduledDate;
            private long _messageRef;

            public Message()
            {
                this.MessageRef = DateTime.Now.Ticks;
                this.Status = "Pending";
            }

            [Required]
            [Display(Name = "Scheduled Date")]
            // DataType.Date shows Date only (not time) and allows easy hook-up of jQuery DatePicker
            [DataType(DataType.Date)]
            public DateTime? ScheduledDate 
            {
                get
                {
                    return _scheduledDate;
                }
                set
                {
                    _scheduledDate = value;
                    this.PartitionKey = value.Value.ToString("yyyy-MM-dd");
                }
            }
            
            public long MessageRef 
            {
                get
                {
                    return _messageRef;
                }
                set
                {
                    _messageRef = value;
                    this.RowKey = "message" + value.ToString();
                }
            }

            [Required]
            [Display(Name = "List Name")]
            public string ListName { get; set; }

            [Required]
            [Display(Name = "Subject Line")]
            public string SubjectLine { get; set; }

            // Pending, Queuing, Processing, Complete
            public string Status { get; set; }
        }
        

A classe `Message` define um construtor padrão que define a propriedade `MessageRef` como um valor exclusivo para a mensagem. Como esse valor faz parte da chave de linha, o setter da propriedade `MessageRef` também define automaticamente a propriedade `RowKey`. O setter da propriedade `MessageRef` concatena a literal "message" e o valor de `MessageRef` e coloca isso na propriedade `RowKey`.

O valor de `MessageRef` é criado pela obtenção do valor de `Ticks` em `DateTime.Now`. Isso garante que, por padrão, ao exibir mensagens na interface do usuário da web elas sejam exibidas na ordem em que foram criadas para uma determinada data agendada (`ScheduledDate` é a chave da partição). Você pode usar um GUID para tornar as linhas de mensagem exclusivas, mas nesse caso o pedido de recuperação padrão será aleatório.

O construtor padrão também configura o status padrão de Pendente para novas linhas `message`.

Para obter mais informações sobre a estrutura da tabela `Message`, consulte o [primeiro tutorial da série][o primeiro tutorial da série].

### Incluir o controlador do MVC Mensagem

1.  Na pasta *Controladores* no projeto MVC, inclua o arquivo *MessageController.cs* a partir do projeto transferido por download.

A maioria do código neste controlador é semelhante ao que você viu no controlador `Subscriber`. O que é novo aqui é o código para trabalhar com blobs. Para cada mensagem, o conteúdo HTML e de texto sem formatação do email é carregado na forma de arquivos .htm e .txt e armazenado em blobs.

Os blobs são armazenados em contêineres de blobs. O aplicativo do Serviço de Email do Azure armazena todos os seus blobs em um contêiner de blob único chamado "azuremailblobcontainer", e o código no construtor do controlador obtém uma referência a esse contêiner de blob:

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

Para cada arquivo que um usuário seleciona para carregar, a exibição do MVC fornece um objeto `HttpPostedFile` que contém informações sobre o arquivo. Quando o usuário cria uma nova mensagem, o objeto `HttpPostedFile` é usado para salvar o arquivo em um blob. Quando o usuário edita uma mensagem, o usuário pode optar por carregar um arquivo de substituição ou deixar o blob inalterado.

O controlador inclui um método que chama os métodos `HttpPost Create` e `HttpPost Edit` para salvar um blob:

        private async Task SaveBlobAsync(string blobName, HttpPostedFileBase httpPostedFile)
        {
            // Retrieve reference to a blob.
            var blob = blobContainer.GetBlockBlobReference(blobName);
            // Create the blob or overwrite the existing blob by uploading a local file.
            using (var fileStream = httpPostedFile.InputStream)
            {
                await blob.UploadFromStreamAsync(fileStream);
            }
        }

O método `HttpPost Create` salva os dois blobs e, em seguida, adiciona a linha da tabela `Message`. Os Blobs são nomeados concatenando o valor de `MessageRef` com a extensão do nome do arquivo ".htm" ou ".txt".

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
        {
            if (file == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide an HTML file path");
            }
        
            if (txtFile == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide a Text file path");
            }
        
            if (ModelState.IsValid)
            {
                await SaveBlobAsync(message.MessageRef + ".htm", file);
                await SaveBlobAsync(message.MessageRef + ".txt", txtFile);
        
                var insertOperation = TableOperation.Insert(message);
                await messageTable.ExecuteAsync(insertOperation);
        
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            return View(message);
        }

O método `HttpGet Edit` valida que a mensagem recuperada está no status `Pending` de forma que o usuário não possa alterar uma mensagem depois que a função de trabalho B começou a processá-la. Um código semelhante está presente no método `HttpPost Edit` e nos métodos `Delete` e `DeleteConfirmed`.

        if (message.Status != "Pending")
        {
            throw new Exception("Message can't be edited because it isn't in Pending status.");
        }

No método `HttpPost Edit`, o código salva um novo blob apenas se o usuário escolher fazer upload de um novo arquivo.

        if (httpFile == null)
        {
            // They didn't enter a path or navigate to a file, so don't update the file.
            excludePropLst.Add("HtmlPath");
        }
        else
        {
            // They DID enter a path or navigate to a file, assume it's changed.
            await SaveBlobAsync(editedMsg.MessageRef + ".htm", httpFile);
        }
        
        if (txtFile == null)
        {
            excludePropLst.Add("TextPath");
        }
        else
        {
            await SaveBlobAsync(editedMsg.MessageRef + ".txt", txtFile);
        }

Se a data agendada for alterada, a chave da partição será alterada, e uma linha precisará ser excluída e inserida. Isso não pode ser feito em uma única transação, porque afeta mais de uma partição.

        var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
        await messageTable.ExecuteAsync(deleteOperation);
        var insertOperation = TableOperation.Insert(editedMsg);
        await messageTable.ExecuteAsync(insertOperation);

O método `HttpPost Delete` exclui os blobs quando exclui a linha na tabela:

        [HttpPost, ActionName("Delete")]
        public async Task<ActionResult> DeleteConfirmed(String partitionKey, string rowKey)
        {
            // Get the row again to make sure it's still in Pending status.
            var message = await FindRowAsync(partitionKey, rowKey);
            if (message.Status != "Pending")
            {
                throw new Exception("Message can't be deleted because it isn't in Pending status.");
            }
        
            await DeleteBlobAsync(message.MessageRef + ".htm");
            await DeleteBlobAsync(message.MessageRef + ".txt");
            var deleteOperation = TableOperation.Delete(message);
            messageTable.Execute(deleteOperation);
            return RedirectToAction("Index");
        }
        
        private async Task DeleteBlobAsync(string blobName)
        {
            var blob = blobContainer.GetBlockBlobReference(blobName);
            await blob.DeleteAsync();
        }

### Incluir as visualizações do MVC de Mensagem

1.  No projeto do MVC, crie uma nova pasta na pasta *Views* e denomine-a `Message`.

2.  Na nova pasta *Views\\Message*, inclua todos os cinco arquivos *.cshtml* a partir do projeto transferido por download.

O método `HttpPost Edit` precisa da chave de partição e da chave de linha, portanto, o código no arquivo *Edit.cshtml* fornece essas chaves em campos ocultos.

        @using (Html.BeginForm("Edit", "Message", FormMethod.Post, new { enctype = "multipart/form-data" }))
        {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.ETag)
            <fieldset>
                <legend>Message</legend>
                @Html.HiddenFor(model => model.MessageRef)
                @Html.HiddenFor(model => model.PartitionKey)
                @Html.HiddenFor(model => model.RowKey)
                

Os campos ocultos não eram necessários no controlador de `Subscriber`, porque (a) as propriedades `ListName` e `EmailAddress` no modelo `Subscriber` atualizam as propriedades `PartitionKey` e `RowKey`, e (b) as propriedades `ListName` e `EmailAddress` foram incluídas com auxiliares `EditorFor` na exibição Editar. Quando o associador de modelo MVC do modelo `Subscriber` atualiza a propriedade `ListName`, a propriedade `PartitionKey` é automaticamente atualizada, e quando o associador de modelo MVC atualiza a propriedade `EmailAddress` no modelo `Subscriber`, a propriedade `RowKey` é atualizada automaticamente. No modelo `Message`, os campos que mapeiam para a chave de partição e para a chave de linha não são campos editáveis, portanto, não são definidos dessa forma.

Um campo oculto também é incluído para a propriedade `MessageRef`. Este é o mesmo valor que o da chave de partição, mas está incluído a fim de permitir um esclarecimento maior do código no método `HttpPost Edit`. A inclusão do campo oculto `MessageRef` permite que o código no método `HttpPost Edit` faça referência ao valor de `MessageRef` por esse nome quando constrói nomes de arquivo para os blobs.

A visualização **Índice** da Mensagem no arquivo *Index.cshtml* é diferente das outras visualizações **Índice** no sentido de que os links **Editar** e **Excluir** são mostrados apenas para mensagens que estão no status `Pending`:

        <td>
            @if (item.Status == "Pending")
            {
                @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
                @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
            }
            @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
        </td>
                

Isso ajuda a impedir que o usuário faça alterações em uma mensagem depois que função de trabalho A começou a processá-la.

### Testar o aplicativo

1.  Execute o projeto pressionando CTRL+F5 e, em seguida, clique em **Messages**.

    ![Página de índice de mensagens vazia][Página de índice de mensagens vazia]

2.  Use a função **Criar** para adicionar algumas listas de endereçamento e tente as funções **Edit** e **Delete** para verificar se funcionam.

    ![Página de índice de assinantes com linhas][2]

## <a name="unsubscribe"></a>Criar e testar o controlador e as visualizações de Cancelar Assinatura

Em seguida, você implementará a interface do usuário para o processo de cancelamento de assinatura.

Este tutorial apenas inclui instruções para criar o controlador para o processo de cancelamento de assinatura, não o processo de assinatura. Como o [primeiro tutorial][o primeiro tutorial da série] explicou, a UI e o método de serviço para o processo de assinatura estão incluídos no download, mas foram deixados de fora das instruções do tutorial, porque o aplicativo função Web não implementa a segurança para o método de serviço. Para fins de teste, é possível usar as páginas do administrador **Assinante** para assinar endereços de email para listas.

### Incluir o modelo de visualização de Cancelar Assinatura na pasta Modelos

1.  Na pasta `Models` no projeto MVC, inclua o arquivo `UnsubscribeVM.cs` a partir do projeto baixado.

O modelo de exibição `UnsubscribeVM` é usado para passar dados entre o controlador `Unsubscribe` e sua exibição.

        public class UnsubscribeVM
        {
            public string EmailAddress { get; set; }
            public string ListName { get; set; }
            public string ListDescription { get; set; }
            public string SubscriberGUID { get; set; }
            public bool? Confirmed { get; set; }
        }

Links de cancelamento de assinatura contêm `SubscriberGUID`. Esse valor é usado para obter o endereço de email, o nome da lista e a descrição da lista da tabela `MailingList`. A exibição mostra o endereço de email e a descrição da lista da qual a assinatura deve ser cancelada e exibe um botão **Confirm** no qual o usuário deve clicar para concluir o processo de cancelamento da assinatura.

### Incluir o controlador de Cancelar Assinatura

1.  Na pasta `Controllers` no projeto MVC, inclua o arquivo *UnsubscribeController.cs* a partir do projeto baixado.

Esse controlador tem um método `HttpGet Index` que exibe a página de cancelamento de assinatura inicial e um método `HttpPost Index` que processa o botão **Confirmar** ou **Cancelar**.

O método `HttpGet Index` usa o GUID e o nome da lista na cadeia de consulta para obter a linha da tabela `MailingList` do assinante. Em seguida, ele coloca todas as informações necessárias para a exibição no modelo de exibição e exibe a página **Unsubscribe**. Define a propriedade `Confirmed` como null para informar à exibição para mostrar a versão inicial da página **Unsubscribe**.

        public async Task<ActionResult> Index(string id, string listName)
        {
            if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
            {
                ViewBag.errorMessage = "Empty subscriber ID or list name.";
                return View("Error");
            }
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            if (subscriber == null)
            {
                ViewBag.Message = "You are already unsubscribed";
                return View("Message");
            }
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = id;
            unsubscribeVM.Confirmed = null;
            return View(unsubscribeVM);
        }

Observação: O SubscriberGUID não está na chave de partição ou chave de linha, portanto, o desempenho desta consulta será degradado conforme o tamanho de partição (o número de endereços de email em uma lista de correio) aumentar. Para obter informações sobre alternativas para tornar essa consulta mais escalonável, consulte [o primeiro tutorial na série][o primeiro tutorial da série].

O método `HttpPost Index` usa novamente o GUID e o nome da lista para obter as informações do assinante e preenche as propriedades do modelo de visualização. Em seguida, se o botão **Confirmar** for clicado, ele excluirá a linha do assinante na tabela `MailingList`. Se o botão **Confirmar** for pressionado, ele também definirá a propriedade `Confirm` como `true`, caso contrário, ele definirá a propriedade `Confirm` como `false`. O valor da propriedade `Confirm` é o que informa a exibição para mostrar a versão confirmada ou cancelada da página **Unsubscribe**.

        [HttpPost] 
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Index(string subscriberGUID, string listName, string action)
        {
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = subscriberGUID;
            unsubscribeVM.Confirmed = false;
        
            if (action == "Confirm")
            {
                unsubscribeVM.Confirmed = true;
                var deleteOperation = TableOperation.Delete(subscriber);
                mailingListTable.Execute(deleteOperation);
            }
        
            return View(unsubscribeVM);
        }

### Criar as visualizações do MVC

1.  Na pasta *Visualizações* no projeto do MVC, crie uma nova pasta e chame-a de *Cancelar Assinatura*.

2.  Na nova pasta *Views\\Unsubscribera*, inclua o arquivo *Index.cshtml* a partir do projeto transferido por download.

No arquivo *Index.cshtml*, a linha `Layout = null` especifica que o arquivo \_Layout.cshtml não deve ser usado para exibir essa página. A página **Cancelar Assinatura** exibe uma UI simples sem os cabeçalhos e rodapés que são usados para as páginas do administrador.

        @model MvcWebRole.Models.UnsubscribeVM
        
        @{
            ViewBag.Title = "Unsubscribe";
            Layout = null;
        }

No corpo da página, a propriedade `Confirmed` determina o que será exibido na página: Os botões **Confirmar** e **Cancelar** se a propriedade for nula, mensagem de cancelamento de assinatura confirmado se a propriedade for true, mensagem de cancelamento de assinatura cancelada se a propriedade for false.

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            <fieldset>
                <legend>Unsubscribe from Mailing List</legend>
                @Html.HiddenFor(model => model.SubscriberGUID)
                @Html.HiddenFor(model => model.EmailAddress)
                @Html.HiddenFor(model => model.ListName)
                @if (Model.Confirmed == null) {
                    <p>
                        Do you want to unsubscribe  @Html.DisplayFor(model => model.EmailAddress) from:  @Html.DisplayFor(model => model.ListDescription)?
                   </p>
                    <br />
                    <p>
                        <input type="submit" value="Confirm" name="action"/> 
                        &nbsp; &nbsp;
                        <input type="submit" value="Cancel" name="action"/>
                    </p>
                }
                @if (Model.Confirmed == false) {
                    <p>
                        @Html.DisplayFor(model => model.EmailAddress)  will NOT be unsubscribed from: @Html.DisplayFor(model => model.ListDescription).
                    </p>
                }
                @if (Model.Confirmed == true) {
                    <p>
                        @Html.DisplayFor(model => model.EmailAddress)  has been unsubscribed from:  @Html.DisplayFor(model => model.ListDescription).
                    </p>
                }
            </fieldset>
        }

### Testar o aplicativo

1.  Execute o projeto pressionando CTRL-F5 e, em seguida, clique em **Subscribers**.

2.  Clique em **Create** e crie um novo assinante para qualquer lista de endereçamento que você criou nos testes anteriores.

    Deixe a janela do navegador aberta na página **Subscriber** **Index**.

3.  Abra **Gerenciador de Servidores** e selecione o nó **Azure / Armazenamento / (Desenvolvimento)**.

4.  Expanda **Tabelas**, clique com o botão direito na tabela **MailingList** e clique em **Visualizar Tabela**.

5.  Clique duas vezes na linha do assinante que você adicionou.

6.  Na caixa de diálogo **Editar Entidade**, selecione e copie o valor `SubscriberGUID`.

    ![Gerenciador de Armazenamento do Azure][Gerenciador de Armazenamento do Azure]

7.  Volte para a janela do navegador. Na barra de endereço do navegador, altere "Subscriber" na URL para "unsubscribe?ID=[guidvalue]&listName=[listname]" em que [guidvalue] é o GUID que você copiou do Gerenciador de Armazenamento do Azure e [listname] é o nome da lista de correio. Por exemplo:

        http://127.0.0.1/unsubscribe?ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

    A versão da página **Unsubscribe** que solicita uma confirmação é exibida:

    ![Página Unsubscribe][Página Unsubscribe]

8.  Clique em **Confirm** e você verá a confirmação de que a inscrição do endereço de email foi cancelada.

    ![Página de confirmação do cancelamento da inscrição][Página de confirmação do cancelamento da inscrição]

9.  Volte para a página **Subscribers** **Index** para verificar se a linha do assinante não está mais presente.

## <a name="nextsteps"></a>Próximas etapas

Conforme explicado no [primeiro tutorial na série][o primeiro tutorial da série], esse tutorial não mostra os componentes do processo de assinatura, porque o método de serviço da API Web ASP.NET não implementa a segurança do segredo compartilhado. Entretanto, a restrição de IP que você configurou no segundo tutorial também protege o método de serviço e é possível incluir a funcionalidade de assinatura copiando os arquivos a seguir a partir do projeto transferido por download.

Para o método de serviço da API Web ASP.NET:

-   Controllers\\SubscribeAPI.cs

Para a página da web que os assinantes obtêm quando clicam no link **Confirm** no email que é gerado pelo método de serviço:

-   Models\\SubscribeVM.cs
-   Controllers\\SubscribeController.cs
-   Views\\Subscribe\\Index.cshtml

No [próximo tutorial][próximo tutorial] você irá configurar e programar a função de trabalho A, a função de trabalho que agenda emails.

Para obter links para recursos adicionais a fim de trabalhar com tabelas, filas e blobs do Armazenamento do Azure, consulte [o último tutorial nesta série][o último tutorial nesta série].

<div>

<div><a href="/pt-br/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/" class="site-arrowboxcta download-cta">Tutorial 4</a></div>

</div>

  [o primeiro tutorial da série]: /pt-br/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Criar a solução do Visual Studio]: #cloudproject
  [Atualizar o pacote NuGet da Biblioteca do Cliente de Armazenamento]: #updatescl
  [Configurar os projetos para usar o emulador de armazenamento]: #configurestorage
  [Configurar rastreamento e tratar reinicializações]: #tracing
  [Adicionar código para criar tabelas, fila e contêiner de blob no método Application\_Start]: #createifnotexists
  [Criar e testar a Lista de Correio]: #mailinglist
  [Criar e testar o controlador e as visualizações do Assinante]: #subscriber
  [Criar e testar o controlador e as visualizações da Mensagem]: #message
  [Criar e testar o controlador e as visualizações de Cancelar Assinatura]: #unsubscribe
  [Próximas etapas]: #nextsteps
  [Menu Novo Projeto]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png
  [Caixa de diálogo Novo Projeto]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png
  [Caixa de diálogo Novo Projeto de Nuvem do Azure]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png
  [Caixa de diálogo Novo Projeto de Nuvem do Azure - renomeando a função da web]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png
  [Caixa de diálogo Novo Projeto de Nuvem do Azure - incluindo uma função de trabalho]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png
  [Sem autenticação]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/noauth.png
  [solução completa]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [home page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png
  [Emulador de computação na bandeja do sistema]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png
  [Gerenciar Pacotes NuGet para Solução no menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png
  [Pacote de Armazenamento do Azure na caixa de diálogo Gerenciar Pacotes NuGet]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png
  [Selecionando os dois projetos na caixa de diálogo Selecionar Projetos]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png
  [Propriedades da função web]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png
  [Clicar com o botão direito do mouse em Propriedades]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png
  [Erro dos arquivos de configuração e de definição do serviço inválidos]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png
  [segundo tutorial]: /pt-br/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [A instância da função é reinicializada devido a atualizações do SO]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [Transferência sob demanda]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433075.aspx
  [dbgview]: http://technet.microsoft.com/pt-br/sysinternals/bb896647.aspx
  [TableEntity]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx
  [DynamicTableEntity]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx
  [Aprofundando-se nas tabelas da Storage Client Library 2.0 do Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [Compreendendo o modelo de dados do serviço Tabela]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd179338.aspx
  [Caractere % em PartitionKey ou RowKey]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx
  [Usar suporte assíncrono do .NET 4.5 para evitar o bloqueio de chamadas]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async
  [TableOperation.Retrieve]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx
  [Como aproveitar ao máximo as Tabelas do Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [Tabelas do Azure: Esperar Tokens de Continuação, Sério]: http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously
  [Criação de Log do Armazenamento do Azure: Usando Logs para Controlar Solicitações de Armazenamento]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Página de índice da lista de endereçamento vazia]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png
  [Página de índice da lista de endereçamento com linhas]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png
  [Página de índice de assinante vazia]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png
  [Página de índice de assinantes com linhas]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png
  [Página de índice de mensagens vazia]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png
  [Gerenciador de Armazenamento do Azure]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png
  [Página Unsubscribe]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png
  [Página de confirmação do cancelamento da inscrição]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png
  [próximo tutorial]: /pt-br/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [o último tutorial nesta série]: /pt-br/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
