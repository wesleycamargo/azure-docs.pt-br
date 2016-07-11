<properties
	pageTitle="Aplicativo local com armazenamento de blob (Java) | Microsoft Azure"
	description="Saiba como criar um aplicativo de console que carrega uma imagem para o Azure e a exibe no navegador. Amostras de código em Java."
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="06/24/2016"
	ms.author="rmcmurray"/>

# Aplicativo local com armazenamento de blob

## Visão geral

O exemplo a seguir mostra como você pode usar o armazenamento do Azure paraarmazenar imagens no Azure. O código deste arquivo é de um aplicativo de console que carrega uma imagem para o Azure e cria um arquivo HTML que exibe a imagem no seu navegador.

## Pré-requisitos

- Um JDK (Java Developer Kit) versão 1.6 ou posterior deve estar instalado.
- O SDK do Azure deve estar instalado.
- O JAR das bibliotecas do Azure para Java e todos os JARs de dependência aplicáveis devem estar instalados e no caminho de compilação usado por seu compilador Java. Para saber mais sobre como instalar as bibliotecas do Azure para Java, consulte [Baixar o SDK do Azure para Java](java-download-azure-sdk.md).
- Uma conta de armazenamento do Azure deve ter sido configurada. O nome e a chave da conta de armazenamento serão usados pelo código deste artigo. Consulte [Como criar uma Conta de Armazenamento](storage-create-storage-account.md#create-a-storage-account) para obter informações sobre como criar uma conta de armazenamento e [Exibir e copiar chaves de acesso de armazenamento](storage-create-storage-account.md#view-and-copy-storage-access-keys) para obter informações sobre como recuperar a chave de conta.

- Você criou um arquivo de imagem local armazenado no caminho c:\\minhasimagens\\image1.jpg. Como alternativa, modifique o construtor **FileInputStream** no exemplo para usar um caminho de imagem e um nome de arquivo diferentes.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Para usar o armazenamento de blobs do Azure para carregar um arquivo

Um procedimento passo a passo é apresentado aqui. Se você quiser pular, todo o código será apresentado mais tarde neste artigo.

Inicie o código incluindo as importações das principais classes de armazenamento do Azure, as classes de cliente do blob do Azure, as classes de E/S de Java e a classe **URISyntaxException**.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

Declare uma classe chamada **StorageSample** e inclua o colchete de abertura, **{**.

    public class StorageSample {

Na classe **StorageSample**, declare uma variável de cadeia de caracteres que conterá o protocolo de ponto de extremidade padrão, o nome da sua conta de armazenamento e a sua chave de acesso de armazenamento, conforme especificado na conta de armazenamento do Azure. Substitua os valores dos espaços reservados **nome\_da\_sua\_conta** e **sua\_chave\_de\_conta** pelo nome da sua conta e por sua chave de conta, respectivamente.

    public static final String storageConnectionString =
           "DefaultEndpointsProtocol=http;" +
               "AccountName=your_account_name;" +
               "AccountKey=your_account_name";

Adicione sua declaração para **main**, inclua um bloco **try** e inclua os colchetes de abertura necessários, **{**.

    public static void main(String[] args)
    {
        try
        {

Declare as variáveis do tipo a seguir (as descrições são sobre como elas são usadas neste exemplo):

-   **CloudStorageAccount**: usada para inicializar o objeto de conta com o nome e a chave da sua conta de armazenamento do Azure e para criar o objeto de cliente do blob.
-   **CloudBlobClient**: usada para acessar o serviço Blob.
-   **CloudBlobContainer**: usada para criar um contêiner de blobs, listar os blobs no contêiner e excluir o contêiner.
-   **CloudBlockBlob**: usada para carregar um arquivo de imagem local para o contêiner.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Atribua um valor à variável **account**.

    account = CloudStorageAccount.parse(storageConnectionString);

Atribua um valor à variável **serviceClient**.

    serviceClient = account.createCloudBlobClient();

Atribua um valor à variável **container**. Obteremos uma referência para um contêiner chamado **gettingstarted**.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

Crie o contêiner. Esse método criará o contêiner se ele ainda não existir (e retornará **true**). Se o contêiner existir, retornará **false**. Uma alternativa para **createIfNotExist** é o método **create** (que retornará um erro se o contêiner já existir).

    container.createIfNotExists();

Definir acesso anônimo para o contêiner.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Obtenha uma referência para o blob de blocos, que representará o blob no armazenamento do Azure.

    blob = container.getBlockBlobReference("image1.jpg");

Use o construtor **File** para obter uma referência para o arquivo criado localmente que será carregado. Certifique-se de criar esse arquivo antes de executar o código.

    File fileReference = new File ("c:\\myimages\\image1.jpg");

Carregue o arquivo local por meio de uma chamada para o método **CloudBlockBlob.upload**. O primeiro parâmetro para o método **CloudBlockBlob.upload** é um objeto **FileInputStream** que representa o arquivo local que será carregado para o armazenamento do Azure. O segundo parâmetro é o tamanho, em bytes, do arquivo.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Chame uma função auxiliar denominada **MakeHTMLPage** para fazer uma página HTML básica que contém um elemento **&lt;image&gt;** com a origem definida para o blob que agora está na sua conta de armazenamento do Azure. O código de **MakeHTMLPage** será discutido posteriormente neste artigo.

    MakeHTMLPage(container);

Imprima uma mensagem de status e informações sobre a página HTML criada.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Feche o bloco **try** inserindo um colchete de fechamento: **}**

Utilize as seguintes exceções:

-   **FileNotFoundException**: pode ser gerada pelos construtores **FileInputStream** ou **FileOutputStream**.
-   **StorageException**: pode ser gerada pela biblioteca de armazenamento de cliente do Azure.
-   **URISyntaxException**: pode ser gerada pelo método **ListBlobItem.getUri**.
-   **Exception**: manipulação de exceção genérica.

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Feche o **main** inserindo um colchete de fechamento: **}**

Crie um método chamado **MakeHTMLPage** para criar uma página HTML básica. Esse método tem um parâmetro do tipo **CloudBlobContainer**, que será usado para iterar pela lista de blobs carregados. Esse método gerará exceções do tipo **FileNotFoundException**, que podem ser geradas pelo construtor **FileOutputStream** e **URISyntaxException**, que podem ser geradas pelo método **ListBlobItem.getUri**. Inclua o colchete de abertura, **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Crie um arquivo local chamado **index.html**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Grave no arquivo local, adicionando os elementos **&lt;html&gt;**, **&lt;header&gt;** e **&lt;body&gt;**.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Itere pela lista de blobs carregados. Para cada blob, na página HTML crie um elemento **&lt;img&gt;** que tenha seu atributo **src** enviado para o URI do blob caso ele exista na sua conta de armazenamento do Azure. Embora tenha adicionado apenas uma imagem neste exemplo, se você adicionar mais imagens, este código iteraria todos eles.

Para simplificar, este exemplo assume que cada blob carregado é uma imagem. Se você tiver atualizado blobs que não sejam imagens, ou blobs de páginas, em vez de blobs de blocos, ajuste o código conforme o necessário.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Feche o elemento **&lt;body&gt;** e o elemento **&lt;html&gt;**.

    stream.println("</body>");
    stream.println("</html>");

Feche o arquivo local.

    stream.close();

Feche **MakeHTMLPage** inserindo um colchete de fechamento: **}**

Feche **StorageSample** inserindo um colchete de fechamento: **}**

A seguir está o código completo deste exemplo. Lembre-se de modificar os valores dos espaço reservado **your\_account\_name** e**your\_account\_key** para que usem seu nome de conta e chave de conta, respectivamente.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                       "AccountName=your_account_name;" +
                       "AccountKey=your_account_name";

        public static void main(String[] args) {
            try {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExists();

                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");

                File fileReference = new File("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            } catch (FileNotFoundException fileNotFoundException) {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            } catch (StorageException storageException) {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            } catch (URISyntaxException uriSyntaxException) {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            } catch (Exception e) {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
        {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

Além de carregar o arquivo de imagem local para o armazenamento do Azure, esse código de exemplo cria um arquivo local chamado namedindex.html, que você pode abrir no navegador para ver a sua imagem carregada.

Como o código contém o nome e a chave da sua conta, certifique-se de que seu código-fonte seja seguro.

## Para excluir um contêiner

Como você é cobrado pelo armazenamento, talvez queira excluir o contêiner **gettingstarted** após concluir os testes com este exemplo. Para excluir um contêiner, use o método **CloudBlobContainer.delete**.

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Para chamar o método **CloudBlobContainer.delete**, o processo de inicializar objetos **CloudStorageAccount**, **CloudBlobClient** e **CloudBlobContainer** é o mesmo mostrado para o método **createIfNotExist**. A seguir é fornecido um exemplo completo que exclui o contêiner chamado **gettingstarted**.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

    public class DeleteContainer {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                   "AccountName=your_account_name;" +
                   "AccountKey=your_account_key";

        public static void main(String[] args)
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();

                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

Para obter uma visão geral de outras classes e outros métodos de armazenamento de blobs, consulte [Como usar o Armazenamento de Blobs do Java](storage-java-how-to-use-blob-storage.md).

## Próximas etapas

Siga estes links para saber mais sobre as tarefas mais complexas de armazenamento.

- [SDK de Armazenamento do Azure para Java](https://github.com/azure/azure-storage-java)
- [Referência de SDK do Cliente de Armazenamento do Azure](http://dl.windowsazure.com/storage/javadoc/)
- [API REST de serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog da equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)

<!---HONumber=AcomDC_0629_2016-->