<properties 
	pageTitle="Como usar o armazenamento de Blob do Node.js | Microsoft Azure" 
	description="Saiba como usar o serviço Blob do Azure para carregar, baixar, listar e excluir o conteúdo de blob. Amostras escritas em Node.js." 
	services="storage" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="mwasson"/>



# Como usar o Armazenamento de Blob do Node.js

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Visão geral

Este guia mostra como executar cenários comuns usando o serviço Blob do Azure. As amostras são gravadas usando a API do Node.js. Os cenários cobertos incluem **carregamento**, **listagem**, **download** e **exclusão** de blobs.

[AZURE.INCLUDE [armazenamento-blob-conceitos-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Criar um aplicativo do Node.js

Criar um aplicativo Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js em um site do Azure], [Serviço de Nuvem do Node.js][Node.js Cloud Service] (usando o Windows PowerShell) ou [Site com o WebMatrix].

## Configurar seu aplicativo para acessar o armazenamento

Para usar o armazenamento do Azure, você precisa do SDK de Armazenamento do Azure para Node.js, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST do armazenamento.

### Usar o NPM (gerenciador de pacotes de nós) para obter o pacote

1.  Use uma interface de linha de comando, como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix), e navegue até a pasta onde você criou o aplicativo de exemplo.

2.  Digite **npm install azure-storage** na janela de comando, que deve resultar na seguinte saída:

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  Você pode executar o comando **ls** manualmente para verificar se uma pasta **node_modules** foi criada. Dentro dessa pasta, encontre o pacote **azure-storage**, que contém as bibliotecas necessárias para acessar o armazenamento.

### Importar o pacote

Usando o Bloco de Notas ou outro editor de texto, adicione o seguinte à parte superior do arquivo **server.js** do aplicativo no qual você pretende usar o armazenamento:

    var azure = require('azure-storage');

## Configurar uma conexão de armazenamento do Azure

O módulo do Azure lerá as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY`, ou `AZURE_STORAGE_CONNECTION_STRING` para obter as informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta chamando **createBlobService**.

Para obter um exemplo de como definir variáveis de ambiente no portal de gerenciamento de um Site do Azure, consulte [Aplicativo Node.js na Web com armazenamento].

## Como: criar um contêiner

O objeto **serviço Blob** permite que você trabalhe com contêineres e blobs. O código a seguir cria um objeto **BlobService**. Adicione o seguinte, próximo à parte superior do **server.js**:

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE]Você pode acessar um blob anonimamente usando **createBlobServiceAnonymous** e fornecendo o endereço do host. Por exemplo: `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.

[AZURE.INCLUDE [armazenamento-contêiner-nomeando-regras-include](../../includes/storage-container-naming-rules-include.md)]

Para criar um novo contêiner, use **createContainerIfNotExists**. O exemplo a seguir cria um novo contêiner denominado 'mycontainer'

	blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows 
        // anonymous read access to blob 
        // content and metadata within this container
      }
	});

Se o container for criado, `result` será true. Se o contêiner já existir, `result` será false. `response` conterá informações sobre a operação, incluindo as informações de [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) para o contêiner.

### Segurança do contêiner

Por padrão, novos contêineres são privados e não podem ser acessados ​​anonimamente. Para tornar o contêiner público, para que eles possam ser acessados de forma anônima, você poderá definir o nível de acesso do contêiner como **blob** ou **contêiner**.

* **blob** – permite acesso anônimo de leitura ao conteúdo e aos metadados do blob dentro desse contêiner, mas não aos metadados do contêiner, como a listagem de todos os blobs de um contêiner. 

* **contêiner** – permite acesso anônimo de leitura a conteúdo e metadados do blob e também aos metadados do contêiner.

O exemplo as seguir demonstra a configuração do nível de acesso para **blob**:

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
      if(!error){
        // Container exists and is private
      }
	});

Como alternativa, você poderá modificar o nível de acesso de um contêiner, usando **setContainerAcl** para especificar o nível de acesso. O exemplo a seguir altera o nível de acesso para contêiner:

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, 'container' /* publicAccessLevel*/, function(error, result, response){
	  if(!error){
		// Container access level set to 'container'
	  }
	});

O resultado irá conter informações sobre a operação, incluindo a atual **ETag** do contêiner.

### Filtros

É possível aplicar operações de filtragem opcionais às operações executadas usando **BlobService**. As operações de filtragem podem incluir registro em log, repetição automática, etc. Os filtros são objetos que implementam um método com a assinatura:

		function handle (requestOptions, next)

Após fazer seu pré-processamento nas opções de solicitação, o método precisará chamar "next", passando um retorno de chamada com a assinatura a seguir:

		function (returnObject, finalCallback, next)

