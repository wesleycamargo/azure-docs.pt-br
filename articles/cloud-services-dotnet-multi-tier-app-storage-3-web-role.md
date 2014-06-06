<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" urlDisplayName="Etapa 3: Função Web" pageTitle="Aplicativo Web Multicamadas do ASP.NET do Azure - Etapa 3: Função Web" metaKeywords="Tutorial do Azure, aplicativo de Serviço de Email, função web do ASP.NET MVC, controladores MVC, controlador de API Web, projeto de Serviço de Nuvem" description="Terceiro tutorial de uma série que ensina como criar e implantar o aplicativo de Serviço de Email em um serviço de nuvem do Azure." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Criando a função web para o aplicativo de Serviço de Email do Azure - 3 de 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

# Criando a função web para o aplicativo de Serviço de Email do Azure - 3 de 5. 

Este é o terceiro de uma série de cinco tutoriais que mostram como criar e implantar o aplicativo de exemplo do Serviço de Email do Azure.  Para obter informações sobre o aplicativo e a série de tutoriais, consulte [o primeiro tutorial da série][firsttutorial].

Neste tutorial, você aprenderá:

* Como criar uma solução que contém um projeto de Serviço de Nuvem com uma função web e uma função de trabalho. 
* Como trabalhar com tabelas, blobs e filas do Azure em controladores e exibições do MVC 4.
* Como tratar conflitos de simultaneidade ao trabalhar com tabelas do Azure.
* Como configurar uma função web ou um projeto web para usar sua conta de Armazenamento do Azure.

 
<h2><a name="cloudproject"></a><span class="short-header">Criar solução</span>Criar a solução do Visual Studio</h2>

Você começa criando uma solução do Visual Studio com um projeto para o front-end da Web e um projeto para uma das funções de trabalho de back-end do Azure. Mais tarde, você adicionará a segunda função de trabalho. 

(Se desejar executar a interface do usuário da web em um Site do Azure, em vez de um Serviço de Nuvem do Azure, consulte a seção [Arquitetura alternativa][alternativearchitecture] mais adiante neste tutorial para obter as alterações dessas instruções.)

### Criar um projeto de serviço de nuvem com uma função web e uma função de trabalho

1. Inicie o Visual Studio com privilégios elevados.

	>[WACOM.NOTE] Para o Visual Studio 2013, você não precisa usar privilégios elevados, porque, por padrão, os novos projetos usam o emulador de computação expresso.
   
2. No menu **Arquivo**, selecione **Novo Projeto**.

	![Menu Novo Projeto][mtas-file-new-project]

3. Expanda **C#** e selecione **Nuvem** sob **Modelos Instalados** e, em seguida, selecione **Serviço de Nuvem do Azure**.  

4. Nomeie o aplicativo como **AzureEmailService** e clique em **OK**.

	![Caixa de diálogo Novo Projeto][mtas-new-cloud-project]

5. Na caixa de diálogo **Novo Serviço de Nuvem do Azure**, selecione **Função Web do ASP.NET** e clique na seta que aponta para a direita.

	>[WACOM.NOTE] O código baixado que você usa para esse tutorial é o MVC 4, mas você não pode criar uma Função de Trabalho MVC 4 no Visual Studio 2013 usando as instruções escritas para o Visual Studio 2012. Para o Visual Studio 2013, faça o seguinte: (1) Ignore as etapas para criar a função web e execute a etapa para a função de trabalho. (2) Depois que a função de trabalho for criada, clique com o botão direito do mouse no **Gerenciador de Soluções** e clique em **Adicionar** -- **Novo Projeto**. No painel esquerdo da caixa de diálogo **Adicionar Novo Projeto**, expanda **Web** e selecione **Visual Studio 2012**.  (3) Escolha **Aplicativo Web do ASP.NET MVC 4**, nomeie o projeto como **MvcWebRole** e, em seguida, clique em **OK**. (4) Na caixa de diálogo **Novo Projeto ASP.NET** selecione o modelo **Aplicativo da Internet**. (5) Clique com o botão direito do mouse em **Funções** sob **AzureEmailService** no **Explorador de Soluções** e, em seguida, clique em **Adicionar** - **Projeto de Função Web na Solução**. (6) Na caixa de diálogo **Associar com o Projeto de Função** selecione o projeto **MvcWebRole** e, em seguida, clique em **OK**.

	![Caixa de diálogo Novo Projeto de Nuvem do Azure][mtas-new-cloud-service-dialog]

6. Na coluna à direita, focalize o ponteiro sobre **MvcWebRole1** e, em seguida, clique no ícone de lápis para alterar o nome da função web. 

7. Digite MvcWebRole como o novo nome e pressione Enter.

	![Caixa de diálogo Novo Projeto de Nuvem do Azure - renomeando a função web][mtas-new-cloud-service-dialog-rename]

8. Siga o mesmo procedimento para adicionar uma **Função de Trabalho**, nomeie-a como WorkerRoleA e, em seguida, clique em **OK**.

	![Caixa de diálogo Novo Projeto de Nuvem do Azure - adicionando uma função de trabalho][mtas-new-cloud-service-add-worker-a]

5. Na caixa de diálogo **Novo Projeto ASP.NET** selecione o modelo **Aplicativo da Internet**.

6. Na lista suspensa **Mecanismo de Exibição**, verifique se **Razor** está selecionado e clique em **OK**.

	![Caixa de diálogo Novo Projeto][mtas-new-mvc4-project]

### Definir o cabeçalho, o menu e o rodapé da página

Nesta seção você atualiza os cabeçalhos, rodapés e itens de menu que são exibidos em cada página da interface da web do administrador.  O aplicativo terá três conjuntos de páginas da web de administrador: um para Listas de Endereçamento, um para Assinantes de listas de endereçamento e outro para mensagens.

1. No **Gerenciador de Soluções**, expanda a pasta Views\Shared e abra o arquivo &#95;Layout.cshtml.

	![_Layout.cshtml no Gerenciador de Soluções][mtas-opening-layout-cshtml]

2. No elemento **&lt;title&gt;**, altere "Meu Aplicativo ASP.NET MVC" para "Serviço de Email do Azure".

3. No elemento **&lt;p&gt;** com classe"site-title", altere "seu logotipo aqui" para "Serviço de Email do Azure" e altere "Home" para "MailingList".

	![título e cabeçalho no _Layout.cshtml][mtas-title-and-logo-in-layout]

4. Exclua a seção de menu:

	![menu in _Layout.cshtml][mtas-menu-in-layout]

4. Insira uma nova seção de menu onde estava a antiga:

        <ul id="menu">
            <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
            <li>@Html.ActionLink("Messages", "Index", "Message")</li>
            <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
        </ul>

4. No elemento **&lt;footer&gt;**, altere "Meu Aplicativo ASP.NET MVC" para "Serviço de Email do Azure".<br/>

	![footer in _Layout.cshtml][mtas-footer-in-layout]

### Executar o aplicativo localmente

1. Pressione CTRL+F5 para executar o aplicativo.

	A home page do aplicativo é exibida no navegador padrão.

	![página inicial][mtas-home-page-before-adding-controllers]

	O aplicativo executa no emulador de computação do Azure.  Você pode ver o ícone do emulador de computação na bandeja do sistema do Windows:

	![Emulador de computação na bandeja do sistema][mtas-compute-emulator-icon]


<h2><a name="tracing"></a><span class="short-header">Configurar o rastreamento</span>Configurar o rastreamento</h2>

Para permitir que os dados de rastreamento sejam salvos, abra o arquivo *WebRole.cs* e adicione o seguinte método `ConfigureDiagnostics`. Adicione o código que chama o novo método no método `OnStart`.

>[WACOM.NOTE] No Visual Studio 2013, no lugar das etapas a seguir que alteram manualmente o código em *WebRole.cs*, clique com botão direito do mouse no projeto MvcWebRole, clique em **Adicionar Item Existente** e adicione o arquivo *WebRole.cs* do projeto baixado.

    private void ConfigureDiagnostics()
    {
        DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
        config.Logs.BufferQuotaInMB = 500;
        config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
        config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

        DiagnosticMonitor.Start(
            "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
            config);
    }

    public override bool OnStart()
    {
        ConfigureDiagnostics();
        return base.OnStart();
    }

O método `ConfigureDiagnostics` é explicado no [segundo tutorial][tut2].





<h2><a name="restarts"></a><span class="short-header">Reinicializações</span>Adicione código para lidar eficientemente com reinicializações.</h2>

Os aplicativos do Serviço de Nuvem do Azure são reiniciados aproximadamente duas vezes por mês para atualizações do sistema operacional. (Para obter mais informações sobre as atualizações do SO, consulte [A instância da função é reinicializada devido a atualizações do SO (a página pode estar em inglês)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).) Quando um aplicativo web estiver para ser desligado, é gerado um evento `OnStop`. O clichê da função web criado pelo Visual Studio não substitui o método `OnStop`, portanto, o aplicativo terá apenas alguns segundos para concluir o processamento das solicitações HTTP antes de ser encerrado. Você pode adicionar código para substituir o método `OnStop` para garantir que os desligamentos sejam manipulados normalmente.

Para lidar com desligamentos e reinicializações, abra o arquivo *WebRole.cs* e adicione a seguinte substituição do método `OnStop`.

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

Esse código requer uma instrução `using` adicional:

      using System.Diagnostics;

O método `OnStop` tem até 5 minutos para fechar antes que o aplicativo seja encerrado. Você pode adicionar uma chamada de suspensão por 5 minutos para o método `OnStop` para dar ao aplicativo o máximo de tempo para processar as solicitações atuais, mas se o aplicativo estiver dimensionado corretamente, ele deverá ser capaz de processar as solicitações restantes em muito menos de 5 minutos. É aconselhável interromper o mais rápido possível, para que o aplicativo possa reiniciar o mais rápido possível e continuar o processamento de solicitações.

