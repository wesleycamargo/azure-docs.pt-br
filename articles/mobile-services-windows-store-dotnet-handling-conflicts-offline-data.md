<properties linkid="develop-mobile-tutorials-handle-conflcits-offline-data-dotnet" urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Handle Conflicts with offline data in Mobile Services (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services handle conflicts when syncing offline data in your Windows Store application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling conflicts with offline data in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Tratamento de conflitos com sincronização de dados offline nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="C# da Windows Store" class="current">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone">Windows Phone</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este t&oacute;pico mostra como sincronizar dados e tratar conflitos ao usar os recursos offline dos Servi&ccedil;os M&oacute;veis do Azure.</p>
<p>Se voc&ecirc; preferir assistir a um v&iacute;deo, o clipe &agrave; direita segue as mesmas etapas deste tutorial.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o v&iacute;deo</span></a> <span class="time">14:36:00</span></div>

</div>

Neste tutorial, você baixará um aplicativo que oferece suporte a dados offline e online, integrará o serviço móvel ao aplicativo e fará logon no Portal de Gerenciamento do Azure para exibir e atualizar o banco de dados ao executar o aplicativo.

Esse tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução aos dados offline][Introdução aos dados offline]. Antes de começar este tutorial, você deve primeiro concluir a [Introdução aos dados offline][Introdução aos dados offline].

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Baixar o projeto de aplicativo da Windows Store][Baixar o projeto de aplicativo da Windows Store]
2. [Adicionar uma coluna de data de conclusão para o banco de dados][Adicionar uma coluna de data de conclusão para o banco de dados]
  * [Atualizando o banco de dados para serviços móveis de back-end do .NET][Atualizando o banco de dados para serviços móveis de back-end do .NET]
  * [Atualizando o banco de dados para os serviços móveis do JavaScript][Atualizando o banco de dados para os serviços móveis do JavaScript]  
1.  [Testar o aplicativo no serviço móvel][Testar o aplicativo no serviço móvel]
2.  [Atualizar manualmente os dados no back-end para criar um conflito][Atualizar manualmente os dados no back-end para criar um conflito]

Este tutorial requer o Visual Studio 2013 em execução no Windows 8.1.

## <a name="download-app"></a>Baixar o projeto de amostra

![][0]

Este tutorial é baseado no [Exemplo de código de tratamento de conflitos][Exemplo de código de tratamento de conflitos], que é um projeto de aplicativo da Windows Store para o Visual Studio 2013. A interface do usuário desse aplicativo é semelhante ao aplicativo do tutorial [Introdução aos dados offline][Introdução aos dados offline], com a exceção de que há uma nova coluna para cada TodoItem.

1.  Baixe a versão do C# do [Código de exemplo de tratamento de conflitos][Exemplo de código de tratamento de conflitos].

2.  Instalar o [SQLite para Windows 8.1][SQLite para Windows 8.1] se ainda não estiver instalado.

3.  No Visual Studio 2013, abra o projeto transferido por download. Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

4.  No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**. Você também pode modificar a data de conclusão dos itens de tarefas pendentes que adicionar.

Observe que o aplicativo ainda não está conectado a nenhum serviço móvel, portanto, os botões **Push** e **Pull** gerarão exceções.

## <a name="add-column"></a>Incluir uma coluna no model de dados

Nesta seção, você irá atualizar o banco de dados para o serviço móvel para incluir uma tabela TodoItem com uma coluna de data de conclusão. O aplicativo permite que você altere a data de conclusão de um item em tempo de execução para que possa gerar conflitos de sincronização em uma seção posterior deste tutorial.

A classe `TodoItem` no exemplo está definida no MainPage.xaml.cs. Observe que a classe tem o seguinte atributo que se destina às operações de sincronização nessa tabela.

        [DataTable("TodoWithDate")]

Atualize seu banco de dados para incluir essa tabela.

### <a name="dotnet-backend"></a>Atualizando o banco de dados para serviços móveis de back-end do .NET

Se estiver usando o back-end do .NET para o seu serviço móvel, siga estas etapas para atualizar o esquema do seu banco de dados.

1.  Abra seu projeto de serviço móvel de back-end do .NET no Visual Studio.
2.  No Gerenciador de Soluções para o Visual Studio, no seu projeto de serviço, expanda a pasta **Modelos** e abra ToDoItem.cs. Inclua a propriedade `DueDate` como mostrado a seguir.

          public class TodoItem : EntityData
          {
            public string Text { get; set; }
            public bool Complete { get; set; }
            public System.DateTime DueDate { get; set; }
          }

