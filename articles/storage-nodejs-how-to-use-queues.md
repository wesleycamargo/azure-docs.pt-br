<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="Serviço de fila" pageTitle="Como usar o serviço Fila (Node.js) | Microsoft Azure" metaKeywords="O serviço Fila do Azure recebe mensagens Node.js" description="Aprenda a usar o serviço Fila do Azure para criar e excluir filas, inserir, obter e excluir mensagens. Amostras escritas em Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="Como usar o serviço Fila do Node.js" authors="" solutions="" manager="" editor="" />





# Como usar o serviço Fila do Node.js

Este guia mostra como executar cenários comuns usando o serviço Fila do Azure. As amostras são escritas usando a API do Node.js. Os cenários abordados incluem mensagens das filas de **inserção**, **inspeção**,
**obtenção** e **exclusão**, bem como a **criação e exclusão de filas**. Para obter mais informações sobre filas, consulte a seção [Próximas etapas][].

## Sumário

* [O que é o serviço Fila?][]   
* [Conceitos][]   
* [Criar uma conta de armazenamento do Azure][]   
* [Criar um aplicativo do Node.js][]   
* [Configurar seu aplicativo para acessar o armazenamento][]   
* [Configurar uma cadeia de conexão de armazenamento do Azure][]   
* [Como criar uma fila][]   
* [Como inserir uma mensagem em uma fila][]   
* [Como inspecionar a próxima mensagem][]   
* [Como remover a próxima mensagem da fila][]   
* [Como alterar o conteúdo de uma mensagem em fila][]   
* [Como adicionar opções para remover mensagens da fila][]   
* [Como obter o comprimento da fila][]   
* [Como excluir uma fila][]   
* [Próximas etapas][]

## <a name="what-is"> </a>O que é o serviço Fila?

O serviço Fila do Azure é um serviço de armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma mensagem de fila única pode ter até 64 KB em tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. Os usos comuns do serviço Fila incluem:

-   <span>Criar uma lista de pendências de trabalho para processar de maneira assíncrona</span>
-   Passar mensagens de uma função Web do Azure para uma função de trabalho

## <a name="concepts"> </a>Conceitos

O serviço Fila contém os seguintes componentes:

![Fila1][Queue1]

-   **Formato de URL:** As filas são acessadas usando o seguinte formato de URL:   
    
		http://storageaccount.queue.core.windows.net/queue  
      
    A URL a seguir aborda uma das filas no diagrama:  
    
		http://myaccount.queue.core.windows.net/imagesToDownload

-   **Conta de Armazenamento:** Todo o acesso ao Armazenamento do Azure é feito através de uma conta de armazenamento. Uma conta de armazenamento é o nível mais alto do namespace para acessar filas. O tamanho total do conteúdo do blob, da tabela e da fila
    em uma conta de armazenamento não pode ultrapassar 100 TB.

-   **Fila:** Uma fila contém um conjunto de mensagens. Todas as mensagens devem estar em uma fila.

-   **Mensagem:** Uma mensagem, em qualquer formato, de até 64 KB.

## <a name="create-account"> </a>Criar uma conta de armazenamento do Azure

Para usar as operações de armazenamento, você precisa de uma conta de armazenamento do Azure. Você
pode criar uma conta de armazenamento seguindo essas etapas. (Você também pode
pode criar uma conta de armazenamento [usando a API REST][].)

1.  Faça logon no [Portal de Gerenciamento do Azure].

2.  Na parte inferior do painel de navegação, clique em **+NOVO**.

	![+novo][plus-new]

3.  Clique em **Conta de Armazenamento** e depois em **Criação Rápida**.

	![Caixa de diálogo Criação rápida][quick-create-storage]

4.  Na URL, digite um nome de subdomínio para usar na URI para a conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse valor se torna o nome do host no URI que é usado para lidar com os recursos Blob, Fila ou Tabela da assinatura.

5.  Escolha uma Região/Grupo de Afinidade no qual deseja localizar o armazenamento. Se você usar o armazenamento de seu aplicativo do Azure, selecione a mesma região onde você implantará seu aplicativo.

6.  Clique em **Criar Conta de Armazenamento**.

## <a name="create-app"> </a>Criar um aplicativo do Node.js

Crie um aplicativo do Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js em um site do Azure], [Serviço de Nuvem do Node.js] (usando o Windows PowerShell) ou [Site com o WebMatrix].

## <a name="configure-access"> </a>Configurar seu aplicativo para acessar o armazenamento

Para usar o armazenamento do Azure, você precisa baixar e usar o pacote do Azure Node.js, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST do armazenamento.

