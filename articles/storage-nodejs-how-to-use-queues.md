<properties 
	pageTitle="Como usar o Armazenamento de Fila do Node.js | Microsoft Azure" 
	description="Saiba como usar o serviço Fila do Azure para criar e excluir filas, bem como para inserir, obter e excluir mensagens. Amostras escritas em Node.js." 
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
	ms.date="03/11/2015" 
	ms.author="mwasson"/>


# Como usar o Armazenamento de Fila do Node.js

[AZURE.INCLUDE [storage-selector-queue-include](../includes/storage-selector-queue-include.md)]

## Visão geral

Este guia mostra como executar cenários comuns usando o serviço
Serviço de fila do Azure. Os exemplos são escritos usando a API do Node.js
. Os cenários abordados incluem mensagens das filas de **inserção**, **inspeção**,
**obtenção** e **exclusão** de mensagens da fila, bem como **criação e
exclusão de filas**.

[AZURE.INCLUDE [storage-queue-concepts-include](../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Criar um aplicativo do Node.js

Criar um aplicativo Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js em um site do Azure], [Serviço em Nuvem Node.js][Serviço de Nuvem Node.js] (usando o Windows PowerShell) ou [site com WebMatrix].

## Configurar seu aplicativo para acessar o armazenamento

Para usar o armazenamento do Azure, você precisa do SDK de armazenamento do Azure para Node.js, que inclui um conjunto de bibliotecas convenientes que
se comunicam com os serviços REST do armazenamento.

### Usar o NPM (gerenciador de pacotes de nós) para obter o pacote

1.  Use uma interface de linha de comando, como o **PowerShell** (Windows,) **Terminal** (Mac) ou **Bash** (Unix), e vá até a pasta onde você criou a amostra do aplicativo.

2.  Digite **npm install azure-storage** na janela de comando, que deve
    resultar na seguinte saída:

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  Você pode executar manualmente o comando **ls** para verificar se uma pasta
    **node\_modules** foi criada. Dentro dessa pasta você irá
    localizar o pacote **azure-storage** que contém as bibliotecas necessárias para
    acessar o armazenamento.

### Importar o pacote

Usando o bloco de notas ou outro editor de texto, adicione o seguinte à parte superior do
arquivo **server.js** do aplicativo onde você pretende usar o armazenamento:

    var azure = require('azure-storage');

## Configurar uma conexão de armazenamento do Azure

O módulo do azure lerá as variáveis de ambiente AZURE\_STORAGE\_ACCOUNT e AZURE\_STORAGE\_ACCESS\_KEY ou AZURE\_STORAGE\_CONNECTION\_STRING para obter as informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta ao chamar **createQueueService**.

Para obter um exemplo de como definir as variáveis de ambiente no portal de gerenciamento para um Site do Azure, confira [Aplicativo da Web do Node.js com Armazenamento].

## Como: Criar uma fila

O código a seguir cria um objeto **QueueService** objeto, que permite que você
trabalhe com filas.

    var queueSvc = azure.createQueueService();

Use o método **createQueueIfNotExists**, que retorna a
fila especificada, se ela já existe, ou cria uma nova fila com nome especificado
se ele ainda não existir.

	queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
	  }
	});

Se a fila for criada,  `result` é verdadeiro. Se a fila existir,  `result` é falso.

### Filtros

É possível aplicar operações de filtragem opcionais às operações executadas usando **QueueService**. As operações de filtragem podem incluir log, repetição automática etc. Os filtros são objetos que implementam um método com a assinatura:

		function handle (requestOptions, next)

Após fazer seu pré-processamento nas opções de solicitação, o método precisará chamar "next", passando um retorno de chamada com a assinatura a seguir:

		function (returnObject, finalCallback, next)

