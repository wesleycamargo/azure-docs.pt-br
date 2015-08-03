<properties 
	pageTitle="Introdução à criptografia do lado do cliente para o Armazenamento do Microsoft Azure (Visualização) | Microsoft Azure" 
	description="A visualização da Biblioteca de cliente do Armazenamento do Azure para .NET oferece suporte à criptografia do lado do cliente e à integração com o Cofre da Chave do Azure. A criptografia do lado do cliente oferece o máximo de segurança para seus aplicativos do Armazenamento do Azure, uma vez que suas chaves de acesso nunca ficam disponíveis para o serviço. A criptografia do lado do cliente está disponível para blobs, filas e tabelas." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="tamram"/>


# Introdução à criptografia do lado do cliente para o Armazenamento do Microsoft Azure (Visualização)

## Visão geral

Bem-vindo à [visualização da nova biblioteca de cliente do Armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/4.4.1-preview). Essa biblioteca de visualização contém novos recursos para ajudar os desenvolvedores a criptografar dados nos aplicativos de cliente antes de carregar para o Armazenamento do Azure e descriptografar os dados durante o download. A biblioteca de visualização também oferece suporte à integração com o [Cofre da Chave](http://azure.microsoft.com/services/key-vault/) do Azure para gerenciamento de chaves de contas de armazenamento.

## Criptografia e descriptografia com a técnica de envelope

Os processos de criptografia e descriptografia seguem a técnica de envelope.

### Criptografia com a técnica de envelope

A criptografia com a técnica de envelope funciona da seguinte maneira:

1. A biblioteca do cliente do Armazenamento do Azure gera um CEK (Chave de Criptografia de Conteúdo) que é uma chave simétrica de uso único.
2. Os dados do usuário são criptografados usando esse CEK.
3. O CEK é empacotada (criptografada) usando o KEK (Chave de Criptografia de Chave). O KEK é identificado por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica e pode ser gerenciada localmente ou armazenada no Cofre da Chave do Azure. 
	
	A biblioteca de cliente de armazenamento em si nunca tem acesso ao KEK. Ela simplesmente invoca o algoritmo de disposição de chave fornecido pelo Cofre da Chave. Os usuários podem escolher usar provedores personalizados para desempacotamento/quebra da chave, se desejado.

4. Os dados criptografados, em seguida, são carregados para o serviço de Armazenamento do Azure. A chave empacotada junto com alguns metadados adicionais de criptografia está armazenada como metadados (em um blob) ou interpolada com os dados criptografados (fila de mensagens e entidades de tabela).

### Descriptografia com a técnica de envelope

A descriptografia com a técnica de envelope funciona da seguinte maneira:

1. A biblioteca de cliente presume que o usuário está gerenciando o KEK (chave de criptografia de chave) localmente ou no Cofre da Chave do Azure. O usuário não precisa conhecer a chave específica que foi usada para criptografia. Em vez disso, um resolvedor de chave que resolve diferentes identificadores de chaves pode ser configurado e usado.
2. A biblioteca de cliente baixa os dados criptografados junto com demais materiais de criptografia armazenados no serviço.
3. O CEK (chave de criptografia de conteúdo) empacotado é então desempacotado usando KEK. Novamente aqui, a biblioteca de cliente não tem acesso a KEK. Ela simplesmente invoca o Cofre da Chave personalizado ou algoritmo de descompactação do provedor.
4. A CEK (chave de criptografia de conteúdo) é então usada para descriptografar os dados de usuário criptografados.

## Mecanismo de criptografia

A biblioteca de cliente de armazenamento usa [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para criptografar os dados do usuário. Especificamente, [CBC (Encadeamento de Blocos de Criptografia)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) modo com AES. Cada serviço funciona ligeiramente diferente, portanto, discutiremos cada uma deles aqui.

### Blobs

Na versão de visualização, a biblioteca de cliente dá suporte à criptografia somente de blobs completos. Especificamente, a criptografia tem suporte quando os usuários utilizarem os métodos **UploadFrom*** ou **BlobWriteStream**. Para downloads, ambos downloads completo e de intervalo tem suporte.

Durante a criptografia, a biblioteca de cliente gerará um vetor de inicialização aleatório (IV) de 16 bytes, juntamente com uma chave de criptografia aleatória de conteúdo (CEK) de 32 bytes e executará a criptografia de envelope dos dados blob usando essas informações. O CEK encapsulado e alguns metadados adicionais de criptografia são armazenadas como metadados com o blob criptografado no serviço de blob.

> [AZURE.WARNING]Se você estiver editando ou carregando seus próprios metadados para o blob, deverá garantir que esses metadados sejam preservado. Se você carregar novos metadados sem esses metadados, o CEK encapsulado, IV e outros metadados serão perdidos e o conteúdo do blob nunca mais poderá ser recuperado.

Baixar um blob criptografado envolve a recuperar o conteúdo do blob inteiro usando os métodos de conveniência **DownloadTo***/**BlobReadStream**. O CEK encapsulado é desempacotado e usado em conjunto com o IV (armazenado como metadados de blob neste caso) para retornar os dados descriptografados para os usuários.

Baixar um intervalo arbitrário (métodos **DownloadRange***) no blob criptografado envolve o ajuste do intervalo fornecido por usuários para obter uma pequena quantidade de dados adicionais que podem ser usados para descriptografar o intervalo solicitado com sucesso.

Todos os tipos de blob (blobs de blocos e páginas) podem ser criptografados/descriptografadas usando este esquema.

### Filas

Uma vez que a fila de mensagens pode ser de qualquer formato, a biblioteca de cliente define um formato personalizado que inclui o vetor de inicialização (IV) e a chave de criptografia do conteúdo criptografado (CEK) no texto da mensagem.

Durante a criptografia, a biblioteca de cliente gera um IV aleatório de 16 bytes com um CEK aleatória de 32 bytes e executa criptografia de envelope do texto da mensagem de fila usando essas informações. O CEK encapsulado e alguns metadados adicionais de criptografia são adicionados à mensagem da fila criptografada. Essa mensagem modificada (mostrada abaixo) é armazenada no serviço.

	<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Durante a descriptografia, a chave encapsulada é extraída da mensagem da fila e envolta. O IV também é extraído da mensagem da fila e usado juntamente com a chave desencapsulada para descriptografar os dados de mensagem da fila. Observe que os metadados de criptografia são pequeno (abaixo de 500 bytes), por isso embora contem para o limite de 64 KB para uma mensagem da fila, tal impacto é administrável.

### Tabelas

Na versão de visualização, a biblioteca de cliente dá suporte à criptografia de propriedades de entidade para as operações de inserção e substituição.

>[AZURE.NOTE]Atualmente não há suporte para a mesclagem. Como um subconjunto de propriedades pode ter sido criptografado anteriormente usando uma chave diferente, simplesmente mesclar as novas propriedades e atualizar os metadados resultará em perda de dados. Mesclar requer fazer chamadas de serviço extra para ler a entidade já existente no serviço ou usar uma nova chave por propriedade, os quais não são ambos adequados por motivos de desempenho.

Criptografia de dados de tabela funciona da seguinte maneira:

1. Os usuários especificam as propriedades que devem ser criptografadas.
2. A biblioteca de cliente gera um vetor de inicialização aleatório (IV) de 16 bytes com uma chave de criptografia aleatória de conteúdo (CEK) de 32 bytes para cada entidade e executa a criptografia de envelope sobre as propriedades individuais que devem ser criptografadas, derivando uma nova IV por propriedade.
3. O CEK encapsulado e alguns metadados adicionais de criptografia, em seguida, são armazenados como duas propriedades reservadas adicionais. A primeira propriedade reservada (_ClientEncryptionMetadata1) é uma propriedade de cadeia de caracteres que contém informações sobre o IV, versão e chave encapsuladas. Outra propriedade reservada (_ClientEncryptionMetadata2) é uma propriedade binária que armazena as informações sobre as propriedades que são criptografados.
4. Devido a essas propriedades reservadas adicionais necessárias para a criptografia, os usuários agora podem ter apenas 250 propriedades personalizadas, em vez de 252. O tamanho total da entidade deve ser menor que 1 MB.

Observe que somente as propriedades de cadeia de caracteres podem ser criptografadas. Se outros tipos de propriedades precisarem ser criptografados, elas devem ser convertidas em cadeias de caracteres.

Para tabelas, além da política de criptografia, os usuários devem especificar as propriedades que devem ser criptografadas. Isso pode ser feito especificando o atributo [EncryptProperty] (para entidades POCO que derivam de TableEntity) ou um resolvedor de criptografia nas opções de solicitação. Um resolvedor de criptografia é um delegado que usa uma chave de partição, a chave de linha e o nome da propriedade e retorna um valor booliano que indica se essa propriedade deve ser criptografada. Durante a criptografia, a biblioteca de cliente usará essas informações para decidir se uma propriedade deve ser criptografada durante a gravação para a transmissão. O representante também oferece a possibilidade de lógica em torno de como as propriedades são criptografadas. (Por exemplo, se X, então criptografar a propriedade A; caso contrário, criptografar as propriedades A e B.) Observe que não é necessário fornecer essas informações durante a leitura ou ap consultar entidades.

### Operações em lote

Em operações em lote, o mesmo KEK será usado em todas as linhas de operação em lote porque a biblioteca de cliente permite apenas um objeto de opções (e, portanto, uma política/KEK) por operação em lote. No entanto, a biblioteca de cliente gerará internamente um novo IV e CEK aleatórios por linha no lote. Os usuários também podem optar por criptografar propriedades diferentes para cada operação em lote definindo esse comportamento no resolvedor de criptografia.

### Consultas

Para executar operações de consulta, você deve especificar que um resolvedor de chave é capaz de resolver todas as chaves no conjunto de resultados. Se uma entidade contida no resultado da consulta não puder ser resolvida para um provedor, a biblioteca de cliente gerará um erro. Para qualquer consulta que realiza as projeções de lado do servidor, a biblioteca de cliente adicionará as propriedades de metadados de criptografia especial (_ClientEncryptionMetadata1 e _ClientEncryptionMetadata2) por padrão para as colunas selecionadas.

## Cofre da Chave do Azure

O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Usando o Cofre da Chave do Azure, os usuários podem criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados, arquivos .PFX e senhas) usando chaves que são protegidas por HSMs (módulos de segurança de hardware). Para obter mais informações, consulte [O que é o Cofre da Chave do Azure?](../articles/key-vault-whatis.md).

A biblioteca de cliente de armazenamento usa a biblioteca principal do Cofre da Chave para fornecer uma estrutura comum no Azure para o gerenciamento de chaves. Os usuários também recebem o benefício adicional de usar a biblioteca de extensões do Cofre da Chave. A biblioteca de extensões fornece funcionalidades úteis com Symmetric simples/RSA local e provedores de chave de nuvem e com agregação e armazenamento em cache.

### Interface e dependências

Há três pacotes do Cofre da Chave:

- Microsoft.Azure.KeyVault.Core contém IKey e IKeyResolver. Este é um pequeno pacote sem dependências. As bibliotecas do cliente de armazenamento para .NET e Windows Phone o definem como uma dependência.
- Microsoft.Azure.KeyVault contém o cliente REST do Cofre da Chave.
- Microsoft.Azure.KeyVault.Extensions contém o código de extensão que inclui implementações de algoritmos criptográficos e um RSAKey e SymmetricKey. Ele depende dos namespaces básicos e KeyVault, e fornece a funcionalidade para definir um resolvedor de agregação (quando os usuários desejam usar vários provedores de chave) e um resolvedor de chave em cache. Embora a biblioteca de cliente de armazenamento não dependa diretamente deste pacote, se os usuários quiserem usar o Cofre da Chave do Azure para armazenar suas chaves ou usar as extensões do Cofre da Chave para consumir provedores criptográficos local e na nuvem, eles precisarão esse pacote.

O Cofre da Chave foi criado para chaves mestras de alto valor e os limites por Cofre da Chave são criados com tal prioridade em mente. Ao executar a criptografia do lado do cliente com o Cofre da Chave, o modelo preferido é usar chaves mestras simétricas armazenadas como segredos no Cofre da Chave e armazenadas em cache localmente. Os usuários devem fazer o seguinte:

1. Crie um segredo offline e carregue-o no Cofre da Chave.
2. Use o identificador de base do segredo como um parâmetro para resolver a versão atual do segredo para criptografia e armazenar essas informações localmente em cache. Use CachingKeyResolver para armazenamento em cache. Os usuários não deverão implementar sua própria lógica de cache.
3. Use o resolvedor de cache como uma entrada ao criar a política de criptografia.

Para obter mais informações sobre o uso do Cofre da Chave, consulte os [exemplos de código de criptografia](https://github.com/Azure/azure-storage-net/tree/preview/Samples/GettingStarted/EncryptionSamples).

### Práticas recomendadas

O suporte à criptografia está disponível somente nas bibliotecas de cliente de armazenamento para .NET e Windows Phone. Atualmente, o Tempo de Execução do Windows não dá suporte à criptografia. Além disso, as extensões do Cofre da Chave atualmente não dão suporte ao Windows Phone. Se você quiser usar a criptografia de cliente de armazenamento no telefone, precisará implementar seus próprios provedores de chave. Além disso, devido a uma limitação na plataforma .NET do Windows Phone, a criptografia de blob de páginas não tem suporte atualmente no Windows Phone.

>[AZURE.IMPORTANT]Lembre-se dos seguintes pontos importantes ao usar a biblioteca de visualização:
>
>- Não use a biblioteca de visualização de dados de produção. No futuro, as alterações na biblioteca afetarão os esquemas usados. A descriptografia de dados que foram criptografados com a biblioteca de visualização não é garantida em versões futuras.  
>- Durante a leitura ou gravação de um blob criptografado, use comandos de carregamento de blob completo e comandos de download de blob completo/em intervalos. Evite gravar em um blob criptografado usando operações de protocolo tais como Put Block, Put Block List, Write Pages ou Clear Pages; caso contrário, você pode corromper o blob criptografado e torná-lo ilegível.
>- Para tabelas, existe uma restrição semelhante. Tenha cuidado para não atualizar propriedades criptografadas sem atualizar os metadados de criptografia.
>- Se você definir metadados no blob criptografado, poderá substituir os metadados relacionados à criptografia necessários para a descriptografia, uma vez que a definição de metadados não é aditiva. Isso também ocorre em instantâneos. Evite especificar metadados ao criar um instantâneo de um blob criptografado.


## API do cliente / Interface

Ao criar um objeto EncryptionPolicy, os usuários podem fornecer somente uma chave (Implementando IKey), somente um resolvedor (Implementando IKeyResolver) ou ambos. IKey é o tipo de chave básico que é identificado usando um identificador de chave e que fornece a lógica para empacotamente/desempacotamento. IKeyResolver é usado para resolver uma chave durante o processo de descriptografia. Ele define um método ResolveKey que retorna um IKey dado um certo identificador de chave. Isso fornece aos usuários a capacidade de escolher entre várias chaves que são gerenciadas em vários locais.

- Para criptografia, a chave é usada sempre e a ausência de uma chave resultará em um erro.
- Para descriptografar:
	- O resolvedor de chave é invocado se especificado para obter a chave. Se o resolvedor for especificado, mas não tiver um mapeamento para o identificador de chave, um erro será gerado.
	- Se o resolvedor não for especificado, mas uma chave for especificada, o identificador de chave na chave é comparado com o que está armazenado para o serviço.

Os [exemplos de criptografia](https://github.com/Azure/azure-storage-net/tree/preview/Samples/GettingStarted/EncryptionSamples) demonstram um cenário ponta a ponta mais detalhado para blobs, filas e tabelas, juntamente com a integração do Cofre da Chave.

### Blobs

Os usuários criam um objeto **BlobEncryptionPolicy** e o definem nas opções de solicitação (por API ou no nível do cliente usando **DefaultRequestOptions**). Todo o resto será tratado pela biblioteca de cliente internamente.

	// Create the IKey used for encryption.
 	RsaKey key = new RsaKey("private:key1" /* key identifier */);
  
 	// Create the encryption policy to be used for upload and download.
 	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);
  
 	// Set the encryption policy on the request options.
 	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };
  
 	// Upload the encrypted contents to the blob.
 	blob.UploadFromStream(stream, size, null, options, null);
  
 	// Download and decrypt the encrypted contents from the blob.
 	MemoryStream outputStream = new MemoryStream();
 	blob.DownloadToStream(outputStream, null, options, null);

