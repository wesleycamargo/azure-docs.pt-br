---
title: Conectar ao Armazenamento do Azure em seu aplicativo Xamarin Forms
description: "Adicionar imagens ao aplicativo móvel Xamarin.Forms de lista de tarefas pendentes conectando-se ao Armazenamento de blobs do Azure"
documentationcenter: xamarin
author: adrianhall
manager: erikre
editor: 
services: app-service\mobile
ms.assetid: bb1a1437-0a31-46bb-9237-1b692b0ede21
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: c8568846837f404eee0293be284c70bd27f06380


---
# <a name="connect-to-azure-storage-in-your-xamarinforms-app"></a>Conectar ao Armazenamento do Azure em seu aplicativo Xamarin Forms
## <a name="overview"></a>Visão geral
O cliente de Aplicativos Móveis do Azure e o SDK do servidor oferecem suporte a sincronização offline de dados estruturados com operações CRUD com base no ponto de extremidade /tables. Geralmente, esses dados são armazenados em um banco de dados ou armazenamento semelhante, e geralmente esses armazenamentos de dados não podem armazenar grandes quantidades de dados binários com eficiência. Além disso, alguns aplicativos têm dados relacionados armazenados em outro lugar (por exemplo, armazenamento de blobs, compartilhamentos de arquivos), e ser capaz de criar associações entre registros no ponto de extremidade /tables e em outros dados é bastante útil.

Este tópico mostra como adicionar suporte para imagens ao início rápido de lista de tarefas dos Aplicativos Móveis. Primeiro, você precisa concluir o tutorial [Criar um aplicativo Xamarin.Forms].

Neste tutorial, você criará uma conta de armazenamento e adicionará uma cadeia de conexão ao back-end do Aplicativo Móvel. Em seguida, você adicionará uma nova herança do novo tipo de Aplicativos Móveis `StorageController<T>` ao projeto de seu servidor.

> [!TIP]
> Este tutorial tem um [exemplo complementar](https://azure.microsoft.com/documentation/samples/app-service-mobile-dotnet-todo-list-files/) disponível, que pode ser implantado em sua própria conta do Azure. 
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* Conclua o tutorial [Criar um aplicativo Xamarin.Forms] , que lista outros pré-requisitos. Este artigo usa o aplicativo concluído desse tutorial.

> [!NOTE]
> Se você quiser ter uma introdução ao Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/mobile/). Lá, você pode criar imediatamente um aplicativo móvel de curta duração inicial no Serviço de Aplicativo – sem cartão de crédito e sem compromissos.
> 
> 

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
1. Crie uma conta de armazenamento seguindo o tutorial [Criar uma conta de armazenamento do Azure]. 
2. No Portal do Azure, navegue até a conta de armazenamento recém-criada e clique no ícone **Chaves** . Copie a **Cadeia de Conexão Primária**.
3. Navegue até seu back-end de aplicativo móvel. Em **Todas as Configurações** -> **Configurações do Aplicativo** -> **Cadeias de Conexão**, crie uma nova chave chamada `MS_AzureStorageAccountConnectionString` e use o valor copiado de sua conta de armazenamento. Use **Personalizado** como o tipo de chave.

## <a name="add-a-storage-controller-to-the-server"></a>Adicionar um controlador de armazenamento ao servidor
Você precisa adicionar um novo controlador ao projeto do seu servidor, que responderá às solicitações de um token SAS para o armazenamento do Azure, bem como retornará uma lista de arquivos que correspondem a um registro:

