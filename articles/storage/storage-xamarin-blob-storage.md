<properties
	pageTitle="Como usar o Armazenamento de Blob no Xamarin (visualização) | Microsoft Azure"
	description="A visualização da biblioteca de cliente do Armazenamento do Azure para Xamarin permite que os desenvolvedores criem aplicativos iOS, Android e da Windows Store com suas interfaces de usuário nativo. Este tutorial mostra como usar o Xamarin para criar um aplicativo Android que usa o Armazenamento de Blob do Azure."
	services="storage"
	documentationCenter="xamarin"
	authors="micurd"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="micurd"/>

# Como usar o Armazenamento de Blob no Xamarin (visualização)

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Visão geral

O Xamarin permite que os desenvolvedores usem uma base de código C# compartilhada para criar aplicativos iOS, Android e da Windows Store com suas interfaces de usuário nativo. A biblioteca de cliente do Armazenamento do Azure para Xamarin é uma biblioteca de visualização; observe que ela pode mudar no futuro.

Este tutorial mostra como usar o armazenamento de Blob do Azure com um aplicativo Android Xamarin. Se você quiser saber sobre o Armazenamento do Azure antes de entrarmos no código, consulte [Próximas etapas](#next-steps) ao final deste documento.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Gerar uma assinatura de acesso compartilhado

Ao desenvolver com a biblioteca de cliente do Armazenamento do Azure para Xamarin, não é possível autenticar o acesso a uma conta de Armazenamento do Azure usando as suas chaves de acesso da conta. Isso é para evitar que as suas credenciais de conta sejam distribuídas para usuários que possam baixar seu aplicativo. Em vez disso, recomendamos o uso de assinaturas de acesso compartilhado (SAS), que não expõem as suas credenciais de conta.

Neste guia, usaremos o PowerShell do Azure para gerar um token SAS. Em seguida, criaremos um aplicativo Xamarin que use a SAS gerada.

Primeiro, você precisará instalar o PowerShell do Azure. Para obter instruções, confira [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md#Install).

Em seguida, abra o PowerShell do Azure e execute os comandos a seguir. Lembre-se de substituir `ACCOUNT_NAME` e `ACCOUNT_KEY== ` por suas credenciais de conta de armazenamento. Substitua `CONTAINER_NAME` por um nome da sua escolha.

    PS C:\> $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
	PS C:\> New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
	PS C:\> $now = Get-Date
	PS C:\> New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

O URI de assinatura de acesso compartilhado do novo contêiner deve ser semelhante ao seguinte:

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

A assinatura de acesso compartilhado criada no contêiner será válida para o dia seguinte. A assinatura concede permissões completas (*por exemplo*, leitura, gravação, exclusão e lista) para os blobs do contêiner.

Para obter mais informações sobre as assinaturas de acesso compartilhado, consulte o [Tutorial de SAS para .NET](storage-dotnet-shared-access-signature-part-2.md).

## Criar um novo aplicativo Xamarin

Para este tutorial, criaremos nosso aplicativo Xamarin no Visual Studio. Siga estas etapas para criar o aplicativo:

1. Baixe e instale o [Visual Studio](https://www.visualstudio.com/).
2. Baixe e instale o [Xamarin](http://xamarin.com/platform).
3. Abra o Visual Studio e selecione **Arquivo -> Novo -> Projeto > Android > Aplicativo em branco (Android)**.
4. Clique com o botão direito no seu projeto no painel do Gerenciador de Soluções e selecione **Gerenciar Pacotes NuGet**. Em seguida, procure **armazenamento do Azure** e instale **Azure Storage 4.4.0-preview**.

Agora você deve ter um aplicativo que permite que você clique em um botão e incremente um contador.

## Usar a assinatura de acesso compartilhado para executar operações de contêiner

Em seguida, adicione o código para executar uma série de operações de contêiner usando o URI de SAS gerado.

Primeiro adicione as seguintes instruções **using**:

	using System.IO;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.Storage.Blob;


Em seguida, adicione uma linha para o token SAS. Substitua a cadeia de caracteres do `"SAS_URI"` pelo URI de SAS gerado no PowerShell do Azure. Em seguida, adicione uma linha para a chamada do método `UseContainerSAS` que criaremos abaixo. Observe que a palavra-chave **async** foi adicionada antes do delegado.


	public class MainActivity : Activity
	{
    	int count = 1;
    	string sas = "SAS_URI";
    	protected override void OnCreate(Bundle bundle)
    	{
        	base.OnCreate(bundle);

        	// Set our view from the "main" layout resource
        	SetContentView(Resource.Layout.Main);

        	// Get our button from the layout resource, and attach an event to it
        	Button button = FindViewById<Button>(Resource.Id.MyButton);

        	button.Click += async delegate	{
             	button.Text = string.Format("{0} clicks!", count++);
             	await UseContainerSAS(sas);
         	};
     }

Adicione um novo método, `UseContainerSAS`, no método `OnCreate`.

	static async Task UseContainerSAS(string sas)
	{
    	//Try performing container operations with the SAS provided.

    	//Return a reference to the container using the SAS URI.
    	CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));
    	string date = DateTime.Now.ToString();
    	try
    	{
        	//Write operation: write a new blob to the container.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_" + date + ".txt");

        	string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        	MemoryStream msWrite = new
        	MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        	msWrite.Position = 0;
        	using (msWrite)
         	{
             	await blob.UploadFromStreamAsync(msWrite);
         	}
         	Console.WriteLine("Write operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Write operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
     	try
     	{
        	//Read operation: Get a reference to one of the blobs in the container and read it.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
        	string data = await blob.DownloadTextAsync();

        	Console.WriteLine("Read operation succeeded for SAS " + sas);
        	Console.WriteLine("Blob contents: " + data);
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Additional error information: " + e.Message);
       		Console.WriteLine("Read operation failed for SAS " + sas);
        	Console.WriteLine();
     	}
     	Console.WriteLine();
     	try
     	{
        	//Delete operation: Delete a blob in the container.
         	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
         	await blob.DeleteAsync();

         	Console.WriteLine("Delete operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Delete operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
	}

## Executar o aplicativo

Agora você pode executar esse aplicativo em um emulador ou dispositivo Android.

Embora este guia de introdução tenha focalizado o Android, você pode usar o método `UseContainerSAS` nos seus aplicativos iOS ou da Windows Store. O Xamarin também permite que os desenvolvedores criem aplicativos do Windows Phone; no entanto, nossa biblioteca ainda não dá suporte a esse recurso.

## Próximas etapas

Neste tutorial, você aprendeu a usar o armazenamento de Blob do Azure e SAS com um aplicativo Xamarin. Como um exercício adicional, poderia ser aplicado um padrão semelhante para gerar um token SAS para uma tabela ou fila do Azure.

Saiba mais sobre blobs, tabelas e filas consultando os links a seguir:

[Introdução ao Armazenamento do Microsoft Azure](storage-introduction.md)
[Como usar o Armazenamento de Blobs no .NET](storage-dotnet-how-to-use-blobs.md)
[Como usar o Armazenamento de Tabelas no .NET](storage-dotnet-how-to-use-tables.md)
[Como usar o Armazenamento de Filas no .NET](storage-dotnet-how-to-use-queues.md)
[Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0114_2016-->