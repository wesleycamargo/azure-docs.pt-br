<properties linkid="develop-mobile-tutorials-get-started-offline-data-ios" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data in Mobile Services" authors="donnam,wesmc" editor="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam,wesmc" />

# Introdução à sincronização de dados offline nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="C# da Windows Store">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/pt-br/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS">iOS</a>
<a href="/pt-br/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
<a href="/pt-br/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

Este tópico mostra como usar os recursos offline dos Serviços Móveis do Azure. Esses recursos permitem que você interaja com um banco de dados local quando estiver em um cenário offline com seu Serviço Móvel. Os recursos offline permitem que você sincronize suas alterações locais com o serviço móvel quando estiver online novamente.

Neste tutorial, você irá atualizar o aplicativo do tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] ou [Introdução aos Dados][Introdução aos Dados] para oferecer suporte aos recursos offline dos Serviços Móveis do Azure. Você irá adicionar dados em um cenário desconectado offline, sincronizar esses itens ao banco de dados online e, em seguida, fazer logon no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.

> [WACOM.NOTE] O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados em um aplicativo da Windows Store. Desse modo, este tópico explica muitas das etapas que são concluídas para você no Guia de início rápido dos Serviços Móveis. Se esta for sua primeira experiência com os Serviços Móveis, primeiro conclua o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].
>
> Para concluir este tutorial, será necessária uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Atualizar o aplicativo para dar suporte aos recursos offline][Atualizar o aplicativo para dar suporte aos recursos offline]
2.  [Testar o aplicativo conectado ao Serviço Móvel][Testar o aplicativo conectado ao Serviço Móvel]

Este tutorial exige o seguinte:

-   XCode 4.5 e iOS 6.0 (ou versões posteriores)
-   Visual Studio com a [extensão Xamarin][extensão Xamarin] **ou** [Xamarin Studio][Xamarin Studio] no OS X
-   Conclusão do tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] ou [Introdução aos Dados][Introdução aos Dados]
-   [Azure Mobile Services SDK versão 1.3.0-alpha3 (ou posterior)][Azure Mobile Services SDK versão 1.3.0-alpha3 (ou posterior)]
-   [Azure Mobile Services SQLite Store versão 1.0.0-alpha2 (ou posterior)][Azure Mobile Services SQLite Store versão 1.0.0-alpha2 (ou posterior)]

> [WACOM.NOTE] As instruções abaixo presumem que você esteja usando o Visual Studio 2012 ou uma versão posterior com a extensão Xamarin. Se estiver utilizando o Xamarin Studio no OS X, a maioria das instruções é igual, porém, você deve também instalar o [Suplemento NuGet para Xamarin][Suplemento NuGet para Xamarin] para que possa adicionar com facilidade o pré-lançamento dos pacotes do NuGet para Serviços Móveis ao seu projeto.

## <a name="enable-offline-app"></a>Atualizar o aplicativo para dar suporte aos recursos offline

Os recursos offline dos Serviços Móveis do Azure permitem que você interaja com um banco de dados local quando estiver em um cenário offline com seu Serviço Móvel. Para usar esses recursos em seu aplicativo, você deve inicializar `MobileServiceClient.SyncContext` para um armazenamento local. Faça referência à sua tabela através da interface `IMobileServiceSyncTable`.

1.  No Visual Studio, abra o projeto que você concluiu no tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] ou [Introdução aos Dados][Introdução aos Dados]. No Gerenciador de Soluções, remova a referência ao **Azure Mobile Services SDK** em **Componentes**.

2.  Instale o pacote de pré-lançamento do Mobile Services SQLiteStore usando o seguinte comando no Console do Gerenciador de Pacotes:

        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    Isso também instalará todas as dependências necessárias.

3.  No nó de referências, remova as referências a `System.IO`, `System.Runtime` e `System.Threading.Tasks`.

### Editar o arquivo QSTodoService.cs

