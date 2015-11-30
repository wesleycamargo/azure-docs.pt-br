<properties 
	pageTitle="Assinaturas de acesso compartilhado: noções básicas sobre o Modelo SAS | Microsoft Azure" 
	description="Saiba mais sobre como delegar acesso a recursos de Armazenamento do Azure, incluindo blobs, filas, tabelas e arquivos, usando SAS (assinaturas de acesso compartilhado). Com as assinaturas de acesso compartilhado, você pode proteger sua chave de conta de armazenamento enquanto concede acesso a recursos em sua conta a outros usuários. Você pode controlar as permissões concedidas e o intervalo no qual a SAS é válida. Se você também estabelecer uma política de acesso armazenada, pode revogar a SAS caso tema que a segurança da sua conta esteja comprometida." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="tamram"/>



# Assinaturas de acesso compartilhado, Parte 1: Noções básicas sobre o modelo SAS

## Visão geral

O uso de SAS (assinatura de acesso compartilhado) é uma maneira eficiente de conceder acesso limitado a objetos na sua conta de armazenamento a outros clientes, sem precisar expor sua chave de conta. Na Parte 1 deste tutorial sobre assinaturas de acesso compartilhado, apresentaremos uma visão geral do modelo SAS e examinaremos as práticas recomendadas à SAS. A [Parte 2](storage-dotnet-shared-access-signature-part-2.md) do tutorial o orientará pelo processo de criação de assinaturas de acesso compartilhado com o serviço Blob.

## O que é uma assinatura de acesso compartilhado?

Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento. Isso significa que você pode conceder a um cliente permissões limitadas a objetos na sua conta de armazenamento por um período especificado e com um conjunto determinado de permissões, sem precisar compartilhar suas chaves de acesso de conta. A SAS é um URI que engloba em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a SAS, o cliente só precisa passar a SAS ao construtor apropriado ou ao método apropriado.

## Quando você deve usar uma assinatura de acesso compartilhado?

Você pode usar uma SAS quando desejar fornecer acesso aos recursos de sua conta de armazenamento a um cliente não confiável com a chave de conta. As chaves de conta de armazenamento incluem uma chave primária e uma chave secundária, que concedem acesso administrativo à sua conta e a todos os recursos que ela contém. A exposição de qualquer uma de suas chaves de conta torna sua conta vulnerável a um possível uso mal-intencionado ou negligente. As assinaturas de acesso compartilhado são uma alternativa segura que permite que outros clientes leiam, gravem e excluam dados da sua conta de armazenamento de acordo com as permissões que você tiver concedido a eles e sem que eles precisem da chave de conta.

Um cenário comum em que uma SAS é útil é um serviço onde os usuários leem e gravam seus próprios dados na sua conta de armazenamento. Em um cenário em que uma conta de armazenamento armazena dados do usuário, há dois padrões de design comuns:


1\. Os clientes carregam e baixam dados por meio de um serviço de proxy front-end, que executa a autenticação. Esse serviço de proxy front-end tem a vantagem de permitir a validação de regras de negócio, mas, para grandes quantidades de dados ou transações de alto volume, a criação de um serviço que possa ser dimensionado de acordo com a demanda pode ser difícil ou dispendiosa.

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2\. Um serviço leve autentica o cliente, conforme necessário, e gera uma SAS. Depois que o cliente recebe a SAS, ele pode acessar os recursos da conta de armazenamento diretamente com as permissões definidas pela SAS e para o intervalo permitido pela SAS. A SAS reduz a necessidade de roteamento de todos os dados por meio do serviço de proxy front-end.

![sas-storage-provider-service][sas-storage-provider-service]

Vários serviços reais podem usar uma mistura híbrida dessas duas abordagens, dependendo do cenário envolvido, com alguns dados processados e validados por meio do proxy front-end, enquanto outros dados são salvos e/ou lidos diretamente com a SAS.

Além disso, você precisará usar uma SAS para autenticar o objeto de origem em uma operação de cópia em determinados cenários:

- Quando você copia um blob para outro que reside em uma conta de armazenamento diferente, deve usar uma SAS para autenticar o blob de origem. Com a versão de 05-04-2015, opcionalmente, você pode usar uma SAS para autenticar o blob de destino.
- Quando você copia um arquivo para outro que reside em uma conta de armazenamento diferente, deve usar uma SAS para autenticar o arquivo de origem. Com a versão de 05/04/2015, você pode usar uma SAS para autenticar também o arquivo de destino.
- Quando você estiver copiando um blob em um arquivo, ou um arquivo em um blob, use uma SAS para autenticar o objeto de origem, mesmo se os objetos de origem e destino residirem dentro da mesma conta de armazenamento.

>[AZURE.NOTE]Atualmente, há suporte à SAS de conta apenas para os serviços Blob e Arquivo. Ele terá suporte para os serviços Tabela e Fila em um futuro próximo.

## Tipos de assinaturas de Acesso compartilhado.

A versão de 05-04-2015 do armazenamento do Azure apresenta um novo tipo de assinatura de acesso compartilhado, a SAS de conta. Agora você pode criar qualquer um dos dois tipos de assinaturas de acesso compartilhado:

- **SAS de Conta.** A SAS de conta delega acesso a recursos em um ou mais dos serviços de armazenamento. Todas as operações disponíveis através de um serviço SAS também estão disponíveis por meio de uma SAS de conta. Além disso, com a SAS de conta, você pode delegar acesso a operações que se aplicam a um determinado serviço, como **Obter/Definir Propriedades do Serviço** e **Obter Estatísticas do Serviço**. Você também pode delegar acesso para operações de leitura, gravação e exclusão em filas, tabelas e contêineres de blob, além de compartilhamentos de arquivos que não são permitidos com uma SAS de serviço. Veja [Construindo uma SAS de conta](https://msdn.microsoft.com/library/mt584140.aspx) para obter informações detalhadas sobre como construir o token SAS de conta.

- **SAS de Serviço.** A SAS de serviço delega acesso a um recurso em apenas um dos serviços de armazenamento: o serviço Blob, Fila, Tabela ou Arquivo. Veja [Construindo uma SAS de serviço](https://msdn.microsoft.com/library/dn140255.aspx) e [Exemplos de SAS de serviço](https://msdn.microsoft.com/library/dn140256.aspx) para obter informações detalhadas sobre como construir o token SAS de serviço.

## Como funciona uma assinatura de acesso compartilhado

Uma assinatura de acesso compartilhado é um URI que aponta para um ou mais recursos de armazenamento e inclui um token que contém um conjunto especial de parâmetros de consulta. O token indica como os recursos podem ser acessados pelo cliente. Um desses parâmetros de consulta, a assinatura, é construído a partir dos parâmetros SAS e assinado com a chave de conta. Essa assinatura é usada pelo Armazenamento do Azure para autenticar a SAS.

A SAS de conta e os tokens SAS de serviço incluem alguns parâmetros comuns e também usam alguns parâmetros que são diferentes.

### Parâmetros comuns a tokens SAS de conta e SAS de serviço

- **Versão da API** Um parâmetro opcional que especifica a versão do serviço de armazenamento a ser usada para executar a solicitação. 
- **Versão do serviço** Um parâmetro obrigatório que especifica a versão do serviço de armazenamento a ser usada para executar a solicitação.
- **Hora de início.** É a hora em que a SAS torna-se válida. A hora de início para uma assinatura de acesso compartilhado é opcional; se estiver omitida, a SAS entrará em vigor imediatamente. 
- **Hora de expiração.** É a hora após a qual a SAS não é mais válida. As práticas recomendadas sugerem que você especifique uma hora de expiração para uma SAS ou associe-a a uma política de acesso armazenada (veja mais abaixo).
- **Permissões.** As permissões especificadas nas SAS indicam quais operações o cliente pode executar com o recurso de armazenamento usando a SAS. As permissões disponíveis são diferentes entre SAS de conta e de serviço.
- **IP.** Um parâmetro opcional que especifica um endereço IP ou um intervalo de endereços IP fora do Azure (veja a seção [Estado de configuração da sessão de roteamento](../expressroute/expressroute-workflows.md#routing-session-configuration-state) da Rota Expressa) do qual as solicitações serão aceitas. 
- **Protocolo.** Um parâmetro opcional que especifica o protocolo permitido para uma solicitação. Os valores possíveis são HTTPS e HTTP (http, https), que é o valor padrão, ou somente HTTPS (https). Observe que somente HTTP não é um valor permitido.
- **Assinatura.** A assinatura é construída com os parâmetros especificados como parte do token e depois criptografada. Ela é usada para autenticar a SAS.

### Parâmetros para um token SAS de conta

- **Serviço ou serviços.** Uma SAS de conta pode delegar acesso a um ou mais serviços de armazenamento. Por exemplo, você pode criar uma SAS de conta que delegada acesso aos serviços de arquivo e Blob. Ou você pode criar uma SAS que delega acesso a todos os quatro serviços (Blob, fila, tabela e arquivo).
- **Tipos de recurso de armazenamento.** Uma SAS de conta se aplica a uma ou mais classes de recursos de armazenamento, em vez de a um recurso específico. Você pode criar uma SAS de conta para delegar acesso a:
	- APIs de nível de serviço, que são chamadas em relação ao recurso da conta de armazenamento. Os exemplos incluem **Obter/Definir Propriedades do Serviço**, **Obter Estatísticas do Serviço** e **Listar Contêineres/Filas/Tabelas/Compartilhamentos**.
	- APIs de nível de contêiner, que são chamadas em relação a objetos de contêiner para cada serviço: tabelas, filas, contêineres de blob e compartilhamentos de arquivos. Os exemplos incluem **Criar/Excluir Contêiner**, **Criar/Excluir Fila**, **Criar/Excluir Tabela**, **Criar/Excluir Compartilhamento** e **Listar Blobs/Arquivos e Diretórios**.
	- APIs de nível de objeto, que são chamadas em relação a blobs, mensagens de fila, entidades de tabela e arquivos. Por exemplo, **Colocar Blob**, **Consultar Entidade**, **Obter Mensagens** e **Criar Arquivo**.

### Parâmetros para um token SAS de serviço

- **Recurso de armazenamento.** Os recursos de armazenamento para os quais é possível delegar acesso com SAS de serviço incluem:
	- Contêineres e blobs
	- Compartilhamentos de arquivos e arquivos
	- Filas
	- Tabelas e intervalos de entidades de tabela.

## Exemplos de URIs de SAS

Este é um exemplo de um URI SAS de serviço que fornece permissões de leitura e gravação para um blob. A tabela divide cada parte do URI para explicar como ele também contribui para a SAS:

	https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

Nome|Parte SAS|Descrição
---|---|---
URI do blob|https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt |O endereço do blob. Observe que o uso de HTTPS é altamente recomendável.
Versão dos serviços de armazenamento|sv=2015-04-05|Para os serviços de armazenamento de versão 12-02-2012 e posterior, este parâmetro indica a versão a ser usada.
Hora de início|st=2015-04-29T22%3A18%3A26Z|Especificada em um formato ISO 8601. Se você quiser que a SAS seja imediatamente válida, omita a hora de início.
Hora de expiração|se=2015-04-30T02%3A23%3A26Z|Especificada em um formato ISO 8601.
Recurso|sr=b|O recurso é um blob.
Permissões|sp=rw|As permissões concedidas pelas SAS incluem Ler (r) e Gravar (w).
Intervalo IP|sip=168.1.5.60-168.1.5.70|O intervalo de endereços IP do qual uma solicitação será aceita.
Protocolo|spr=https|São permitidas somente solicitações usando HTTPS.
Assinatura|sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D|Usada para autenticar o acesso ao blob. A assinatura é um HMAC computado em uma cadeia-para-assinar e uma chave que usa o algoritmo SHA256 e depois codificado usando a codificação Base64.

E aqui está um exemplo de uma SAS de conta que usa os mesmos parâmetros comuns no token. Como esses parâmetros estão descritos acima, eles não serão descritos aqui. Somente os parâmetros específicos para a SAS de conta são descritos na tabela a seguir.

	https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B

Nome|Parte SAS|Descrição
---|---|---
URI de recurso|https://myaccount.blob.core.windows.net/?restype=service&comp=properties|The Ponto de extremidade de serviço Blob, com parâmetros para obter as propriedades do serviço (quando chamado com GET) ou definir as propriedades do serviço (quando chamado com SET).
Serviços|ss=bf|A SAS se aplica a serviços de arquivo e Blob
Tipos de recurso|srt=s|A SAS se aplica a operações de nível de serviço.
Permissões|sp=rw|As permissões concedem acesso a operações de leitura e gravação.  

Considerando que as permissões são restritas ao nível de serviço, as operações acessíveis com essa SAS são **Obter Propriedades do Serviço Blob** (leitura) e **Definir Propriedades do Serviço Blob** (gravação). Contudo, com um URI de recurso diferente, o mesmo token SAS também pode ser usado para delegar acesso a **Obter Estatísticas do Serviço Blob** (leitura).

## Controlando uma SAS com uma política de acesso armazenada ##

Uma assinatura de acesso compartilhado pode assumir uma destas duas formas:

- **SAS ad hoc:** quando você cria uma SAS ad hoc, a hora de início, a hora de vencimento e as permissões para a SAS são todas especificadas no URI SAS (ou implícitas, quando a hora de início é omitida). Esse tipo de SAS pode ser criado como uma SAS de conta ou uma SAS de serviço. 

- **SAS com política de acesso armazenado:** uma política de acesso armazenado é definida em um contêiner de recurso - um contêiner de blob, tabela, fila ou compartilhamento de arquivos - e pode ser usada para gerenciar as restrições de uma ou mais assinaturas de acesso compartilhado. Quando você associa uma SAS a uma política de acesso armazenada, a SAS herda as restrições - a hora de início, a hora de expiração e as permissões - definidas para a política de acesso armazenada.

>[AZURE.NOTE]Atualmente, uma SAS de conta deve ser uma SAS ad hoc. As SAS de conta ainda não dão suporte a políticas de acesso armazenadas.

A diferença entre as duas formas é importante para um cenário fundamental: revogação. Uma SAS é uma URL, portanto qualquer pessoa que obtiver a SAS poderá usá-la, independentemente de quem a tiver solicitado em primeiro lugar. Se uma SAS for publicada publicamente, ela poderá ser usada por qualquer pessoa no mundo. Uma SAS distribuída será válida até que ocorra um destes quatro fatores:

1.	A hora de expiração especificada na SAS é atingida.
2.	A hora de expiração especificada na política de acesso armazenada referenciada pelas SAS é atingida (se uma política de acesso armazenada for referenciada e especificar uma hora de expiração). Isso pode ocorrer porque transcorreu o intervalo ou porque você modificou a política de acesso armazenada para ter uma hora de expiração no passado, que é uma maneira de revogar a SAS.
3.	A política de acesso armazenada referenciada pelas SAS é excluída, que é uma outra maneira de revogar a SAS. Observe que, se você recriar a política de acesso armazenada exatamente com o mesmo nome, todos os tokens SAS existentes serão novamente válidos de acordo com as permissões associadas a essa política de acesso armazenada (supondo que a hora de expiração não tenha passado na SAS). Se você estiver pretendendo revogar a SAS, certifique-se de usar um nome diferente se recriar a política de acesso com uma hora de expiração no futuro.
4.	A chave de conta usada para criar as SAS é regenerada. Observe que isso causará uma falha de autenticação de todos os componentes do aplicativo que usam essa chave de conta até que eles sejam atualizados para usar a outra chave de conta válida ou a chave de conta recém-regenerada.

>[AZURE.IMPORTANT]Um URI de assinatura de acesso compartilhado é associado com a chave de conta usada para criar a assinatura e a política de acesso armazenado associada (se houver). Se nenhuma política de acesso armazenado for especificada, a única maneira de revogar uma assinatura de acesso compartilhado é alterar a chave da conta.

## Exemplos: criar e usar assinaturas de acesso compartilhado

Abaixo estão alguns exemplos de ambos os tipos de assinatura de acesso compartilhado: SAS de conta e SAS de serviço.

Para executar esses exemplos, você precisará baixar e referenciar esses pacotes:

- [Biblioteca de cliente de armazenamento do Azure para .NET](http://www.nuget.org/packages/WindowsAzure.Storage), versão 6. x ou posterior (para usar a conta SAS).
- [Azure Configuration Manager](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) 

### Exemplo: SAS de conta

O exemplo de código a seguir cria uma SAS de conta que é válida para os serviços de arquivo e de blob e dá ao cliente permissões de leitura, gravação e listagem de permissões para acessar as APIs de nível de serviço. A SAS de conta restringe o protocolo para HTTPS, para que a solicitação deva ser feita com HTTPS.

    static string GetAccountSASToken()
    {
        // To create the account SAS, you need to use your shared key credentials. Modify for your account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Create a new access policy for the account.
        SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
            {
                Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
                Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
                ResourceTypes = SharedAccessAccountResourceTypes.Service,
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
                Protocols = SharedAccessProtocol.HttpsOnly
            };

        // Return the SAS token.
        return storageAccount.GetSharedAccessSignature(policy);
    }

Para usar a SAS de conta e acessar as APIs de nível de serviço para o serviço Blob, construa um objeto de cliente Blob usando a SAS e o ponto de extremidade de armazenamento de Blob para sua conta de armazenamento.

    static void UseAccountSAS(string sasToken)
    {
        // In this case, we have access to the shared key credentials, so we'll use them
        // to get the Blob service endpoint.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create new storage credentials using the SAS token.
        StorageCredentials accountSAS = new StorageCredentials(sasToken);
        // Use these credentials and the Blob storage endpoint to create a new Blob service client.
        CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, blobClient.StorageUri, null, null, null);
        CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

        // Now set the service properties for the Blob client created with the SAS.
        blobClientWithSAS.SetServiceProperties(new ServiceProperties()
        {
            HourMetrics = new MetricsProperties()
            {
                MetricsLevel = MetricsLevel.ServiceAndApi,
                RetentionDays = 7,
                Version = "1.0"
            },
            MinuteMetrics = new MetricsProperties()
            {
                MetricsLevel = MetricsLevel.ServiceAndApi,
                RetentionDays = 7,
                Version = "1.0"
            },
            Logging = new LoggingProperties()
            {
                LoggingOperations = LoggingOperations.All,
                RetentionDays = 14,
                Version = "1.0"
            }
        });

        // The permissions granted by the account SAS also permit you to retrieve service properties.
        ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
        Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
        Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
        Console.WriteLine(serviceProperties.HourMetrics.Version);
    }

### Exemplo: SAS de serviço com política de acesso armazenada

O exemplo de código a seguir cria uma política de acesso armazenada em um contêiner e gera uma SAS de serviço para o contêiner. Essa SAS pode ser oferecida a clientes para permissões de leitura e gravação no contêiner. Altere o código para usar seu próprio nome de conta:

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the storage account with the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
       
    // Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Get a reference to the container for which shared access signature will be created.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();
    
    // Get the current permissions for the blob container.
    BlobContainerPermissions blobPermissions = container.GetPermissions();

    // Clear the container's shared access policies to avoid naming conflicts.
    blobPermissions.SharedAccessPolicies.Clear();
    
    // The new shared access policy provides read/write access to the container for 24 hours.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // To ensure SAS is valid immediately, don’t set the start time.
       // This way, you can avoid failures caused by small clock differences.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
       Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Add
    });
    
    // The public access setting explicitly specifies that 
    // the container is private, so that it can't be accessed anonymously.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;
    
    // Set the new stored access policy on the container.
    container.SetPermissions(blobPermissions);
    
    // Get the shared access signature token to share with users.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

Um cliente em posse da SAS de serviço pode usá-la no seu código para autenticar uma solicitação para ler ou gravar em um blob no contêiner. Por exemplo, o código a seguir usa o token SAS para criar um novo blob de blocos no contêiner. Altere o código para usar seu próprio nome de conta:

    Uri blobUri = new Uri("https://<myaccount>.blob.core.windows.net/mycontainer/myblob.txt");
    
    // Create credentials with the SAS token. The SAS token was created in previous example.
    StorageCredentials credentials = new StorageCredentials(sasToken);
    
    // Create a new blob.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);
    
    // Upload the blob. 
    // If the blob does not yet exist, it will be created. 
    // If the blob does exist, its existing content will be overwritten.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Temp\myblob.txt"))
    {
    	blob.UploadFromStream(fileStream);
    }


## Práticas recomendadas para uso de assinaturas de acesso compartilhado

Ao usar assinaturas de acesso compartilhado nos seus aplicativos, você precisará estar ciente de dois riscos possíveis:

- Se ocorrer perda de uma SAS, ela poderá ser usada por qualquer pessoa que a obtiver, o que poderá comprometer a sua conta de armazenamento.
- Se uma SAS fornecida para um aplicativo cliente expirar e o aplicativo não conseguir recuperar uma nova SAS do seu serviço, a funcionalidade do aplicativo poderá ser prejudicada.  

As recomendações a seguir para uso de assinaturas de acesso compartilhado ajudarão a equilibrar esses riscos:

1. **Sempre use HTTPS** para criar ou distribuir uma SAS. Se uma SAS for passada por HTTP e interceptada, um invasor que estiver realizando um ataque intermediário poderá lê-la e, em seguida, usá-la exatamente como o usuário previsto, o que poderá comprometer dados confidenciais ou gerar dados corrompidos pelo usuário mal-intencionado.
2. **Faça referência às políticas de acesso armazenadas sempre que possível.** As políticas de acesso armazenadas permitem que você revogue permissões sem precisar regenerar as chaves de conta de armazenamento. Defina a expiração nessas políticas para um tempo muito longo (ou infinito) e certifique-se de que ela seja atualizada regularmente para uma data mais distante no futuro.
3. **Use horas de expiração de curto prazo em uma SAS ad hoc.** Dessa forma, mesmo que uma SAS seja comprometida inadvertidamente, ela só será viável por um curto período. Esta prática será especialmente importante se você não puder fazer referência a uma política de acesso armazenada. Essa prática também ajudará a limitar a quantidade de dados que podem ser gravados em um blob, limitando o tempo disponível para carregá-los.
4. **Faça com que os clientes renovem a SAS automaticamente, se necessário.** Os clientes devem renovar a SAS bem antes da expiração esperada, para que haja tempo para novas tentativas se o serviço que fornece a SAS não estiver disponível. Se o objetivo de uso da SAS for destinado a um pequeno número de operações imediatas e de curta duração, que devem ser concluídas dentro do tempo de expiração fornecido, isso talvez não seja necessário, pois não se espera que a SAS seja renovada. No entanto, se você tiver um cliente que está sempre fazendo solicitações via SAS, surge a possibilidade de expiração. A principal consideração consiste em equilibrar a necessidade de a SAS ser de curta duração (conforme mencionado acima) com a necessidade de garantir que o cliente esteja solicitando renovação com antecedência suficiente para evitar uma interrupção devido à expiração da SAS antes da renovação bem-sucedida.
5. **Tenha cuidado com a hora de início da SAS.** Se você definir a hora de início de uma SAS para **agora**, poderão ser observadas falhas intermitentemente para os primeiros minutos devido à defasagem horária (diferenças na hora atual de acordo com computadores diferentes). Em geral, defina a hora de início para, no mínimo, 15 minutos antes, ou não a defina, o que a tornará imediatamente válida em todos os casos. Em geral, o mesmo também aplica-se à hora de expiração - lembre-se de que você pode observar até 15 minutos de defasagem horária em uma das duas direções em qualquer solicitação. Observação para os clientes que usam uma versão de REST anterior à 2012-02-12, a duração máxima de uma SAS que não faz referência a uma política de acesso armazenada é 1 hora, e qualquer política que especifique um período maior do que esse falhará.
6.	**Seja específico com o recurso a ser acessado.** Uma prática recomendada de segurança comum consiste em fornecer os privilégios mínimos necessários a um usuário. Se um usuário precisar apenas de acesso de leitura a uma única entidade, conceda-lhe acesso de leitura a essa única entidade, e não acesso de leitura/gravação/exclusão a todas as entidades. Isso também ajuda a minimizar a ameaça da SAS que estiver sendo comprometida, pois a SAS terá menos poder nas mãos de um invasor.
7.	**É importante que você entenda que a sua conta será cobrada por qualquer uso, incluindo o realizado com a SAS.** Se você fornecer acesso de gravação a um blob, um usuário poderá optar por carregar um blob de 200 GB. Se você também tiver concedido a ele acesso de leitura, é possível que ele decida baixá-lo 10 vezes, incorrendo em 2 TB de custos de egresso para você. Mais uma vez, forneça permissões limitadas, para ajudar a reduzir a possibilidade de usuários mal-intencionados. Use SAS de curta duração para reduzir essa ameaça (mas, tenha cuidado com a defasagem horária na hora de término).
8.	**Valide os dados gravados com a SAS.** Quando um aplicativo cliente gravar dados na sua conta de armazenamento, tenha em mente de que poderá haver problemas com esses dados. Se o seu aplicativo necessitar de que esses dados sejam validados ou autorizados antes que estejam prontos para uso, você deverá realizar essa validação depois que os dados forem gravados e antes que eles sejam usados pelo seu aplicativo. Essa prática também protegerá contra dados corrompidos ou mal-intencionados que estiverem sendo gravados na sua conta por um usuário que adquiriu a SAS de forma adequada ou por um usuário que estiver explorando uma SAS vazada.
9. **Não use sempre SAS.** Às vezes, os riscos associados a uma determinada operação em relação à sua conta de armazenamento superam os benefícios da SAS. Para essas operações, crie um serviço de camada intermediária que grave na sua conta de armazenamento após a validação, a autenticação e a auditoria da regra de negócio. Além disso, algumas vezes é mais simples de gerenciar o acesso de outras maneiras. Por exemplo, se quiser tornar todos os blobs de um contêiner publicamente legíveis, você poderá tornar o contêiner Público, em vez de fornecer uma SAS para o acesso de cada cliente.
10.	**Use a Análise de Armazenamento para monitorar seu aplicativo.** Você pode usar log e métricas para observar qualquer pico nas falhas de autenticação devido a uma interrupção no seu serviço de provedor de SAS ou à remoção acidental de uma política de acesso armazenada. Consulte o [Blog da Equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) para obter informações adicionais.

## Conclusão ##

As assinaturas de acesso compartilhado são úteis para fornecer permissões limitadas para a sua conta de armazenamento aos clientes que não devem ter a chave de conta. Desse modo, elas são uma parte vital do modelo de segurança para qualquer aplicativo que utilize o Armazenamento do Azure. Se você seguir as práticas recomendadas listadas aqui, poderá usar a SAS para oferecer mais flexibilidade de acesso aos recursos da sua conta de armazenamento, sem comprometer a segurança do seu aplicativo.

## Próximas etapas ##

- [Assinaturas de acesso compartilhado, Parte 2: criar e usar uma SAS com o serviço Blob](storage-dotnet-shared-access-signature-part-2.md)
- [Como usar o armazenamento de arquivo do Azure com o Windows](storage-dotnet-how-to-use-files.md)
- [Gerenciar o acesso aos recursos de Armazenamento do Azure](storage-manage-access-to-resources.md)
- [Delegando acesso com uma assinatura de acesso compartilhado](http://msdn.microsoft.com/library/azure/ee395415.aspx)
- [Introdução ao SAS de Fila e Tabela](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx) [sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png [sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png


 

<!---HONumber=Nov15_HO4-->