Depois que uma função é desativada pelo Azure, o balanceador de carga interrompe o envio de solicitações à instância da função e, depois disso, o método `OnStop` é chamado. Se você não tiver outra instância de sua função, nenhuma solicitação será processada até que sua função conclua o desligamento e reinicie (o que normalmente leva vários minutos). Esse é um motivo pelo qual o contrato de nível de serviço do Azure requer que você tenha pelo menos duas instâncias de cada função para se beneficiar da garantia de tempo de atividade.

No código mostrado para o método `OnStop`, um contador de desempenho do ASP.NET é criado para `Solicitações Atuais`. O valor do contador `Solicitações Atuais` contém o número atual de solicitações, incluindo as que estão na fila, em execução ou aguardando para serem gravadas no cliente. O valor de `Solicitações Atuais` é verificado a cada segundo, e quando cai para zero, o método `OnStop` retorna. Quando o método `OnStop` retorna, a função é desligada.

Os dados de rastreamento não são salvos quando chamados do método `OnStop` sem executar uma [Transferência sob demanda](http://msdn.microsoft.com/pt-br/library/windowsazure/gg433075.aspx). Você pode exibir as informações de rastreamento do método `OnStop` em tempo real com o utilitário [dbgview](http://technet.microsoft.com/pt-br/sysinternals/bb896647.aspx) em uma conexão de área de trabalho remota.

<h2><a name="updatescl"></a><span class="short-header">Atualizar a Storage Client Library</span>Atualizar o pacote NuGet da Storage Client Library</h2>

>[WACOM.NOTE] Esta etapa pode não ser necessária. Se o pacote NuGet do Armazenamento do Azure não aparecer na lista de atualizações, a versão instalada será a atual.

A estrutura da API que você usa para trabalhar com tabelas, filas e blobs do Armazenamento do Azure é a Storage Client Library (SCL). Essa API é incluída em um pacote NuGet no modelo do projeto do Serviço de Nuvem. No entanto, na data da edição deste tutorial, os modelos de projeto incluem a versão 1.7 da SCL, não a versão 2.0 atual. Portanto, antes de começar a escrever código, você atualizará o pacote NuGet.

1. No menu **Ferramentas** do Visual Studio, focalize o **Gerenciador de Pacotes de Biblioteca** e, em seguida, clique em **Gerenciar Pacotes NuGet para a Solução**.

	![Gerenciar Pacotes NuGet para Solução no menu][mtas-manage-nuget-for-solution]

2. No painel esquerdo da caixa de diálogo **Gerenciar Pacotes NuGet**, selecione **Atualizações**, em seguida, role para baixo até o pacote **Armazenamento do Azure** e clique em **Atualizar**.

	![Pacote de Armazenamento do Azure na caixa de diálogo Gerenciar Pacotes NuGet][mtas-update-storage-nuget-pkg]

3. Na caixa de diálogo **Selecionar Projetos**, verifique se os dois projetos estão selecionados e, em seguida, clique em **OK**.

	![Selecionando os dois projetos na caixa de diálogo Selecionar Projetos][mtas-nuget-select-projects]
 
4. Aceite os termos de licença para concluir a instalação do pacote e, em seguida, feche a caixa de diálogo **Gerenciar Pacotes NuGet**.

5. No projeto WorkerRoleA em *WorkerRole.cs*, se a seguinte instrução `using` estiver presente, exclua-a, porque não é mais necessária:

		using Microsoft.WindowsAzure.StorageClient;


A versão 1.7 da SCL inclui um provedor LINQ que simplifica a codificação de consultas de tabela. Na data da edição deste tutorial, a Table Service Layer 2.0 (TSL) ainda não tem um provedor LINQ. Se quiser usar o LINQ, você ainda terá acesso ao provedor LINQ da SCL 1.7 no namespace [Microsoft.WindowsAzure.Storage.Table.DataServices](http://msdn.microsoft.com/pt-br/library/microsoft.windowsazure.storage.table.dataservices.aspx). A TSL 2.0 foi projetada para melhorar o desempenho, e o provedor LINQ 1.7 não se beneficia de todos esses aprimoramentos. O aplicativo de exemplo usa a TSL 2.0, portanto, não usa LINQ para consultas. Para obter mais informações sobre SCL e TSL 2.0, consulte os recursos no final do [último tutorial desta série][tut5].

>[WACOM.NOTE] A Storage Client Library 2.1 adicionou novamente o suporte ao LINQ, mas esse tutorial não usa o LINQ para consultas de tabela de armazenamento. A SCL atual também oferece suporte à programação assíncrona, mas o código assíncrono não é mostrado neste tutorial. Para obter mais informações sobre a programação assíncrona e um exemplo de código que a usa com a SCL do Azure, consulte o seguinte capítulo do livro eletrônico e o projeto para download que o acompanha: [Usar o suporte assíncrono do .NET 4.5 para evitar bloqueio de chamadas](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).


<h2><a name="addref2"></a><span class="short-header">Adicionar referência à SCL 1.7</span>Adicionar uma referência a um assembly SCL 1.7</h2>

>[WACOM.NOTE] Se você estiver usando a SCL 2.1 ou posterior (Visual Studio 2013 com o SDK mais recente), ignore esta seção.

A versão 2.0 da Storage Client Library (SCL) 2.0 não tem tudo o que é necessário para diagnóstico, portanto, você precisa adicionar uma referência a um assembly 1.7.

4. Clique com o botão direito do mouse no projeto MvcWebRole e escolha **Adicionar Referência**.

5. Clique no botão **Procurar...** na parte inferior da caixa de diálogo.

6. Navegue até a seguinte pasta:

        C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\2012-10\ref

7. Selecione *Microsoft.WindowsAzure.StorageClient.dll* e clique em **Adicionar**.

8. Na caixa de diálogo **Gerenciador de Referência**, clique em **OK**.

1. Repita o processo para o projeto WorkerRoleA.



<h2><a name="createifnotexists"></a><span class="short-header">Código App_Start</span>Adicionar código para criar tabelas, filas e contêiner de blob ao método Application_Start</h2>

O aplicativo web usará a tabela `MailingList`, a tabela `Message`, a fila `azuremailsubscribequeue` e o contêiner de blob `azuremailblobcontainer`. Você pode criá-los manualmente usando uma ferramenta como o Gerenciador de Armazenamento do Azure, mas você precisa fazer isso manualmente toda vez que você começar a usar o aplicativo com uma nova conta de armazenamento. Nesta seção você adicionará um código que é executado quando o aplicativo é iniciado, verificará se as tabelas, filas e contêineres de blob necessários existem e, caso contrário, irá criá-los. 

Você pode adicionar esse código de inicialização de uma única vez ao método `OnStart` no arquivo *WebRole.cs* ou ao arquivo *Global.asax*. Para este tutorial você inicializará o Armazenamento do Azure no arquivo *Global.asax* uma vez que isso funciona com os Sites do Azure, bem como com as funções web do Serviço de Nuvem do Azure.

1. No **Gerenciador de Soluções**, expanda *Global.asax* e, em seguida, abra *Global.asax.cs*.

2. Adicione um novo método `CreateTablesQueuesBlobContainers` depois do método `Application_Start` e, em seguida, chame o novo método no método `Application_Start`, conforme mostrado no exemplo a seguir:

        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            WebApiConfig.Register(GlobalConfiguration.Configuration);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
            AuthConfig.RegisterAuth();
            // Verify that all of the tables, queues, and blob containers used in this application
            // exist, and create any that don't already exist.
            CreateTablesQueuesBlobContainers();
        }

        private static void CreateTablesQueuesBlobContainers()
        {
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
            // If this is running in an Azure Web Site (not a Cloud Service) use the Web.config file:
            //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
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
	
3. Clique com o botão direito do mouse sob a linha rabiscada azul sob `RoleEnvironment`, selecione **Resolver** e, em seguida, selecione **using Microsoft.WindowsAzure.ServiceRuntime**. 

	![rightClick][mtas-4]

1. Clique com o botão direito do mouse em `CloudStorageAccount`, selecione **Resolver** e, em seguida, selecione **using Microsoft.WindowsAzure.Storage**.  

1. Como alternativa, você pode adicionar manualmente as seguintes instruções using:

	    using Microsoft.WindowsAzure.ServiceRuntime;
	    using Microsoft.WindowsAzure.Storage;
	
1. Compile o aplicativo, o que salva o arquivo e verifica se não há erros de compilação.

Nas seções a seguir, você cria os componentes do aplicativo da web e pode testá-los com o armazenamento de desenvolvimento ou com sua conta de armazenamento, sem que seja necessário primeiro criar tabelas, filas ou contêiner de blob manualmente.



<h2><a name="mailinglist"></a><span class="short-header">Lista de Endereçamento</span>Criar e testar o controlador e as exibições da Lista de Endereçamento</h2>

A interface do usuário da web **Lista de Endereçamento** é usada por administradores para criar, editar e exibir listas de endereçamento, como "Contoso University History Department announcements" e "Fabrikam Engineering job postings".

### Adicionar a classe de entidade MailingList à pasta Models

A classe de entidade `MailingList` é usada para as linhas da tabela `MailingList` que contêm informações sobre a lista, como a descrição e o endereço de email "De" dos emails enviados para a lista.  

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta `Models` no projeto MVC e escolha **Adicionar Item Existente**.

	![Adicionar item existente à pasta Models][mtas-add-existing-item-to-models]

2. Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione o arquivo *MailingList.cs* na pasta `Models` e, em seguida, clique em **Adicionar**.

3. Abra o *MailingList.cs* e examine o código.

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
			

	A API do TSL 2.0 do Armazenamento do Azure requer que as classes de entidade que você usa para operações de tabela derivem de [TableEntity][]. Essa classe define os campos `PartitionKey`, `RowKey`, `TimeStamp` e `ETag`. As propriedades `TimeStamp` e `ETag` são usadas pelo sistema. Você verá como a propriedade `ETag` é usada para tratamento de simultaneidade mais adiante no tutorial. 

	(Também há uma classe [DynamicTableEntity] para uso quando você desejar trabalhar com linhas de tabela, como coleções de dicionários de pares de chave e valor, em vez de usar classes de modelo predefinidas. Para obter mais informações, consulte [Aprofundando-se nas tabelas da Storage Client Library 2.0 do Azure][deepdive].)

	A chave de partição da tabela `mailinglist` é o nome da lista. Nessa classe de entidade, o valor da chave de partição pode ser acessado usando a propriedade `PartitionKey` (definida na classe `TableEntity`) ou a propriedade `ListName` (definida na classe `MailingList`).  A propriedade `ListName` usa `PartitionKey` como sua variável de backup. A definição da propriedade `ListName` permite que você use um nome de variável mais descritivo no código e facilita a programação da interface do usuário da Web, uma vez que a formatação e a validação dos atributos de DataAnnotations podem ser adicionados à propriedade `ListName`, mas não podem ser adicionados diretamente à propriedade `PartitionKey`.

	O atributo `RegularExpression` na propriedade `ListName` faz com que o MVC valide a entrada do usuário para garantir que o valor do nome da lista inserido contenha somente caracteres alfanuméricos ou sublinhados. Essa restrição foi implementada para simplificar nomes de lista para que possam ser facilmente usados em cadeias de consulta em URLs. 

	**Observação:** se você desejar que o formato do nome de lista seja menos restritivo, você pode permitir outros caracteres e nomes de lista em codificação de URL quando usados em cadeias de consulta. No entanto, certos caracteres não são permitidos em chaves de partição ou chaves de linha da Tabela do Azure, e você precisa excluir pelo menos esses caracteres. Para obter informações sobre os caracteres que não são permitidos ou provocam problemas nos campos de chave de partição ou de chave de linha, consulte [Compreendendo o modelo de dados do serviço Tabela][tabledatamodel] e [Caractere % em PartitionKey ou RowKey][percentinkeyfields].

	A classe `MailingList` define um construtor padrão que define `RowKey` como a cadeia de caracteres embutida no código "mailinglist", porque todas as linhas da lista de endereçamento nessa tabela têm esse valor como sua chave de linha. (Para obter uma explicação sobre a estrutura da tabela, consulte o [primeiro tutorial da série][firsttutorial].) Qualquer valor constante poderia ter sido escolhido para essa finalidade, desde que nunca fosse igual a um endereço de email, que é a chave de linha das linhas de assinantes nessa tabela.

	O nome da lista e o endereço de email "de" devem sempre ser inseridos quando é criada uma nova entidade `MailingList`, para que tenham atributos `Obrigatórios`.

	Os atributos de `Display` especificam a legenda padrão a ser usada para um campo na interface do usuário do MVC. 

### Adicionar o controlador MVC de MailingList

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Controllers no projeto MVC e escolha **Adicionar Item Existente**.

	![Add existing item to Controllers folder][mtas-add-existing-item-to-controllers]

2. Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione o arquivo *MailingListController.cs* na pasta `Controllers` e, em seguida, clique em **Adicionar**.

3. Abra o *MailingListController.cs* e examine o código.

	O construtor padrão cria um objeto `CloudTable` a ser usado para trabalhar com a tabela `mailinglist`.

	    public class MailingListController : Controller
	    {
	        private CloudTable mailingListTable;
	
	        public MailingListController()
	        {
	            var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
	            // If this is running in an Azure Web Site (not a Cloud Service) use the Web.config file:
	            //    var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
	
	            var tableClient = storageAccount.CreateCloudTableClient();
	            mailingListTable = tableClient.GetTableReference("mailinglist");
	        }
		
	O código obtém as credenciais da sua conta de Armazenamento do Azure do arquivo de configurações do projeto do Serviço de Nuvem para estabelecer uma conexão com a conta de armazenamento. (Você configurará esses parâmetros posteriormente neste tutorial, antes de testar o controlador.) Se for executar o projeto MVC em um Site do Azure, você poderá obter a cadeia de conexão do arquivo Web.config.

	Em seguida, há um método `FindRow` que é chamado sempre que o controlador precisa procurar uma entrada específica na lista de endereçamento da tabela `MailingList`, por exemplo, para editar uma entrada da lista de endereçamento. O código recupera uma única entidade `MailingList` usando os valores das chaves de partição e das chaves de linha passados para ele. As linhas que esse controlador edita são as que têm "MailingList" como a chave de linha, portanto, "MailingList" pode ter sido embutido no código para a chave de linha, mas a especificação da chave de partição e da chave de linha é um padrão usado para os métodos `FindRow` em todos os controladores.

        private MailingList FindRow(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                throw new Exception("No mailing list found for: " + partitionKey);
            }

            return mailingList;
        }

	É instrutivo comparar o método `FindRow` no controlador `MailingList`, que retorna uma linha da lista de endereçamento, com o método `FindRow` no controlador `Subscriber`, que retorna uma linha de assinante da mesma tabela `mailinglist`.

        private Subscriber FindRow(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var subscriber = retrievedResult.Result as Subscriber;
            if (subscriber == null)
            {
                throw new Exception("No subscriber found for: " + partitionKey + ", " + rowKey);
            }
            return subscriber;
        }

	A única diferença nas duas consultas é o tipo de modelo que passam para o método [TableOperation.Retrieve](http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx). O tipo de modelo especifica o esquema (as propriedades) da linha ou linhas que você espera que a consulta retorne. Uma única tabela pode ter diferentes esquemas em linhas diferentes. Normalmente, você especifica o mesmo tipo de modelo ao ler uma linha que foi usada para criar a linha.
        
	A página **Index** exibe todas as linhas da lista de endereçamento, portanto, a consulta no método `Index` retorna todas as entidades de `MailingList` que têm "mailinglist" como a chave de linha (as outras linhas da tabela têm endereço de email como a chave de linha e contêm informações sobre o assinante).

                var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
                lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();

	O método `Index` envolve essa consulta com código desenvolvido para lidar com condições de tempo limite. 

        public ActionResult Index()
        {
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            List<MailingList> lists;
            try
            {
                var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
                lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();
            }
            catch (StorageException se)
            {
                ViewBag.errorMessage = "Timeout error, try again. ";
                Trace.TraceError(se.Message);
                return View("Error");
            }

            return View(lists);
        }

	Se você não especificar os parâmetros de tempo limite, a API tentará novamente três vezes com tempos limite exponencialmente maiores. Para uma interface da web com um usuário que está aguardando uma página aparecer, isso pode resultar em tempos de espera inaceitavelmente longos. Portanto, esse código especifica tentativas lineares (de forma que o tempo limite não aumente a cada vez) e um tempo limite que seja razoável para o usuário a esperar. 

	Quando o usuário clica no botão **Criar** na página **Criar**, o associador do modelo MVC cria uma entidade `MailingList` da entrada inserida na exibição, e o método `HttpPost Create` adiciona a entidade à tabela.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create(MailingList mailingList)
        {
            if (ModelState.IsValid)
            {
                var insertOperation = TableOperation.Insert(mailingList);
                mailingListTable.Execute(insertOperation);
                return RedirectToAction("Index");
            }

            return View(mailingList);
        }

 Para a página **Edit**, o método `HttpGet Edit` procura a linha, e o método `HttpPost` atualiza a linha.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit(string partitionKey, string rowKey, MailingList editedMailingList)
        {
            if (ModelState.IsValid)
            {
                var mailingList = new MailingList();
                UpdateModel(mailingList);
                try
                {
                    var replaceOperation = TableOperation.Replace(mailingList);
                    mailingListTable.Execute(replaceOperation);
                    return RedirectToAction("Index");
                }
                catch (StorageException ex)
                {
                    if (ex.RequestInformation.HttpStatusCode == 412)
                    {
                        // Concurrency error
                        var currentMailingList = FindRow(partitionKey, rowKey);
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
            }
            return View(editedMailingList);
        }

	O bloco try-catch lida com erros de simultaneidade. Uma exceção de simultaneidade será gerada se um usuário selecionar uma lista de endereçamento para edição e, enquanto a página **Editar** é exibida no navegador, outro usuário edita a mesma lista de endereçamento. Quando isso ocorre, o código exibe uma mensagem de aviso e indica quais campos foram alterados pelo outro usuário.  A API da TSL usa `ETag` para verificar se há conflitos de simultaneidade. Sempre que uma linha da tabela é atualizada, o valor de `ETag` é alterado.  Ao obter uma linha para edição, você salva o valor de `ETag` e, quando executa uma operação de atualização ou de exclusão, você passa o valor de `ETag` que salvou. (A exibição `Editar` tem um campo oculto para o valor de ETag.). Se a operação de atualização detectar que o valor de `ETag` no registro que você está atualizando é diferente do valor de `ETag` que é passado para a operação de atualização, ele gerará uma exceção de simultaneidade. Se não se importar com conflitos de simultaneidade, você poderá definir o campo ETag como um asterisco ("*") na entidade que você passa para a operação de atualização, e os conflitos serão ignorados. 

	Observação: o erro de HTTP 412 não é exclusivo para erros de simultaneidade. Ele pode ser gerado para outros erros pela API SCL.

	Para a página **Delete**, o método `HttpGet Delete` procura a linha para exibir seu conteúdo, e o método `HttpPost` exclui a linha de `MailingList` junto com quaisquer linhas de `Subscriber` associadas a ela na tabela `MailingList`.

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(string partitionKey)
        {
            // Delete all rows for this mailing list, that is, 
            // Subscriber rows as well as MailingList rows.
            // Therefore, no need to specify row key.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            var listRows = mailingListTable.ExecuteQuery(query).ToList();
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    mailingListTable.ExecuteBatch(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                mailingListTable.ExecuteBatch(batchOperation);
            }
            return RedirectToAction("Index");
        }

	Quando um grande número de assinantes precisar ser excluído, o código exclui os registros em lotes. O custo da transação de exclusão de uma linha é o mesmo que o da exclusão de 100 linhas em um lote. O número máximo de operações que podem ser executadas em um lote é 100. 

	Embora o loop processe linhas de `MailingList` e linhas de `Subscriber`, ele lê todas elas na classe de entidade `MailingList` porque os únicos campos necessários para a operação `Delete` são os campos `PartitionKey`, `RowKey` e `ETag`.

### Adicionar as exibições MVC de MailingList

2. No **Gerenciador de Soluções**, crie uma nova pasta na pasta *Views* no projeto MVC e dê a ela o nome *MailingList*.

1. Clique com o botão direito do mouse na nova pasta *Views\MailingList* e escolha **Adicionar Item Existente**.

	![Adicionar item existente à pasta Views][mtas-add-existing-item-to-views]

2. Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione todos os quatro arquivos .cshtml na pasta *Views/MailingList* e clique em **Adicionar**.

3. Abra o arquivo *Edit.cshtml* e examine o código.

		@model MvcWebRole.Models.MailingList
				@{
		    ViewBag.Title = "Edit Mailing List";
		}
				<h2>Edit Mailing List</h2>
              @using (Html.BeginForm()) {
              @Html.AntiForgeryToken()
              @Html.ValidationSummary(true)
              @Html.HiddenFor(model => model.ETag)
              <fieldset>
		        <legend>MailingList</legend>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.ListName)
		        </div>
		        <div class="editor-field">
		            @Html.DisplayFor(model => model.ListName)
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.Description)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.Description)
		            @Html.ValidationMessageFor(model => model.Description)
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.FromEmailAddress)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.FromEmailAddress)
		            @Html.ValidationMessageFor(model => model.FromEmailAddress)
		        </div>
		        <p>
		            <input type="submit" value="Save" />
		        </p>
		    </fieldset>
		}
		<div>
		    @Html.ActionLink("Back to List", "Index")
		</div>		
		@section Scripts {
		    @Scripts.Render("~/bundles/jqueryval")
		}
				
	Esse código é típico de exibições MVC.  Observe o campo oculto que é incluído para preservar o valor de `ETag` que é usado para tratar os conflitos de simultaneidade. Observe também que o campo `ListName` possui um auxiliar `DisplayFor` em vez de um auxiliar `EditorFor`. Não permitimos que a página **Edit** alterasse o nome da lista, pois isso exigiria código complexo no controlador: o método `HttpPost Edit` precisaria excluir a lista de endereçamento existente e todas as linhas de assinantes associadas e inseri-las novamente com o novo valor de chave. Em um aplicativo de produção, você pode decidir se a complexidade adicional vale a pena. Como você verá posteriormente, o controlador `Subscriber` permite alterações do nome da lista, uma vez que apenas uma linha é afetada a cada vez. 

	O código de *Create.cshtml* e de *Delete.cshtml* é semelhante ao de *Edit.cshtml*.

