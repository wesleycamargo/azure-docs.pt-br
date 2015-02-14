
##<a name="add-select-images"></a>Atualizar o aplicativo cliente quickstart para capturar e carregar imagens

Nesta seção, você atualizará o projeto do tutorial [Introdução aos Serviços Móveis] para tirar fotos e carregá-las no Armazenamento de Blob do Azure. Para capturar a imagem, este tutorial usa a [CameraCaptureTask] do namespace  `Microsoft.Phone.Tasks`. Essa classe inicia a câmera da interface do usuário no dispositivo Windows Phone para capturar a foto e salva a imagem automaticamente no Álbum da Câmera no dispositivo Windows Phone. Se não desejar que as imagens sejam salvas no Álbum da Câmera, use a classe [PhotoCamera] no namespace  `Microsoft.Devices`.

1. No Gerenciador de Soluções para Visual Studio, sob o projeto, expanda **Propriedades**. Em seguida, abra o arquivo WMAppManifest.xml e, na guia **Recursos**, habilite a câmera clicando em **ID\_CAP\_ISV\_CAMERA**. Feche o arquivo para salvar as alterações.

   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png)

   	Isso garantirá que seu aplicativo possa usar uma câmera conectada ao computador. Os usuários serão solicitados a permitir acesso à câmera na primeira vez que o aplicativo for executado.

2. Abra o arquivo MainPage.xaml e substitua o elemento **Grade** chamado **ContentPanel** com o seguinte código:

        <!--ContentPanel - place additional content here-->
        <Grid x:Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="2*" />
                <ColumnDefinition Width="2*" />
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.ColumnSpan="2" Text="Enter some text below, click Capture Image to add a captured image. Then click Save to insert a new TodoItem item into your database" TextWrapping="Wrap" Margin="12"/>
            <TextBox Grid.Row="1" Grid.ColumnSpan="2" Name="TextInput" Text="" />
            <Button Name="ButtonCaptureImage" Grid.Row="2" Click="ButtonCaptureImage_Click">Capture Image</Button>
            <Button Grid.Row ="2" Grid.Column="1" Name="ButtonSave" Click="ButtonSave_Click">Save</Button>
            <TextBlock Grid.Row="3" Grid.ColumnSpan="2" Text="Click refresh below to load the unfinished TodoItems from your database. Use the checkbox to complete and update your TodoItems" TextWrapping="Wrap" Margin="12" />
            <Button Grid.Row="4" Grid.ColumnSpan="2" Name="ButtonRefresh" Click="ButtonRefresh_Click">Refresh</Button>
            <phone:LongListSelector Grid.Row="5" Grid.ColumnSpan="2" Name="ListItems">
                <phone:LongListSelector.ItemTemplate>
                    <DataTemplate>
                        <StackPanel Orientation="Vertical">
                            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
                            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}" MaxHeight="150"/>
                        </StackPanel>
                    </DataTemplate>
                </phone:LongListSelector.ItemTemplate>
            </phone:LongListSelector>
        </Grid>


   	Isso adiciona um novo botão para iniciar a [CameraCaptureTask] e define uma imagem como o **ItemTemplate** e define sua origem de associação como o URI da imagem carregada no serviço de Armazenamento de Blob.

3. Abra o arquivo de projeto MainPage.xaml.cs e adicione o seguinte **usando** as instruções:
	
		using Microsoft.Phone.Tasks;
		using System.IO;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
    
4. No arquivo do projeto MainPage.xaml.cs, atualize a classe TodoItem adicionando as seguintes propriedades:

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

5. No arquivo de projeto MainPage.xaml.cs, atualize a classe MainPage. Adicione o seguinte código para declarar a [CameraCaptureTask] e um objeto de fluxo que fará referência à imagem capturada:

        // Using the CameraCaptureTask to allow the user to capture a todo item image //
        CameraCaptureTask cameraCaptureTask;
		
        // Using a stream reference to upload the image to blob storage.
        Stream imageStream = null;

