---
title: Como usar o Armazenamento de Blobs no Xamarin | Microsoft Docs
description: A Biblioteca de Cliente do Armazenamento do Azure para Xamarin permite que os desenvolvedores criem aplicativos iOS, Android e da Windows Store com suas interfaces de usuário nativo. Este tutorial mostra como usar o Xamarin para criar um aplicativo que usa o Armazenamento de Blobs do Azure.
services: storage
documentationcenter: xamarin
author: micurd
manager: jahogg
editor: tysonn

ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2016
ms.author: micurd

---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Como usar o Armazenamento de Blobs no Xamarin
[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## <a name="overview"></a>Visão geral
O Xamarin permite que os desenvolvedores usem uma base de código C# compartilhada para criar aplicativos iOS, Android e da Windows Store com suas interfaces de usuário nativo. Este tutorial mostra como usar o armazenamento de Blobs do Azure com um aplicativo Xamarin. Se você quiser saber mais sobre o Armazenamento do Azure, antes de mergulharmos no código, confira [Introdução ao Armazenamento do Microsoft Azure](storage-introduction.md).

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Criar um novo aplicativo Xamarin
Neste guia de Introdução, criaremos um aplicativo para Android, iOS e Windows. Este aplicativo simplesmente criará um contêiner e carregará um blob nesse contêiner. Usaremos o Visual Studio no Windows para este guia de introdução, mas as mesmas lições podem ser aplicadas durante a criação de um aplicativo usando o Xamarin Studio no Mac OS.

Execute estas etapas para criar o aplicativo:

1. Se você ainda não o fez, baixe e instale o [Xamarin para Visual Studio](https://www.xamarin.com/download).
2. Abra o Visual Studio e crie um Aplicativo em Branco (Nativo Compartilhado): **Arquivo > Novo > Projeto > Plataforma Cruzada > Aplicativo em Branco (Nativo Compartilhado)**.
3. Clique com o botão direito no painel do Gerenciador de Soluções e selecione **Gerenciar Pacotes NuGet para a Solução**. Procure por **WindowsAzure.Storage** e instale a versão estável mais recente para todos os projetos em sua solução.
4. Compile e execute seu projeto.

Agora você deve ter um aplicativo que permite que você clique em um botão que incrementa um contador.

> [!NOTE]
> A biblioteca de cliente de armazenamento do Azure para Xamarin no momento dá suporte aos seguintes tipos de projeto: Nativo Compartilhado, Xamarin.Forms compartilhado, Xamarin.Android e Xamarin.iOS.
> 
> 

## <a name="create-container-and-upload-blob"></a>Criar contêiner e carregar blob
Em seguida, você adicionará um código à classe compartilhada `MyClass.cs` que cria um contêiner e carrega um blob neste contêiner. `MyClass.cs` deve se parecer com o seguinte:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;

    namespace XamarinApp
    {
        public class MyClass
        {
            public MyClass ()
            {
            }

            public static async Task createContainerAndUpload()
            {
                // Retrieve storage account from connection string.
                CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

                // Create the blob client.
                CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

                // Retrieve reference to a previously created container.
                CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

                // Create the container if it doesn't already exist.
                await container.CreateIfNotExistsAsync();

                // Retrieve reference to a blob named "myblob".
                CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

                // Create the "myblob" blob with the text "Hello, world!"
                await blockBlob.UploadTextAsync("Hello, world!");
            }
        }
    }

Substitua "your_account_name_here" e "your_account_key_here" pelo nome e chave de conta. Você pode usar essa classe compartilhada em seu aplicativo para iOS, Android e Windows Phone. Basta adicionar `MyClass.createContainerAndUpload()` a cada projeto. Por exemplo:

### <a name="xamarinapp.droid->-mainactivity.cs"></a>XamarinApp.Droid > MainActivity.cs
    using Android.App;
    using Android.Widget;
    using Android.OS;

    namespace XamarinApp.Droid
    {
        [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
        public class MainActivity : Activity
        {
            int count = 1;

            protected override async void OnCreate (Bundle bundle)
            {
                base.OnCreate (bundle);

                // Set our view from the "main" layout resource
                SetContentView (Resource.Layout.Main);

                // Get our button from the layout resource,
                // and attach an event to it
                Button button = FindViewById<Button> (Resource.Id.myButton);

                button.Click += delegate {
                    button.Text = string.Format ("{0} clicks!", count++);
                };

              await MyClass.createContainerAndUpload();
            }
        }
    }

### <a name="xamarinapp.ios->-viewcontroller.cs"></a>XamarinApp.iOS > ViewController.cs
    using System;
    using UIKit;

    namespace XamarinApp.iOS
    {
        public partial class ViewController : UIViewController
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.createContainerAndUpload();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc that aren't in use.
            }
        }
    }

### <a name="xamarinapp.winphone->-mainpage.xaml->-mainpage.xaml.cs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Navigation;

    // The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

    namespace XamarinApp.WinPhone
    {
        /// <summary>
        /// An empty page that can be used on its own or navigated to within a Frame.
        /// </summary>
        public sealed partial class MainPage : Page
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.createContainerAndUpload();
            }
        }
    }


## <a name="run-the-application"></a>Executar o aplicativo
Agora você pode executar esse aplicativo em um emulador do Android ou do Windows Phone. Também é possível executar esse aplicativo em um emulador de iOS, mas isso exigirá um Mac. Para obter instruções específicas sobre como fazer isso, leia a documentação sobre como [Conectar o Visual Studio a um Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Após a execução do aplicativo, ele criará o contêiner `mycontainer` em sua conta de armazenamento. Ele deve conter o blob, `myblob`, que tem o texto `Hello, world!`. Você pode verificar isso usando o [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/).

## <a name="next-steps"></a>Próximas etapas
Neste guia de introdução, você aprendeu como criar um aplicativo de plataforma cruzada no Xamarin que usa o Armazenamento do Azure. Este guia de introdução se concentrou especificamente em um cenário no Armazenamento de Blobs. No entanto, você pode fazer muito mais, não apenas com o Armazenamento de Blobs, mas também com o Armazenamento de Tabelas, de Arquivo e de Filas. Para saber mais, veja os artigos a seguir:

* [Introdução ao Armazenamento de Blobs do Azure usando o .NET](storage-dotnet-how-to-use-blobs.md)
* [Introdução ao Armazenamento de Tabelas do Azure usando o .NET](storage-dotnet-how-to-use-tables.md)
* [Introdução ao Armazenamento de Filas do Azure usando o .NET](storage-dotnet-how-to-use-queues.md)
* [Introdução ao Armazenamento de Arquivos do Azure no Windows](storage-dotnet-how-to-use-files.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

<!--HONumber=Oct16_HO2-->