Edite a classe `QSTodoService` para permitir o uso dos recursos offline dos Serviços Móveis com um repositório local do SQLite.

1.  Adicione o seguinte usando as instruções na parte superior do arquivo.

        using Microsoft.WindowsAzure.MobileServices; 
        using Microsoft.WindowsAzure.MobileServices.Sync; 
        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;

2.  Altere o tipo do membro `todoTable` de `IMobileServiceTable` para `IMobileServicesSyncTable`

        IMobileServiceSyncTable<ToDoItem> todoTable; 

3.  No construtor para `QSTodoService`, altere o inicializador para `todoTable`:

        todoTable = client.GetSyncTable <ToDoItem> ();

4.  No construtor para `QSTodoService`, adicione uma chamada para `SQLitePCL.CurrentPlatform.Init()` como a segunda linha do código:

        QSTodoService ()
        {
            CurrentPlatform.Init ();
            SQLitePCL.CurrentPlatform.Init(); // add this line

            // Initialize the Mobile Service client with your URL and key
            client = new MobileServiceClient (applicationURL, applicationKey, this);

            // Create an MSTable instance to allow us to work with the TodoItem table
            todoTable = client.GetSyncTable <ToDoItem> ();
        }

5.  Na classe `QSTodoService`, defina um novo método `InitializeAsync`:

        public async Task InitializeStoreAsync()
        {
            string path = "syncstore.db";
            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();
            await client.SyncContext.InitializeAsync(store);
        }

6.  Na classe `QSTodoService`, defina um novo método `SyncAsync`:

        public async Task SyncAsync()
        {
            try
            {
                await this.client.SyncContext.PushAsync();
                await this.todoTable.PullAsync();
            }
            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

### Editar ToDoItem.cs

-   Adicione a instrução de uso:

        using Microsoft.WindowsAzure.MobileServices; 

-   Adicione os seguintes membros à classe `ToDoItem`:

        [Version]
        public string Version { get; set; }


        public override string ToString()
        {
            return "Text: " + Text + "\nComplete: " + Complete + "\n";
        }

### Editar QSTodoListViewController.cs

Modifique `QSTodoListViewController` para chamar o novo método `SyncAsync` quando o usuário realizar o gesto de atualização.

1.  Adicione uma chamada a `InitializeStoreAsync` em `ViewDidLoad()`, após a inicialização de `todoService`:

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;
            await todoService.InitializeStoreAsync();

            ...    // the rest of the code in the method is unchanged
        }

2.  Modifique o método `AddRefreshControl` para chamar `SyncAsync` antes da chamada a `RefreshAsync`:

        RefreshControl.ValueChanged += async (sender, e) => {
            await todoService.SyncAsync();
            await RefreshAsync();
        }; 

<!--  DM: commenting this out because this tutorial doesn't show OC conflict handling ### Edit ToDoItem.cs   Modify the strongly-type data class to add a version field  1. In the top of the file, add the using statement:           using Microsoft.WindowsAzure.MobileServices;   2. Add the following members to the class `ToDoItem`:           [Version]         public string Version { get; set; }                  public override string ToString()         {             return "Text: " + Text + "\nComplete: " + Complete + "\n";         }  -->

## <a name="test-online-app"></a>Testar o aplicativo

Nesta seção, você testará o método `SyncAsync`, que sincroniza o repositório local com o banco de dados do serviço móvel.

1.  No Visual Studio, pressione o botão **Executar** para criar o projeto e iniciar o aplicativo no emulador do iPhone, que é o padrão para este projeto.

2.  Observe que a lista de itens no aplicativo está vazia. Como resultado das alterações de código na seção anterior, o aplicativo não lê mais itens do serviço móvel, mas sim do repositório local.

3.  Adicione itens à Lista de Tarefas Pendentes.

    ![][0]

