<properties
	pageTitle="Usar os Serviços Móveis para carregar imagens no armazenamento de blob (Android) | Serviços Móveis"
	description="Saiba como usar os Serviços Móveis para carregar imagens no Armazenamento do Azure e acessar as imagens por meio do seu aplicativo do Android."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/18/2015"
	ms.author="ricksal"/>

# Carregar imagens no Armazenamento do Azure por meio de um dispositivo Android

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

Este tópico mostra como habilitar o aplicativo de Serviços Móveis do Azure no Android para carregar imagens no Armazenamento do Azure.

Os Serviços Móveis usam um banco de dados SQL para armazenar dados. No entanto, é mais eficiente armazenar dados de objeto binário grande (BLOB) no Armazenamento do Azure. Neste tutorial, você habilitará o aplicativo de início rápido dos Serviços Móveis para tirar fotos com a câmera do Android e carregará as imagens no Armazenamento do Azure.


## Para começar, você precisa do seguinte:

Antes de iniciar este tutorial, primeiro você deve concluir o início rápido dos Serviços Móveis: [Introdução aos Serviços Móveis].

Este tutorial também exige os seguintes itens:

+ Uma [conta do Armazenamento do Azure](../storage-create-storage-account.md)
+ Um dispositivo Android com uma câmera

## Como o aplicativo funciona

Carregar a imagem da foto é um processo que leva várias etapas:

- Primeiro você tira uma foto e insere uma linha de TodoItem no banco de dados SQL que contém os novos campos de metadados usados pelo Armazenamento do Azure.
- Um novo script de **inserção** do SQL do serviço móvel solicita uma Assinatura de Acesso Compartilhado (SAS) ao Armazenamento do Azure.
- Esse script retorna a SAS e um URI para o blob ao cliente.
- O cliente carrega a foto usando a SAS e o URI do blob.

O que é uma SAS?

Não é seguro armazenar as credenciais necessárias para carregar dados no serviço do Armazenamento do Azure no seu aplicativo cliente. Em vez disso, armazene essas credenciais em seu serviço móvel e use-as para gerar uma Assinatura de Acesso Compartilhado (SAS) que concede permissão para carregar uma nova imagem. A SAS, uma credencial com expiração de 5 minutos, será retornada com segurança pelos Serviços Móveis para o aplicativo cliente. O aplicativo usará, então, essa credencial temporária para carregar a imagem. Para obter mais informações, veja [Assinaturas de Acesso Compartilhado, parte 1: entendendo o modelo SAS](storage-dotnet-shared-access-signature-part-1.md)

## Exemplo de código
[Aqui](https://github.com/Azure/mobile-services-samples/tree/master/UploadImages) está a parte de código-fonte concluída do cliente desse aplicativo. Para executá-lo, você deve concluir as partes de back-end dos Serviços Móveis deste tutorial.

## Atualizar o script de inserção registrados no Portal de Gerenciamento

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]


## Atualize o aplicativo de início rápido para capturar e carregar imagens.

### Consulte a biblioteca de cliente para o Android do Armazenamento do Azure

1. Para adicionar a referência à biblioteca, no **aplicativo** > arquivo **build.gradle**, adicione esta linha à seção `dependencies`:

		compile 'com.microsoft.azure.android:azure-storage-android:0.6.0@aar'


2. Altere o valor de `minSdkVersion` para 15 (exigido pela API da câmera).

3. Pressione o ícone **Sincronizar Projeto com os Arquivos do Gradle**.

### Atualize o arquivo de manifesto na câmera e no armazenamento

1. Especifique se o seu aplicativo precisa ter uma câmera disponível adicionando esta linha ao arquivo **AndroidManifest.xml**:

	    <uses-feature android:name="android.hardware.camera"
	        android:required="true" />

