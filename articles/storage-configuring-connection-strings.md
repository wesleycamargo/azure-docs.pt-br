<properties 
	pageTitle="Configurando cadeias de conexão do Azure" 
	description="Saiba como configurar cadeias de conexão para a conta de armazenamento no Azure." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Configurando cadeias de conexão do Azure

## Visão geral
Uma cadeia de conexão contém os parâmetros que são necessários para acessar sua conta de armazenamento do Azure. Você pode configurar uma cadeia de conexão das seguintes maneiras:

- Conecte-se ao emulador de armazenamento do Azure quando testar localmente seu serviço ou aplicativo.

- Conecte-se a uma conta de armazenamento no Azure usando os pontos de extremidade padrão para os serviços de armazenamento.

- Conecte-se a uma conta de armazenamento no Azure usando pontos de extremidade explícitos para os serviços de armazenamento.

Se seu aplicativo é um serviço de nuvem em execução no Azure, o lugar mais conveniente para armazenar a cadeia de conexão está no [Esquema de configuração de serviço do Azure (arquivo. cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Se seu aplicativo é executado em outro ambiente (por exemplo, na área de trabalho), você provavelmente desejará armazenar a cadeia de conexão em um arquivo app.config ou em outro arquivo de configuração. Você pode usar a classe CloudConfigurationManager do Azure para acessar a cadeia de conexão em tempo de execução, independentemente de onde ela está sendo executada.

## Conectando-se ao emulador de armazenamento

O emulador de armazenamento é uma conta local com um nome conhecido e uma chave. Como o nome da conta e a chave são os mesmos para todos os usuários, você pode usar um formato de cadeia de caracteres de atalho para se referir ao emulador de armazenamento dentro de uma cadeia de conexão. Defina o valor da cadeia de conexão para `UseDevelopmentStorage=true`.

Você também pode especificar um proxy HTTP para usar quando estiver testando seu serviço no emulador de armazenamento. Isso pode ser útil para observar solicitações e respostas HTTP enquanto você estiver depurando operações nos serviços de armazenamento. Para especificar um proxy, adicione a opção DevelopmentStorageProxyUri à cadeia de conexão e defina seu valor para o URI de proxy. Por exemplo, aqui está uma cadeia de conexão que aponta para o emulador de armazenamento e configura um proxy HTTP:

    UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

## Conectando-se a uma conta de armazenamento no Azure

Você pode definir uma cadeia de conexão para uma conta de armazenamento no Azure em uma das seguintes maneiras:

- Suponha que os pontos de extremidade padrão para os serviços de armazenamento. Essa é a opção mais simples para a criação de uma cadeia de conexão. Quando você usa esse formato de cadeia de caracteres de conexão, você precisa 

- Especificar pontos de extremidade explícitos para os serviços de armazenamento. Esta opção permite que você crie uma cadeia de conexão mais complexa. Quando você usa esse formato de cadeia de caracteres, você pode especificar pontos de extremidade de serviço de armazenamento que incluem um nome de domínio personalizado ou minimizar a exposição de informações para uma cadeia de caracteres de conexão baseada em assinatura de acesso compartilhado.


> [AZURE.NOTE] Os serviços de armazenamento do Azure oferecem suporte a HTTP e HTTPS. No entanto, usar HTTPS é altamente recomendável.

## Criando uma cadeia de conexão com pontos de extremidade padrão

Para criar uma cadeia de conexão que se baseia nos pontos de extremidade padrão para o serviço de armazenamento, use o seguinte formato de cadeia de caracteres de conexão. Indique se você deseja se conectar à conta de armazenamento por meio de HTTP ou HTTPS, substitua myAccountName pelo nome da sua conta de armazenamento, substitua myAccountKey por sua chave de acesso da conta:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Por exemplo, a cadeia de conexão deve ser semelhante ao seguinte exemplo de cadeia de conexão:

    DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtR7wYQk33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIHy5l/Yhg==

## Criando uma cadeia de conexão com pontos de extremidade explícitos

Você talvez queira especificar explicitamente os pontos de extremidade do serviço em sua cadeia de conexão pelos seguintes motivos:

- Você registrou um nome de domínio personalizado para sua conta de armazenamento com o serviço Blob.

- Você tem uma assinatura de acesso compartilhado para acessar recursos de armazenamento.

### Especificando um ponto de extremidade de blob com um nome de domínio personalizado 
Se você registrou o nome de domínio personalizado para uso com o serviço Blob, convém configurar explicitamente o ponto de extremidade de blob em sua cadeia de conexão. O valor de ponto de extremidade que está listado na cadeia de conexão é usado para construir os URIs de solicitação ao serviço Blob e ele determina a forma que os URIs são retornados ao seu código. 

Para criar uma cadeia de conexão que especifica os pontos de extremidade explícitos, especifique o ponto de extremidade de serviço completo para cada serviço, incluindo a especificação do protocolo (HTTP ou HTTPS) usando o seguinte formato:

    BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;[credentials]

Quando você especifica explicitamente pontos de extremidade de serviço, você tem duas opções para especificar credenciais. Você pode especificar o nome da conta e a chave (`AccountName=myAccountName;AccountKey=myAccountKey`), conforme mostrado na seção anterior, ou você pode **especificar uma assinatura de acesso compartilhado**, conforme mostrado em Especificando pontos de extremidade com uma seção de assinatura de acesso compartilhado. Se você estiver especificando o nome da conta e a chave, o formato de cadeia de caracteres completo é:
    BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;AccountName=myAccountName;AccountKey=myAccountKey

Você pode especificar pontos de extremidade de blob, tabela e fila em uma cadeia de conexão. Você deve especificar pelo menos um ponto de extremidade, mas não é necessário especificar todos os três. Por exemplo, se você estiver criando uma cadeia de conexão para uso com um ponto de extremidade de blob personalizado, especificar os pontos de extremidade de fila e tabela é opcional. Observe que se você optar por omitir os pontos de extremidade de fila e tabela da cadeia de conexão, então você não poderá acessar os serviços de fila e tabela no seu código usando essa cadeia de conexão.

### Especificando pontos de extremidade com uma assinatura de acesso compartilhado 
Você pode criar uma cadeia de conexão com pontos de extremidade explícitos para acessar recursos de armazenamento por meio de uma assinatura de acesso compartilhado. Nesse caso, você pode especificar a assinatura de acesso compartilhado como parte da cadeia de conexão, em vez do nome da conta e credenciais de chave. O token de assinatura de acesso compartilhado encapsula informações sobre o recurso a ser acessado, o período de tempo em que está disponível e as permissões concedidas. Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [Delegando acesso com uma assinatura de acesso compartilhado](https://msdn.microsoft.com/library/ee395415.aspx).

Para criar uma cadeia de conexão que inclui uma assinatura de acesso compartilhado, especifique a cadeia de caracteres no seguinte formato:

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

O ponto de extremidade pode ser um ponto de extremidade de serviço padrão ou um ponto de extremidade personalizado. O `base64Signature` corresponde à parte de assinatura de uma assinatura de acesso compartilhado. A assinatura é um HMAC computado em uma cadeia de caracteres para assinatura válida e uma chave usando o algoritmo SHA256, que é codificado na Base64.

<!--HONumber=47-->