4. Abra o *Index.cshtml* e examine o código.

	    @model IEnumerable<MvcWebRole.Models.MailingList>
	    @{
	        ViewBag.Title = "Mailing Lists";
	    }
	    <h2>Mailing Lists</h2>
	    <p>
	        @Html.ActionLink("Create New", "Create")
	    </p>
	    <table>
	        <tr>
	            <th>
	                @Html.DisplayNameFor(model => model.ListName)
	            </th>
	            <th>
	                @Html.DisplayNameFor(model => model.Description)
	            </th>
	            <th>
	                @Html.DisplayNameFor(model => model.FromEmailAddress)
	            </th>
	            <th></th>
	        </tr>
	    @foreach (var item in Model) {
	        <tr>
	            <td>
	                @Html.DisplayFor(modelItem => item.ListName)
	            </td>
	            <td>
	                @Html.DisplayFor(modelItem => item.Description)
	            </td>
	            <td>
	                @Html.DisplayFor(modelItem => item.FromEmailAddress)
	            </td>
	            <td>
	                @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
	                @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
	            </td>
	        </tr>
	    }
	    </table>
	
	Esse código também é típico de exibições MVC. Os hiperlinks **Edit** e **Delete** especificam parâmetros de cadeias de consulta das chaves de partição e de linha para identificar uma linha específica.  Para entidades de `MailingList`, apenas a chave da partição é realmente necessária porque a chave de linha é sempre "MailingList", mas as duas são mantidas para que o código de exibição MVC seja consistente em todos os controladores e exibições.