Nesse retorno de chamada, e após processar o returnObject (a resposta da solicitação ao servidor), o retorno de chamada precisará invocar avançar, se ele existir, para continuar processando outros filtros ou simplesmente invocar finalCallback para terminar a invocação de serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte código cria um objeto **QueueService** que usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var queueSvc = azure.createQueueService().withFilter(retryOperations);

## Como: Inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila, use o método **createMessage** para
criar uma nova mensagem e adicioná-la à fila.

	queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
	  if(!error){
	    // Message inserted
	  }
	});

## Como: Espiar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la
da fila chamando o método **PeekMessage**. Por padrão,
**peekMessages** inspeciona uma única mensagem.

	queueSvc.peekMessages('myqueue', function(error, result, response){
	  if(!error){
		// Messages peeked
	  }
	});

O `result` contém a mensagem.

> [AZURE.NOTE] Usando **peekMessages** quando não existem mensagens na fila não retornará um erro, no entanto, nenhuma mensagem será retornada.

## Como: Remover a próxima mensagem da fila

O processamento de uma mensagem é um processo de duas fases:

1. Remover a mensagem.

2. Excluir a mensagem.

Para remover uma mensagem da fila, use **getMessage**. Isso torna a mensagem invisível na fila, para que outros clientes não possam processá-la. Depois que seu aplicativo processar a mensagem, chame **deleteMessage** para excluí-la da fila. O exemplo a seguir obtém uma mensagem e, em seguida, a exclui:

	queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
	    // message dequed
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageid, message.popreceipt, function(error, response){
	      if(!error){
		    //message deleted
		  }
		});
	  }
	});

> [AZURE.NOTE] Por padrão, uma mensagem só é oculta por 30 segundos; depois disso, fica visível para os outros clientes. Você pode especificar um valor diferente usando `options.visibilityTimeout` com **getMessages**.

> [AZURE.NOTE]
> Usando <b>getMessages</b> quando não existem mensagens na fila não retornará um erro, no entanto, nenhuma mensagem será retornada.

## Como: Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem na fila usando **updateMessage**. O exemplo a seguir atualiza o texto de uma mensagem:

    queueSvc.getMessages('myqueue', function(error, result, response){
	  if(!error){
		// Got the message
		var message = result[0];
		queueSvc.updateMessage('myqueue', message.messageid, message.popreceipt, 10, {messageText: 'new text'}, function(error, result, response){
		  if(!error){
			// Message updated successfully
		  }
		});
	  }
	});

## Como: Opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila:

* 'options.numOfMessages' - Recuperar um lote de mensagens (até 32).
* 'options.visibilityTimeout' - Definir um tempo limite de invisibilidade mais longo ou mais curto.

O seguinte exemplo usa o método **getMessages** para receber 15 mensagens em uma chamada. Em seguida, ele processa
cada mensagem usando um loop for. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem retornada por este método.

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
	  if(!error){
		// Messages retreived
		for(var index in result){
		  // text is available in result[index].messageText
		  var message = result[index];
		  queueSvc.deleteMessage(queueName, message.messageid, message.popreceipt, function(error, response){
			if(!error){
			  // Message deleted
			}
		  });
		}
	  }
	});

## Como: Obter o tamanho da fila

O **getQueueMetadata** retorna metadados sobre a fila, incluindo o número aproximado de mensagens em espera na fila.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
	  if(!error){
		// Queue length is available in result.approximatemessagecount
	  }
	});

## Como: Listar filas

Para recuperar uma lista de filas, use **listQueuesSegmented**. Para recuperar uma lista filtrada por um prefixo específico, use **listQueuesSegmentedWithPrefix**.

	queueSvc.listQueuesSegmented(null, function(error, result, response){
	  if(!error){
	    // result.entries contains the list of queues
	  }
	});

Se todas as filas não puderem ser retornadas, `result.continuationToken` poderá ser usado como o primeiro parâmetro de **listQueuesSegmented** ou o segundo parâmetro de **listQueuesSegmentedWithPrefix** para recuperar mais resultados.

