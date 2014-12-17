<properties urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Tratar conflitos com dados offline nos Serviços Móveis (Windows Phone) | Centro de desenvolvimento móvel" metaKeywords="" description="Learn how to use Azure Mobile Services handle conflicts when syncing offline data in your Windows phone application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling conflicts with offline data in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />


# Tratamento de conflitos com sincronização de dados offline nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="Windows Store C#">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone" class="current">Windows Phone</a>
<a href="/pt-br/documentation/articles/mobile-services-ios-handling-conflicts-offline-data" title="iOS">iOS</a>
</div>


<p>Este tópico mostra como sincronizar dados e tratar conflitos ao usar os recursos offline dos Serviços Móveis do Azure. Neste tutorial, você baixará um aplicativo que dá suporte a dados offline e online, integrará o serviço móvel ao aplicativo e fará logon no Portal de Gerenciamento do Azure para exibir e atualizar o banco de dados ao executar o aplicativo.
</p>

Esse tutorial baseia-se nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução aos dados offline]. Antes de começar este tutorial, você deve primeiro concluir a [Introdução aos dados offline].


Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Baixar o projeto Windows Phone] 
2. [Adicionar uma coluna de data de conclusão para o banco de dados]
  * [Atualizando o banco de dados para os serviços móveis de back-end do .NET] 
  * [Atualizando o banco de dados para serviços móveis JavaScrip]  
3. [Testar o aplicativo no serviço móvel]
4. [Atualizar manualmente os dados no back-end para criar um conflito]

Este tutorial requer o Visual Studio 2012 e o [SDK do Windows Phone 8].


## <a name="download-app"></a>Baixar o projeto de exemplo



Este tutorial é baseado no [Exemplo de código para tratamento de conflitos], que é um projeto do Windows Phone 8 para o Visual Studio 2012. 
A interface do usuário para esse aplicativo é semelhante ao aplicativo no tutorial [Introdução aos dados offline], exceto que há uma nova coluna para cada TodoItem.

![][0]


1. Baixe a versão para Windows Phone do[Exemplo de código para tratamento de conflitos]. 

2. Instale o [SQLite para Windows Phone 8] se ainda não estiver instalado.

3. No Visual Studio 2012, abra o projeto baixado. Inclua uma referência a **SQLite para Windows Phone** em **Windows Phone** > **Extensões**.

4. No Visual Studio 2012, pressione a chave **F5** para criar e executar o aplicativo no depurador.
 
5. No aplicativo, digite algum texto para alguns novos itens pendentes, em seguida, clique em **Salvar** para salvar cada um. Você também pode modificar a data de conclusão dos itens de tarefas pendentes que adicionar.


Observe que o aplicativo ainda não está conectado a nenhum serviço móvel, portanto, os botões **Push** e **Pull** gerarão exceções.


## <a name="add-column"></a>Adicionar uma coluna ao modelo de dados

Nesta seção, você atualizará o banco de dados do serviço móvel para incluir uma tabela TodoItem com uma coluna de data de conclusão. O aplicativo permite que você altere a data de conclusão de um item em tempo de execução para que possa gerar conflitos de sincronização em uma seção posterior deste tutorial. 

A classe `TodoItem` no exemplo está definida no MainPage.xaml.cs. Observe que a classe tem o seguinte atributo que se destina a operações de sincronização nessa tabela.

        [DataTable("TodoWithDate")]

Atualize seu banco de dados para incluir essa tabela.

### <a name="dotnet-backend"></a>Atualizando o banco de dados para serviços móveis de back-end do .NET 

Se estiver usando o back-end do .NET para o seu serviço móvel, siga estas etapas para atualizar o esquema do seu banco de dados.

1. Abra seu projeto de serviço móvel de back-end do .NET no Visual Studio.
2. No Gerenciador de Soluções para o Visual Studio, no seu projeto de serviço, expanda a pasta **Modelos** e abra ToDoItem.cs. Inclua a propriedade `DueDate` como mostrado a seguir.

          public class TodoItem : EntityData
          {
            public string Text { get; set; }
            public bool Complete { get; set; }
            public System.DateTime DueDate { get; set; }
          }


3. No Gerenciador de Soluções do Visual Studio, expanda a pasta **App_Start** e abra o arquivo WebApiConfig.cs. 

    No arquivo WebApiConfig.cs, observe que a classe inicializadora do banco de dados padrão é derivada da classe `DropCreateDatabaseIfModelChanges`. Isso significa que qualquer alteração no modelo resultará na tabela que está sendo descartada e recriada para acomodar o modelo novo. Portanto, os dados da tabela serão perdidos e a tabela será refeita. Modifique o método Seed do inicializador do banco de dados para que a inicialização `Seed()` funcione da seguinte maneira para inicializar a nova coluna DueDate. Salve o arquivo WebApiConfig.cs.

    >[WACOM.NOTE] Ao usar o inicializador de banco de dados padrão, o Entity Framework irá descartar e recriar o banco de dados sempre que detectar uma alteração no modelo de dados na definição do modelo Code First. Para fazer com que esse modelo de dados altere e mantenha os dados existentes no banco de dados, você deve usar as Migrações Code First. Para obter mais informações, consulte [Como usar as Migrações Code First para atualizar o modelo de dados](/pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations).


        new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
        new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

          