2. Especifique se o seu aplicativo precisa de permissão para gravar no armazenamento externo adicionando esta linha ao arquivo **AndroidManifest.xml**:

	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

	Vale lembrar que o armazenamento externo do Android não é necessariamente um cartão SD: para obter mais informações, veja [Salvando arquivos](http://developer.android.com/training/basics/data-storage/files.html).

### Atualize os arquivos de recurso na nova interface do usuário

1. Adicione títulos aos novos botões adicionando o seguinte ao arquivo **strings.xml** no diretório *values*:

	    <string name="preview_button_text">Take Photo</string>
	    <string name="upload_button_text">Upload</string>

2. No arquivo **activity\_to\_do.xml** da pasta **res => layout**, adicione este código antes do código existente no botão **Adicionar**.

         <Button
             android:id="@+id/buttonPreview"
             android:layout_width="120dip"
             android:layout_height="wrap_content"
             android:onClick="takePicture"
             android:text="@string/preview_button_text" />

3. Substitua o elemento do botão **Adicionar** por este código:

         <Button
             android:id="@+id/buttonUpload"
             android:layout_width="100dip"
             android:layout_height="wrap_content"
             android:onClick="uploadPhoto"
             android:text="@string/upload_button_text" />


### Adicionar código para a captura de fotos

1. Em **ToDoActivity.java**, adicione este código para criar um objeto **Arquivo** com um nome exclusivo.

		// Create a File object for storing the photo
	    private File createImageFile() throws IOException {
	        // Create an image file name
	        String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
	        String imageFileName = "JPEG_" + timeStamp + "_";
	        File storageDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
	        File image = File.createTempFile(
	                imageFileName,  /* prefix */
	                ".jpg",         /* suffix */
	                storageDir      /* directory */
	        );
	        return image;
	    }

5. Adicione este código para iniciar o aplicativo de câmera do Android. Depois, você pode tirar fotos e quando gostar de uma foto, pressione **Salvar**, o que fará com que ela seja armazenada no arquivo que você acabou de criar.

		// Run an Intent to start up the Android camera
	    static final int REQUEST_TAKE_PHOTO = 1;
	    public Uri mPhotoFileUri = null;
	    public File mPhotoFile = null;

	    public void takePicture(View view) {
	        Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
	        // Ensure that there's a camera activity to handle the intent
	        if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
	            // Create the File where the photo should go
	            try {
	                mPhotoFile = createImageFile();
	            } catch (IOException ex) {
	                // Error occurred while creating the File
	                //
	            }
	            // Continue only if the File was successfully created
	            if (mPhotoFile != null) {
	                mPhotoFileUri = Uri.fromFile(mPhotoFile);
	                takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, mPhotoFileUri);
	                startActivityForResult(takePictureIntent, REQUEST_TAKE_PHOTO);
	            }
	        }
	    }

### Adicionar um código para carregar o arquivo de foto no armazenamento de blob


1. Primeiro, adicione alguns novos campos de metadados ao objeto `ToDoItem` adicionando este código a **ToDoItem.java**.

		/**
	     *  imageUri - points to location in storage where photo will go
	     */
	    @com.google.gson.annotations.SerializedName("imageUri")
	    private String mImageUri;

	    /**
	     * Returns the item ImageUri
	     */
	    public String getImageUri() {
	        return mImageUri;
	    }

	    /**
	     * Sets the item ImageUri
	     *
	     * @param ImageUri
	     *            Uri to set
	     */
	    public final void setImageUri(String ImageUri) {
	        mImageUri = ImageUri;
	    }

	    /**
	     * ContainerName - like a directory, holds blobs
	     */
	    @com.google.gson.annotations.SerializedName("containerName")
	    private String mContainerName;

	    /**
	     * Returns the item ContainerName
	     */
	    public String getContainerName() {
	        return mContainerName;
	    }

	    /**
	     * Sets the item ContainerName
	     *
	     * @param ContainerName
	     *            Uri to set
	     */
	    public final void setContainerName(String ContainerName) {
	        mContainerName = ContainerName;
	    }

	    /**
	     *  ResourceName
	     */
	    @com.google.gson.annotations.SerializedName("resourceName")
	    private String mResourceName;

	    /**
	     * Returns the item ResourceName
	     */
	    public String getResourceName() {
	        return mResourceName;
	    }

	    /**
	     * Sets the item ResourceName
	     *
	     * @param ResourceName
	     *            Uri to set
	     */
	    public final void setResourceName(String ResourceName) {
	        mResourceName = ResourceName;
	    }

	    /**
	     *  SasQueryString - permission to write to storage
	     */
	    @com.google.gson.annotations.SerializedName("sasQueryString")
	    private String mSasQueryString;

	    /**
	     * Returns the item SasQueryString
	     */
	    public String getSasQueryString() {
	        return mSasQueryString;
	    }

	    /**
	     * Sets the item SasQueryString
	     *
	     * @param SasQueryString
	     *            Uri to set
	     */
	    public final void setSasQueryString(String SasQueryString) {
	        mSasQueryString = SasQueryString;
	    }

2. Substitua o construtor de 0 parâmetro por este código:

	    /**
	     * ToDoItem constructor
	     */
	    public ToDoItem() {
	        mContainerName = "";
	        mResourceName = "";
	        mImageUri = "";
	        mSasQueryString = "";
	    }