### Tornar MailingList o controlador padrão

1. Abra *Route.config.cs* na pasta *App_Start*.

2. Na linha que especifica os padrões, altere o controlador padrão de "Home" para "MailingList".

         routes.MapRoute(
             name: "Default",
             url: "{controller}/{action}/{id}",
             defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }





<h2><a name="configurestorage"></a><span class="short-header">Configurar o armazenamento</span>Configurar a função web para usar sua conta de teste do Armazenamento do Azure</h2>

Você inserirá configurações para sua conta de armazenamento, que serão usadas ao executar o projeto localmente.  Para adicionar uma nova configuração, você precisa adicioná-la à nuvem e ao local, mas você pode alterar o valor da nuvem posteriormente. Você adicionará as mesmas configurações para a função de trabalho A posteriormente.

(Se desejar executar a interface do usuário da web em um Site do Azure, em vez de um Serviço de Nuvem do Azure, consulte a seção [Arquitetura alternativa][alternativearchitecture] mais adiante neste tutorial para obter as alterações dessas instruções.)

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **MvcWebRole** sob **Funções** no projeto em nuvem **AzureEmailService** e, em seguida, escolha **Propriedades**.

	![Propriedades da função web][mtas-mvcwebrole-properties-menu]

2. Verifique se a opção **Todas as Configurações** está selecionada na lista suspensa **Configuração de Serviço**.

2. Selecione a guia **Configurações** e, em seguida, clique em **Adicionar Configuração**.

3. Digite "StorageConnectionString" na coluna **Nome**.

4. Selecione **Cadeia de Conexão** na lista suspensa **Tipo**.  

6. Clique no botão de reticências (**...**) na extremidade direita da linha para abrir a caixa de diálogo **Cadeia de Conexão da Conta de Armazenamento**.

	![Clicar com o botão direito do mouse em Propriedades][mtas-elip]<br/>

7. Na caixa de diálogo **Criar Cadeia de Conexão de Armazenamento**, clique no botão de opção **Sua assinatura** e, em seguida, clique no link **Baixar Configurações de Publicação**. 

	>[WACOM.NOTE] Com o SDK mais recente não é necessário baixar nada; você escolhe a partir das contas de armazenamento disponíveis em uma lista suspensa.

	**Observação:** se você configurou os parâmetros do armazenamento para o tutorial 2 e estiver fazendo este tutorial na mesma máquina, você não precisará baixar as configurações novamente, basta clicar em **Sua assinatura** e escolher a **Assinatura** e o **Nome da Conta** corretos.

	![Clicar com o botão direito do mouse em Propriedades][mtas-enter]<br/>

	Quando você clica no link **Baixar Configurações de Publicação**, o Visual Studio inicia uma nova instância de navegador padrão com a URL da página de configurações de publicação de download do Portal de Gerenciamento do Azure. Se você não estiver conectado ao portal, o logon será solicitado. Depois de fazer logon, o navegador solicita que você salve as configurações de publicação. Anote o local onde você salva as configurações.

	![configurações de publicação][mtas-3]

1. Na caixa de diálogo **Criar Cadeia de Conexão de Armazenamento**, clique em **Importar** e, em seguida, navegue até o arquivo de configurações de publicação que você salvou na etapa anterior.

1. Selecione a assinatura e a conta de armazenamento que você deseja usar e clique em **OK**.

	![selecionar conta de armazenamento][mtas-5]

