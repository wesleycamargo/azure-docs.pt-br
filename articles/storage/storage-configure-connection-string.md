<properties 
	pageTitle="Configurar uma Cadeia de Conexão para o Armazenamento do Azure | Microsoft Azure"
	description="Criar uma cadeia de conexão para uma conta de armazenamento do Azure. Uma cadeia de conexão inclui as informações necessárias para autenticar o acesso a uma conta de armazenamento de seu aplicativo no tempo de execução."
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
	ms.date="04/12/2016"
	ms.author="tamram"/>

# Configurar cadeias de conexão do Armazenamento do Azure

## Visão geral

Uma cadeia de conexão inclui as informações necessárias de autenticação necessárias para acessar os dados em uma conta de armazenamento do Azure de seu aplicativo no tempo de execução. Você pode configurar uma cadeia de conexão para:

- Conecte-se ao emulador de armazenamento do Azure.
- Acesse uma conta de armazenamento no Azure.
- Acessar recursos especificados no Azure por uma SAS (Assinatura de Acesso Compartilhado).

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## Armazenar a sua cadeia de conexão

Seu aplicativo precisará acessar a cadeia de conexão no tempo de execução para autenticar solicitações feitas para o Armazenamento o Azure. Você tem algumas opções diferentes para armazenar a cadeia de conexão:

- Para um aplicativo em execução na área de trabalho ou em um dispositivo, você pode armazenar a cadeia de conexão em um arquivo `app.config ` ou `web.config`. Adicionar a cadeia de conexão à seção **AppSettings**.
- Para um aplicativo em execução em um serviço de nuvem do Azure, você pode armazenar a cadeia de conexão no [arquivo de esquema (.cscfg) de configuração de serviço do Azure](https://msdn.microsoft.com/library/ee758710.aspx). Adicionar a cadeia de conexão à seção **ConfigurationSettings** do arquivo de configuração de serviço.
- Você também pode usar a cadeia de conexão diretamente no seu código. Na maioria dos cenários, no entanto, recomendamos armazenar sua cadeia de configuração em um arquivo de configuração.

Armazenar a cadeia de conexão em um arquivo de configuração facilita a atualização da cadeia de conexão para alternar entre o emulador de armazenamento e uma conta de armazenamento do Azure na nuvem. Você precisa apenas editar a cadeia de conexão para apontar para seu ambiente de destino.

Você pode usar a classe [Gerenciador de Configuração do Microsoft Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) para acessar a cadeia de conexão no tempo de execução, independentemente de onde ela estiver sendo executada.

## Criar uma cadeia de conexão para o emulador de armazenamento

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Consulte [Usar o Emulador de Armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md) para obter mais informações sobre o emulador de armazenamento.

## Criar uma cadeia de conexão para uma conta de armazenamento do Azure

Para criar uma cadeia de conexão para sua conta de armazenamento do Azure, use o formato de cadeia de caracteres de conexão abaixo. Indique se você deseja se conectar à conta de armazenamento por meio de HTTPS (recomendado) ou HTTP, substitua `myAccountName` pelo nome da sua conta de armazenamento e substitua `myAccountKey` pela chave de acesso da sua conta:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Por exemplo, a cadeia de conexão será semelhante à seguinte cadeia de conexão de amostra:

	DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>

> [AZURE.NOTE] O Armazenamento do Azure dá suporte a HTTP e HTTPS em uma cadeia de conexão; no entanto, usar HTTPS é altamente recomendável.

## Criar uma cadeia de conexão usando uma assinatura de acesso compartilhado

Se você possuir uma URL de SAS (Assinatura de Acesso Compartilhado), você poderá usar a SAS em sua cadeia de conexão. Como a SAS inclui no URI as informações necessárias para autenticar a solicitação, o URI de SAS fornece o protocolo, o ponto de extremidade de serviço e as credenciais necessárias para acessar o recurso.

Para criar uma cadeia de conexão que inclui uma assinatura de acesso compartilhado, especifique a cadeia de caracteres no seguinte formato:

    BlobEndpoint=myBlobEndpoint;
	QueueEndpoint=myQueueEndpoint;
	TableEndpoint=myTableEndpoint;
	FileEndpoint=myFileEndpoint;
	SharedAccessSignature=sasToken

Cada ponto de extremidade de serviço é opcional, embora a cadeia de conexão deve conter pelo menos um.

Usar HTTPS com uma SAS é uma prática recomendada. Para obter mais informações sobre assinatura de acesso compartilhado, Confira [Assinaturas de Acesso Compartilhado: entendendo o modelo SAS](storage-dotnet-shared-access-signature-part-1.md).

>[AZURE.NOTE] Se você estiver especificando uma SAS em uma cadeia de conexão em um arquivo de configuração, precisará codificar caracteres especiais na URL.

### Exemplo de SAS de serviço

Aqui está um exemplo de uma cadeia de conexão que inclui um serviço SAS para o Armazenamento de Blobs:

	BlobEndpoint=https://storagesample.blob.core.windows.net;
	SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D

E aqui está um exemplo da mesma cadeia de conexão com a codificação de URL:

	BlobEndpoint=https://storagesample.blob.core.windows.net;
	SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D

### Exemplo de SAS de conta

Aqui está um exemplo de uma cadeia de conexão que inclui uma conta SAS para o Armazenamento de Blobs e de Arquivos: Observe que os pontos de extremidade para ambos os serviços são especificados:

	BlobEndpoint=https://storagesample.blob.core.windows.net;
	FileEndpoint=https://storagesample.file.core.windows.net;
	SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl

E aqui está um exemplo da mesma cadeia de conexão com a codificação de URL:

	BlobEndpoint=https://storagesample.blob.core.windows.net;
	FileEndpoint=https://storagesample.file.core.windows.net;
	SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl

## Criando uma cadeia de conexão para um ponto de extremidade explícito do armazenamento

Você pode especificar explicitamente os pontos de extremidade de serviço na sua cadeia de conexão em vez dos pontos de extremidade padrão. Para criar uma cadeia de conexão que especifique um ponto de extremidade explícito, especifique o ponto de extremidade de serviço completo para cada serviço, incluindo a especificação do protocolo (HTTP ou HTTPS, sendo este o recomendado) usando o seguinte formato:

	DefaultEndpointsProtocol=[http|https];
	BlobEndpoint=myBlobEndpoint;
	QueueEndpoint=myQueueEndpoint;
	TableEndpoint=myTableEndpoint;
	FileEndpoint=myFileEndpoint;
	AccountName=myAccountName;
	AccountKey=myAccountKey

Um cenário em que pode ser útil especificar um ponto de extremidade explícito é se você mapeou o ponto de extremidade do Armazenamento de Blobs para um domínio personalizado. Nesse caso, você pode especificar seu ponto de extremidade personalizado para o Armazenamento de Blobs na sua cadeia de conexão e, opcionalmente, especificar os pontos de extremidade padrão para o outro serviço se seu aplicativo usá-los.

Estes são exemplos de cadeias de conexão válidas que especificam um ponto de extremidade explícito para o serviço Blob:

	# Blob endpoint only
	DefaultEndpointsProtocol=https;
	BlobEndpoint=www.mydomain.com;
	AccountName=storagesample;
	AccountKey=account-key

	# All service endpoints
	DefaultEndpointsProtocol=https;
	BlobEndpoint=www.mydomain.com;
	FileEndpoint=myaccount.file.core.windows.net;
	QueueEndpoint=myaccount.queue.core.windows.net;
	TableEndpoint=myaccount;
	AccountName=storagesample;
	AccountKey=account-key

O valor de ponto de extremidade que está listado na cadeia de conexão é usado para construir os URIs de solicitação ao serviço Blob e ele determina a forma que os URIs são retornados ao seu código.

Observe que se você optar por omitir um ponto de extremidade de serviço da cadeia de conexão, não será possível usá-la para acessar os dados neste serviço do seu código.

### Criando uma cadeia de conexão com um sufixo de ponto de extremidade

Para criar uma cadeia de conexão para o serviço de armazenamento em regiões ou instâncias com sufixos de ponto de extremidade diferente, como para o Azure China ou governança do Azure, use o seguinte formato de cadeia de caracteres de conexão. Indique se deseja se conectar à conta de armazenamento por meio de HTTP ou HTTPS, substitua `myAccountName` pelo nome da sua conta de armazenamento, substitua `myAccountKey` pela chave de acesso da sua conta e substitua `mySuffix` pelo sufixo do URI:


	DefaultEndpointsProtocol=[http|https];
	AccountName=myAccountName;
	AccountKey=myAccountKey;
	EndpointSuffix=mySuffix;


Por exemplo, sua cadeia de conexão deve ser semelhante à seguinte cadeia de conexão:

	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>;
	EndpointSuffix=core.chinacloudapi.cn;

## Analisando uma cadeia de conexão

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]


## Próximas etapas

- [Usar o Emulador de Armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md)
- [Pesquisadores de armazenamento do Azure](storage-explorers.md)

<!---HONumber=AcomDC_0420_2016-->