* [Adicionar um controlador de armazenamento ao projeto de seu servidor](#add-controller-code)
* [Rotas registradas pelo controlador de armazenamento](#routes-registered)
* [Comunicação de cliente e servidor](#client-communication)

### <a name="a-nameadd-controller-codeaadd-a-storage-controller-to-your-server-project"></a><a name="add-controller-code"></a>Adicionar um controlador de armazenamento ao projeto de seu servidor
1. No Visual Studio, abra o projeto de servidor .NET. Adicione o pacote NuGet [Microsoft.Azure.Mobile.Server.Files]. Lembre-se de selecionar a opção **Incluir pré-lançamento**.
2. No Visual Studio, abra o projeto de servidor .NET. Clique com o botão direito do mouse na pasta **Controladores** e selecione **Adicionar** -> **Controlador** -> **Controlador da API Web 2 - Vazio**. Dê ao controlador o nome `TodoItemStorageController`.
3. Adicione as seguintes instruções using:
   
        using Microsoft.Azure.Mobile.Server.Files;
        using Microsoft.Azure.Mobile.Server.Files.Controllers;
4. Altere a classe base para `StorageController`:
   
        public class TodoItemStorageController : StorageController<TodoItem>
5. Adicione os seguintes métodos à classe:
   
        [HttpPost]
        [Route("tables/TodoItem/{id}/StorageToken")]
        public async Task<HttpResponseMessage> PostStorageTokenRequest(string id, StorageTokenRequest value)
        {
            StorageToken token = await GetStorageTokenAsync(id, value);
   
            return Request.CreateResponse(token);
        }
   
        // Get the files associated with this record
        [HttpGet]
        [Route("tables/TodoItem/{id}/MobileServiceFiles")]
        public async Task<HttpResponseMessage> GetFiles(string id)
        {
            IEnumerable<MobileServiceFile> files = await GetRecordFilesAsync(id);
   
            return Request.CreateResponse(files);
        }
   
        [HttpDelete]
        [Route("tables/TodoItem/{id}/MobileServiceFiles/{name}")]
        public Task Delete(string id, string name)
        {
            return base.DeleteFileAsync(id, name);
        }
6. Atualizar a configuração da API Web para configurar o roteamento de atributo. Em **Startup.MobileApp.cs**, adicione a seguinte linha ao método `ConfigureMobileApp()`, após a definição da variável `config`:
   
        config.MapHttpAttributeRoutes();
7. Publique o seu projeto de servidor em seu back-end de aplicativo móvel.

### <a name="a-nameroutes-registeredaroutes-registered-by-the-storage-controller"></a><a name="routes-registered"></a>Rotas registradas pelo controlador de armazenamento
O novo `TodoItemStorageController` expõe dois sub-recursos sob o registro que gerencia:

* StorageToken
  
  * HTTP POST: cria um token de armazenamento
    
      `/tables/TodoItem/{id}/MobileServiceFiles`
* MobileServiceFiles
  
  * HTTP GET: recupera uma lista de arquivos associados ao registro
    
      `/tables/TodoItem/{id}/MobileServiceFiles`
  * HTTP DELETE: exclui o arquivo especificado no identificador de recurso do arquivo
    
      `/tables/TodoItem/{id}/MobileServiceFiles/{fileid}`

### <a name="a-nameclient-communicationaclient-and-server-communication"></a><a name="client-communication"></a>Comunicação de cliente e servidor
Observe que o `TodoItemStorageController`*não* tem uma rota para carregar ou baixar um blob. Isso ocorre porque um cliente móvel interage *diretamente* com o Armazenamento de Blobs para executar essas operações, depois de obter primeiro um token SAS (Assinatura de Acesso Compartilhado) para acessar um contêiner ou blob específico com segurança. Esse é um design arquitetônico importante, pois, de outra forma, o acesso ao armazenamento ficaria limitado pela escalabilidade e disponibilidade do back-end móvel. Em vez disso, conectando-se diretamente ao Armazenamento do Azure, o cliente móvel pode aproveitar seus recursos, como o particionamento automático e a distribuição geográfica.

Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento. Isso significa que você pode conceder a um cliente permissões limitadas a objetos na sua conta de armazenamento por um período especificado e com um conjunto determinado de permissões, sem precisar compartilhar suas chaves de acesso de conta. Para saber mais, confira as [Noções básicas das Assinaturas de Acesso Compartilhado].

O diagrama a seguir mostra as interações entre cliente e servidor. Antes de carregar um arquivo, o cliente solicita um token SAS do serviço. O serviço usa a cadeia de conexão de armazenamento para gerar uma nova SAS que, em seguida, retorna ao cliente. A SAS é limitada por tempo e restringe permissões para um determinado arquivo ou contêiner. O cliente móvel usa esse SAS e o SDK do cliente de Armazenamento do Azure para carregar o arquivo no armazenamento de blobs.

![Solicitar um token SAS](./media/app-service-mobile-xamarin-forms-blob-storage/storage-token-diagram.png)

## <a name="update-your-client-app-to-add-image-support"></a>Atualizar o aplicativo cliente para adicionar suporte de imagem
Abra o projeto de início rápido do Xamarin.Forms no Visual Studio ou no Xamarin Studio. Você instalará pacotes NuGet, atualizará o projeto de biblioteca portátil e os projetos de cliente iOS, Android e Windows:

* [Adicionar pacotes NuGet](#add-nuget)
* [Adicionar interface IPlatform](#add-iplatform)
* [Adicionar classe FileHelper](#add-filehelper)
* [Adicionar um manipulador de sincronização de arquivo](#file-sync-handler)
* [Atualizar TodoItemManager](#update-todoitemmanager)
* [Adicionar uma exibição de detalhes](#add-details-view)
* [Atualizar exibição principal ](#update-main-view)
* [Atualizar os projetos do Android](#update-android), [do iOS](#update-ios) e [do Windows](#update-windows)

> [!NOTE]
> Este tutorial contém apenas instruções para as plataformas Android, iOS e Windows Store, não para o Windows Phone.
> 
> 

### <a name="a-nameadd-nugetaadd-nuget-packages"></a><a name="add-nuget"></a>Adicionar pacotes NuGet
Clique com o botão direito do mouse na solução e selecione **Gerenciar pacotes NuGet para a solução**. Adicione os seguintes pacotes NuGet a **todos** os projetos na solução. Lembre-se de marcar a opção **Incluir pré-lançamento**.

* [Microsoft.Azure.Mobile.Client.Files]
* [Microsoft.Azure.Mobile.Client.SQLiteStore]
* [PCLStorage]

Para sua conveniência, este exemplo usa a biblioteca [PCLStorage] , mas ela não é exigida pelo SDK do cliente de Aplicativos Móveis do Azure.

[PCLStorage]: https://www.nuget.org/packages/PCLStorage/

### <a name="a-nameadd-iplatformaadd-iplatform-interface"></a><a name="add-iplatform"></a>Adicionar interface IPlatform
Crie uma nova interface `IPlatform` no projeto da biblioteca portátil principal. Isso segue o padrão [Xamarin.Forms DependencyService] para carregar a classe específica da plataforma correta no tempo de execução. Posteriormente, você adicionará implementações específicas de plataforma em cada um dos projetos de cliente.

1. Adicione as seguintes instruções using:
   
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Sync;
2. Substitua a implementação pelo seguinte:
   
        public interface IPlatform
        {
            Task <string> GetTodoFilesPathAsync();
   
            Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata);
   
            Task<string> TakePhotoAsync(object context);
   
            Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename);
        }

### <a name="a-nameadd-filehelperaadd-filehelper-class"></a><a name="add-filehelper"></a>Adicionar classe FileHelper
1. Crie uma nova classe `FileHelper` no projeto da biblioteca portátil principal. Adicione as seguintes instruções using:
   
        using System.IO;
        using PCLStorage;
        using System.Threading.Tasks;
        using Xamarin.Forms;
2. Adicione a definição de classe:
   
        public class FileHelper
        {
            public static async Task<string> CopyTodoItemFileAsync(string itemId, string filePath)
            {
                IFolder localStorage = FileSystem.Current.LocalStorage;
   
                string fileName = Path.GetFileName(filePath);
                string targetPath = await GetLocalFilePathAsync(itemId, fileName);
   
                var sourceFile = await localStorage.GetFileAsync(filePath);
                var sourceStream = await sourceFile.OpenAsync(FileAccess.Read);
   
                var targetFile = await localStorage.CreateFileAsync(targetPath, CreationCollisionOption.ReplaceExisting);
   
                using (var targetStream = await targetFile.OpenAsync(FileAccess.ReadAndWrite)) {
                    await sourceStream.CopyToAsync(targetStream);
                }
   
                return targetPath;
            }
   
            public static async Task<string> GetLocalFilePathAsync(string itemId, string fileName)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
   
                string recordFilesPath = Path.Combine(await platform.GetTodoFilesPathAsync(), itemId);
   
                    var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(recordFilesPath);
                    if (checkExists == ExistenceCheckResult.NotFound) {
                        await FileSystem.Current.LocalStorage.CreateFolderAsync(recordFilesPath, CreationCollisionOption.ReplaceExisting);
                    }
   
                return Path.Combine(recordFilesPath, fileName);
            }
   
            public static async Task DeleteLocalFileAsync(Microsoft.WindowsAzure.MobileServices.Files.MobileServiceFile fileName)
            {
                string localPath = await GetLocalFilePathAsync(fileName.ParentId, fileName.Name);
                var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(localPath);
   
                if (checkExists == ExistenceCheckResult.FileExists) {
                    var file = await FileSystem.Current.LocalStorage.GetFileAsync(localPath);
                    await file.DeleteAsync();
                }
            }
        }

### <a name="a-namefile-sync-handlera-add-a-file-sync-handler"></a><a name="file-sync-handler"></a> Adicionar um manipulador de sincronização de arquivo
Crie uma nova classe `TodoItemFileSyncHandler` no projeto da biblioteca portátil principal. Essa classe contém retornos de chamada do SDK do Azure a fim de notificar seu código quando um arquivo é adicionado ou removido.

O SDK do cliente móvel do Azure não armazena nenhum dado do arquivo: o SDK do cliente invoca a implementação do `IFileSyncHandler` que, por sua vez, determina se os arquivos são armazenados no dispositivo local e de que maneira.

1. Adicione as seguintes instruções using:
   
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Xamarin.Forms;
2. Substitua a definição de classe pelo seguinte: 
   
        public class TodoItemFileSyncHandler : IFileSyncHandler
        {
            private readonly TodoItemManager todoItemManager;
   
            public TodoItemFileSyncHandler(TodoItemManager itemManager)
            {
                this.todoItemManager = itemManager;
            }
   
            public Task<IMobileServiceFileDataSource> GetDataSource(MobileServiceFileMetadata metadata)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
                return platform.GetFileDataSource(metadata);
            }
   
            public async Task ProcessFileSynchronizationAction(MobileServiceFile file, FileSynchronizationAction action)
            {
                if (action == FileSynchronizationAction.Delete) {
                    await FileHelper.DeleteLocalFileAsync(file);
                }
                else { // Create or update. We're aggressively downloading all files.
                    await this.todoItemManager.DownloadFileAsync(file);
                }
            }
        }

### <a name="a-nameupdate-todoitemmanageraupdate-todoitemmanager"></a><a name="update-todoitemmanager"></a>Atualizar TodoItemManager
1. Em **TodoItemManager.cs**, remova a marca de comentário da linha `#define OFFLINE_SYNC_ENABLED`.
2. Em **TodoItemManager.cs**, adicione as seguintes instruções using:
   
        using System.IO;
        using Xamarin.Forms;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Eventing;
3. No construtor do `TodoItemManager`, adicione o seguinte após a chamada para `DefineTable()`:
   
        // Initialize file sync
        this.client.InitializeFileSyncContext(new TodoItemFileSyncHandler(this), store);
4. No construtor, substitua a chamada para `InitializeAsync` pelo seguinte. Isso garantirá a existência de retornos de chamada quando os registros forem modificados no repositório local. O recurso de sincronização de arquivos usa esses retornos de chamada para disparar seu manipulador de sincronização de arquivo.
   
        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);
5. No `SyncAsync()`, adicione o seguinte após a chamada para `PushAsync()`:
   
        await this.todoTable.PushFileChangesAsync();
6. Adicione os métodos a seguir para `TodoItemManager`:
   
        internal async Task DownloadFileAsync(MobileServiceFile file)
        {
            var todoItem = await todoTable.LookupAsync(file.ParentId);
            IPlatform platform = DependencyService.Get<IPlatform>();
   
            string filePath = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name); 
            await platform.DownloadFileAsync(this.todoTable, file, filePath);
        }
   
        internal async Task<MobileServiceFile> AddImage(TodoItem todoItem, string imagePath)
        {
            string targetPath = await FileHelper.CopyTodoItemFileAsync(todoItem.Id, imagePath);
            return await this.todoTable.AddFileAsync(todoItem, Path.GetFileName(targetPath));
        }
   
        internal async Task DeleteImage(TodoItem todoItem, MobileServiceFile file)
        {
            await this.todoTable.DeleteFileAsync(file);
        }
   
        internal async Task<IEnumerable<MobileServiceFile>> GetImageFilesAsync(TodoItem todoItem)
        {
            return await this.todoTable.GetFilesAsync(todoItem);
        }

### <a name="a-nameadd-details-viewaadd-a-details-view"></a><a name="add-details-view"></a>Adicionar uma exibição de detalhes
Nesta seção, você adicionará uma nova exibição de detalhes para um item de tarefa. A exibição é criada quando o usuário seleciona um item de tarefa e permite que novas imagens sejam adicionados a um item.

1. Adicione uma nova classe **TodoItemImage** ao projeto de biblioteca portátil com a seguinte implementação:
   
        public class TodoItemImage : INotifyPropertyChanged
        {
            private string name;
            private string uri;
   
            public MobileServiceFile File { get; private set; }
   
            public string Name
            {
                get { return name; }
                set
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }
   
            public string Uri
            {
                get { return uri; }      
                set
                {
                    uri = value;
                    OnPropertyChanged(nameof(Uri));
                }
            }
   
            public TodoItemImage(MobileServiceFile file, TodoItem todoItem)
            {
                Name = file.Name;
                File = file;
   
                FileHelper.GetLocalFilePathAsync(todoItem.Id, file.Name).ContinueWith(x => this.Uri = x.Result);
            }
   
            public event PropertyChangedEventHandler PropertyChanged;
   
            private void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }
2. Edite **App.cs**. Substitua a inicialização de `MainPage` pelo seguinte:
   
        MainPage = new NavigationPage(new TodoList());
3. No **App.cs**, adicione a seguinte propriedade:
   
        public static object UIContext { get; set; }
4. Clique com o botão direito do mouse no projeto de biblioteca portátil e selecione **Adicionar** -> **Novo Item** -> **Plataforma cruzada** -> **Página de Formulários XAML**. Dê o nome `TodoItemDetailsView`à exibição.
5. Abra **TodoItemDetailsView.xaml** e substitua o corpo da ContentPage pelo seguinte:
   
          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Button Clicked="OnAdd" Text="Add image"></Button>
            <ListView x:Name="imagesList"
                      ItemsSource="{Binding Images}"
                      IsPullToRefreshEnabled="false"
                      Grid.Row="2">
              <ListView.ItemTemplate>
                <DataTemplate>
                  <ImageCell ImageSource="{Binding Uri}"
                             Text="{Binding Name}">
                  </ImageCell>
                </DataTemplate>
              </ListView.ItemTemplate>
            </ListView>
          </Grid>
6. Edite **TodoItemDetailsView.xaml.cs** e adicione as seguintes instruções using:
   
        using System.Collections.ObjectModel;
        using Microsoft.WindowsAzure.MobileServices.Files;
7. Substitua a implementação de `TodoItemDetailsView` pelo seguinte:
   
        public partial class TodoItemDetailsView : ContentPage
        {
            private TodoItemManager manager;
   
            public TodoItem TodoItem { get; set; }        
            public ObservableCollection<TodoItemImage> Images { get; set; }
   
            public TodoItemDetailsView(TodoItem todoItem, TodoItemManager manager)
            {
                InitializeComponent();
                this.Title = todoItem.Name;
   
                this.TodoItem = todoItem;
                this.manager = manager;
   
                this.Images = new ObservableCollection<TodoItemImage>();
                this.BindingContext = this;
            }
   
            public async Task LoadImagesAsync()
            {
                IEnumerable<MobileServiceFile> files = await this.manager.GetImageFilesAsync(TodoItem);
                this.Images.Clear();
   
                foreach (var f in files) {
                    var todoImage = new TodoItemImage(f, this.TodoItem);
                    this.Images.Add(todoImage);
                }
            }
   
            public async void OnAdd(object sender, EventArgs e)
            {
                IPlatform mediaProvider = DependencyService.Get<IPlatform>();
                string sourceImagePath = await mediaProvider.TakePhotoAsync(App.UIContext);
   
                if (sourceImagePath != null) {
                    MobileServiceFile file = await this.manager.AddImage(this.TodoItem, sourceImagePath);
   
                    var image = new TodoItemImage(file, this.TodoItem);
                    this.Images.Add(image);
                }
            }
        }

### <a name="a-nameupdate-main-viewaupdate-the-main-view"></a><a name="update-main-view"></a>Atualizar exibição principal
Atualize a exibição principal para abrir a exibição de detalhes quando um item de tarefas for selecionado.

Em **TodoList.xaml.cs**, substitua a implementação de `OnSelected` pelo seguinte:

    public async void OnSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var todo = e.SelectedItem as TodoItem;

        if (todo != null) {
            var detailsView = new TodoItemDetailsView(todo, manager);
            await detailsView.LoadImagesAsync();
            await Navigation.PushAsync(detailsView);
        }

        todoList.SelectedItem = null;
    }

### <a name="a-nameupdate-androidaupdate-the-android-project"></a><a name="update-android"></a>Atualizar o projeto do Android
Adicione o código específico da plataforma ao projeto do Android, incluindo o código para baixar um arquivo e usar a câmera para capturar uma nova imagem. 

Esse código usa o [DependencyService](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/) do Xamarin.Forms para carregar a classe específica da plataforma certa no tempo de execução.

1. Adicione o componente **Xamarin.Mobile** ao projeto do Android.
2. Adicione uma nova classe `DroidPlatform` com a seguinte implementação. Substitua "YourNamespace" pelo namespace principal de seu projeto.
   
        using System;
        using System.IO;
        using System.Threading.Tasks;
        using Android.Content;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;
   
        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.Droid.DroidPlatform))]
        namespace YourNamespace.Droid
        {
            public class DroidPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }
   
                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }
   
                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var uiContext = context as Context;
                        if (uiContext != null) {
                            var mediaPicker = new MediaPicker(uiContext);
                            var photo = await mediaPicker.TakePhotoAsync(new StoreCameraMediaOptions());
   
                            return photo.Path;
                        }
                    }
                    catch (TaskCanceledException) {
                    }
   
                    return null;
                }
   
                public Task<string> GetTodoFilesPathAsync()
                {
                    string appData = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
                    string filesPath = Path.Combine(appData, "TodoItemFiles");
   
                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }
   
                    return Task.FromResult(filesPath);
                }
            }
        }
