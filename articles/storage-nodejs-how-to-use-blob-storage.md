<properties linkid="dev-nodejs-how-to-blob-storage" urlDisplayName="Serviço Blob" pageTitle="Como usar o armazenamento de blob (Node.js) | Microsoft Azure" metaKeywords="Introdução ao blob do Azure, dados não estruturados do Azure, armazenamento não estruturado do Azure, blob do Azure, armazenamento de blob do Azure, Node.js para blob do Azure" description="Saiba como usar o serviço Blob do Azure para carregar, baixar, listar e excluir conteúdo de blob. Exemplos escritos em Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="Como usar o serviço Blob do Node.js" authors="" solutions="" manager="" editor="" />





# Como usar o serviço Blob do Node.js

Este guia mostra como executar cenários comuns usando o serviço Blob do Azure. As amostras são escritas usando a
API do Node.js. Os cenários cobertos incluem **carregamento**, **listagem**, **download** e **exclusão** de blobs. Para obter mais informações sobre blobs,
consulte a seção [Próximas etapas][].

## Sumário

* [O que é o serviço Blob?][]    
* [Conceitos][]    
* [Criar uma conta de Armazenamento do Azure][]   
* [Criar um aplicativo Node.js][]   
* [Configurar seu aplicativo para acessar o armazenamento][]   
* [Configurar uma cadeia de conexão de Armazenamento do Azure][]   
* [Como criar um contêiner][]   
* [Como carregar um blob em um contêiner][]   
* [Como listar os blobs em um contêiner][]   
* [Como baixar blobs][]   
* [Como excluir um blob][]   
* [Próximas etapas][]

## <a name="what-is"> </a>O que é o serviço Blob?

O serviço Blob do Azure é um serviço para armazenar grandes quantidades de dados não estruturados que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Um único blob pode ter centenas de gigabytes de tamanho e uma
única conta de armazenamento pode conter até 100 TB de blobs. Os usos comuns de
Blobs incluem:

-   Fornecendo imagens ou documentos diretamente em um navegador
-   Armazenando arquivos para acesso distribuído
-   Streaming de áudio e vídeo
-   Executando backup e recuperação de desastres seguros
-   Armazenando dados para análise por um serviço local ou hospedado do Azure

Você pode usar Blobs para expor dados publicamente para o mundo ou
em particular para armazenamento interno de aplicativo.

## <a name="concepts"> </a>Conceitos

O serviço Blob contém os seguintes componentes:

![Blob1](./media/storage-nodejs-how-to-use-blob-storage/blob1.jpg)

-   **Formato de URL:** os blobs são endereçáveis usando o seguinte formato de URL:
   
    	http://storageaccount.blob.core.windows.net/container/blob  
      
    O URL a seguir endereça um dos blobs no diagrama: 
 
	    http://sally.blob.core.windows.net/movies/MOV1.AVI

-   **Conta de Armazenamento:** Todo o acesso ao Armazenamento do Azure é feito através de uma conta de armazenamento. Este é o nível mais alto do
    namespace para o acesso de blobs. Uma conta pode conter um número ilimitado
    de contêineres, desde que seu tamanho total esteja abaixo de 100 TB.

-   **Contêiner:** um contêiner fornece um agrupamento de um conjunto de blobs.
    Todos os blobs devem ter um contêiner. Uma conta pode conter um número ilimitado de contêineres. Um contêiner pode armazenar um número ilimitado de blobs.

-   **Blob:** um arquivo de qualquer tipo e tamanho. Há dois tipos de blobs; bloco e página. A maioria dos arquivos são
    blobs de bloco. Um único blob de bloco pode ter até 200 GB de tamanho. Este tutorial
    usa blobs de bloco. Os blobs de página, um outro tipo de blob, podem ter até 1 TB de
    tamanho e são mais eficientes quando os intervalos de bytes em um arquivo são
    modificados com frequência.

## <a name="create-account"> </a>Criar uma conta de armazenamento do Azure

Para usar as operações de armazenamento, você precisa de uma conta de armazenamento do Azure. Você
pode criar uma conta de armazenamento seguindo essas etapas. (Você também pode
pode criar uma conta de armazenamento [usando a API REST][].)

1.  Faça logon no [Portal de Gerenciamento do Azure].

2.  Na parte inferior do painel de navegação, clique em **+NOVO**.

	![+novo](./media/storage-nodejs-how-to-use-blob-storage/plus-new.png)

3.  Clique em **Conta de Armazenamento** e depois em **Criação Rápida**.

	![Caixa de diálogo Criação rápida](./media/storage-nodejs-how-to-use-blob-storage/quick-storage.png)