6. No arquivo de projeto MainPage.xaml.cs, atualize a classe MainPage. Adicione o seguinte código para atualizar o construtor para criar a CameraCaptureTask e adicione um manipulador de eventos para o evento concluído:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
			
            cameraCaptureTask = new CameraCaptureTask();
            cameraCaptureTask.Completed += cameraCaptureTask_Completed;
        }
		
        void cameraCaptureTask_Completed(object sender, PhotoResult e)
        {
            imageStream = e.ChosenPhoto;
        }

7. No arquivo de projeto MainPage.xaml.cs, atualize a classe MainPage. Adicione o seguinte código que exibe a interface do usuário da câmera para permitir que o usuário capture uma imagem quando o botão **Capturar Imagem** for clicado:

        private void ButtonCaptureImage_Click(object sender, RoutedEventArgs e)
        {
            cameraCaptureTask.Show();
        }


8. No arquivo de projeto MainPage.xaml.cs, atualize a classe MainPage. Substitua o método  `InsertTodoItem` existente por este código:
 
        private async void InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;            
			
            if (imageStream != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";
                todoItem.ResourceName = Guid.NewGuid().ToString() + ".jpg";
            }                       
			
            // Send the item to be inserted. When blob properties are set this
            // generates an SAS in the response.
            await todoTable.InsertAsync(todoItem);  
			
            // If we have a returned SAS, then upload the blob.
            if (!string.IsNullOrEmpty(todoItem.SasQueryString))
            {
                // Get the URI generated that contains the SAS 
                // and extract the storage credentials.
                StorageCredentials cred = new StorageCredentials(todoItem.SasQueryString);
                var imageUri = new Uri(todoItem.ImageUri);
				
                // Instantiate a Blob store container based on the info in the returned item.
                CloudBlobContainer container = new CloudBlobContainer(
                    new Uri(string.Format("https://{0}/{1}",
                        imageUri.Host, todoItem.ContainerName)), cred);                
				
                // Upload the new image as a BLOB from the stream.
                CloudBlockBlob blobFromSASCredential =
                    container.GetBlockBlobReference(todoItem.ResourceName);
                await blobFromSASCredential.UploadFromStreamAsync(imageStream);
				
				// When you request an SAS at the container-level instead of the blob-level,
				// you are able to upload multiple streams using the same container credentials.

                imageStream = null;
            }              
			
            // Add the new item to the collection.
            items.Add(todoItem);
            TextInput.Text = "";
        }


	Esse código envia uma solicitação para o serviço móvel para inserir um novo TodoItem, incluindo o nome do arquivo de imagem. A resposta contém a SAS, que será usada para inserir a imagem no armazenamento de Blob, e o URI da imagem para vinculação de dados.

A etapa final consiste em testar o aplicativo e validar que os carregamentos sejam bem-sucedidos.
		
##<a name="test"></a>Testar o carregamento das imagens no seu aplicativo

1. No Visual Studio, você pode pressionar a tecla F5 para testar o aplicativo no emulador ou com um dispositivo real de destino.

2. Digite texto na caixa de texto e clique em **Capturar Imagem**.

   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-wp8.png)

  	Isso exibirá a interface do usuário de captura da câmera. 

3. Clique na imagem ou no botão de instantâneo no telefone para tirar uma foto.
  
   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-wp8.png)

4. Clique em **aceitar** para aceitar a imagem e sair da interface do usuário da câmera.

    ![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png)

5. Clique em **Salvar** para inserir o novo item e carregar a imagem.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-save-wp8.png)

6. O novo item, junto com a imagem carregada, é exibido no modo de exibição de lista.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-final-wp8.png)

   >[AZURE.NOTE]A imagem é baixada automaticamente do serviço de Armazenamento de Blob quando a propriedade <code>imageUri</code> do novo item está vinculado ao controle <strong>Imagem</strong>.


[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started
[CameraCaptureTask]: http://msdn.microsoft.com/pt-br/library/windowsphone/develop/microsoft.phone.tasks.cameracapturetask(v=vs.105).aspx
[PhotoCamera]: http://msdn.microsoft.com/pt-br/library/windowsphone/develop/microsoft.devices.photocamera(v=vs.105).aspx
<!--HONumber=42-->