3. Edite **MainActivity.cs**. No `OnCreate`, adicione o seguinte antes da chamada para `LoadApplication()`:
   
        App.UIContext = this;

### <a name="a-nameupdate-iosaupdate-the-ios-project"></a><a name="update-ios"></a>Atualizar o projeto do iOS
Adicione o código específico da plataforma ao projeto do iOS.

1. Adicione o componente **Xamarin.Mobile** ao projeto do iOS.
2. Adicione uma nova classe `TouchPlatform` com a seguinte implementação. Substitua "YourNamespace" pelo namespace principal de seu projeto.
   
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;
   
        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.iOS.TouchPlatform))]
        namespace YourNamespace.iOS
        {
            class TouchPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }
   
                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }
   
                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var mediaPicker = new MediaPicker();
                        var mediaFile = await mediaPicker.PickPhotoAsync();
                        return mediaFile.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
   
                public Task<string> GetTodoFilesPathAsync()
                {
                    string filesPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "TodoItemFiles");
   
                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }
   
                    return Task.FromResult(filesPath);
                }
            }
        }
3. Edite **AppDelegate.cs** e remova a marca de comentário da chamada para `SQLitePCL.CurrentPlatform.Init()`.

### <a name="a-nameupdate-windowsaupdate-the-windows-project"></a><a name="update-windows"></a>Atualizar o projeto do Windows
1. Instale a extensão do Visual Studio [SQLite para Windows 8.1](http://go.microsoft.com/fwlink/?LinkID=716919). 
   Para saber mais, confira o tutorial [Habilitar sincronização offline para seu aplicativo do Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md). 
2. Edite **Package.appxmanifest** e verifique a capacidade da **Webcam**.
3. Adicione uma nova classe `WindowsStorePlatform` com a seguinte implementação. Substitua "YourNamespace" pelo namespace principal de seu projeto.
   
        using System;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Windows.Foundation;
        using Windows.Media.Capture;
        using Windows.Storage;
        using YourNamespace;
   
        [assembly: Xamarin.Forms.Dependency(typeof(WinApp.WindowsStorePlatform))]
        namespace WinApp
        {
            public class WindowsStorePlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }
   
                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }
   
                public async Task<string> GetTodoFilesPathAsync()
                {
                    var storageFolder = ApplicationData.Current.LocalFolder;
                    var filePath = "TodoItemFiles";
   
                    var result = await storageFolder.TryGetItemAsync(filePath);
   
                    if (result == null) {
                        result = await storageFolder.CreateFolderAsync(filePath);
                    }
   
                    return result.Name; // later operations will use relative paths
                }
   
                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        CameraCaptureUI dialog = new CameraCaptureUI();
                        Size aspectRatio = new Size(16, 9);
                        dialog.PhotoSettings.CroppedAspectRatio = aspectRatio;
   
                        StorageFile file = await dialog.CaptureFileAsync(CameraCaptureUIMode.Photo);
                        return file.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
            }
        }