### Usar o Gerenciador de Pacotes de Nós (NPM) para obter o pacote

1.  Use uma interface de linha de comando, como **PowerShell** (Windows,) **Terminal** (Mac) ou **Bash** (Unix), e vá até a pasta onde você criou a amostra do aplicativo.

2.  Digite **npm install azure** na janela de comando, que deve
    resultar na seguinte saída:

        azure@0.7.5 node_modules\azure
		|-- dateformat@1.0.2-1.2.3
		|-- xmlbuilder@0.4.2
		|-- node-uuid@1.2.0
		|-- mime@1.2.9
		|-- underscore@1.4.4
		|-- validator@1.1.1
		|-- tunnel@0.0.2
		|-- wns@0.5.3
		|-- xml2js@0.2.7 (sax@0.5.2)
		|-- request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Você pode executar o comando **ls** manualmente para verificar se uma pasta
    **nó\ módulos** foi criada. Dentro dessa pasta você
    encontrará o pacote **azure**, que contém as bibliotecas necessárias para
    acessar o armazenamento.

### Importar o pacote

Usando o bloco de notas ou outro editor de texto, adicione o seguinte à parte superior do
arquivo **server.js** do aplicativo onde você pretende usar o armazenamento:

    var azure = require('azure');

## <a name="setup-connection-string"> </a>Configurar uma conexão de armazenamento do Azure

O módulo do azure lerá as variáveis de ambiente AZURE\ ARMAZENAMENTO\ CONTA e AZURE\ ARMAZENAMENTO\ ACESSO\ CHAVE para obter as informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta ao chamar **createQueueService**.

Para obter um exemplo de como definir as variáveis de ambiente em um arquivo de configuração para um Serviço de Nuvem do Azure, consulte [Serviço de Nuvem do Node.js com Armazenamento].

Para obter um exemplo de como definir as variáveis de ambiente no portal de gerenciamento para um Site do Azure, consulte [Aplicativo da Web do Node.js com Armazenamento].

## <a name="create-queue"> </a>Como criar uma fila

O código a seguir cria o objeto **QueueService**, permitindo que você trabalhe com filas.

    var queueService = azure.createQueueService();

Use o método **createQueueIfNotExists**, que retorna a fila especificada se já existente ou cria uma nova fila com o nome especificado, se ele ainda não existir.

	queueService.createQueueIfNotExists(queueName, function(error){
    	if(!error){
        	// Queue exists
	    }
	});

###Filtros

É possível aplicar operações de filtragem opcionais às operações executadas usando **QueueService**. As operações de filtragem podem incluir log, repetição automática etc. Os filtros são objetos que implementam um método com a assinatura:

		identificador de função (requestOptions, avançar)

Após fazer seu pré-processamento nas opções de solicitação, o método precisará chamar "next", passando um retorno de chamada com a assinatura a seguir:

		função (returnObject, finalCallback, avançar)

Nesse retorno de chamada, e após processar o returnObject (a resposta da solicitação ao servidor), o retorno de chamada precisará invocar avançar, se ele existir, para continuar processando outros filtros ou simplesmente invocar finalCallback para terminar a invocação de serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte código cria um objeto **QueueService** que usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var queueService = azure.createQueueService().withFilter(retryOperations);

## <a name="insert-message"> </a>Como inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila, use o método **createMessage** para criar uma nova mensagem e adicione-a à fila.

	queueService.createMessage(queueName, "Hello world!", function(error){
	    if(!error){
	        // Message inserted
	    }
	});


## <a name="peek-message"> </a>Como inspecionar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método **peekMessages**. Por padrão,
**peekMessages** inspeciona uma única mensagem.

	queueService.peekMessages(queueName, function(error, messages){
		if(!error){
			// Messages peeked
			// Text is available in messages[0].messagetext
		}
	});


> [WACOM.NOTE] 
>Usando <strong>peekMessage</strong> quando não existem mensagens na fila não retornará um erro, no entanto, nenhuma mensagem será retornada.

## <a name="get-message"> </a>Como remover a próxima mensagem da fila

Seu código remove uma mensagem de uma fila em duas etapas. Quando você chamar
**getMessages**, você receberá a próxima mensagem em uma fila por padrão. As mensagens retornadas de **getMessages** se tornam invisíveis para todas as outras mensagens de leitura de código desta fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para terminar de remover a mensagem da fila, você também deve chamar **deleteMessage**. Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chama **deleteMessage** logo depois que a mensagem é processada.

	queueService.getMessages(queueName, function(error, messages){
	    if(!error){
	        // Process the message in less than 30 seconds, the message
	        // text is available in messages[0].messagetext 
			var message = messages[0]
	        queueService.deleteMessage(queueName
				, message.messageid
				, message.popreceipt
				, function(error){
	            	if(!error){
	                	// Message deleted
	            	}
	        	});
	    }
	});