1. Siga o mesmo procedimento usado para a cadeia de conexão `StorageConnectionString` para definir a cadeia de conexão `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

	Não é necessário baixar o arquivo de configurações de publicação novamente. Quando você clicar nas reticências da cadeia de conexão `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`, descobrirá que a caixa de diálogo **Criar Cadeia de Conexão de Armazenamento** lembrará as informações de sua assinatura. Quando você clicar no botão de opção **Sua assinatura**, tudo o que você precisará fazer é selecionar a mesma **Assinatura** e **Nome da Conta** que selecionou anteriormente e clicar em **OK**. 

2. Siga o mesmo procedimento usado para as duas cadeias de conexão para a função MvcWebRole para definir as cadeias de conexão para a função WorkerRoleA.

Quando você adicionou uma nova configuração com o botão **Adicionar Configurações**, a nova configuração foi adicionada ao XML no arquivo *ServiceDefinition.csdf* e em cada um dos dois arquivos de configuração *.cscfg*. O XML a seguir é adicionado pelo Visual Studio ao arquivo *ServiceDefinition.csdf*.

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

O XML a seguir é adicionado a cada arquivo de configuração *.cscfg*.

	   <Setting name="StorageConnectionString"
	   value="DefaultEndpointsProtocol=https;
	   AccountName=azuremailstorage;
	   AccountKey=[your account key]" />

Você pode adicionar manualmente as configurações ao arquivo *ServiceDefinition.csdf* e aos dois arquivos de configuração *.cscfg*, mas o uso do editor de propriedades tem as seguintes vantagens para cadeias de conexão:

- Você apenas adiciona a nova configuração em um único local, e a configuração XML correta é adicionada a todos os três arquivos.
- O XML correto é gerado para os três arquivos de configuração. O arquivo *ServiceDefinition.csdf* define as configurações que devem estar em cada arquivo de configuração *.cscfg*. Se o arquivo *ServiceDefinition.csdf* e os dois arquivos de configuração *.cscfg* estiverem inconsistentes, você poderá obter a seguinte mensagem de erro do Visual Studio: "O modelo de serviço atual está fora de sincronia. Verifique se os arquivos de configuração e de definição do serviço são válidos."

	![Erro dos arquivos de configuração e de definição do serviço inválidos][mtas-er1]

Se você receber esse erro, o editor de propriedades não irá funcionar até que você resolva o problema de inconsistência.

### Testar o aplicativo

1. Execute o projeto pressionando CTRL+F5.

	![Página de índice da lista de endereçamento vazia][mtas-mailing-list-empty-index-page]

2. Use a função **Create** para adicionar algumas listas de endereçamento e tente as funções **Edit** e **Delete** para verificar se funcionam.

	![Página de índice da lista de endereçamento com linhas][mtas-mailing-list-index-page]



<h2><a name="subscriber"></a><span class="short-header">Assinante</span>Criar e testar o controlador e as exibições de Assinante</h2>

A interface do usuário da Web **Assinante** é usada pelos administradores para adicionar novos assinantes a uma lista de endereçamento e para editar, exibir e excluir assinantes existentes. 

### Adicionar a classe de entidade Subscriber à pasta Models

A classe de entidade `Subscriber` é usada para as linhas da tabela `MailingList` que contêm informações sobre os assinantes de uma lista. Essas linhas contêm informações, como o endereço de email da pessoa e se o endereço está verificado.  

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Models* no projeto MVC e escolha **Adicionar Item Existente**.

2. Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione o arquivo *Subscriber.cs* na pasta *Models* e, em seguida, clique em **Adicionar**.

3. Abra o *Subscriber.cs* e examine o código.

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
		

	Como a classe de entidade `MailingList`, a classe de entidade `Subscriber` é usada para ler e gravar linhas na tabela `mailinglist`. As linhas de `Subscriber` usam o endereço de email em vez da constante "mailinglist" da chave de linha.  (Para obter uma explicação sobre a estrutura da tabela, consulte o [primeiro tutorial da série][firsttutorial].) Portanto, é definida uma propriedade `EmailAddress` que usa a propriedade `RowKey` como seu campo de suporte, da mesma forma como `ListName` usa `PartitionKey` como seu campo de suporte. Conforme explicado anteriormente, isso permite que você coloque atributos de DataAnnotations de formatação e de validação nas propriedades.

	O valor `SubscriberGUID` é gerado quando uma entidade `Subscriber` é criada. Ele é usado nos links para assinar e cancelar assinatura para ajudar a garantir que somente pessoas autorizadas possam assinar ou cancelar a assinatura de endereços de email.
	Quando uma linha é criada inicialmente para um novo assinante, o valor `Verified ` é `false`. O valor `Verified` é alterado para `true` apenas depois que o novo assinante clica no hiperlink **Confirmar** no email de boas-vindas. Se uma mensagem for enviada a uma lista enquanto um assinante tiver `Verified` = `false`, nenhum email será enviado a esse assinante.

	A propriedade `Verified` na entidade `Subscriber` é definida como anulável. Quando você especificar que uma consulta deve retornar entidades `Subscriber`, é possível que algumas das linhas recuperadas não tenham uma propriedade `Verified`. Portanto, a entidade `Subscriber` define sua propriedade `Verified` como anulável para que possa refletir mais precisamente o conteúdo real de uma linha, se linhas de tabela que não possuam uma propriedade *Verified* forem retornadas por uma consulta. Você pode estar acostumado a trabalhar com as tabelas do SQL Server, no qual cada linha de uma tabela tem o mesmo esquema. Em uma tabela de Armazenamento do Azure, cada linha é apenas uma coleção de propriedades, e cada linha pode ter um conjunto diferente de propriedades. Por exemplo, no aplicativo de exemplo do Serviço de Email do Azure, as linhas que têm "MailingList" como a chave da linha não têm uma propriedade `Verified`.  Se uma consulta retornar uma linha da tabela que não possua uma propriedade `Verified`, quando a classe de entidade `Subscriber` for instanciada, a propriedade `Verified` no objeto de entidade será nula.  Se a propriedade era não anulável, você obterá o mesmo valor de `false` para linhas que têm `Verified` = `false` e para linhas que não têm nenhuma propriedade `Verified`. Portanto, uma prática recomendada para trabalhar com Tabelas do Azure é tornar anulável cada propriedade de uma classe de entidade para ler com precisão as linhas que foram criadas usando classes de entidade diferentes ou versões diferentes da classe da entidade atual. 

### Adicionar o controlador MVC de Assinante

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Controllers* no projeto MVC e escolha **Adicionar Item Existente**.

2. Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione o arquivo *SubscriberController.cs* na pasta *Controllers* e, em seguida, clique em **Adicionar**. (Verifique se você obtém *Subscriber.cs* e não *Subscribe.cs*; você adicionará *Subscribe.cs* posteriormente.)

3. Abra o *SubscriberController.cs* e examine o código.

	A maior parte do código desse controlador é semelhante ao que você viu no controlador `MailingList`. Até o nome da tabela é o mesmo, porque as informações do assinante são mantidas na tabela `MailingList`. Depois do método `FindRow`, você vê um método `GetListNames`. Esse método obtém os dados de uma lista suspensa nas páginas **Criar** e **Editar**, nas quais você pode selecionar a lista de endereçamento na qual inscrever um endereço de email.

        private List<MailingList> GetListNames()
        {
            var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
            var lists = mailingListTable.ExecuteQuery(query).ToList();
            return lists;
        }

	Esta é a mesma consulta que você viu no controlador `MailingList`. Para a lista suspensa, você deseja linhas que tenham informações sobre listas de endereçamento, portanto, você seleciona apenas as que têm RowKey = "mailinglist".

	Para o método que recupera dados para a página **Index**, você deseja linhas que contenham informações do assinante, portanto, você seleciona todas as linhas que não tenham RowKey = "MailingList".

        public ActionResult Index()
        {
            var query = (new TableQuery<Subscriber>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist")));
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();
            return View(subscribers);
        }

	Observe que a consulta especifica que os dados serão lidos em objetos `Subscriber` (especificando `<Subscriber>`) mas os dados serão lidos na tabela `mailinglist`.

	**Observação:** o número de assinantes pode crescer muito para serem tratados dessa maneira em uma única consulta. Em uma versão futura do tutorial, esperamos implementar a funcionalidade de paginação e mostrar como tratar tokens de continuação. Você precisa tratar os tokens de continuação ao executar consultas que possam retornar mais de 1.000 linhas: o Azure retorna 1.000 linhas e um token de continuação que você usa para executar outra consulta que começa onde a anterior parou. (O Pesquisador de Armazenamento do Azure não processa tokens de continuação; portanto, suas consultas não retornarão mais de 1.000 linhas). Para obter mais informações sobre conjuntos de resultados grandes e tokens de continuação, consulte [Como obter o máximo das Tabelas do Azure][howtogetthemost] e [Tabelas do Azure: espere tokens de continuação, seriamente (a página pode estar em inglês)](http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously). 

	No método `HttpGet Create`, você configura dados para a lista suspensa; e no método `HttpPost`, você deve definir valores padrão antes de salvar a nova entidade.

        public ActionResult Create()
        {
            var lists = GetListNames();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            var model = new Subscriber() { Verified = false };
            return View(model);
        }

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create(Subscriber subscriber)
        {
            if (ModelState.IsValid)
            {
                subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                if (subscriber.Verified.HasValue == false)
                {
                    subscriber.Verified = false;
                }

                var insertOperation = TableOperation.Insert(subscriber);
                mailingListTable.Execute(insertOperation);
                return RedirectToAction("Index");
            }

            var lists = GetListNames();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);

            return View(subscriber);
        }

	A página `HttpPost Edit` é mais complexa do que o que você viu no controlador `MailingList` porque a página `Subscriber` permite que você altere o nome ou o endereço de email da lista, que são campos-chave. Se o usuário alterar um desses campos, você precisará excluir o registro existente e adicionar um novo, em vez de atualizar o registro existente. O código a seguir mostra a parte do método de edição que trata dos diferentes procedimentos para alterações de chave versus não chave:
     
            if (ModelState.IsValid)
            {
                try
                {
                    UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
                    if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
                    {
                        //Keys didn't change -- Update the row
                        var replaceOperation = TableOperation.Replace(editedSubscriber);
                        mailingListTable.Execute(replaceOperation);
                    }
                    else
                    {
                        // Keys changed, delete the old record and insert the new one.
                        if (editedSubscriber.PartitionKey != partitionKey)
                        {
                            // PartitionKey changed, can't do delete/insert in a batch.
                            var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                            mailingListTable.Execute(deleteOperation);
                            var insertOperation = TableOperation.Insert(editedSubscriber);
                            mailingListTable.Execute(insertOperation);
                        }
                        else
                        {
                            // RowKey changed, do delete/insert in a batch.
                            var batchOperation = new TableBatchOperation();
                            batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                            batchOperation.Insert(editedSubscriber);
                            mailingListTable.ExecuteBatch(batchOperation);
                        }
                    }
                    return RedirectToAction("Index");

	Os parâmetros que o associador de modelo MVC passa para o método `Edit` incluem os valores de nome e endereço de email originais da lista (nos parâmetros `partitionKey` e `rowKey`) e os valores inseridos pelo usuário (nos parâmetros `listName` e `emailAddress`): 

        public ActionResult Edit(string partitionKey, string rowKey, string listName, string emailAddress)

	Os parâmetros passados para o método `UpdateModel` excluem as propriedades `PartitionKey` e `RowKey` da associação de modelo:

            var excludeProperties = new string[] { "PartitionKey", "RowKey" };
            
	O motivo disso é que as propriedades `ListName` e `EmailAddress` usam `PartitionKey` e `RowKey` como suas propriedades de suporte, e o usuário pode ter alterado um desses valores. Quando o associador de modelo atualiza o modelo definindo a propriedade `ListName`, a propriedade `PartitionKey` é atualizada automaticamente. Se o associador de modelo atualizar a propriedade `PartitionKey` com o valor original da propriedade depois de atualizar a propriedade `ListName`, ele substituirá o novo valor definido pela propriedade `ListName`. A propriedade `EmailAddress` atualiza automaticamente a propriedade `RowKey` da mesma maneira.  

	Depois de atualizar o objeto de modelo `editedSubscriber` o código determina se a chave da partição ou a chave da linha foi alterada. Se uma das chaves de valor for alterada, a linha de assinante existente precisará ser excluída e um nova inserida. Se apenas a chave de linha for alterada, a exclusão e a inserção podem ser feitas em uma transação em lotes atômica.

	Observe que o código cria uma nova entidade para passar para a operação `Delete`:

            // RowKey changed, do delete/insert in a batch.
            var batchOperation = new TableBatchOperation();
            batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
            batchOperation.Insert(editedSubscriber);
            mailingListTable.ExecuteBatch(batchOperation);

	As entidades que você passa para operações em um lote devem ser entidades distintas. Por exemplo, você não pode criar uma entidade `Subscribe`, passá-la para uma operação `Delete` e, em seguida, alterar um valor na mesma entidade `Subscriber` e passá-la para uma operação `Insert`. Se você fizer isso, o estado da entidade após a alteração da propriedade estará em vigor para a operação de Delete e de Insert.

	**Observação:** todas operações em lote devem estar na mesma partição. Como uma alteração do nome da lista altera a chave da partição, ela não pode ser feita em uma transação.



### Adicionar as exibições MVC de Subscriber

2. No **Gerenciador de Soluções**, crie uma nova pasta na pasta *Views* no projeto MVC e dê a ela o nome *Subscriber*.

1. Clique com o botão direito do mouse na nova pasta *Views\Subscriber* e escolha **Adicionar Item Existente**.

2. Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione todos os cinco arquivos .cshtml na pasta *Views/Subscriber* e clique em **Adicionar**.

3. Abra o arquivo *Edit.cshtml* e examine o código.

		@model MvcWebRole.Models.Subscriber
		
		@{
		    ViewBag.Title = "Edit Subscriber";
		}
		
		<h2>Edit Subscriber</h2>
		
		@using (Html.BeginForm()) {
		    @Html.AntiForgeryToken()
		    @Html.ValidationSummary(true)
		     @Html.HiddenFor(model => model.SubscriberGUID)
             @Html.HiddenFor(model => model.ETag)
		     <fieldset>
		        <legend>Subscriber</legend>
		        <div class="display-label">
		             @Html.DisplayNameFor(model => model.ListName)
		        </div>
		        <div class="editor-field">
		            @Html.DropDownList("ListName", String.Empty)
		            @Html.ValidationMessageFor(model => model.ListName)
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.EmailAddress)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.EmailAddress)
		            @Html.ValidationMessageFor(model => model.EmailAddress)
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.Verified)
		        </div>
		        <div class="display-field">
		            @Html.EditorFor(model => model.Verified)
		        </div>
		        <p>
		            <input type="submit" value="Save" />
		        </p>
		    </fieldset>
		}
		
		<div>
		    @Html.ActionLink("Back to List", "Index")
		</div>
		
		@section Scripts {
		    @Scripts.Render("~/bundles/jqueryval")
		}
						
	Esse código é semelhante ao que você viu anteriormente para a exibição **Editar** de `MailingList`. O valor `SubscriberGUID` não é mostrado, portanto, o valor não é fornecido automaticamente em um campo de formulário para o método do controlador `HttpPost`. Portanto, um campo oculto é incluído para preservar esse valor.

	As outras exibições contêm código que é semelhante ao que você já viu para o controlador `MailingList`.

### Testar o aplicativo

1. Execute o projeto pressionando CTRL+F5 e, em seguida, clique em **Subscribers**.

	![Página de índice de assinante vazia][mtas-subscribers-empty-index-page]

2. Use a função **Create** para adicionar algumas listas de endereçamento e tente as funções **Edit** e **Delete** para verificar se funcionam.

	![Página de índice de assinantes com linhas][mtas-subscribers-index-page]



<h2><a name="message"></a><span class="short-header">Message</span>Criar e testar o controlador e as exibições de Message</h2>

A interface do usuário da web **Message** é usada pelos administradores para criar, editar e exibir informações sobre mensagens que estão agendadas para serem enviadas a listas de endereçamento.

### Adicionar a classe de entidade Message à pasta Models

A classe de entidade `Message` é usada para as linhas da tabela `Message` que contém informações sobre uma mensagem que está agendada para ser enviada a uma lista. Essas linhas incluem informações, como a linha de assunto, a lista para envio de uma mensagem e a data agendada para o envio. 

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Models* no projeto MVC e escolha **Adicionar Item Existente**.

2. Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione o arquivo *Message.cs* na pasta Models e, em seguida, clique em **Adicionar**.

3. Abra o *Message.cs* e examine o código.

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

	O construtor padrão também define o status padrão de Pending para novas linhas de `message`.

	Para obter mais informações sobre a estrutura da tabela `Message`, consulte o [primeiro tutorial da série][firsttutorial].

### Adicionar o controlador MVC de Message

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Controllers no projeto MVC e escolha **Adicionar Item Existente**.

2. Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione o arquivo *MessageController.cs* na pasta *Controllers* e, em seguida, clique em **Adicionar**.

3. Abra o *MessageController.cs* e examine o código.

	A maior parte do código desse controlador é semelhante ao que você viu no controlador `Subscriber`. O que é novo aqui é o código para trabalhar com blobs. Para cada mensagem, o conteúdo HTML e de texto sem formatação do email é carregado na forma de arquivos .htm e .txt e armazenado em blobs.

	Os blobs são armazenados em contêineres de blob. O aplicativo do Serviço de Email do Azure armazena todos os seus blobs em um contêiner de blob único chamado "azuremailblobcontainer", e o código no construtor do controlador obtém uma referência a esse contêiner de blob:

	    public class MessageController : Controller
	    {
	        private TableServiceContext serviceContext;
	        private static CloudBlobContainer blobContainer;

		      public MessageController()
	        {
	            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
	            // If this is running in an Azure Web Site (not a Cloud Service) use the Web.config file:
	            //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
	
	            // Get context object for working with tables and a reference to the blob container.
	            var tableClient = storageAccount.CreateCloudTableClient();
            serviceContext = tableClient.GetTableServiceContext();
	            var blobClient = storageAccount.CreateCloudBlobClient();
	            blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
	        }
	
	Para cada arquivo que um usuário seleciona para carregar, a exibição do MVC fornece um objeto `HttpPostedFile` que contém informações sobre o arquivo. Quando o usuário cria uma nova mensagem, o objeto `HttpPostedFile` é usado para salvar o arquivo em um blob. Quando o usuário edita uma mensagem, o usuário pode optar por carregar um arquivo de substituição ou deixar o blob inalterado.

	O controlador inclui um método que chama os métodos `HttpPost Create` e `HttpPost Edit` para salvar um blob:

        private void SaveBlob(string blobName, HttpPostedFileBase httpPostedFile)
        {
            // Retrieve reference to a blob. 
            CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);
            // Create the blob or overwrite the existing blob by uploading a local file.
            using (var fileStream = httpPostedFile.InputStream)
            {
                blob.UploadFromStream(fileStream);
            }
        }

	O método `HttpPost Create` salva os dois blobs e, em seguida, adiciona a linha da tabela `Message`. Os Blobs são nomeados concatenando o valor de `MessageRef` com a extensão do nome do arquivo ".htm" ou ".txt". 

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
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
                SaveBlob(message.MessageRef + ".htm", file);
                SaveBlob(message.MessageRef + ".txt", txtFile);

                var insertOperation = TableOperation.Insert(message);
                messageTable.Execute(insertOperation);

                return RedirectToAction("Index");
            }

            var lists = GetListNames();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            return View(message);
        }

	O método `HttpGet Edit` valida que a mensagem recuperada está no status `Pending` de forma que o usuário não possa alterar uma mensagem depois que a função de trabalho B começou a processá-la.  Um código semelhante está no método `HttpPost Edit` e nos métodos `Delete` e `DeleteConfirmed`.

        public ActionResult Edit(string partitionKey, string rowKey)
        {
            var message = FindRow(partitionKey, rowKey);
            if (message.Status != "Pending")
            {
                throw new Exception("Message can't be edited because it isn't in Pending status.");
            }

            var lists = GetListNames();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description", message.ListName);
            return View(message);
        }

	No método `HttpPost Edit`, o código salvará um novo blob somente se o usuário optar por carregar um novo arquivo. O código a seguir omite a parte de tratamento de simultaneidade do método, que é igual ao que vimos anteriormente para o controlador `MailingList`. 
 
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit(string partitionKey, string rowKey, Message editedMsg,
            DateTime scheduledDate, HttpPostedFileBase httpFile, HttpPostedFileBase txtFile)
        {
            if (ModelState.IsValid)
            {
                var excludePropLst = new List<string>();
                excludePropLst.Add("PartitionKey");
                excludePropLst.Add("RowKey");

                if (httpFile == null)
                {
                    // They didn't enter a path or navigate to a file, so don't update the file.
                    excludePropLst.Add("HtmlPath");
                }
                else
                {
                    // They DID enter a path or navigate to a file, assume it's changed.
                    SaveBlob(editedMsg.MessageRef + ".htm", httpFile);
                }

                if (txtFile == null)
                {
                    excludePropLst.Add("TextPath");
                }
                else
                {
                    SaveBlob(editedMsg.MessageRef + ".txt", txtFile);
                }

                string[] excludeProperties = excludePropLst.ToArray();

                try
                {
                    UpdateModel(editedMsg, string.Empty, null, excludeProperties);
                    if (editedMsg.PartitionKey == partitionKey)
                    {
                        // Keys didn't change -- update the row.
                        var replaceOperation = TableOperation.Replace(editedMsg);
                        messageTable.Execute(replaceOperation);
                    }
                    else
                    {
                        // Partition key changed -- delete and insert the row.
                        // (Partition key has scheduled date which may be changed;
                        // row key has MessageRef which does not change.)
                        var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
                        messageTable.Execute(deleteOperation);
                        var insertOperation = TableOperation.Insert(editedMsg);
                        messageTable.Execute(insertOperation);
                    }
                    return RedirectToAction("Index");
                }

	Se a data agendada for alterada, a chave da partição será alterada, e uma linha precisará ser excluída e inserida. Isso não pode ser feito em uma transação porque afeta mais de uma partição.

	O método `HttpPost Delete` exclui os blobs quando exclui a linha da tabela:

        [HttpPost, ActionName("Delete")]
        public ActionResult DeleteConfirmed(String partitionKey, string rowKey)
        {
            // Get the row again to make sure it's still in Pending status.
            var message = FindRow(partitionKey, rowKey);
            if (message.Status != "Pending")
            {
                throw new Exception("Message can't be deleted because it isn't in Pending status.");
            }

            DeleteBlob(message.MessageRef + ".htm");
            DeleteBlob(message.MessageRef + ".txt");
            var deleteOperation = TableOperation.Delete(message);
            messageTable.Execute(deleteOperation);
            return RedirectToAction("Index");
        }

        private void DeleteBlob(string blobName)
        {
            var blob = blobContainer.GetBlockBlobReference(blobName);
            blob.Delete();
        }

### Adicionar as exibições MVC de Message

2. No **Gerenciador de Soluções**, crie uma nova pasta na pasta *Views* no projeto MVC e dê a ela o nome `Message`.

1. Clique com o botão direito do mouse na nova pasta *Views\Message* e escolha **Adicionar Item Existente**.

2. Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione todos os cinco arquivos .cshtml na pasta *Views/Message* e clique em **Adicionar**.

3. Abra o arquivo *Edit.cshtml* e examine o código.

		@model MvcWebRole.Models.Message
		
		@{
		    ViewBag.Title = "Edit Message";
		}
		
		<h2>Edit Message</h2>
		
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
		        <div class="editor-label">
		            @Html.LabelFor(model => model.ListName, "MailingList")
		        </div>
		        <div class="editor-field">
		            @Html.DropDownList("ListName", String.Empty)
		            @Html.ValidationMessageFor(model => model.ListName)
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.SubjectLine)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.SubjectLine)
		            @Html.ValidationMessageFor(model => model.SubjectLine)
		        </div>
		        <div class="editor-label">
		            HTML Path: Leave blank to keep current HTML File.
		        </div>
		        <div class="editor-field">
		            <input type="file" name="file" />
		        </div>
		        <div class="editor-label">
		            Text Path: Leave blank to keep current Text File.
		        </div>
		        <div class="editor-field">
		            <input type="file" name="TxtFile" />
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.ScheduledDate)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.ScheduledDate)
		            @Html.ValidationMessageFor(model => model.ScheduledDate)
		        </div>
		        <div class="display-label">
		            @Html.DisplayNameFor(model => model.Status)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.Status)
		        </div>
		       <p>
		            <input type="submit" value="Save" />
		        </p>
		    </fieldset>
		}
		
		<div>
		    @Html.ActionLink("Back to List", "Index")
		</div>
		
		@section Scripts {
		    @Scripts.Render("~/bundles/jqueryval")
		}
				
	O método `HttpPost Edit` precisa da chave de partição e da chave de linha, portanto, o código fornece essas chaves em campos ocultos. Os campos ocultos não eram necessários no controlador `Subscriber` porque (a) as propriedades `ListName` e `EmailAddress` no modelo `Subscriber` atualizam as propriedades `PartitionKey` e `RowKey` e (b) as propriedades `ListName` e `EmailAddress` eram incluídas com auxiliares `EditorFor` na exibição Edit. Quando o associador de modelo MVC do modelo `Subscriber` atualiza a propriedade `ListName`, a propriedade `PartitionKey` é automaticamente atualizada, e quando o associador de modelo MVC atualiza a propriedade `EmailAddress` no modelo `Subscriber`, a propriedade `RowKey` é atualizada automaticamente. No modelo `Message`, os campos que mapeiam para a chave de partição e para a chave de linha não são campos editáveis, portanto, não são definidos dessa forma.

	Um campo oculto também é incluído para a propriedade `MessageRef`. Esse é o mesmo valor que o da chave de partição, mas é incluído para permitir maior clareza no código no método `HttpPost Edit`. A inclusão do campo oculto `MessageRef` permite que o código no método `HttpPost Edit` faça referência ao valor de `MessageRef` por esse nome quando constrói nomes de arquivo para os blobs. 
   
3. Abra o arquivo *Index.cshtml* e examine o código.

		@model IEnumerable<MvcWebRole.Models.Message>
		
		@{
		    ViewBag.Title = "Messages";
		}
		
		<h2>Messages</h2>
		
		<p>
		    @Html.ActionLink("Create New", "Create")
		</p>
		<table>
		    <tr>
		        <th>
		            @Html.DisplayNameFor(model => model.ListName)
		        </th>
		        <th>
		            @Html.DisplayNameFor(model => model.SubjectLine)
		        </th>
		        <th>
		            @Html.DisplayNameFor(model => model.ScheduledDate)
		        </th>
		        <th>
		            @Html.DisplayNameFor(model => model.Status)
		        </th>
		        <th></th>
		    </tr>
		    @foreach (var item in Model)
		    {
		        <tr>
		            <td>
		                @Html.DisplayFor(modelItem => item.ListName)
		            </td>
		            <td>
		                @Html.DisplayFor(modelItem => item.SubjectLine)
		            </td>
		            <td>
		                @Html.DisplayFor(modelItem => item.ScheduledDate)
		            </td>
		            <td>
		                @item.Status
		            </td>
		            <td>
		                @if (item.Status == "Pending")
		                {
		                    @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
		                    @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
		                }
		                @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
		            </td>
		        </tr>
		    }
		
		</table>
				
	Uma diferença aqui em relação às outras exibições de **Index** é que os links **Edit** e **Delete** são mostrados somente para as mensagens que estão no status `Pending`:

        @if (item.Status == "Pending")
        {
            @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
            @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
        }

	Isso ajuda a impedir que o usuário faça alterações em uma mensagem depois que função de trabalho A começou a processá-la.

	As outras exibições contêm código semelhante ao da exibição **Edit** ou de outras exibições que você viu dos outros controladores.

### Testar o aplicativo

1. Execute o projeto pressionando CTRL+F5 e, em seguida, clique em **Messages**.

	![Página de índice de mensagens vazia][mtas-message-empty-index-page]

2. Use a função **Create** para adicionar algumas listas de endereçamento e tente as funções **Edit** e **Delete** para verificar se funcionam.

	![Página de índice de assinantes com linhas][mtas-message-index-page]




<h2><a name="unsubscribe"></a><span class="short-header">Unsubscribe</span>Criar e testar o controlador e a exibição de Unsubscribe</h2>

Em seguida, você implementará a interface do usuário para o processo de cancelamento de assinatura.

**Observação:** este tutorial cria apenas o controlador para o processo de cancelamento de assinatura, não o processo de assinatura. Como foi explicado no [primeiro tutorial][firsttutorial], a interface do usuário e o método de serviço para o processo de assinatura foi deixado de fora até que implementemos a segurança apropriada para o método de serviço. Até então, você pode usar as páginas do administrador de **Subscriber** para inscrever endereços de email em listas.

### Adicionar o modelo de exibição Unsubscriber à pasta Models

O modelo de exibição `UnsubscribeVM` é usado para passar dados entre o controlador `Unsubscribe` e sua exibição.  

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta `Models` no projeto MVC e escolha **Adicionar Item Existente**.

2. Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione o arquivo `UnsubscribeVM.cs` na pasta Models e, em seguida, clique em **Adicionar**.

3. Abra o `UnsubscribeVM.cs` e examine o código.


	    public class UnsubscribeVM
	    {
	        public string EmailAddress { get; set; }
	        public string ListName { get; set; }
	        public string ListDescription { get; set; }
	        public string SubscriberGUID { get; set; }
	        public bool? Confirmed { get; set; }
	    }

	Os links de Unsubscribe contêm `SubscriberGUID`. Esse valor é usado para obter o endereço de email, o nome da lista e a descrição da lista da tabela `MailingList`. A exibição mostra o endereço de email e a descrição da lista da qual a assinatura deve ser cancelada e exibe um botão **Confirm** no qual o usuário deve clicar para concluir o processo de cancelamento da assinatura.

### Adicionar o controlador Unsubscribe

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta `Controllers` no projeto MVC e escolha **Adicionar Item Existente**.

2. Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione o arquivo *UnsubscribeController.cs* na pasta *Controllers* e, em seguida, clique em **Adicionar**.

3. Abra o *UnsubscribeController.cs* e examine o código.

	Esse controlador tem um método `HttpGet Index` que exibe a página de cancelamento de assinatura inicial e um método `HttpPost Index` que processa o botão **Confirm** ou **Cancel**.

	O método `HttpGet Index` usa o GUID e o nome da lista na cadeia de consulta para obter a linha da tabela `MailingList` do assinante. Em seguida, ele coloca todas as informações necessárias para a exibição no modelo de exibição e exibe a página **Unsubscribe**. Define a propriedade `Confirmed` como null para informar à exibição para mostrar a versão inicial da página **Unsubscribe**.
 
	     public ActionResult Index(string id, string listName)
	     {
	         if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
	         {
	             ViewBag.errorMessage = "Empty subscriber ID or list name.";
	             return View("Error");
	         }
	         string filter = TableQuery.CombineFilters(
	             TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
	             TableOperators.And,
	             TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
	         var query = new TableQuery<Subscriber>().Where(filter);
	         var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
	         if (subscriber == null)
	         {
	             ViewBag.Message = "You are already unsubscribed";
	             return View("Message");
	         }
	         var unsubscribeVM = new UnsubscribeVM();
	         unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
	         unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
	         unsubscribeVM.SubscriberGUID = id;
	         unsubscribeVM.Confirmed = null;
	         return View(unsubscribeVM);
	     }

	Observação: o SubscriberGUID não é a chave de partição ou a chave de linha, portanto o desempenho dessa consulta será degradado à medida que o tamanho da partição (o número de endereços de email em uma lista de endereçamento) aumentar.  Para obter mais informações sobre as alternativas para tornar essa consulta mais escalonável, consulte [o primeiro tutorial da série][firsttutorial].

	O método `HttpPost Index` usa novamente o GUID e o nome da lista para obter as informações do assinante e popula as propriedades do modelo de exibição. Em seguida, se o botão **Confirm** for clicado, excluirá a linha do assinante na tabela `MailingList`. Se o botão **Confirm** for pressionado, ele também definirá a propriedade `Confirm` como `true`, caso contrário, definirá a propriedade `Confirm` como `false`. O valor da propriedade `Confirm` é o que informa a exibição para mostrar a versão confirmada ou cancelada da página **Unsubscribe**.

        [HttpPost] 
        [ValidateAntiForgeryToken]
        public ActionResult Index(string subscriberGUID, string listName, string action)
        {
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();

            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
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

### Criar as exibições MVC

2. No **Gerenciador de Soluções**, crie uma nova pasta na pasta *Views* no projeto MVC e dê a ela o nome *Unsubscribe*.

1. Clique com o botão direito do mouse na nova pasta *Views\Unsubscribe* e escolha **Adicionar Item Existente**.

2. Navegue até a pasta onde você baixou o aplicativo de exemplo, selecione o arquivo *Index.cshtml* na pasta *Views\Unsubscribe* e, em seguida, clique em **Adicionar**.

3. Abra o arquivo *Index.cshtml* e examine o código.
		
		@model MvcWebRole.Models.UnsubscribeVM
		
		@{
		    ViewBag.Title = "Unsubscribe";
		    Layout = null;
		}
		
		<h2>Email List Subscription Service</h2>
		
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
		
		@section Scripts {
		    @Scripts.Render("~/bundles/jqueryval")
		}
						
	A linha `Layout = null` especifica que o arquivo _Layout.cshtml não deve ser usado para exibir essa página. A página **Unsubscribe** exibe uma interface do usuário muito simples sem cabeçalhos e rodapés usados para as páginas de administrador.

	No corpo da página, a propriedade `Confirmed` determina o que será exibido na página: os botões **Confirm** e **Cancel**, se a propriedade for null, a mensagem de confirmação do cancelamento da assinatura, se a propriedade for true, a mensagem de cancelamento da assinatura cancelada, se a propriedade for false.

### Testar o aplicativo

1. Execute o projeto pressionando CTRL-F5 e, em seguida, clique em **Subscribers**.

2. Clique em **Create** e crie um novo assinante para qualquer lista de endereçamento que você criou nos testes anteriores.

	Deixe a janela do navegador aberta na página **Subscriber** **Index**.

3. Abra o Pesquisador de Armazenamento do Azure e selecione sua conta de armazenamento de teste.

4. Clique em **Tabelas** em **Tipo de Armazenamento**, selecione a tabela **MailingList** e, em seguida, clique em **Consulta**.

5. Clique duas vezes na linha do assinante que você adicionou.

	![Gerenciador de Armazenamento do Azure][mtas-ase-unsubscribe]

6. Na caixa de diálogo **Editar Entidade**, selecione e copie o valor de `SubscriberGUID`.

	![Gerenciador de Armazenamento do Azure][mtas-ase-edit-entity-unsubscribe]

7. Volte para a janela do navegador.  Na barra de endereços do navegador, altere "Subscriber" na URL para "unsubscribe?ID=[guidvalue]&listName=[listname]" em que [guidvalue] é o GUID que você copiou do Gerenciador de Armazenamento do Azure, e [listname] é o nome da lista de endereçamento.  Por exemplo:

        http://127.0.0.1/unsubscribe?ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

	A versão da página **Unsubscribe** que solicita uma confirmação é exibida:

	![Página Unsubscribe][mtas-unsubscribe-page]

2. Clique em **Confirm** e você verá a confirmação de que a assinatura do endereço de email foi cancelada.

	![Página de confirmação do cancelamento da inscrição][mtas-unsubscribe-confirmed-page]

3. Volte para a página **Subscribers** **Index** para verificar se a linha do assinante não está mais presente.




<h2><a name="alternativearchitecture"></a><span class="short-header">Arquitetura alternativa</span>(Opcional) Criar a arquitetura alternativa</h2>

As seguintes alterações nas instruções se aplicarão se você desejar criar uma arquitetura alternativa, -- isto é, executar a interface do usuário da web em um Site do Azure em vez de em uma função web do Serviço de Nuvem do Azure.

* Ao criar a solução, crie primeiro o projeto do **Aplicativo Web ASP.NET MVC 4** e, em seguida, adicione à solução um projeto do **Serviço de Nuvem do Azure** com uma função de trabalho.

* Armazene a cadeia de conexão do Armazenamento do Azure no arquivo Web.config em vez do arquivo de configurações do serviço de nuvem. (Isso só funciona para os Sites do Azure. Se você tentar usar o arquivo Web.config para a cadeia de conexão do armazenamento em uma função web do Serviço de Nuvem do Azure, você obterá um erro HTTP 500.) 

Adicione uma nova cadeia de conexão chamada `StorageConnectionString` ao arquivo *Web.config*, conforme mostrado no exemplo a seguir:

	       <connectionStrings>
	          <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-MvcWebRole-20121010185535;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-MvcWebRole-20121010185535.mdf" providerName="System.Data.SqlClient" />
	          <add name="StorageConnectionString" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[primarykey]" />
	       </connectionStrings>
	
Obtenha os valores da cadeia de conexão no [Portal de Gerenciamento do Azure][managementportal]: selecione a guia **Armazenamento** e sua conta de armazenamento e, em seguida, clique em **Gerenciar Chaves** na parte inferior da página.

* Sempre que você vir `RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString")` no código, substitua-o por `ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString`.