3.  No Gerenciador de Soluções para o Visual Studio, expanda a pasta **App\_Start** e abra o arquivo WebApiConfig.cs.

    No arquivo WebApiConfig.cs, observe que a classe de inicializador padrão do banco de dados é derivada da classe `DropCreateDatabaseIfModelChanges`. Isso significa que qualquer alteração no modelo resultará na tabela que está sendo descartada e recriada para acomodar o modelo novo. Portanto, os dados da tabela serão perdidos e a tabela será refeita. Modifique o método Seed do inicializador do banco de dados para que a inicialização `Seed()` funcione da seguinte maneira para inicializar a nova coluna DueDate. Salve o arquivo WebApiConfig.cs.

    > [WACOM.NOTE] Ao usar o inicializador de banco de dados padrão, o Entity Framework eliminará e recriará o banco de dados sempre que detectar uma mudança do modelo de dados na definição de modelo Code First. Para fazer com que esse modelo de dados altere e mantenha os dados existentes no banco de dados, você deve usar as Migrações Code First. Para obter mais informações, consulte [Como usar as Migrações Code First para atualizar o modelo de dados][Como usar as Migrações Code First para atualizar o modelo de dados].

        new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
        new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

4.  No Gerenciador de Soluções para o Visual Studio, expanda a pasta **Controladores** e abra ToDoItemController.cs. Renomeie a classe `TodoItemController` para `TodoWithDateController`. Isso irá alterar o ponto de extremidade REST para as operações da tabela.

        public class TodoWithDateController : TableController<TodoItem>

5.  No Gerenciador de Soluções para o Visual Studio, clique com o botão direito em seu projeto de serviço móvel de back-end do .NET e clique em **Publicar** para publicar suas mudanças.

### <a name="javascript-backend"></a>Atualizando o banco de dados para os serviços móveis de back-end do JavaScript

Para os serviços móveis de back-end do JavaScript, você irá adicionar uma nova tabela com o nome **TodoWithDate**. Para adicionar a tabela **TodoWithDate** nos serviços móveis de back-end do JavaScript, siga estas etapas.

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  Navegue até a guia **Dados** de seu serviço móvel.

3.  Clique em **Criar** na parte inferior da página e crie uma nova tabela chamada **TodoWithDate**.

## <a name="test-app"></a>Testar o aplicativo no serviço móvel

Está na hora de testar o aplicativo nos Serviços Móveis.

1.  No Portal de Gerenciamento do Azure, localize sua chave do aplicativo de serviço móvel clicando em **Gerenciar Chaves** na barra de comandos da guia **Painel**. Copie a **Chave do Aplicativo**.

2.  No Gerenciador de Soluções para o Visual Studio, abra o arquivo App.xaml.cs no projeto de amostra do cliente. Altere a inicialização do **MobileServiceClient** para usar a URL do seu serviço móvel e a chave do aplicativo:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3.  No Visual Studio, pressione a chave **F5** para criar e executar o aplicativo.

4.  Como anteriormente, digite texto na caixa de texto e clique no botão **Salvar**. Isso salva os dados na tabela de sincronização local, mas não no servidor.

    ![][0]

5.  Para ver o estado atual de seu banco de dados, faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu serviço móvel.

  * Se estiver usando o back-end do JavaScript para seu serviço móvel, clique na guia **Dados**, em seguida, clique na tabela **TodoWithDate**. Clique em **Procurar** para ver que a tabela ainda estará vazia, uma vez que não enviamos por push as alterações do aplicativo para o servidor.

        ![][1]

  * Se estiver usando o back-end do .NET para seu serviço móvel, clique na guia **Configurar**, em seguida, clique em seu banco de dados SQL. Clique em **Gerenciar** na parte inferior da tela para fazer logon no Portal de Gerenciamento do Azure SQL para exibir seu banco de dados executando uma consulta SQL semelhante ao seguinte.

            SELECT * FROM todolist.todowithdate

        ![][2]

1.  Novamente no aplicativo, clique em **Enviar por Push**.

2.  No Portal de Gerenciamento, clique em **Atualizar** na tabela **TodoItem**. Agora você deve ver os dados que inseriu em seu aplicativo.

    ![][1]

## <a name="handle-conflict"></a>Atualizar os dados no back-end para criar um conflito

Em um cenário do mundo real, um conflito de sincronização ocorrerá quando um aplicativo enviar atualizações por push para um registro no banco de dados e, em seguida, outro aplicativo tentar enviar uma alteração por push para o mesmo registro usando uma versão desatualizada desse registro. Se uma instância do aplicativo tentar atualizar o mesmo registro sem efetuar pull no registro atualizado, ocorrerá um conflito e o conflito será capturado como um `MobileServicePreconditionFailedException` no aplicativo.