## <a name="summary"></a>Resumo
Este artigo descreveu como usar o novo suporte de arquivo no SDK do servidor e cliente móvel do Azure para trabalhar com o Armazenamento do Azure. 

* Crie uma conta de armazenamento e adicione uma cadeia de conexão ao back-end do aplicativo móvel. Somente o back-end tem a chave para o Armazenamento do Azure: o cliente móvel solicita um token SAS (Assinatura de Acesso Compartilhado) sempre que precisar acessar o Armazenamento do Azure. Para saber mais sobre tokens SAS no Armazenamento do Azure, confira as [Noções básicas das Assinaturas de Acesso Compartilhado].
* Crie um controlador que pode efetuar subclasses de `StorageController` a fim de manipular as solicitações de token SAS e obter os arquivos que estão associados a um registro. Por padrão, os arquivos são associados a um registro usando a ID do registro como parte do nome do contêiner; o comportamento pode ser personalizado especificando uma implementação de `IContainerNameResolver`. A política de token SAS também pode ser personalizada.
* O SDK do cliente móvel do Azure não armazena realmente quaisquer dados do arquivo. Em vez disso, o SDK do cliente chama o `IFileSyncHandler`, que decide como (e se) os arquivos são armazenados no dispositivo local. O manipulador de sincronização é registrado da seguinte maneira:
  
        client.InitializeFileSync(new MyFileSyncHandler(), store);
  
      + `IFileSyncHandler.GetDataSource` é chamado quando o SDK do cliente móvel do Azure precisa dos dados do arquivo (por exemplo, como parte do processo de upload). Isso lhe dá a capacidade de gerenciar como (e se) os arquivos são armazenados no dispositivo local e de retornar essas informações quando for necessário.
  
      + `IFileSyncHandler.ProcessFileSynchronizationAction` é chamado como parte do fluxo de sincronização do arquivo. Uma referência de arquivo e um valor de enumeração FileSynchronizationAction são fornecidos para que você possa decidir como seu aplicativo deve tratar o evento (por exemplo, baixando automaticamente um arquivo quando ele é criado ou atualizado, excluindo um arquivo do dispositivo local quando esse arquivo é excluído do servidor).
