<properties 
	pageTitle="Configurar uma Cadeia de Conexão para o Armazenamento do Azure | Microsoft Azure"
	description="Crie uma cadeia de conexão para uma conta de armazenamento do Azure. Uma cadeia de conexão inclui as informações necessárias para autenticar o acesso a recursos em uma conta de armazenamento de seu aplicativo."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/01/2016"
	ms.author="tamram"/>

# Configurar cadeias de conexão do Armazenamento do Azure

## Visão geral

Uma cadeia de conexão inclui as informações necessárias para acessar programaticamente os recursos do Armazenamento do Azure. Seu aplicativo usa a cadeia de conexão para fornecer as informações necessárias para autenticar o acesso ao Armazenamento do Azure.

Você pode configurar uma cadeia de conexão para:

- Conecte-se ao emulador de armazenamento do Azure quando testar localmente seu serviço ou aplicativo.
- Conecte-se a uma conta de armazenamento no Azure usando os pontos de extremidade padrão para os serviços de armazenamento ou pontos de extremidade explícitos que você definiu.
- Acessar recursos em uma conta de armazenamento por meio de uma assinatura de acesso compartilhado (SAS).

## Armazenar a sua cadeia de conexão

Seu aplicativo precisará armazenar a cadeia de conexão para autenticar o acesso ao Armazenamento do Azure durante a execução. Você tem algumas opções diferentes para armazenar a cadeia de conexão:

- Para um aplicativo em execução na área de trabalho ou em um dispositivo, você pode armazenar a cadeia de conexão em um arquivo app.config ou em outro arquivo de configuração. Se você estiver usando um arquivo App. config, adicione a cadeia de conexão na seção **AppSettings**.
- Para um aplicativo em execução em um serviço de nuvem do Azure, você pode armazenar a cadeia de conexão no [arquivo de esquema (.cscfg) de configuração de serviço do Azure](https://msdn.microsoft.com/library/ee758710.aspx). Adicionar a cadeia de conexão à seção **ConfigurationSettings** do arquivo de configuração de serviço.

Armazenar a cadeia de conexão em um arquivo de configuração facilita a atualização da cadeia de conexão para alternar entre o emulador de armazenamento e uma conta de armazenamento do Azure na nuvem. Você precisa editar a cadeia de conexão para apontar para sua conta de armazenamento.

Você pode usar a classe [Gerenciador de Configuração do Microsoft Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) para acessar a cadeia de conexão em tempo de execução, independentemente de onde ela estiver sendo executada.

## Criar uma cadeia de conexão para o emulador de armazenamento

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Consulte [Usar o Emulador de Armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md) para obter mais informações sobre o emulador de armazenamento.

## Criar uma cadeia de conexão para uma conta de armazenamento do Azure

Para criar uma cadeia de conexão para sua conta de armazenamento do Azure, use o formato de cadeia de caracteres de conexão abaixo. Indique se você deseja se conectar à conta de armazenamento por meio de HTTP ou HTTPS (recomendado), substitua `myAccountName` pelo nome da sua conta de armazenamento e substitua `myAccountKey` pela chave de acesso da sua conta:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Por exemplo, a cadeia de conexão será semelhante à seguinte cadeia de conexão de amostra:

	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>

> [AZURE.NOTE] O Armazenamento do Azure dá suporte a HTTP e HTTPS em uma cadeia de conexão; no entanto, usar HTTPS é altamente recomendável.

## Criando uma cadeia de conexão para um ponto de extremidade explícito do armazenamento

Você pode para especificar explicitamente os pontos de extremidade do serviço em sua cadeia de conexão se:

- Você tiver registrado um nome de domínio personalizado para sua conta de armazenamento com o serviço Blob.
- Você tem uma assinatura de acesso compartilhado para acessar recursos de armazenamento em uma conta de armazenamento.

Para criar uma cadeia de conexão que especifique um pontos de extremidade de Blob explícito, especifique o ponto de extremidade de serviço completo para cada serviço, incluindo a especificação do protocolo (HTTP ou HTTPS) usando o seguinte formato:

	BlobEndpoint=myBlobEndpoint;
	QueueEndpoint=myQueueEndpoint;
	TableEndpoint=myTableEndpoint;
	FileEndpoint=myFileEndpoint;
	[credentials]


Você deve especificar pelo menos um ponto de extremidade de serviço, mas não é necessário especificar todos os três. Por exemplo, se você estiver criando uma cadeia de conexão para uso com um ponto de extremidade de blob personalizado, especificar os pontos de extremidade de fila e tabela é opcional. Observe que se você optar por omitir os pontos de extremidade de fila e tabela da cadeia de conexão, então você não poderá acessar os serviços de fila e tabela no seu código usando essa cadeia de conexão.

Quando você especifica explicitamente pontos de extremidade de serviço na cadeia de conexão, há duas opções para especificar `credentials` na cadeia de caracteres acima:

- Você pode especificar o nome da conta e a chave: `AccountName=myAccountName;AccountKey=myAccountKey`
- Você pode especificar uma assinatura de acesso compartilhado: `SharedAccessSignature=base64Signature`

### Especificando um ponto de extremidade de blob com um nome de domínio personalizado

Se você registrou o nome de domínio personalizado para uso com o serviço Blob, convém configurar explicitamente o ponto de extremidade de blob em sua cadeia de conexão. O valor de ponto de extremidade que está listado na cadeia de conexão é usado para construir os URIs de solicitação ao serviço Blob e ele determina a forma que os URIs são retornados ao seu código.

Por exemplo, uma cadeia de conexão para um ponto de extremidade de Blob em um domínio personalizado pode ser semelhante a:

	DefaultEndpointsProtocol=https;
	BlobEndpoint=www.mydomain.com;
	AccountName=storagesample;
	AccountKey=<account-key>


### Especificando um ponto de extremidade de Blob com uma assinatura de acesso compartilhado

Você pode criar uma cadeia de conexão com pontos de extremidade explícitos para acessar recursos de armazenamento por meio de uma assinatura de acesso compartilhado. Nesse caso, você pode especificar a assinatura de acesso compartilhado como parte da cadeia de conexão, em vez do nome da conta e credenciais de chave. O token de assinatura de acesso compartilhado encapsula informações sobre o recurso a ser acessado, o período de tempo em que está disponível e as permissões concedidas. Para obter mais informações sobre assinatura de acesso compartilhado, Confira [Assinaturas de Acesso Compartilhado: entendendo o modelo SAS](storage-dotnet-shared-access-signature-part-1.md).

Para criar uma cadeia de conexão que inclui uma assinatura de acesso compartilhado, especifique a cadeia de caracteres no seguinte formato:

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=sasToken

O ponto de extremidade pode ser um ponto de extremidade de serviço padrão ou um ponto de extremidade personalizado. O `sasToken` é a cadeia de consulta que segue o ponto de interrogação (?) na URL do SAS.

### Criando uma cadeia de conexão com um sufixo de ponto de extremidade

Para criar uma cadeia de conexão para o serviço de armazenamento em regiões ou instâncias com sufixos de ponto de extremidade diferente, como para o Azure China ou governança do Azure, use o seguinte formato de cadeia de caracteres de conexão. Indique se deseja se conectar à conta de armazenamento por meio de HTTP ou HTTPS, substitua `myAccountName` pelo nome da sua conta de armazenamento, substitua `myAccountKey` pela chave de acesso da sua conta e substitua `mySuffix` pelo sufixo do URI:


	DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=mySuffix;


Por exemplo, a cadeia de conexão deve ser semelhante ao seguinte exemplo de cadeia de conexão:

	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>;
	EndpointSuffix=core.chinacloudapi.cn;

## Próximas etapas

- [Usar o Emulador de Armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md)
- [Pesquisadores de armazenamento do Azure](storage-explorers.md)

<!---HONumber=AcomDC_0406_2016-->