4. No Gerenciador de Soluções para o Visual Studio, expanda a pasta **Controladores** e abra ToDoItemController.cs. Renomeie a classe `TodoItemController` para `TodoWithDateController`. Isso irá alterar o ponto de extremidade REST para as operações da tabela. 

        public class TodoWithDateController : TableController<TodoItem>
    

5. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do serviço móvel de back-end .NET e clique em **Publicar** para publicar suas alterações.


### <a name="javascript-backend"></a>Atualizando o banco de dados para os serviços móveis de back-end do JavaScript

Para os serviços móveis de back-end do JavaScript, você adicionará uma nova tabela com o nome **TodoWithDate**. Para adicionar a tabela **TodoWithDate** nos serviços móveis de back-end do JavaScript, siga estas etapas.

  1. Faça logon no [Portal de Gerenciamento do Azure]. 

  2. Navegue até a guia **Dados** do seu serviço móvel. 

  3. Clique em **Criar** na parte inferior da página e crie uma nova tabela com o nome **TodoWithDate**. 


## <a name="test-app"></a>Testar o aplicativo no serviço móvel

Está na hora de testar o aplicativo nos Serviços Móveis.

1. No Portal de Gerenciamento do Azure, localize sua chave do aplicativo de serviço móvel clicando em **Gerenciar Chaves** na barra de comandos da guia **Painel**. Copie a **Chave do Aplicativo**.

2. No Gerenciador de Soluções do Visual Studio, abra o arquivo App.xaml.cs no projeto de exemplo do cliente. Altere a inicialização de **MobileServiceClient** para usar a URL do seu serviço móvel e a chave do aplicativo:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. No Visual Studio, pressione a tecla **F5** para compilar e executar o aplicativo.

4. Como antes, digite o texto na caixa de texto e clique em **Salvar** para salvar alguns novos itens pendentes. Isso salva os dados na tabela de sincronização local, mas não no servidor.

    ![][0]

5. Para ver o estado atual do banco de dados, faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis**, e, em seguida, clique no serviço móvel.

  * Se estiver usando o back-end do JavaScript para o serviço móvel, clique na guia **Dados** e, em seguida, clique na tabela **TodoWithDate**. Clique em **Procurar** para ver se a tabela ainda está vazia, uma vez que não enviamos por push as alterações do aplicativo para o servidor.

        ![][1]

  *  Se estiver usando o back-end do .NET para o serviço móvel, clique na guia **Configurar** e, em seguida, clique no seu Banco de Dados SQL. Clique em **Gerenciar** na parte inferior da tela para fazer logon no Portal de Gerenciamento do Azure SQL para exibir seu banco de dados executando uma consulta SQL semelhante à seguinte.
    
            SELECT * FROM todolist.todowithdate

        ![][2]

   	 

7. De volta ao aplicativo, clique em **Enviar por Push**.

8. No Portal de Gerenciamento, clique em **Atualizar** na tabela **TodoItem**. Agora você deve ver os dados que inseriu em seu aplicativo.

   	![][3]

9. Mantenha o **Emulador WVGA 512 MB** ativo e em execução para a próxima seção onde você executará o aplicativo em dois emuladores para gerar um conflito.

## <a name="handle-conflict"></a>Atualizar os dados no back-end para criar um conflito

Em um cenário do mundo real, um conflito de sincronização ocorre quando um aplicativo envia atualizações por push para um registro no banco de dados e, em seguida, outro aplicativo tenta enviar uma alteração por push para o mesmo registro usando uma versão desatualizada desse registro. Se uma instância do aplicativo tentar atualizar o mesmo registro sem efetuar pull no registro atualizado, ocorrerá um conflito e o conflito será capturado como um `MobileServicePreconditionFailedException` no aplicativo.  

Nesta seção, você executará duas instâncias do aplicativo ao mesmo tempo para gerar um conflito. 


1. Se o **Emulador WVGA 512 MB** ainda não estiver ativo e em execução, pressione **Ctrl+F5** para reativá-lo.

2. No Visual Studio, altere o dispositivo de saída para **Emulador WVGA** e execute uma segunda instância do aplicativo no novo emulador pressionando **F5**.
 
    ![][5]
 
   
