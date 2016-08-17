<properties
 pageTitle="Como Gerenciar a Expiração do Conteúdo de Blob na CDN (Rede de Distribuição de Conteúdo) do Azure | Microsoft Azure"
 description="Aprenda sobre as opções para controlar a vida útil de blobs no cache do Azure CDN."
 services="cdn"
 documentationCenter=".NET"
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="07/28/2016"
 ms.author="casoper"/>


#Como gerenciar a expiração do conteúdo de blob na CDN (Rede de Distribuição de Conteúdo) do Azure  

Os blobs que mais se beneficiam do cache da CDN do Azure são aqueles que são acessados frequentemente durante seu período de TTL (vida útil). Um blob permanece no cache pelo período de TTL e é atualizado pelo serviço de blob depois que esse tempo termina. Em seguida, o processo se repete.

Você tem duas opções para controlar o TTL.

1.	Não defina valores de cache dessa forma usando a TTL padrão de sete dias.
2.	Defina explicitamente a propriedade *x-ms-blob-cache-control* em uma solicitação **Blob Put**, **Put Block List** ou **Set Blob Properties** ou use a Biblioteca Gerenciada do Azure para definir a propriedade [BlobProperties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx). A configuração dessa propriedade define o valor do cabeçalho *Cache-Control* para o blob. O valor do cabeçalho ou da propriedade deve especificar o valor apropriado em segundos. Por exemplo, para definir o período máximo do cache como um ano, você pode especificar o cabeçalho de solicitação como `x-ms-blob-cache-control: public, max-age=31556926`. Para obter detalhes sobre como definir cabeçalhos de cache, consulte a [especificação HTTP/1.1](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

Qualquer conteúdo que você deseje armazenar em cache pela CDN deverá ser armazenado em sua conta de armazenamento do Azure como um blob publicamente acessível. Para obter mais detalhes sobre o serviço Blob do Azure, consulte [Conceitos do serviço Blob](https://msdn.microsoft.com/library/dd179376.aspx).

Existem algumas maneiras diferentes de trabalhar com conteúdo no serviço de Blob:

-	Usando a API gerenciada fornecida pela [Biblioteca de clientes do Armazenamento do Azure para .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).
-	Usando uma ferramenta de gerenciamento de armazenamento de terceiros.
-	Usando a [API REST dos Serviços de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx).

O exemplo de código a seguir é um aplicativo de console que usa o Cliente de Armazenamento do Azure para criar um contêiner, definir suas permissões para acesso público e criar um blob no contêiner. Ele também especifica explicitamente um intervalo de atualização desejado definindo o cabeçalho Cache-Control no blob.

Supondo que você tenha habilitado a CDN como mostrado acima, o blob que será criado será armazenado em cache pela CDN. Especifique as credenciais de sua conta usando sua própria conta de armazenamento e chave de acesso:

```csharp
using System;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.StorageClient;

namespace BlobsInCDN
{
	class Program
	{
		static void Main(string[] args)
		{
			//Specify storage credentials.
			StorageCredentialsAccountAndKey credentials = new StorageCredentialsAccountAndKey("storagesample",
				"<your storage account key>");

			//Create a reference to your storage account, passing in your credentials.
			CloudStorageAccount storageAccount = new CloudStorageAccount(credentials, true);

			//Create a new client object, which will provide access to Blob service resources.
			CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

			//Create a new container.
			CloudBlobContainer container = blobClient.GetContainerReference("cdncontent");
			container.CreateIfNotExist();

			//Specify that the container is publicly accessible.
			BlobContainerPermissions containerAccess = new BlobContainerPermissions();
			containerAccess.PublicAccess = BlobContainerPublicAccessType.Container;
			container.SetPermissions(containerAccess);

			//Create a new blob and write some text to it.
			CloudBlob blob = blobClient.GetBlobReference("cdncontent/testblob.txt");
			blob.UploadText("This is a test blob.");

			//Set the Cache-Control header on the blob to specify your desired refresh interval.
			blob.SetCacheControl("public, max-age=31536000");
		}
	}

	public static class BlobExtensions
	{
		//A convenience method to set the Cache-Control header.
		public static void SetCacheControl(this CloudBlob blob, string value)
		{
			blob.Properties.CacheControl = value;
			blob.SetProperties();
		}
	}
}
```

Teste se o blob está disponível por meio da URL específica da CDN. Para o blob mostrado acima, a URL seria semelhante a:

	http://<endpoint>.azureedge.net/cdncontent/testblob.txt  

Se desejar, você poderá usar uma ferramenta como **wget** ou Fiddler para examinar os detalhes da solicitação e da resposta.

##Consulte também

[Como gerenciar a expiração do conteúdo do serviço de nuvem na CDN do Azure (Rede de Distribuição de Conteúdo)](./cdn-manage-expiration-of-cloud-service-content.md)

<!---HONumber=AcomDC_0803_2016-->