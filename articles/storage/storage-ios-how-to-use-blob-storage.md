<properties
    pageTitle="Como usar o Armazenamento de Blob do Azure no iOS | Microsoft Azure"
	description="Armazene dados não estruturados na nuvem com o Armazenamento de Blobs do Azure (armazenamento de objeto)."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Objective-C"
    ms.topic="article"
    ms.date="04/11/2016"
    ms.author="micurd"/>

# Como usar o armazenamento de Blob no iOS

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Visão geral

Este artigo mostra como executar cenários comuns usando o armazenamento de Blob do Microsoft Azure. Os exemplos são escritos em Objective-C e usam a [Biblioteca do Cliente de Armazenamento do Azure para iOS](https://github.com/Azure/azure-storage-ios). Os cenários cobertos incluem **carregamento**, **listagem**, **download** e **exclusão** de blobs. Para obter mais informações sobre blobs, consulte a seção [Próximas etapas](#next-steps). Você também pode baixar o [aplicativo de exemplo](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) para ver rapidamente o uso do Armazenamento do Azure em um aplicativo do iOS.

[AZURE.INCLUDE [armazenamento-blob-conceitos-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Criar o arquivo de estrutura
Para usar a biblioteca de iOS do Armazenamento do Azure, primeiro você precisará criar o arquivo de estrutura.

1. Primeiro, baixe ou clone o [repositório azure-storage-ios](https://github.com/azure/azure-storage-ios).

2. Vá para *azure-storage-ios* -> *Lib* -> *Biblioteca de Cliente de Armazenamento do Azure* e abra `Azure Storage Client Library.xcodeproj` no Xcode.

3. No canto superior esquerdo do Xcode, altere o esquema ativo de "Biblioteca de Cliente de Armazenamento do Azure" para "Estrutura".

4. Compile o projeto (⌘+B). Isso criará um arquivo `Azure Storage Client Library.framework` na Área de Trabalho.

## Importar a biblioteca do iOS de Armazenamento do Azure para seu aplicativo

Você pode importar a biblioteca de iOS do Armazenamento do Azure para seu aplicativo fazendo o seguinte:

1. Crie um novo projeto ou abra um projeto existente no Xcode.

2. Clique no projeto no painel de navegação esquerdo e clique na guia *Geral* na parte superior do editor de projeto.

3. Na seção *Estruturas e Bibliotecas Vinculadas*, clique no botão Adicionar (+).

4. Clique em *Adicionar outro...*. Navegue até o arquivo `Azure Storage Client Library.framework` recém-criado e adicione-o.

5. Na seção *Estruturas e Bibliotecas Vinculadas*, clique no botão Adicionar (+) novamente.

6. Na lista de bibliotecas já fornecida, pesquise `libxml2.2.dylib` e adicione-a ao projeto.

##Instrução import
Você precisará incluir a instrução de importação a seguir ao arquivo em que deseja invocar a API de Armazenamento do Azure.

    // Include the following import statement to use blob APIs.
    #import <Azure Storage Client Library/Azure_Storage_Client_Library.h>

## Configurar seu aplicativo para acessar o armazenamento de blobs

Há duas maneiras de autenticar o aplicativo para acessar os serviços de Armazenamento:

- Chave compartilhada: usar a chave compartilhada somente para fins de teste
- SAS (Assinatura de Acesso Compartilhado): use a SAS para aplicativos de produção

###Chave compartilhada
A autenticação de Chave Compartilhada significa que o aplicativo usará seu nome da conta e chave de conta para acessar os serviços de Armazenamento. Para mostrar rapidamente como usar o Armazenamento de Blob do iOS, vamos usar a autenticação de Chave Compartilhada nesta introdução.

> [AZURE.WARNING (Only use Shared Key authentication for testing purposes!) ] Seu nome de conta e chave de conta, que dão acesso completo de leitura/gravação à conta de Armazenamento associada, serão distribuídos a todas as pessoas que baixarem o aplicativo. Isso **não** é uma prática recomendada, pois você corre o risco de ter sua chave comprometida por clientes não confiáveis.

Ao usar a autenticação de Chave Compartilhada, você criará uma cadeia de caracteres de conexão. A cadeia de conexão é composta dos seguintes itens:

- O **DefaultEndpointsProtocol** -você pode escolher HTTP ou HTTPS. No entanto, é altamente recomendável usar HTTPS.
- O **Nome da Conta** - o nome de sua conta de armazenamento
- A **Chave da Conta** - Se estiver usando o [Portal do Azure](https://portal.azure.com), navegue até a conta de armazenamento e clique no ícone **Chaves** para encontrar essa informação. Se usar o [Portal Clássico do Azure](https://manage.windowsazure.com), navegue até a conta de armazenamento no portal e clique em **Gerenciar Chaves de Acesso**. 

Veja como será sua aparência no aplicativo:

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=account_name;AccountKey=account_key"];

###SAS (Assinaturas de Acesso Compartilhado)
Para um aplicativo do iOS, o método recomendado para autenticar uma solicitação por um cliente em relação ao armazenamento de Blob é usar uma SAS (Assinatura de Acesso Compartilhado). A SAS permite conceder a um cliente acesso a um recurso por um período de tempo especificado, com um conjunto de permissões especificado. Como proprietário da conta de armazenamento, você precisará gerar uma SAS a ser consumida por seus clientes do iOS. Para gerar a SAS, provavelmente você desejará escrever um serviço separado que gere a SAS a ser distribuída a seus clientes. Para fins de teste, você também pode usar a CLI do Azure para gerar uma SAS. Observe que as credenciais de chave compartilhada são usadas para gerar uma SAS, mas seus clientes podem então consumir a SAS usando as informações de autenticação encapsuladas na URL da SAS. Ao criar a SAS, você pode especificar o intervalo de tempo em que a SAS é válida e as permissões que a SAS concede ao cliente. Por exemplo, para um contêiner de blob, a SAS pode conceder permissões de leitura, gravação ou exclusão para um blob no contêiner e permissões de listagem para listar os blobs no contêiner.

O exemplo a seguir mostra como usar a CLI do Azure para gerar um token de SAS que concede permissões de leitura e gravação para o contêiner, *sascontainer*, até às 12h (UTC) de 5 de setembro de 2015.

1. Primeiro, consulte [Instalar a CLI do Azure](../xplat-cli-install.md) para aprender a instalar a CLI do Azure e conecte-se à sua assinatura do Azure.

2. Em seguida, digite este comando na CLI do Azure para obter a cadeia de conexão para sua conta:

		azure storage account connectionstring show youraccountname

3. Crie uma variável de ambiente com a cadeia de conexão que você acabou de gerar:

		export AZURE_STORAGE_CONNECTION_STRING=”your connection string”

4. Gere a URL da SAS:

		azure storage container sas create --container sascontainer --permissions rw --expiry 2015-09-05T00:00:00

5. A URL da SAS deve ser semelhante ao seguinte:

		https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-29T22%3A18%3A26Z&se=2013-04-30T02%3A23%3A26Z&sr=b&sp=rw&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

6. No aplicativo do iOS, agora você pode obter uma referência ao contêiner usando a URL da SAS da seguinte maneira:

		// Get a reference to a container in your Storage account
    	AZSCloudBlobContainer *blobContainer = [[AZSCloudBlobContainer alloc] initWithUrl:[NSURL URLWithString:@" your SAS URL"]];

Como você pode ver, ao usar um token de SAS, você não expõe seu nome de conta e chave de conta no aplicativo do iOS. Você pode aprender mais sobre SAS consultando [Assinaturas de Acesso Compartilhado: noções básicas sobre o modelo SAS](storage-dotnet-shared-access-signature-part-1.md).

##Operações assíncronas
> [AZURE.NOTE] Todos os métodos que realizam uma solicitação ao serviço são operações assíncronas. Nos exemplos de código, você verá que esses métodos têm um manipulador de conclusão. O código no manipulador de conclusão será executado **após** a solicitação ser concluída. O código depois do manipulador de conclusão será executado **enquanto** a solicitação está sendo feita.

## Criar um contêiner
Todos os blobs no Armazenamento do Azure devem residir em um contêiner. O exemplo a seguir mostra como criar um contêiner, chamado *newcontainer*, em sua Conta de armazenamento, se ele ainda não existir. Ao escolher um nome para o contêiner, leve em conta as regras de nomenclatura mencionadas acima.

     -(void)createContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

Você pode confirmar que isso funciona observando o [Portal do Azure](https://portal.azure.com) ou qualquer [Gerenciador de armazenamento](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) e verificando se *newcontainer* está na lista de contêineres para sua conta de Armazenamento.

## Definir permissões de contêiner
As permissões do contêiner são configuradas para acesso **privado** por padrão. No entanto, os contêineres fornecem algumas opções diferentes para acesso ao contêiner:

- **Privado**: os dados de contêiner e blob podem ser lidos apenas pelo proprietário da conta.

- **Blob**: os dados do blob nesse contêiner podem ser lidos por meio de solicitação anônima, mas os dados do contêiner não estão disponíveis. Os clientes não podem enumerar os blobs no contêiner por meio de uma solicitação anônima.

- **Contêiner**: os dados do contêiner e do blob podem ser lidos por solicitação anônima. Os clientes podem enumerar os blobs no contêiner por meio de uma solicitação anônima, mas não podem enumerar os contêineres em uma conta de armazenamento.

O exemplo a seguir mostra como criar um contêiner com permissões de acesso de **Contêiner** que permitirão o acesso público e somente leitura para todos os usuários na Internet:

     -(void)createContainerWithPublicAccess{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## Carregar um blob em um contêiner
Conforme mencionado na seção [Conceitos do serviço de Blob](#blob-service-concepts), o Armazenamento de Blob oferece três tipos diferentes de blobs: blobs de bloco, blobs de acréscimo e blobs de página. No momento, a biblioteca do iOS de Armazenamento do Azure dá suporte apenas a blobs de bloco. Na maioria dos casos, o blob de blocos é o tipo recomendado a ser usado.

O exemplo a seguir mostra como carregar um blob de bloco de um NSString. Se um blob com o mesmo nome já existir no contêiner, o conteúdo desse blob será substituído.

     -(void)uploadBlobToContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
         	 else{
				 // Create a local blob object
             	 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

             	 // Upload blob to Storage
             	 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                 	if (error){
						NSLog(@"Error in creating blob.");
                 	}             
             	 }];
			 }
         }];
     }

Você pode confirmar que isso funciona observando o [Portal do Azure](https://portal.azure.com) ou qualquer [Gerenciador de armazenamento](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) e verificando se o contêiner, *containerpublic*, contém o blob *sampleblob*. Neste exemplo, usamos um contêiner público, então, você também pode verificar se isso funcionou indo para o URI de blobs:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Além de carregar um blob de bloco de um NSString, existem métodos semelhantes para NSData, NSInputStream ou um arquivo local.

## Listar os blobs em um contêiner
O exemplo a seguir mostra como listar todos os blobs em um contêiner. Ao executar essa operação, leve em conta os seguintes parâmetros:

- **continuationToken** - O token de continuação representa onde a operação de listagem deve começar. Se nenhum token for fornecido, ele listará os blobs desde o início. Qualquer número de blobs pode ser listado, desde zero até um máximo definido. Mesmo que esse método retorne zero resultado, se `results.continuationToken` não for nulo, poderá haver mais blobs no serviço que não foram listados.
- **prefixo** -Você pode especificar o prefixo a ser usado para a listagem de blobs. Somente os blobs que começarem com esse prefixo serão listados.
- **useFlatBlobListing** - Conforme mencionado na seção [Nomeando e referenciando contêineres e blobs](#naming-and-referencing-containers-and-blobs), embora o serviço de Blob seja um esquema de armazenamento simples, você pode criar uma hierarquia virtual nomeando blobs com informações de caminho. No entanto, atualmente não há suporte à listagem não simples. Isso estará disponível em breve. Por enquanto, esse valor deve ser `YES`
- **blobListingDetails** - Você pode especificar os itens a serem incluídos ao listar blobs
	- `AZSBlobListingDetailsNone`: lista apenas os blobs confirmados e não retorna metadados de blob.
	- `AZSBlobListingDetailsSnapshots`: lista os blobs confirmados e instantâneos de blob.
	- `AZSBlobListingDetailsMetadata`: recupera metadados de blob para cada blob retornado na listagem.
	- `AZSBlobListingDetailsUncommittedBlobs`: lista os blobs confirmados e não confirmados.
	- `AZSBlobListingDetailsCopy`: incluir propriedades de cópia na listagem.
	- `AZSBlobListingDetailsAll`: lista todos os blobs confirmados disponíveis, blobs não confirmados e instantâneos e retorna todos os metadados e status de cópia para os blobs.
- **maxResults** - O número máximo de resultados a serem retornados para a operação. Use -1 para não definir um limite.
- **completionHandler** - O bloco de código a ser executado com os resultados da operação de listagem.

Neste exemplo, um método auxiliar é usado para chamar recursivamente o método de listagem de blobs sempre que um token de continuação é retornado.

    -(void)listBlobsInContainer{
      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      //List all blobs in container
      [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
         if (error != nil){
             NSLog(@"Error in creating container.");
         }
      }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
      [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
         if (error)
         {
             completionHandler(error);
         }
         else
         {
             for (int i = 0; i < results.blobs.count; i++) {
                 NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
             }
             if (results.continuationToken)
             {
                 [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
             }
             else
             {
                 completionHandler(nil);
             }
         }
      }];
    }

## Baixar um blob

O exemplo a seguir mostra como baixar um blob para um objeto NSString.

     -(void)downloadBlobToString{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob    
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
			else{
            	NSLog(@"%@",text);
			}
        }];
    }

## Excluir um blob

O exemplo a seguir mostra como excluir um blob.

     -(void)deleteBlob{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
          if (error) {
            NSLog(@"Error in deleting blob.");
          }
        }];
     }

## Excluir um contêiner de blob

O exemplo a seguir mostra como excluir um contêiner.

     -(void)deleteContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Delete container
        [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
            if(error){
                NSLog(@"Error in deleting container");
            }
        }];
    }

## Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de blob, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- [Biblioteca de Cliente do Armazenamento do Azure para iOS](https://github.com/azure/azure-storage-ios)
- [API REST de serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md)
- [Blog da equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage)

Se você tiver dúvidas sobre a biblioteca, fique à vontade para postar em nosso [Fórum do Azure do MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) ou no [Excedente de pilha](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files). Se você tiver sugestões de recursos para o Armazenamento do Azure, poste nos [Comentários do Armazenamento do Azure](https://feedback.azure.com/forums/217298-storage/).

<!---HONumber=AcomDC_0511_2016-->