3. Na segunda instância do aplicativo, clique em **Efetuar Pull** para sincronizar o armazenamento local com o banco de dados do serviço móvel. Ambas as instâncias do aplicativo devem ter os mesmos dados.
 
    ![][6]

4. Na segunda instância do aplicativo, clique na caixa de seleção para concluir um dos itens, em seguida, clique em **Enviar por Push** para enviar por push sua mudança ao banco de dados remoto. Na captura de tela a seguir, **Escolher James** foi concluído indicando que James já foi escolhido. A primeira instância do aplicativo agora possui um registro desatualizado.

    ![][9]

5. Na primeira instância do aplicativo, tente alterar a data para o registro desatualizado, em seguida, clique em **Enviar por Push** para tentar atualizar o banco de dados remoto com o registro desatualizado. Na captura de tela abaixo, tentamos planejar para que James seja escolhido em **10/05/2014**.

    ![][7]

6. Quando você clicar no botão **Enviar por Push** para confirmar a mudança de data, verá uma caixa de diálogo indicando que o conflito foi detectado. Será solicitado que você resolva o conflito. Escolha uma das opções para resolver o conflito.

    No cenário mostrado abaixo, James já foi escolhido. Portanto, não há necessidade de planejar uma escolha para ele em **10/5/2014**. A opção **Usar versão do servidor** seria selecionada para que a primeira instância do aplicativo tenha esse registro atualizado com o registro do servidor. 

    ![][8]

## Revisão do código para tratamento de conflitos de sincronização

Para configurar o recurso offline para detectar conflitos, você deve incluir uma coluna de versão no banco de dados local e no objeto de transferência de dados. A classe `TodoItem` tem o seguinte membro:

        [Version]
        public string Version { get; set; }

A coluna `__version` também é especificada no banco de dados local configurado no método `OnNavigatedTo()`.

Para resolver conflitos de sincronização offline em seu código, crie uma classe que implemente o arquivo `IMobileServiceSyncHandler`. Passe um objeto desse tipo na chamada para `InitializeAsync`:

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

A classe `SyncHandler` em **MainPage.xaml.cs** implementa `IMobileServiceSyncHandler`. O método `ExecuteTableOperationAsync` é chamado quando cada operação de push é enviada ao servidor. Se uma exceção do tipo `MobileServicePreconditionFailedException` for lançada, isso significa que há um conflito entre as versões local e remota de um item.

Para resolver conflitos em favor do item local, simplesmente repita a operação. Depois que ocorre um conflito, a versão local do item é atualizada para corresponder à versão do servidor, portanto, a execução de outra operação substituirá as alterações do servidor pelas alterações locais:

    await operation.ExecuteAsync(); 

Para resolver os conflitos em favor do item de servidor, simplesmente retorne de `ExecuteTableOperationAsync`. A versão local do objeto será descartada e substituída pelo valor do servidor.

Para parar a operação de push (mas manter as alterações na fila), use o método `AbortPush()`:

    operation.AbortPush();

Isso interromperá a operação atual de push mas manterá todas as alterações pendentes, inclusive a operação atual, se `AbortPush` for chamado de `ExecuteTableOperationAsync`. Na próxima vez que `PushAsync()` for chamado, essas alterações serão enviadas ao servidor. 

Quando um push é cancelado, `PushAsync` lança uma `MobileServicePushFailedException`, e a propriedade da exceção `PushResult.Status` terá o valor `MobileServicePushStatus.CancelledByOperation`. 


<!-- Anchors. -->
[Baixar o projeto do Windows Phone]: #download-app
[Criar o serviço móvel]: #create-service
[Adicionar uma coluna de data de conclusão para o banco de dados]: #add-column
[Atualizando o banco de dados para os serviços móveis de back-end do .NET]: #dotnet-backend
[Atualizando o banco de dados para os serviços móveis do JavaScript]: #javascript-backend
[Testar o aplicativo no serviço móvel]: #test-app
[Atualizar manualmente os dados no back-end para criar um conflito]: #handle-conflict
[Próximas etapas]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-empty.png
[2]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-empty-sql.png
[3]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
[5]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/vs-emulator-wvga.png
[6]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-synced.png
[7]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-date-change.png
[8]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-conflict-detected.png
[9]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-item-completed.png



<!-- URLs -->
[Tratando exemplos de conflitos de código]: http://go.microsoft.com/fwlink/?LinkId=398257
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started/
[Introdução aos dados offline]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-offline-data
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Tratando conflitos de banco de dados]: /pt-br/documentation/articles/mobile-services-windows-phone-handle-database-conflicts/#test-app
[SDK do Windows Phone 8]: http://go.microsoft.com/fwlink/p/?linkid=268374
[SQLite for Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-data/