4.  Faça logon no Portal de Gerenciamento do Microsoft Azure e observe o banco de dados para seu serviço móvel. Se o serviço usar o back-end do JavaScript para serviços móveis, você poderá procurar os dados na guia **Dados** do serviço móvel. Se estiver usando o back-end do .NET para o serviço móvel, você poderá clicar no botão **Gerenciar** do banco de dados no SQL Azure Extension para executar uma consulta na tabela.

    Observe que os dados não foram sincronizados entre o banco de dados e o repositório local.

5.  No aplicativo, faça o gesto de atualização puxando a lista de itens para baixo. Isso faz com que o aplicativo chame `MobileServiceClient.SyncContext.PushAsync` e `IMobileServiceSyncTable.PullAsync()` e depois `RefreshTodoItems` para atualizar o aplicativo com os itens do repositório local.

    A operação push faz com que o banco de dados do serviço móvel receba os dados do repositório. Ela é executada fora do `MobileServiceClient.SyncContext` em vez do `IMobileServicesSyncTable` e envia por push as alterações em todas as tabelas associadas a esse contexto de sincronização. Isso é para cobrir cenários onde há relacionamentos entre tabelas.

    Em contraste, a operação pull recupera os registros somente da tabela especificada. Se houver operações pendentes para essa tabela no contexto de sincronização, uma operação `PushAsync` será chamada implicitamente pelo Mobile Services SDK.

    ![][1]

    ![][2]

## Resumo

Para oferecer suporte aos recursos offline dos serviços móveis, usamos a interface `IMobileServiceSyncTable` e inicializamos `MobileServiceClient.SyncContext` com um repositório local. Nesse caso, o repositório local era um banco de dados SQLite.

As operações CRUD normais nos serviços móveis funcionam como se o aplicativo ainda estivesse conectado, mas todas as operações ocorrem no repositório local.

Quando desejamos sincronizar o armazenamento local com o servidor, usamos os métodos `IMobileServiceSyncTable.PullAsync` e `MobileServiceClient.SyncContext.PushAsync`.

-   Para executar push das mudanças no servidor, chamamos `IMobileServiceSyncContext.PushAsync()`. Esse método é um membro de `IMobileServicesSyncContext` em vez da tabela de sincronização porque enviará as alterações a todas as tabelas:

    Somente os registros que foram modificados, de alguma forma, localmente (por meio de operações CUD) serão enviados ao servidor.

-   Para executar pull de dados de uma tabela no servidor para o aplicativo, chamamos `IMobileServiceSyncTable.PullAsync`.

    Uma pull sempre envia um push primeiro.

    Também há sobrecargas de **PullAsync()** que permitem que uma consulta seja especificada. Observe que, na versão de visualização do suporte offline para os Serviços Móveis, **PullAsync** lerá todas as linhas na tabela correspondente (ou consulta)--ele não tenta ler apenas as linhas mais recentes do que a última sincronização, por exemplo. Se as linhas já existirem na tabela de sincronização local, elas permanecerão inalteradas.

## Próximas etapas

<!--* [Handling conflicts with offline support for Mobile Services] -->

-   [Como usar o cliente Componente Xamarin para os Serviços Móveis do Azure][Como usar o cliente Componente Xamarin para os Serviços Móveis do Azure]

<!-- Anchors. -->
<!-- Images -->
<!-- URLs. -->

  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started/
  [Introdução aos Dados]: /pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28
  [Atualizar o aplicativo para dar suporte aos recursos offline]: #enable-offline-app
  [Testar o aplicativo conectado ao Serviço Móvel]: #test-online-app
  [extensão Xamarin]: http://xamarin.com/visual-studio
  [Xamarin Studio]: http://xamarin.com/download
  [Suplemento NuGet para Xamarin]: https://github.com/mrward/monodevelop-nuget-addin
  [0]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-startup-ios.png
  [1]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-completed-ios.png
  [2]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-data-browse.png
  [Como usar o cliente Componente Xamarin para os Serviços Móveis do Azure]: /pt-br/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/