Nesse retorno de chamada, e após processar o returnObject (a resposta da solicitação ao servidor), o retorno de chamada precisará invocar next, se ele existir, para continuar processando outros filtros ou simplesmente invocar finalCallback para terminar a invocação de serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte código cria um objeto **BlobService** que usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobSvc = azure.createBlobService().withFilter(retryOperations);

## Como: carregar um blob em um contêiner

Um blob pode ser baseado em bloco ou página. Blobs de bloco permitem que você envie mais eficientemente grandes volumes de dados, enquanto blobs de página são otimizados para operações de leitura/gravação. Para obter mais informações, consulte [Noções básicas sobre blobs de bloco e blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### Blobs de bloco

Para transferir dados para um blob de bloco, use o seguinte:

* **createBlockBlobFromLocalFile** – cria um novo blob de bloco e carrega o conteúdo de um arquivo.

* **createBlockBlobFromStream** – cria um novo blob de bloco e carrega o conteúdo de um fluxo.

* **createBlockBlobFromText** – cria um novo blob de bloco e carrega o conteúdo de uma cadeia de caracteres.

* **createWriteStreamToBlockBlob** – fornece um fluxo de gravação para um blob de bloco.

O exemplo a seguir carrega o conteúdo do arquivo **test.txt** em **myblob**.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

O `result` retornado por esses métodos conterá informações sobre a operação, como o **ETag** do blob.

### Blobs de página

Para carregar dados para um blob de página, use o seguinte:

* **createPageBlob** – cria um novo blob de página de um comprimento específico.

* **createPageBlobFromLocalFile** – cria um novo blob de página e carrega o conteúdo de um arquivo.

* **createPageBlobFromStream** – cria um novo blob de página e carrega o conteúdo de um fluxo.

* **createWriteStreamToExistingPageBlob** – fornece um fluxo de gravação para um blob de página.

* **createWriteStreamToNewPageBlob** – cria um novo blob e, em seguida, fornece um fluxo para gravar nele.

O exemplo a seguir carrega o conteúdo do arquivo **test.txt** em **mypageblob**.

	blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

> [AZURE.NOTE]Blobs de página consistem em 'páginas’ de 512 bytes. Você pode receber um erro ao carregar dados com um tamanho que não seja um múltiplo de 512.

## Como: listar os blobs em um contêiner

Para listar os blobs em um contêiner, use o método **listBlobsSegmented**. Para retornar blobs com um prefixo específico, use **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result contains the entries
	  }
	});

O `result` conterá uma coleção de `entries`, que é uma matriz de objetos que descrevem cada blob. Se todos os blobs não puderem ser retornados, o `result` também fornecerá um `continuationToken`, que poderá ser usado como o segundo parâmetro para recuperar entradas adicionais.

## Como: baixar blobs

Para baixar dados de um blob, use o seguinte:

* **getBlobToFile** – grava o conteúdo do blob em um arquivo

* **getBlobToStream** – grava o conteúdo do blob em um fluxo.

* **getBlobToText** – grava o conteúdo do blob em uma cadeia de caracteres.

* **createReadStream** – fornece um fluxo de leitura a partir do blob

O exemplo a seguir demonstra o uso de **getBlobToStream** para baixar o conteúdo do blob **myblob** e armazená-lo no arquivo **output.txt** usando um fluxo:

    var fs = require('fs');
	blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
	  if(!error){
	    // blob retrieved
	  }
	});

O `result` conterá informações sobre o blob, incluindo informações do **ETag**.

## Como excluir um blob

Finalmente, para excluir um blob, chame **deleteBlob**. O exemplo a seguir exclui o blob denominado **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
	  if(!error){
		// Blob has been deleted
	  }
	});

## Como realizar: Acesso simultâneo

Para suportar o acesso simultâneo a uma blob por meio de vários clientes ou várias instâncias do processo, você pode usar **ETags** ou **concessões**.

* **Etag** – fornece uma maneira de se detectar se o blob ou o contêiner foi modificado por outro processo.

* **Lease** – fornece acesso exclusivo, renovável para gravação ou exclusão de um blob por determinado período.

### ETag

As ETags devem ser usadas se você precisar permitir que vários clientes ou instâncias realizem gravações no blob simultaneamente. A ETag permite determinar se o contêiner ou o blob foi modificado desde que foi criado ou lido, o que lhe permite evitar a substituição de alterações aplicadas por outro cliente ou processo.

Condições ETag podem ser definidas usando o parâmetro opcional `options.accessConditions`. O exemplo a seguir carregará o arquivo **test.txt** apenas se o blob já existir e tiver o valor de ETag contido por `etagToMatch`.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { 'if-match': etagToMatch} }, function(error, result, response){
      if(!error){
	    // file uploaded
	  }
	});