3. Substitua o construtor de vários parâmetros por este código:

	    /**
	     * Initializes a new ToDoItem
	     *
	     * @param text
	     *            The item text
	     * @param id
	     *            The item id
	     */
	    public ToDoItem(String text,
	                    String id,
	                    String containerName,
	                    String resourceName,
	                    String imageUri,
	                    String sasQueryString) {
	        this.setText(text);
	        this.setId(id);
	        this.setContainerName(containerName);
	        this.setResourceName(resourceName);
	        this.setImageUri(imageUri);
	        this.setSasQueryString(sasQueryString);
	    }


4. No arquivo **ToDoActivity.java**, substitua o método **addItem** em **ToDoActivity.java** pelo seguinte código que carrega a imagem.

	    /**
	     * Add a new item
	     *
	     * @param view
	     *            The view that originated the call
	     */
	    public void uploadPhoto(View view) {
	        if (mClient == null) {
	            return;
	        }

	        // Create a new item
	        final ToDoItem item = new ToDoItem();

	        item.setText(mTextNewToDo.getText().toString());
	        item.setComplete(false);
	        item.setContainerName("todoitemimages");

	        // Use a unigue GUID to avoid collisions.
	        UUID uuid = UUID.randomUUID();
	        String uuidInString = uuid.toString();
	        item.setResourceName(uuidInString);

	        // Send the item to be inserted. When blob properties are set this
	        // generates an SAS in the response.
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
		                    final ToDoItem entity = addItemInTable(item);

		                    // If we have a returned SAS, then upload the blob.
		                    if (entity.getSasQueryString() != null) {

	                       // Get the URI generated that contains the SAS
	                        // and extract the storage credentials.
	                        StorageCredentials cred =
								new StorageCredentialsSharedAccessSignature(entity.getSasQueryString());
	                        URI imageUri = new URI(entity.getImageUri());

	                        // Upload the new image as a BLOB from a stream.
	                        CloudBlockBlob blobFromSASCredential =
	                                new CloudBlockBlob(imageUri, cred);

	                        blobFromSASCredential.uploadFromFile(mPhotoFileUri.getPath());
  	                    }

	                    runOnUiThread(new Runnable() {
	                        @Override
	                        public void run() {
	                            if(!entity.isComplete()){
	                                mAdapter.add(entity);
	                            }
	                        }
	                    });
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };

	        runAsyncTask(task);

	        mTextNewToDo.setText("");
	    }


Esse código envia uma solicitação ao serviço móvel para inserir um novo TodoItem. A resposta contém a SAS, que é usada para carregar a imagem do armazenamento local para um blob no armazenamento do Azure.


## Testar o carregamento das imagens

1. No Android Studio, pressione **Executar**. Na caixa de diálogo, escolha o dispositivo que será usado.

2. Quando a interface do usuário do aplicativo for exibida, digite o texto na caixa de texto rotulada **Adicionar um item ToDo**.

3. Pressione **Tirar Foto**. Quando o aplicativo da câmera for iniciado, tire uma foto. Pressione a marca de seleção para aceitar a foto.

4. Pressione **Carregar**. Vale lembrar que ToDoItem foi adicionado à lista, como de costume.

5. No portal do Microsoft Azure, vá para a sua conta de armazenamento, pressione a guia **Contêineres** e depois o nome do seu contêiner na lista.

6. Uma lista dos seus arquivos de blob carregados será exibida. Selecione um e pressione **Baixar**.

7. A imagem que você carregou agora aparece em uma janela do navegador.


## <a name="next-steps"> </a>Próximas etapas

Agora que você conseguiu carregar as imagens com segurança, integrando seu serviço móvel com o serviço de Blob, confira alguns dos outros tópicos de serviço de back-end e integração:

+ [Enviar email dos Serviços Móveis com SendGrid]

  Saiba como adicionar funcionalidade de email ao Serviço Móvel usando o serviço de email SendGrid. Este tópico demonstra como adicionar scripts do lado do servidor para enviar email usando o SendGrid.

+ [Agendar trabalhos de back-end nos Serviços Móveis]

  Saiba como usar a funcionalidade de agendador de trabalhos dos Serviços Móveis para definir o código de script de servidor executado em um agendamento definido por você.

+ [Referência de script de servidor dos Serviços Móveis]

  Tópicos de referência para o uso de scripts de servidor para executar tarefas no lado do servidor e integração com outros componentes do Azure e recursos externos.

+ [Referência conceitual do tutorial do .NET de Serviços Móveis]

  Saiba mais sobre como usar os Serviços Móveis com o .NET


<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Enviar email dos Serviços Móveis com SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Agendar trabalhos de back-end nos Serviços Móveis]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Referência de script de servidor dos Serviços Móveis]: mobile-services-how-to-use-server-scripts.md
[Introdução aos Serviços Móveis]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Referência conceitual do tutorial do .NET de Serviços Móveis]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=Sept15_HO4-->