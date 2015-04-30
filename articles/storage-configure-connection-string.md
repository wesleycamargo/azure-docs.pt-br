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

# Configurar cadeias de conexão do Armazenamento do Azure

## Visão geral

Uma cadeia de conexão inclui as informações necessárias para acessar sua conta de armazenamento do Azure programaticamente. Você pode configurar uma cadeia de conexão para se conectar ao Armazenamento do Azure das seguintes maneiras:

- Conecte-se ao emulador de armazenamento do Azure quando testar localmente seu serviço ou aplicativo.

- Conecte-se a uma conta de armazenamento no Azure usando os pontos de extremidade padrão para os serviços de armazenamento.

- Conecte-se a uma conta de armazenamento no Azure usando pontos de extremidade explícitos para os serviços de armazenamento.

Se o aplicativo for um serviço de nuvem em execução no Azure, você geralmente salvará a cadeia de conexão no [arquivo de esquema de configuração de serviço do Azure (.cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Se seu aplicativo é executado em outro ambiente (por exemplo, na área de trabalho), você normalmente salvará a cadeia de conexão em um arquivo app.config ou em outro arquivo de configuração. Você pode usar a classe  `CloudConfigurationManager` do Azure para acessar a cadeia de conexão em tempo de execução, independentemente de onde ela está sendo executada.

## Criar uma cadeia de conexão para o emulador de armazenamento

O emulador de armazenamento é uma conta local com um nome conhecido e uma chave. Como o nome da conta e a chave são os mesmos para todos os usuários, você pode usar um formato de cadeia de caracteres de atalho para se referir ao emulador de armazenamento dentro de uma cadeia de conexão. Defina o valor da cadeia de conexão para `UseDevelopmentStorage=true`.

Você também pode especificar um proxy HTTP para usar quando estiver testando seu serviço no emulador de armazenamento. Isso pode ser útil para observar solicitações e respostas HTTP enquanto você estiver depurando operações nos serviços de armazenamento. Para especificar um proxy, adicione a opção `DevelopmentStorageProxyUri` à cadeia de conexão e defina seu valor para o URI de proxy. Por exemplo, aqui está uma cadeia de conexão que aponta para o emulador de armazenamento e configura um proxy HTTP:

    UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

## Criar uma cadeia de conexão para uma conta de armazenamento do Azure

Para criar uma cadeia de conexão para sua conta de armazenamento do Azure, use o formato de cadeia de caracteres de conexão abaixo. Indique se você deseja se conectar à conta de armazenamento por meio de HTTP ou HTTPS (recomendado), substitua `myAccountName` pelo nome da sua conta de armazenamento, substitua `myAccountKey` pela sua chave de acesso da conta:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Por exemplo, a cadeia de conexão será semelhante à seguinte cadeia de conexão de amostra:

```        DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtB7wYQw33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIAy5l/Yhg==
```

> [AZURE.NOTE] O Armazenamento do Azure dá suporte a HTTP e HTTPS em uma cadeia de conexão; no entanto, usar HTTPS é altamente recomendável.
    
## Criando uma cadeia de conexão para um ponto de extremidade explícito do Armazenamento

Você pode para especificar explicitamente os pontos de extremidade do serviço em sua cadeia de conexão se:

- Você tiver registrado um nome de domínio personalizado para sua conta de armazenamento com o serviço Blob.
- Você tem uma assinatura de acesso compartilhado para acessar recursos de armazenamento em uma conta de armazenamento.

Para criar uma cadeia de conexão que especifique um pontos de extremidade de Blob explícito, especifique o ponto de extremidade de serviço completo para cada serviço, incluindo a especificação do protocolo (HTTP ou HTTPS) usando o seguinte formato:

``` 
BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;FileEndpoint=myFileEndpoint;[credentials]
```

Você deve especificar pelo menos um ponto de extremidade de serviço, mas não é necessário especificar todos os três. Por exemplo, se você estiver criando uma cadeia de conexão para uso com um ponto de extremidade de blob personalizado, especificar os pontos de extremidade de fila e tabela é opcional. Observe que se você optar por omitir os pontos de extremidade de fila e tabela da cadeia de conexão, então você não poderá acessar os serviços de fila e tabela no seu código usando essa cadeia de conexão.

Quando você especifica explicitamente pontos de extremidade de serviço na cadeia de conexão, há duas opções para especificar `credentials` na sequência de caracteres acima:

- Você pode especificar o nome da conta e a chave: `AccountName=myAccountName;AccountKey=myAccountKey` 
- Você pode especificar uma assinatura de acesso compartilhado: `SharedAccessSignature=base64Signature`

### Especificando um ponto de extremidade de blob com um nome de domínio personalizado 

Se você registrou o nome de domínio personalizado para uso com o serviço Blob, convém configurar explicitamente o ponto de extremidade de blob em sua cadeia de conexão. O valor de ponto de extremidade que está listado na cadeia de conexão é usado para construir os URIs de solicitação ao serviço Blob e ele determina a forma que os URIs são retornados ao seu código. 

Por exemplo, uma cadeia de conexão para um ponto de extremidade de Blob em um domínio personalizado pode ser semelhante a:

```
DefaultEndpointsProtocol=https;BlobEndpoint=www.mydomain.com;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtB7wYQw33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIAy5l/Yhg== 
```

### Especificando um ponto de extremidade de Blob com uma assinatura de acesso compartilhado 

Você pode criar uma cadeia de conexão com pontos de extremidade explícitos para acessar recursos de armazenamento por meio de uma assinatura de acesso compartilhado. Nesse caso, você pode especificar a assinatura de acesso compartilhado como parte da cadeia de conexão, em vez do nome da conta e credenciais de chave. O token de assinatura de acesso compartilhado encapsula informações sobre o recurso a ser acessado, o período de tempo em que está disponível e as permissões concedidas. Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [Delegando acesso com uma assinatura de acesso compartilhado](https://msdn.microsoft.com/library/ee395415.aspx).

Para criar uma cadeia de conexão que inclui uma assinatura de acesso compartilhado, especifique a cadeia de caracteres no seguinte formato:

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

O ponto de extremidade pode ser um ponto de extremidade de serviço padrão ou um ponto de extremidade personalizado. O `base64Signature` corresponde à parte de assinatura de uma assinatura de acesso compartilhado. A assinatura é um HMAC computado em uma cadeia de caracteres para assinatura válida e uma chave usando o algoritmo SHA256, que é codificado na Base64.

### Criando uma cadeia de conexão com um sufixo de ponto de extremidade

Para criar uma cadeia de conexão para o serviço de armazenamento em regiões ou instâncias com sufixos de ponto de extremidade diferente, como para o Azure China ou governança do Azure, use o seguinte formato de cadeia de caracteres de conexão. Indique se deseja conectar-se à conta de armazenamento por meio de HTTP ou HTTPS, substitua `myAccountName` pelo nome da conta de armazenamento, substitua `myAccountKey` pela sua chave de acesso da conta e substitua `mySuffix` pelo sufixo do URI:


	DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=mySuffix;


Por exemplo, a cadeia de conexão deve ser semelhante ao seguinte exemplo de cadeia de conexão:

	DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtR7wYQk33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIHy5l/Yhg==;EndpointSuffix=core.chinacloudapi.cn;


<!--HONumber=52-->