* Um `MobileServiceFile` pode ser usado no modo online ou offline, usando um `IMobileServiceTable` ou `IMobileServiceSyncTable`, respectivamente. No cenário offline, o upload ocorrerá quando o aplicativo chamar `PushFileChangesAsync`. Isso faz com que a fila de operação offline seja processada; para cada operação de arquivo, o SDK do cliente móvel do Azure invocará o método `GetDataSource` na instância `IFileSyncHandler` para recuperar o conteúdo do arquivo para o upload.
* Para recuperar os arquivos de um item, chame o método `GetFilesAsync` na instância `IMobileServiceTable<T>` ou `IMobileServiceSyncTable<T>`. Esse método retorna uma lista de arquivos associados ao item de dados fornecido. (Observação: essa é uma operação *local* e retornará os arquivos com base no estado do objeto quando ele foi sincronizado pela última vez. Para obter uma lista atualizada dos arquivos do servidor, primeiro você deve iniciar uma operação de sincronização.)
  
        IEnumerable<MobileServiceFile> files = await myTable.GetFilesAsync(myItem);
* O recurso de sincronização de arquivos usa notificações de alteração do registro no repositório local para recuperar os registros recebidos pelo cliente como parte de uma operação de push ou pull. Isso é realizado por meio da ativação de notificações locais e do servidor sobre o contexto de sincronização usando o parâmetro `StoreTrackingOptions` . 
  
        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);
  
      + Há outras opções de controle de armazenamento disponíveis, como notificações somente local ou somente do servidor. Você pode adicionar seu próprio retorno de chamada personalizado usando a propriedade `EventManager` de `IMobileServiceClient`:
  
            jobService.MobileService.EventManager.Subscribe<StoreOperationCompletedEvent>(StoreOperationEventHandler);