4.  Na URL, digite um nome de subdomínio para usar na URI para a conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse valor se torna o nome do host no URI que é usado para lidar com os recursos Blob, Fila ou Tabela da assinatura.

5.  Escolha uma Região/Grupo de Afinidade no qual deseja localizar o armazenamento. Se você usar o armazenamento de seu aplicativo do Azure, selecione a mesma região onde você implantará seu aplicativo.

6.  Clique em **Criar Conta de Armazenamento**.

## <a name="create-app"> </a>Criar um aplicativo Node.js

Crie um aplicativo do Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js em um site do Azure], [Serviço de Nuvem do Node.js] (usando o Windows PowerShell) ou [Site com o WebMatrix].

## <a name="configure-access"> </a>Configurar seu aplicativo para acessar o armazenamento

Para usar o armazenamento do Azure, você precisa baixar e usar o pacote do Azure Node.js, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST do armazenamento.

### Usar o Gerenciador de Pacotes de Nós (NPM) para obter o pacote

1.  Use uma interface de linha de comando, como **PowerShell** (Windows,) **Terminal** (Mac) ou **Bash** (Unix), e vá até a pasta onde você criou a amostra do aplicativo.

2.  Digite **npm install azure** na janela de comando, que deve
    resultar na seguinte saída:

        azure@0.7.5 node_modules\azure
		├── dateformat@1.0.2-1.2.3
		├── xmlbuilder@0.4.2
		├── node-uuid@1.2.0
		├── mime@1.2.9
		├── underscore@1.4.4
		├── validator@1.1.1
		├── tunnel@0.0.2
		├── wns@0.5.3
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Você pode executar o comando **ls** manualmente para verificar se uma pasta
    **node\_modules** foi criada. Dentro dessa pasta, você encontrará o pacote **azure**, que contém as bibliotecas necessárias para acessar o armazenamento.

### Importar o pacote

Usando o bloco de notas ou outro editor de texto, adicione o seguinte à parte superior do
arquivo **server.js** do aplicativo onde você pretende usar o armazenamento:

    var azure = require('azure');

## <a name="setup-connection-string"> </a>Configurar uma conexão de armazenamento do Azure

O módulo do Azure lerá as variáveis de ambiente AZURE\_STORAGE\_ACCOUNT e AZURE\_STORAGE\_ACCESS\_KEY para obter as informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta chamando **createBlobService**.

Para obter um exemplo de como definir as variáveis de ambiente em um arquivo de configuração para um Serviço de Nuvem do Azure, consulte [Serviço de Nuvem do Node.js com Armazenamento].

Para obter um exemplo de como definir as variáveis de ambiente no portal de gerenciamento para um Site do Azure, consulte [Aplicativo da Web do Node.js com Armazenamento].

## <a name="create-container"> </a>Como criar um contêiner

O objeto **serviço Blob** permite que você trabalhe com contêineres e blobs. O código a seguir cria um objeto **BlobService**. Adicione o seguinte próximo à parte superior do **server.js**:

    var blobService = azure.createBlobService();

Todos os blobs residem em um contêiner. A chamada para
**createContainerIfNotExists** no objeto **BlobService** retornará o contêiner especificado, se existente, ou criará um novo contêiner com o nome especificado, se ainda não existir. Por padrão, o novo
contêiner é particular e requer o uso da chave de acesso para baixar blobs desse contêiner.

	blobService.createContainerIfNotExists(containerName, function(error){
    	if(!error){
        	// Container exists and is private
    	}
	});


Se quiser tornar os arquivos no contêiner públicos, para que eles possam ser acessados sem a necessidade da chave de acesso, você poderá definir o
nível de acesso do contêiner para **blob** ou **contêiner**. A configuração do nível de acesso como **blob** permite acesso anônimo de leitura ao conteúdo e aos metadados do blob dentro desse contêiner, mas não aos metadados do contêiner, como a listagem de todos os blobs de um contêiner. A configuração do nível de acesso como **contêiner** permite acesso anônimo de leitura ao conteúdo e aos metadados do blob, bem como aos metadados do contêiner. O exemplo as seguir demonstra a configuração do nível de acesso para **blob**: 

    blobService.createContainerIfNotExists(containerName
		, {publicAccessLevel : 'blob'}
		, function(error){
			if(!error){
				// Container exists and is public
			}
		});

Como alternativa, você poderá modificar o nível de acesso de um contêiner, usando **setContainerAcl** para especificar o nível de acesso. O exemplo a seguir altera o nível de acesso para contêiner:

    blobService.setContainerAcl(containerName
		, 'container'
		, function(error){
			if(!error){
				// Container access level set to 'container'
			}
		});