### Filas

Os usuários criam um objeto **QueueEncryptionPolicy** e o definem nas opções de solicitação (por API ou no nível do cliente usando **DefaultRequestOptions**). Todo o resto será tratado pela biblioteca de cliente internamente.


	// Create the IKey used for encryption.
 	RsaKey key = new RsaKey("private:key1" /* key identifier */);
  
 	// Create the encryption policy to be used for upload and download.
 	QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);
  
 	// Add message
 	QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 	queue.AddMessage(message, null, null, options, null);
  
 	// Retrieve message
 	CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);

### Tabelas

Além de criar uma política de criptografia e defini-la nas opções de solicitação, os usuários terão que especificar um **EncryptionResolver** em **TableRequestOptions** ou definir atributos na entidade.

#### Usando o resolvedor


	// Create the IKey used for encryption.
 	RsaKey key = new RsaKey("private:key1" /* key identifier */);
  
 	// Create the encryption policy to be used for upload and download.
 	TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);
  
 	TableRequestOptions options = new TableRequestOptions() 
 	{ 
    	EncryptionResolver = (pk, rk, propName) =>
     	{
        	if (propName == "foo")
         	{
            	return true;
         	}
         	return false;
     	},
     	EncryptionPolicy = policy
 	};
  
 	// Insert Entity
 	currentTable.Execute(TableOperation.Insert(ent), options, null);
  
 	// Retrieve Entity
 	// No need to specify an encryption resolver for retrieve
 	TableRequestOptions retrieveOptions = new TableRequestOptions() 
 	{
    	EncryptionPolicy = policy
 	};
  
 	TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 	TableResult result = currentTable.Execute(operation, retrieveOptions, null);

#### Usando atributos

Conforme mencionado acima, se a entidade implementar TableEntity, as propriedades podem ser decoradas com o atributo [EncryptProperty] em vez de especificar o EncryptionResolver.

	[EncryptProperty]
 	public string EncryptedProperty1 { get; set; }

## Próximas etapas

[Criptografia do lado do cliente do Armazenamento do Microsoft Azure – Visualização](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview.aspx) Baixar a [biblioteca de cliente do Armazenamento do Azure para pacote .NET NuGet](http://www.nuget.org/packages/WindowsAzure.Storage/4.4.0-preview) Baixar a [biblioteca de cliente do Armazenamento do Azure para código-fonte .NET](https://github.com/Azure/azure-storage-net/tree/preview) do GitHub Baixar os pacotes NuGet de [Núcleo](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [Cliente](http://www.nuget.org/packages/Microsoft.Azure.KeyVault/) e [Extensões](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/) do Cofre da Chave Visite a [Documentação do Cofre da Chave do Azure](../articles/key-vault-whatis.md)

<!---HONumber=July15_HO4-->