> [WACOM.NOTE]
> Usando <b>getMessages</b> quando não existem mensagens na fila não retornará um erro, no entanto, nenhuma mensagem será retornada.

## <a name="change-contents"> </a>Como alterar o conteúdo de uma mensagem em fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir usa o método **updateMessage**
method to update a message.para atualizar uma mensagem.

    queueService.getMessages(queueName, function(error, messages){
		if(!error){
			// Got the message
			var message = messages[0];
			queueService.updateMessage(queueName
				, message.messageid
				, message.popreceipt
				, 10
				, { messagetext: 'in your message, doing stuff.' }
				, function(error){
					if(!error){
						// Message updated successfully
					}
				});
		}
	});

## <a name="advanced-get"> </a>Como adicionar opções para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila.
Primeiro, você pode obter um lote de mensagens (até 32). Em segundo lugar, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para o código processar completamente cada mensagem. O seguinte exemplo de código usa o
método **getMessages** para obter 15 mensagens em uma chamada. Em seguida, processa cada mensagem usando um loop. Ele também define o tempo limite de invisibilidade para cinco minutos para cada mensagem.

    queueService.getMessages(queueName
		, {numofmessages: 15, visibilitytimeout: 5 * 60}
		, function(error, messages){
		if(!error){
			// Messages retreived
			for(var index in messages){
				// text is available in messages[index].messagetext
				var message = messages[index];
				queueService.deleteMessage(queueName
					, message.messageid
					, message.popreceipt
					, function(error){
						if(!error){
							// Message deleted
						}
					});
			}
		}
	});

## <a name="get-queue-length"> </a>Como obter o comprimento da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O
método **getQueueMetadata** solicita que o serviço Fila retorne metadados sobre a fila e a propriedade da resposta **approximatemessagecount** contém uma contagem de quantas mensagens estão em uma fila. A conta é aproximada apenas porque as mensagens podem ser adicionadas ou removidas depois que o serviço Fila responde à sua solicitação.

    queueService.getQueueMetadata(queueName, function(error, queueInfo){
		if(!error){
			// Queue length is available in queueInfo.approximatemessagecount
		}
	});

## <a name="delete-queue"> </a>Como excluir uma fila

Para excluir uma fila e todas as mensagens contidas nela, chame o
método **deleteQueue** no objeto fila.

    queueService.deleteQueue(queueName, function(error){
		if(!error){
			// Queue has been deleted
		}
	});

## <a name="next-steps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de fila, siga estes links para saber como executar tarefas de armazenamento mais complexas.

-   Consulte a referência do MSDN: [Armazenando e acessando dados no Azure][].
-   Acesse o [Blog da Equipe de Armazenamento do Azure][].
-   Acesse o repositório [SDK do Azure para Nós] no GitHub.

  [SDK do Azure para Nós]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Próximas etapas]: #next-steps
  [O que é o serviço Fila?]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #create-account
  [Criar um aplicativo do Node.js]: #create-app
  [Configurar seu aplicativo para acessar o armazenamento]: #configure-access
  [Configurar uma cadeia de conexão de armazenamento do Azure]: #setup-connection-string
  [Como criar uma fila]: #create-queue
  [Como inserir uma mensagem em uma fila]: #insert-message
  [Como inspecionar a próxima mensagem]: #peek-message
  [Como remover a próxima mensagem da fila]: #get-message
  [Como alterar o conteúdo de uma mensagem em fila]: #change-contents
  [Como adicionar opções para remover mensagens da fila]: #advanced-get
  [Como obter o comprimento da fila]: #get-queue-length
  [Como excluir uma fila]: #delete-queue
  [usando a API REST]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh264518.aspx
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Criar e implantar um aplicativo do Node.js em um site do Azure]: /pt-br/develop/nodejs/tutorials/create-a-website-(mac)/
  [Serviço de Nuvem do Node.js com armazenamento]: /pt-br/develop/nodejs/tutorials/web-app-with-storage/
  [Aplicativo da web do Node.js com armazenamento]: /pt-br/develop/nodejs/tutorials/web-site-with-storage/
  
  [Fila1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [além do novo]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [armazenamento de criação rápida]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png
  
  
  
  [Serviço de Nuvem do Node.js]: {localLink:2221} "Aplicativo da Web com Express"
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Blog da equipe do Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Site com WebMatrix]: /pt-br/develop/nodejs/tutorials/web-site-with-webmatrix/