###Filtros

É possível aplicar operações de filtragem opcionais às operações executadas usando **BlobService**. As operações de filtragem podem incluir log, repetição automática etc. Os filtros são objetos que implementam um método com a assinatura:

		function handle (requestOptions, next)

Após fazer seu pré-processamento nas opções de solicitação, o método precisará chamar "next", passando um retorno de chamada com a assinatura a seguir:

		function (returnObject, finalCallback, next)

Nesse retorno de chamada, e após processar o returnObject (a resposta da solicitação ao servidor), o retorno de chamada precisará invocar next, se ele existir, para continuar processando outros filtros ou simplesmente invocar finalCallback para terminar a invocação de serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte código cria um objeto **BlobService** que usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobService = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-blob"> </a>Como carregar um blob em um contêiner

Para carregar dados em um blob, use os métodos **createBlockBlobFromFile**, **createBlockBlobFromStream** ou **createBlockBlobFromText**. **createBlockBlobFromFile** carrega o conteúdo de um arquivo, enquanto **createBlockBlobFromStream** carrega o conteúdo de um fluxo. **createBlockBlobFromText** carrega o valor de texto especificado.

O exemplo a seguir carrega o conteúdo do arquivo **test1.txt** no blob 'test1'.

	blobService.createBlockBlobFromFile(containerName
		, 'test1'
		, 'test1.txt'
		, function(error){
			if(!error){
				// File has been uploaded
			}
		});

## <a name="list-blob"> </a>Como listar os blobs em um contêiner

Para listar os blobs em um contêiner, use o método **listBlobs** com um loop **for** para exibir o nome de cada blob no contêiner. O código a seguir gera a saída do **name** de cada blob em um contêiner no console.

    blobService.listBlobs(containerName, function(error, blobs){
		if(!error){
			for(var index in blobs){
				console.log(blobs[index].name);
			}
		}
	});

## <a name="download-blobs"> </a>Como: baixar blobs

Para baixar dados de um blob, use **getBlobToFile**, **getBlobToStream** ou **getBlobToText**. O exemplo a seguir demonstra o uso de **getBlobToStream** para baixar o conteúdo do blob **test1** e armazená-lo no arquivo **output.txt** usando um fluxo:

    var fs=require('fs');
	blobService.getBlobToStream(containerName
		, 'test1'
		, fs.createWriteStream('output.txt')
		, function(error){
			if(!error){
				// Wrote blob to stream
			}
		});

## <a name="delete-blobs"> </a>Como: excluir um blob

Finalmente, para excluir um blob, chame **deleteBlob**. O exemplo a seguir exclui o blob denominado 'blob1'.

    blobService.deleteBlob(containerName
		, 'blob1'
		, function(error){
			if(!error){
				// Blob has been deleted
			}
		});

## <a name="next-steps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de blob, siga estes links para saber como executar tarefas de armazenamento mais complexas.

-   Consulte a referência do MSDN: [Armazenando e acessando dados no Azure][].
-   Visite o [Blog da Equipe de Armazenamento do Azure][].
-   Visite o repositório [SDK do Azure para o nó] no GitHub.

  [SDK do Azure para o nó]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Próximas etapas]: #next-steps
  [O que é o serviço Blob?]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #create-account
  [Criar um aplicativo Node.js]: #create-app
  [Configurar seu aplicativo para acessar o armazenamento]: #configure-access
  [Configurar uma cadeia de conexão de armazenamento do Azure]: #setup-connection-string
  [Como criar um contêiner]: #create-container
  [Como carregar um blob em um contêiner]: #upload-blob
  [Como listar os blobs em um contêiner]: #list-blob
  [Como baixar blobs]: #download-blobs
  [Como excluir um blob]: #delete-blobs
[Criar e implantar um aplicativo Node.js em um site do Azure]: /pt-br/develop/nodejs/tutorials/create-a-website-(mac)/
  [Serviço de nuvem do Node.js com Armazenamento]: /pt-br/develop/nodejs/tutorials/web-app-with-storage/
  [Aplicativo Web Node.js com Armazenamento]: /pt-br/develop/nodejs/tutorials/web-site-with-storage/
 [Site com WebMatrix]: /pt-br/develop/nodejs/tutorials/web-site-with-webmatrix/
  [usando a API REST]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh264518.aspx
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Serviço de nuvem Node.js]: {localLink:2221} "Aplicativo Web Node.js"
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Blog da Equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/

