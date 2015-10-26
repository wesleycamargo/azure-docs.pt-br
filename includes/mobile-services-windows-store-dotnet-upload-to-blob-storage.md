##<a name="add-select-images"></a>Atualizar o aplicativo cliente de início rápido para capturar e carregar imagens

1. No Visual Studio, abra o arquivo Package.appxmanifest e, na guia **Recursos**, habilite os recursos **Webcam** e **Microfone**.

   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-app-manifest-camera.png)
 
   	Isso garantirá que seu aplicativo possa usar uma câmera conectada ao computador. Os usuários serão solicitados a permitir acesso à câmera na primeira vez que o aplicativo for executado.

1. Abra o arquivo MainPage.xaml e substitua o elemento **StackPanel** diretamente após o primeiro elemento **Task** com o código a seguir:

        <StackPanel Orientation="Horizontal" Margin="72,0,0,0">
            <TextBox Name="TextInput" Margin="5" MaxHeight="40" MinWidth="300"></TextBox>
            <Button Name="btnTakePhoto" Style="{StaticResource PhotoAppBarButtonStyle}"
                    Click="OnTakePhotoClick" />
            <Button Name="ButtonSave" Style="{StaticResource UploadAppBarButtonStyle}" 
                    Click="ButtonSave_Click"/>
        </StackPanel>

2. Substitua o elemento **StackPanel** no **DataTemplate** pelo código a seguir:

        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                        Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                        Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}"
                    MaxHeight="250"/>
        </StackPanel> 

   	Isso adicionará uma imagem ao **ItemTemplate** e definirá sua origem da associação como o URI da imagem carregada no serviço de Armazenamento de Blob.

3. Abra o arquivo de projeto MainPage.xaml.cs e adicione as seguintes instruções **using**:
	
		using Windows.Media.Capture;
		using Windows.Storage;
		using Windows.UI.Popups;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
    
4. Na classe TodoItem, adicione as propriedades a seguir:

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

   	>[AZURE.NOTE]Para adicionar novas propriedades ao objeto TodoItem em um serviço móvel de back-end JavaScript, você deve ter o Esquema Dinâmico habilitado no seu serviço móvel. Quando o Esquema Dinâmico estiver habilitado, novas colunas serão automaticamente adicionadas à tabela TodoItem que mapeará para essas novas propriedades. Para um serviço móvel de back-end .NET, consulte o tópico [Como fazer alterações no modelo de dados para um serviço móvel de back-end .NET](../articles/mobile-services/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

5. Na classe MainPage, adicione este código:

        // Use a StorageFile to hold the captured image for upload.
        StorageFile media = null;
		
		private async void OnTakePhotoClick(object sender, RoutedEventArgs e)
		{
			// Capture a new photo or video from the device.
			CameraCaptureUI cameraCapture = new CameraCaptureUI();
			media = await cameraCapture
				.CaptureFileAsync(CameraCaptureUIMode.PhotoOrVideo);
        }

  	Esse código exibe a interface do usuário da câmera para a captura de uma imagem e salva a imagem em um arquivo de armazenamento.

6. Substitua o método `InsertTodoItem` existente por este código:
 
        private async void InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;
			
            if (media != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";
                todoItem.ResourceName = media.Name;
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

                // Get the new image as a stream.
                using (var fileStream = await media.OpenStreamForReadAsync())
                {                   					
                    // Upload the new image as a BLOB from the stream.
                    CloudBlockBlob blobFromSASCredential =
                        container.GetBlockBlobReference(todoItem.ResourceName);
                    await blobFromSASCredential.UploadFromStreamAsync(fileStream.AsInputStream());
                }
				
				// When you request an SAS at the container-level instead of the blob-level,
				// you are able to upload multiple streams using the same container credentials.
            }
			
            // Add the new item to the collection.
            items.Add(todoItem);
        }

	Esse código envia uma solicitação para o serviço móvel para inserir um novo TodoItem, incluindo o nome do arquivo de imagem. A resposta contém a SAS, que será usada para inserir a imagem no armazenamento de Blob, e o URI da imagem para vinculação de dados.

A etapa final consiste em testar o aplicativo e validar que os carregamentos sejam bem-sucedidos.
		
##<a name="test"></a>Testar o carregamento das imagens no seu aplicativo

1. No Visual Studio, pressione F5 para executar o aplicativo.

2. Digite um texto na caixa de texto em **Inserir um TodoItem** e, em seguida, clique em **Foto**.

   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar.png)

  	Isso exibirá a interface do usuário de captura da câmera.

3. Clique na imagem para tirar um retrato e, em seguida, clique em **OK**.
  
   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-camera.png)

4. Clique em **Carregar** para inserir o novo item e carregar a imagem.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar2.png)

5. O novo item, junto com a imagem carregada, é exibido no modo de exibição de lista.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-ie.png)

   	>[AZURE.NOTE]A imagem será baixada automaticamente do serviço de Armazenamento de Blob quando a propriedade <code>imageUri</code> do novo item estiver vinculada ao controle <strong>Image</strong>.

<!---HONumber=Oct15_HO3-->