* É possível adicionar ou remover arquivos de um registro modificando o armazenamento de blobs diretamente, pois a associação é obtida por meio de uma convenção de nomenclatura. No entanto, nesse caso, você deve sempre **atualizar o carimbo de data/hora do registro quando os blobs associados forem modificados**. O SDK do cliente móvel do Azure sempre atualiza um registro ao adicionar ou remover um arquivo. 
  
    O motivo desse requisito é que alguns clientes móveis já terão o registro no armazenamento local. Quando esses clientes realizam uma obtenção incremental, esse registro não é retornado e o cliente não consultará os novos arquivos associados. Para evitar esse problema, recomendamos a atualização do carimbo de data e hora do registro ao executar qualquer mudança no armazenamento de blobs que não usa o SDK do cliente móvel do Azure.
* O projeto cliente usa o padrão [Xamarin.Forms DependencyService] para carregar a classe específica da plataforma correta no tempo de execução. Neste exemplo, definimos uma interface `IPlatform` com implementações em cada um dos projetos específicos da plataforma.

<!-- URLs. -->

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
[Criar um aplicativo Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Xamarin.Forms DependencyService]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[Microsoft.Azure.Mobile.Client.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.Files/
[Microsoft.Azure.Mobile.Client.SQLiteStore]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Microsoft.Azure.Mobile.Server.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Files/
[Noções básicas das Assinaturas de Acesso Compartilhado]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[Criar uma conta de armazenamento do Azure]:  ../storage/storage-create-storage-account.md#create-a-storage-account



<!--HONumber=Jan17_HO3-->