<h2><a name="nextsteps"></a><span class="short
-header">Próximas etapas</span>Próximas etapas</h2>

Conforme explicado no [primeiro tutorial da série][firsttutorial], não estamos mostrando como criar o processo de assinatura em detalhes neste tutorial até que implementemos um segredo compartilhado para proteger o método de serviço da API Web ASP.NET. No entanto, a restrição de IP também protege o método de serviço e você pode adicionar a funcionalidade de assinatura copiando os seguintes arquivos do projeto baixado.

Para o método de serviço da API Web ASP.NET:

* Controllers\SubscribeAPI.cs

Para a página da web que os assinantes obtêm quando clicam no link **Confirm** no email que é gerado pelo método de serviço:

* Models\SubscribeVM.cs
* Controllers\SubscribeController.cs
* Views\Subscribe\Index.cshtml

No [próximo tutorial][nexttutorial] você irá configurar e programar a função de trabalho A, a função de trabalho que agenda emails.

Para obter links para recursos adicionais para trabalhar com tabelas, filas e blobs do Armazenamento do Azure, consulte o final do [último tutorial desta série][tut5].

<div><a href="/pt-br/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/" class="site-arrowboxcta download-cta">Tutorial 4</a></div>



[alternativearchitecture]: #alternativearchitecture