## Como: Excluir uma fila

Para excluir uma fila e todas as mensagens contidas nela, chame o
método **deleteQueue** no objeto fila.

    queueSvc.deleteQueue(queueName, function(error, response){
		if(!error){
			// Queue has been deleted
		}
	});

Para limpar todas as mensagens de uma fila sem excluí-la, use **clearMessages**.

## Como: Trabalhar com assinaturas de acesso compartilhado

Assinaturas de Acesso Compartilhado (SAS) são uma forma segura de fornecer acesso granular a filas sem fornecer o nome ou as chaves da conta de armazenamento. As SAS são muitas vezes usadas para fornecer acesso limitado às filas, como permitir que um aplicativo móvel envie mensagens.

Um aplicativo confiável, como um serviço baseado em nuvem, gera uma SAS usando **generateSharedAccessSignature** de **QueueService**, e o oferece a um aplicativo não confiável ou semiconfiável. Por exemplo, um aplicativo móvel. A SAS é gerada utilizando uma política que descreve as datas inicial e final durante as quais a SAS é válida, assim como o nível de acesso concedido ao titular da SAS.

O exemplo a seguir gera uma nova política de acesso compartilhado que permitirá que o titular da SAS adicione mensagens à fila e expira 100 minutos após o momento em que é criado.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
	
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	    Start: startDate,
	    Expiry: expiryDate
	  }
	};

	var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
	var host = queueSvc.host;

Observe que também devem ser fornecidas as informações do host, já que são necessárias quando o titular da SAS tenta acessar a fila.

O aplicativo cliente usa a SAS com **QueueServiceWithSAS** para executar operações na fila. O exemplo a seguir conecta à fila e cria uma mensagem.

	var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
	sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
	  if(!error){
	    //message added
	  }
	});

Como a SAS foi gerada com acesso para adição, se for feita uma tentativa de ler, atualizar ou excluir mensagens, será retornado um erro.

### Listas de controle de acesso

Você também pode usar uma ACL (Lista de Controle de Acesso) para definir a política de acesso para uma SAS. Isso é útil se você quiser permitir que vários clientes acessem a fila, mas oferece diferentes políticas de acesso para cada cliente.

Uma ACL é implementada através de um conjunto de políticas de acesso, com uma ID associada a cada política. O exemplo a seguir define duas políticas; uma para 'user1' e um para 'user2':

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

O exemplo a seguir obtém a ACL atual para **myqueue**, em seguida, adiciona as novas políticas usando **ssetQueueAcl**. Essa abordagem permite:

	queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		queueSvc.setQueueAcl('myqueue', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

Uma vez que a ACL foi definida, você pode criar uma SAS com base na ID de uma política. O exemplo a seguir cria uma nova SAS para 'user2':

	queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de fila, siga estes links
para obter informações sobre tarefas de armazenamento mais complexas.

-   Consulte a referência de MSDN: [Armazenando e acessando dados no Azure][].
-   Visite o Blog da equipe do [Armazenamento do Azure][].
-   Visite o repositório [SDK de armazenamento do Azure para nó][] no GitHub.

  [SDK de Armazenamento do Azure para nó]: https://github.com/Azure/azure-storage-node
  [usando a API REST]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Criar e implantar um aplicativo do Node.js em um Site do Azure]: web-sites-nodejs-develop-deploy-mac.md
  [Serviço de Nuvem do Node.js com Armazenamento]: storage-nodejs-use-table-storage-cloud-service-app.md
  [Aplicativo Web Node.js com Armazenamento]: storage-nodejs-use-table-storage-web-site.md

  
  [Fila1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png
  
  
  
  [Serviço de Nuvem do Node.js]: cloud-services-nodejs-develop-deploy-app.md
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Blog da equipe do Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
 [Site com WebMatrix]: web-sites-nodejs-use-webmatrix.md

<!--HONumber=49-->