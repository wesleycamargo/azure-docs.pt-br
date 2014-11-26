<properties linkid="develop-mobile-tutorials-handle-conflcits-offline-data-dotnet" urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Handle Conflicts with offline data in Mobile Services (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services handle conflicts when syncing offline data in your Windows phone application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling conflicts with offline data in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc" />

# Tratamento de conflitos com sincronização de dados offline nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="C# da Windows Store">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone" class="current">Windows Phone</a>
</div>

Este tópico mostra como sincronizar dados e tratar conflitos ao usar os recursos offline dos Serviços Móveis do Azure. Neste tutorial, você baixará um aplicativo que oferece suporte a dados offline e online, integrará o serviço móvel ao aplicativo e fará logon no Portal de Gerenciamento do Azure para exibir e atualizar o banco de dados ao executar o aplicativo.

Esse tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução aos dados offline][Introdução aos dados offline]. Antes de começar este tutorial, você deve primeiro concluir a [Introdução aos dados offline][Introdução aos dados offline].

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto do Windows Phone][Baixar o projeto do Windows Phone]
2.  [Adicionar uma coluna de data de conclusão para o banco de dados][Adicionar uma coluna de data de conclusão para o banco de dados]
 -   [Atualizando o banco de dados para serviços móveis de back-end do .NET][Atualizando o banco de dados para serviços móveis de back-end do .NET]
 -   [Atualizando o banco de dados para os serviços móveis do JavaScript][Atualizando o banco de dados para os serviços móveis do JavaScript]
1.  [Testar o aplicativo no serviço móvel][Testar o aplicativo no serviço móvel]
2.  [Atualizar manualmente os dados no back-end para criar um conflito][Atualizar manualmente os dados no back-end para criar um conflito]

Este tutorial requer o Visual Studio 2012 e o [Windows Phone 8 SDK][Windows Phone 8 SDK].

## <a name="download-app"></a>Baixar o projeto de amostra

Este tutorial é baseado no [Exemplo de código para tratamento de conflitos][Exemplo de código para tratamento de conflitos], que é um projeto Windows Phone 8 para o Visual Studio 2012.
A UI para este aplicativo é semelhante ao aplicativo no tutorial [Introdução aos dados offline][Introdução aos dados offline], exceto porque há uma nova coluna de dados em cada TodoItem.

![][0]

1.  Baixe a versão para Windows Phone do [Exemplo de código para tratamento de conflitos][Exemplo de código para tratamento de conflitos].

2.  Instale o [SQLite for Windows Phone 8][SQLite for Windows Phone 8] se ele não tiver sido instalado ainda.

3.  No Visual Studio 2012, abra o projeto transferido por download. Inclua uma referência a **SQLite for Windows Phone** em **Windows Phone** \> **Extensões**.

4.  No Visual Studio 2012, pressione a chave **F5** para criar e executar o aplicativo no depurador.

5.  No aplicativo, digite algum texto para alguns novos itens pendentes, em seguida, clique em **Salvar** para salvar cada um. Você também pode modificar a data de conclusão dos itens de tarefas pendentes que adicionar.

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

4.  Como antes, digite o texto na caixa de texto e clique em **Salvar** para salvar alguns novos itens pendentes. Isso salva os dados na tabela de sincronização local, mas não no servidor.

    ![][0]

5.  Para ver o estado atual de seu banco de dados, faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu serviço móvel.

 -   Se estiver usando o back-end do JavaScript para seu serviço móvel, clique na guia **Dados**, em seguida, clique na tabela **TodoWithDate**. Clique em **Procurar** para ver que a tabela ainda estará vazia, uma vez que não enviamos por push as alterações do aplicativo para o servidor.

        ![][1]

 -   Se estiver usando o back-end do .NET para seu serviço móvel, clique na guia **Configurar**, em seguida, clique em seu banco de dados SQL. Clique em **Gerenciar** na parte inferior da tela para fazer logon no Portal de Gerenciamento do Azure SQL para exibir seu banco de dados executando uma consulta SQL semelhante ao seguinte.

            SELECT * FROM todolist.todowithdate

        ![][2]