[tut5]: /pt-br/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
[tut2]: /pt-br/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
[firsttutorial]: /pt-br/develop/net/tutorials/multi-tier-web-site/1-overview/
[nexttutorial]: /pt-br/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/

[TableEntity]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx
[DynamicTableEntity]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[managementportal]: http://manage.windowsazure.com

[percentinkeyfields]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx
[tabledatamodel]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd179338.aspx 
[deepdive]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
[howtogetthemost]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx

[mtas-home-page-before-adding-controllers]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png
[mtas-menu-in-layout]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-menu-in-layout.png
[mtas-footer-in-layout]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-footer-in-layout.png
[mtas-title-and-logo-in-layout]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-title-and-logo-in-layout.png
[mtas-new-cloud-service-dialog-rename]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png
[mtas-new-mvc4-project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png
[mtas-new-cloud-service-dialog]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png
[mtas-new-cloud-project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png
[mtas-new-cloud-service-add-worker-a]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png
[mtas-mailing-list-empty-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png
[mtas-mailing-list-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png
[mtas-file-new-project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png
[mtas-opening-layout-cshtml]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-opening-layout-cshtml.png

[mtas-add-existing-item-to-models]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-models.png
[mtas-add-existing-item-to-controllers]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-controllers.png
[mtas-add-existing-item-to-views]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-views.png
[mtas-mvcwebrole-properties-menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png




[mtas-subscribers-empty-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png
[mtas-subscribers-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png
[mtas-message-empty-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png
[mtas-message-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png
[mtas-ase-edit-entity-unsubscribe]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png
[mtas-ase-unsubscribe]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-unsubscribe.png
[mtas-unsubscribe-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png
[mtas-unsubscribe-confirmed-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png
[mtas-er1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png
[mtas-4]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-4.png
[mtas-3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-3.png
[mtas-5]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-5.png
[mtas-enter]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-enter.png
[mtas-elip]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png
[mtas-manage-nuget-for-solution]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png
[mtas-update-storage-nuget-pkg]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png
[mtas-nuget-select-projects]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png
[mtas-compute-emulator-icon]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png



