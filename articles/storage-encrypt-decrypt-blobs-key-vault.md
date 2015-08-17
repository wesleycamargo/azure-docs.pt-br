<properties
   pageTitle="Criptografar e Descriptografar Blobs no Armazenamento do Microsoft Azure usando o Cofre da Chave do Azure"
   description="Este tutorial explica como criptografar e descriptografar um blob usando a criptografia do lado do cliente para o armazenamento da Cofre da Chave do Armazenamento do Microsoft Azure"
   services="storage"
   documentationCenter=""
   authors="adhurwit"
   manager=""
   editor=""/>

<tags
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="06/17/2015"
   ms.author="adhurwit"/>

# Criptografar e Descriptografar Blobs no Armazenamento do Microsoft Azure usando o Cofre da Chave do Azure

## Introdução
 
Este tutorial aborda como aproveitar a criptografia de armazenamento do cliente, atualmente em visualização - com o Cofre da Chave do Azure - atualmente em visualização. Ele explica como criptografar e descriptografar um blob em um aplicativo de console usando essas tecnologias.

**Tempo estimado para conclusão:** 20 minutos

Para obter informações gerais sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](key-vault/key-vault-whatis.md)

Para obter informações gerais sobre a criptografia de cliente do Armazenamento do Azure, consulte [Criptografia do Lado do Cliente do Armazenamento do Microsoft Azure – Visualização](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview.aspx)


## Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

- Uma conta de armazenamento do Azure
- Visual Studio 2013 ou posterior.
- PowerShell do Azure 


## Visão geral do processo de Criptografia do Lado do Cliente

Para obter uma visão geral da criptografia do lado do cliente de armazenamento do Microsoft Azure, consulte [http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx "Introdução à criptografia do lado do cliente para o Armazenamento do Microsoft Azure")

Este é o processo conforme descrito na postagem no blog:

1. O SDK do cliente do Armazenamento do Azure irá gerar uma chave de criptografia do conteúdo (CEK) que é uma chave simétrica de um uso.
2. Os dados do usuário são criptografados usando esse CEK.
3. O CEK é empacotado (criptografada) usando a chave de criptografia de chave KEK. O KEK é identificado por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica e pode ser gerenciada localmente ou armazenada no Cofre da Chave do Azure. O cliente de Armazenamento não tem acesso a KEK. Ele simplesmente chama o algoritmo de quebra de chave fornecido pelo Cofre da Chave. Os usuários podem escolher usar provedores personalizados para desempacotamento/quebra da chave, se desejado.
4. Os dados criptografados, em seguida, são carregados para o serviço de Armazenamento do Azure.


## Configure o seu Cofre da Chave do Azure
Para continuar este tutorial, você precisa fazer o seguinte descrito no tutorial: [Introdução à Chave de Cofre do Azure](key-vault/key-vault-get-started.md)

- Criar um cofre de chave
- Adicionar uma chave ou segredo ao cofre de chave
- Registrar um aplicativo com o Active Directory do Azure
- Autorizar o aplicativo a usar a chave ou segredo

Anote o ClientID e ClientSecret que foram gerados ao registrar um aplicativo com o Active Directory do Azure.

Crie dois uma chave no cofre de chave. Vamos supor que o restante do tutorial que você usou os seguintes nomes: ContosoKeyVault e TestRSAKey1.


## Criar um aplicativo de console com pacotes e AppSettings

No Visual Studio, crie um novo Aplicativo de Console.

Adicione pacotes nuget necessárias no Console do Gerenciador de Pacotes:

	// Note that this is the preview version for Azure Storage
	Install-Package WindowsAzure.Storage -Pre

	// This is the latest stable release for ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	// These are currently only available in preview
	Install-Package Microsoft.Azure.KeyVault -Pre
	Install-Package Microsoft.Azure.KeyVault.Extensions -Pre


Adicione AppSettings a App.Config.

	<appSettings>
	    <add key="accountName" value="myaccount"/>
	    <add key="accountKey" value="theaccountkey"/>
	    <add key="clientId" value="theclientid"/>
	    <add key="clientSecret" value="theclientsecret"/>
    	<add key="container" value="stuff"/>
	</appSettings>

Adicione as seguintes instruções using e certifique-se de adicionar uma referência a System.Configuration ao projeto.

	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Configuration;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using Microsoft.Azure.KeyVault;
	using System.Threading;		
	using System.IO;


## Adicione um método para obter um token para o aplicativo de console

O método a seguir é usado pelas classes do Cofre da Chave que precisam ser autenticados para acessar seu Cofre de Chave.

	private async static Task<string> GetToken(string authority, string resource, string scope)
	{
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(
	        ConfigurationManager.AppSettings["clientId"], 
	        ConfigurationManager.AppSettings["clientSecret"]);
		AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	
	    if (result == null)
	        throw new InvalidOperationException("Failed to obtain the JWT token");
	
	    return result.AccessToken;
	}

## Acesse o armazenamento e o Cofre da Chave em seu programa 

Na função Main, adicione o seguinte código:

	// This is standard code to interact with Blob Storage
	StorageCredentials creds = new StorageCredentials(
		ConfigurationManager.AppSettings["accountName"],
       	ConfigurationManager.AppSettings["accountKey"]);
	CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
	CloudBlobClient client = account.CreateCloudBlobClient();
	CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
	contain.CreateIfNotExists();

	// The Resolver object is used to interact with Key Vault for Azure Storage
	// This is where the GetToken method from above is used
	KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);