O padrão geral ao usar ETags é:

1. Obter a ETag como o resultado de uma operação de criação, lista ou obtenção.

2. Executar uma ação, verificando se o valor da ETag não foi modificado.

Se o valor foi modificado, isso indica que outro cliente ou instância modificou o blob ou o contêiner desde que você obteve o valor da ETag.

### Concessão

Uma nova concessão pode ser adquirida através do método **acquireLease** especificando o blob ou o contêiner para o qual você deseja obter uma concessão. Por exemplo, o seguinte adquire uma concessão para **myblob**.

	blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
	  if(!error) {
	    console.log('leaseId: ' + result.id);
	  }
	});

Operações posteriores em **myblob** devem fornecer o parâmetro `options.leaseId`. A ID de concessão é retornada de **acquireLease** como `result.id`.

> [AZURE.NOTE]Por padrão, a duração da concessão é infinita. Você pode especificar uma duração não infinita (entre 15 e 60 segundos) fornecendo o parâmetro `options.leaseDuration`.

Para remover uma concessão, use **releaseLease**. Para interromper uma concessão, mas evitar que outras pessoas obtenham uma nova concessão até a expiração da duração original, use **breakLease**.

## Como: Trabalhar com assinaturas de acesso compartilhado

Assinaturas de Acesso Compartilhado (SAS) são uma forma segura de fornecer acesso granular a blobs e contêiner sem fornecer o nome ou as chaves da conta de armazenamento. As SAS são muitas vezes usadas para fornecer acesso limitado aos seus dados, como permitir que um aplicativo móvel acesse os blobs.

> [AZURE.NOTE]Embora você também possa permitir acesso anônimo aos blobs, a SAS permite que você ofereça acesso mais controlado, uma vez que você gera a SAS.

Um aplicativo confiável, como um serviço baseado em nuvem, gera uma SAS usando **generateSharedAccessSignature** de **BlobService**, e o oferece a um aplicativo não confiável ou semiconfiável. Por exemplo, um aplicativo móvel. A SAS é gerada utilizando uma política que descreve as datas inicial e final durante as quais a SAS é válida, assim como o nível de acesso concedido ao titular da SAS.

O exemplo a seguir gera uma nova política de acesso compartilhado que vai permitir que o titular da SAS execute operações de leitura no blob **myblob**, e expira 100 minutos após o momento em que é criado.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
	    
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};
	
	var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
	var host = blobSvc.host;

Observe que também devem ser fornecidas as informações do host, já que são necessárias quando o titular da SAS tenta acessar o contêiner.

O aplicativo cliente usa a SAS com **BlobServiceWithSAS** para executar operações no blob. O seguinte obtém informações sobre **myblob**.

	var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
	sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
	  if(!error) {
	    // retrieved info
	  }
	});

Como a SAS foi gerada só com acesso de leitura, se for feita uma tentativa de modificar o blob, será retornado um erro.

### Listas de controle de acesso

Você também pode usar uma ACL (Lista de Controle de Acesso) para definir a política de acesso para uma SAS. Isso é útil se você quiser permitir que vários clientes acessem um contêiner, mas oferece diferentes políticas de acesso para cada cliente.

Uma ACL é implementada através de um conjunto de políticas de acesso, com uma ID associada a cada política. O exemplo a seguir define duas políticas; uma para 'user1' e um para 'user2':

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

O exemplo a seguir obtém a ACL atual para **mycontainer**, em seguida, adiciona as novas políticas usando **setBlobAcl**. Essa abordagem permite:

	blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		blobSvc.setBlobAcl('mycontainer', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

Uma vez que a ACL foi definida, você pode criar uma SAS com base na ID de uma política. O exemplo a seguir cria uma nova SAS para 'user2':

	blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de blob, siga estes links para saber como executar tarefas de armazenamento mais complexas.

-   Leia a [Referência do SDK de Armazenamento do Azure para APIs de nó][]
-   Consulte a referência do MSDN: [Armazenando e acessando dados no Azure][].
-   Visite o [Blog da equipe do Armazenamento do Azure][].
-   Visite o repositório [SDK de armazenamento do Azure para nó][] no GitHub.

[SDK de armazenamento do Azure para nó]: https://github.com/Azure/azure-storage-node
[Criar e implantar um aplicativo Node.js em um site do Azure]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
[Aplicativo Node.js na Web com armazenamento]: ../storage-nodejs-use-table-storage-web-site.md
[Site com o WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
[using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure Management Portal]: http://manage.windowsazure.com
[Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
[Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Blog da equipe do Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Referência do SDK de Armazenamento do Azure para APIs de nó]: http://dl.windowsazure.com/nodestoragedocs/index.html
 

<!---HONumber=July15_HO2-->