Se desejar implantar o aplicativo em outro computador para executar duas instâncias do aplicativo para gerar um conflito, você poderá seguir as instruções de implantação do tutorial [Tratando de conflitos no banco de dados][Tratando de conflitos no banco de dados].

As etapas a seguir mostram como você pode atualizar o banco de dados no Visual Studio para provocar um conflito.

1.  No Visual Studio, execute o Gerenciador de Servidores e conecte-se à sua conta do Azure. Expanda o **Bancos de Dados SQL** de sua conta do Azure.

    ![][2]

2.  Clique com o botão direito do mouse em seu Banco de Dados SQL e clique em **Abrir no Pesquisador de Objetos do SQL Server**.
3.  No Pesquisador de Objetos do SQL Server, expanda seu banco de dados e expanda **Tabelas**. Clique com o botão direito do mouse na tabela **TodoWithDate** e clique em **Exibir Dados**.

4.  Altere o campo **Concluído** de um dos itens para Verdadeiro.

    ![][3]

5.  De volta em seu aplicativo Todo, edite o mesmo item que você modificou diretamente no banco de dados.

    ![][4]

6.  Clique no botão **Enviar por Push**. Você verá uma caixa de diálogo perguntando como resolver o conflito. Escolha uma das opções para resolver o conflito.

    ![][5]

## Revisão do código para tratamento de conflitos de sincronização
Para ver o estado atual
Para configurar o recurso offline para detectar conflitos, você deve incluir uma coluna de versão no banco de dados local e no objeto de transferência de dados. A classe `TodoItem` possui o seguinte membro:

        [Version]
        public string Version { get; set; }

A coluna `__version` também é especificada no banco de dados local configurado no método `OnNavigatedTo()`.

Para tratar conflitos de sincronização offline no seu código, crie uma classe que implementa `IMobileServiceSyncHandler`. Passe um objeto deste tipo na chamada para `InitializeAsync`:

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

A classe `SyncHandler` no arquivo **MainPage.xaml.cs** implementa o `IMobileServiceSyncHandler`. O método `ExecuteTableOperationAsync` é chamado quando cada operação de push é enviada ao servidor. Se uma exceção do tipo `MobileServicePreconditionFailedException` for lançada, isso significará que há um conflito entre as versões local e remota de um item.

Para resolver conflitos em favor do item local, simplesmente repita a operação. Depois que um conflito ocorreu, a versão local do item será atualizada para corresponder à versão do servidor, portanto, executar a operação novamente substituirá as alterações de servidor pelas alterações locais:

    await operation.ExecuteAsync(); 

Para resolver conflitos em favor do item do servidor, apenas retorne do `ExecuteTableOperationAsync`. A versão local do objeto será descartada e substituída pelo valor do servidor.

Para parar a operação de push (mas manter as mudanças enfileiradas), use o método `AbortPush()`:

    operation.AbortPush();

Isso interromperá a operação atual de push mas manterá todas as alterações pendentes, inclusive a operação atual, se `AbortPush` for chamado de `ExecuteTableOperationAsync`. Na próxima vez que `PushAsync()` for chamado, essas alterações serão enviadas ao servidor.

Quando um push é cancelado, o `PushAsync` gerará uma `MobileServicePushFailedException` e a propriedade de exceção `PushResult.Status` terá o valor `MobileServicePushStatus.CancelledByOperation`.


<!-- Images -->
<!-- URLs -->

  [Introdução aos dados offline]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
  [Baixar o projeto de aplicativo da Windows Store]: #download-app
  [Adicionar uma coluna de data de conclusão para o banco de dados]: #add-column
  [Atualizando o banco de dados para serviços móveis de back-end do .NET]: #dotnet-backend
  [Atualizando o banco de dados para os serviços móveis do JavaScript]: #javascript-backend
  [Testar o aplicativo no serviço móvel]: #test-app
  [Atualizar manualmente os dados no back-end para criar um conflito]: #handle-conflict
  [0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
  [Exemplo de código de tratamento de conflitos]: http://go.microsoft.com/fwlink/?LinkId=394787
  [SQLite para Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
  [Como usar as Migrações Code First para atualizar o modelo de dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
  [Tratando de conflitos no banco de dados]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app
  [2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-server-explorer.png
  [3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-sql-server-object-explorer-update-data.png
  [4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run2.png
  [5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run3.png