> [AZURE.NOTE]Modelos de Objetos de Chave de Cofre
>
>É importante entender que há realmente dois Chave de Cofre modelos de objeto estar atento: uma baseada na API REST (namespace KeyVault) e a outra é uma extensão para criptografia do lado do cliente.

> O Cliente do Cofre da Chave interage com a API REST e compreende JSON Web chaves e segredos para os dois tipos de coisas que estão contidos no Cofre da Chave.

> As Extensões de Chave de Cofre são classes que parecem criados especificamente para a criptografia de cliente no Armazenamento do Azure. Eles contêm uma interface para chaves - IKey - e classes com base no conceito de um resolvedor de chave. Há duas implementações de IKey que você precisa saber: RSAKey e SymmetricKey. Agora que eles aconteçam coincidir com as coisas que estão contidas em um Cofre da Chave, mas agora são classes independentes (para a chave e o segredo recuperada pelo cliente Cofre da Chave não implementam IKey).


## Criptografar o blob e carregar
Adicione o seguinte código para criptografar um Blob e carregá-lo à sua conta de Armazenamento do Azure. O método ResolveKeyAsync usado retorna um IKey.

	
	// Retrieve the key that you created previously
	// The IKey that is returned here is an RsaKey
	// Remember that we used the names contosokeyvault and testrsakey1
    var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();


	// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy. 
	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

	// Reference a block blob
	CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

	// Upload using the UploadFromStream method
	using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
		blob.UploadFromStream(stream, stream.Length, null, options, null);


A seguir está uma captura de tela do portal de Gerenciamento do Azure atual para um blob que foi criptografado usando a criptografia de cliente com uma chave armazenada no Cofre da Chave. A propriedade KeyId é o URI para a chave no Cofre da Chave que atua como a chave de criptografia de chave (KEK). A propriedade EncryptedKey contém a versão criptografada da chave de criptografia de conteúdo (CEK).

![Captura de tela mostrando os metadados de Blob que inclui metadados de criptografia][1]

> [AZURE.NOTE]Se você examinar o construtor BlobEncryptionPolicy, você verá que ele pode aceitar uma chave e/ou um resolvedor. Lembre-se de que agora, você não pode usar um resolvedor para criptografia porque atualmente não dá suporte a uma chave padrão.



## Descriptografar o blob e baixar
Descriptografia é realmente quando as classes de resolvedor fazem sentido. A ID da chave usada para criptografia é associada ao Blob em seus metadados, portanto, não há motivo para recuperar a chave e lembre-se a associação entre a chave e o blob. Você precisa certificar-se de que a chave permanece no Cofre da Chave.

A chave privada de uma chave RSA permanece no Cofre da Chave, de forma a que descriptografia que ocorre por meio da Chave Criptografada dos metadados do blob que contêm o CEC (chave de criptografia do conteúdo) seja enviada ao Cofre da Chave para descriptografia.

Adicione o seguinte para descriptografar o blob que você acabou de carregar.

	// In this case we will not pass a key and only pass the resolver because 
	// 	this policy will only be used for downloading / decrypting
	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
	    blob.DownloadToStream(np, null, options, null);


> [AZURE.NOTE]Existem alguns outros tipos de Resolvedores para facilitar o gerenciamento de chaves, incluindo: AggregateKeyResolver e CachingKeyResolver.


## Usando os Segredos do Cofre da Chave
A maneira de usar um Segredo com criptografia do lado do cliente é por meio da classe SymmetricKey, dado que um segredo é essencialmente uma chave simétrica. Mas, conforme observado anteriormente, um segredo no cofre de chave não é mapeado exatidão para um SymmetricKey. Há algumas coisas que deve compreender:


- A chave em um SymmetricKey deve ter um comprimento fixo: 192, 128, 256, 384 ou 512 bits
- A chave em um SymmetricKey deve ser codificado em Base64
- Um segredo do Cofre da Chave que será usada como um SymmetricKey deve ter um tipo de conteúdo de "application/octet-stream" no Cofre da Chave

Aqui está um exemplo no PowerShell sobre a criação de um Segredo no Cofre da Chave que pode ser usado como um SymmetricKey:

	// Here we are making a 128-bit key so we have 16 characters. 
	// 	The characters are in the ASCII range of UTF8 so they are
	//	each 1 byte. 16 x 8 = 128
	$key = "qwertyuiopasdfgh"
	$b = [System.Text.Encoding]::UTF8.GetBytes($key)
	$enc = [System.Convert]::ToBase64String($b)
	$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

	// substitute the VaultName and Name in this command
	$secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"

Em seu aplicativo de console, você pode usar a mesma chamada como antes, para recuperar esse segredo como um SymmetricKey.

	SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    	"https://contosokeyvault.vault.azure.net/secrets/TestSecret2/", 
        CancellationToken.None).GetAwaiter().GetResult();

É isso. Aproveite!

## Próximas etapas

Para obter mais informações sobre como usar o armazenamento do Microsoft Azure com C#, consulte [Biblioteca de Cliente de Armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)

Para obter mais informações sobre a API REST do Blob, consulte [API REST do Serviço Blob](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Para obter as informações mais recentes sobre o Armazenamento do Microsoft Azure, vá para o [Blog da Equipe de Armazenamento do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/)


<!--Image references-->
[1]: ./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png

<!---HONumber=August15_HO6-->