1.  Novamente no aplicativo, clique em **Enviar por Push**.

2.  No Portal de Gerenciamento, clique em **Atualizar** na tabela **TodoItem**. Agora você deve ver os dados que inseriu em seu aplicativo.

    ![][1]

3.  Mantenha **Emulador WVGA 512MB** ativo e em execução para a próxima seção onde você executará o aplicativo em dois emuladores para gerar um conflito.

## <a name="handle-conflict"></a>Atualizar os dados no back-end para criar um conflito

Em um cenário do mundo real, um conflito de sincronização ocorrerá quando um aplicativo enviar atualizações por push para um registro no banco de dados e, em seguida, outro aplicativo tentar enviar uma alteração por push para o mesmo registro usando uma versão desatualizada desse registro. Se uma instância do aplicativo tentar atualizar o mesmo registro sem efetuar pull no registro atualizado, ocorrerá um conflito e o conflito será capturado como um `MobileServicePreconditionFailedException` no aplicativo.

Nesta seção, você executará duas instâncias do aplicativo ao mesmo tempo para gerar um conflito.

1.  Se o **Emulador WVGA 512MB** ainda não estiver ativo e em execução, pressione **Ctrl+F5** para reativá-lo.

2.  No Visual Studio, altere o dispositivo de saída para **Emulador WVGA** e execute uma segunda instância do aplicativo no novo emulador pressionando **F5**.

    ![][2]

3.  Na segunda instância do aplicativo, clique em **Executar Pull** para sincronizar o armazenamento local com o banco de dados do serviço móvel. Ambas as instâncias do aplicativo devem ter os mesmos dados.

    ![][3]

4.  Na segunda instância do aplicativo, clique na caixa de seleção para concluir um dos itens, em seguida, clique em **Enviar por Push** para enviar por push sua mudança ao banco de dados remoto. Na captura de tela a seguir, **Escolher James** foi concluído indicando que James já foi escolhido. A primeira instância do aplicativo agora possui um registro desatualizado.

    ![][4]

5.  Na primeira instância do aplicativo, tente alterar a data para o registro desatualizado, em seguida, clique em **Enviar por Push** para tentar atualizar o banco de dados remoto com o registro desatualizado. Na captura de tela abaixo, tentamos planejar que James seja escolhido em **10/05/2014**.

    ![][5]

6.  Quando você clicar no botão **Enviar por Push** para confirmar a mudança de data, você verá uma caixa de diálogo indicando que o confllito foi detectado. Será solicitado que você resolva o conflito. Escolha uma das opções para resolver o conflito.

    No cenário mostrado abaixo, James já foi escolhido. Portanto, não há necessidade de planejar uma escolha para ele em **10/5/2014**. A opção **Usar versão do servidor** seria selecionada para que a primeira instância do aplicativo tenha esse registro atualizado com o registro do servidor.

    ![][6]

## Revisão do código para tratamento de conflitos de sincronização

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

  [Introdução aos dados offline]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [Baixar o projeto do Windows Phone]: #download-app
  [Adicionar uma coluna de data de conclusão para o banco de dados]: #add-column
  [Atualizando o banco de dados para serviços móveis de back-end do .NET]: #dotnet-backend
  [Atualizando o banco de dados para os serviços móveis do JavaScript]: #javascript-backend
  [Testar o aplicativo no serviço móvel]: #test-app
  [Atualizar manualmente os dados no back-end para criar um conflito]: #handle-conflict
  [Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
  [Exemplo de código para tratamento de conflitos]: http://go.microsoft.com/fwlink/?LinkId=398257
  [0]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
  [SQLite for Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
  [Como usar as Migrações Code First para atualizar o modelo de dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
  [2]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/vs-emulator-wvga.png
  [3]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-synced.png
  [4]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-item-completed.png
  [5]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-date-change.png
  [6]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-conflict-detected.png
