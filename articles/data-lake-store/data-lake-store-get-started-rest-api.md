<properties 
   pageTitle="Introdução ao Repositório Data Lake usando APIs REST | Microsoft Azure" 
   description="Usar as APIs REST WebHDFS para executar operações no Repositório Data Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/08/2016"
   ms.author="nitinme"/>

# Introdução ao Repositório Azure Data Lake usando APIs REST

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [SDK .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI do Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Neste artigo, você aprenderá a usar as APIs REST WebHDFS e as APIs REST do Repositório Data Lake para executar o gerenciamento de contas e as operações de sistema de arquivos no Repositório Azure Data Lake. O Repositório Azure Data Lake expõe suas próprias APIs REST para operações de gerenciamento de conta. No entanto, como o Repositório Data Lake é compatível com o ecossistema HDFS e Hadoop, ele oferece suporte usando as APIs REST WebHDFS para operações do sistema de arquivos.

>[AZURE.NOTE] Para obter informações detalhadas sobre o suporte à API REST para o Repositório Data Lake, consulte [Referência da API REST do Repositório Azure Data Lake](https://msdn.microsoft.com/library/mt693424.aspx).

## Pré-requisitos

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite sua assinatura do Azure** para a visualização pública do Repositório Data Lake. Veja [instruções](data-lake-store-get-started-portal.md#signup).
- **Criar um aplicativo do Azure Active Directory**. Veja [Criar o aplicativo do Active Directory e a entidade de serviço usando o portal](../resource-group-create-service-principal-portal.md). Depois de criar o aplicativo, recupere os seguintes valores relacionados ao aplicativo.
	- Obtenha a ID do cliente e a ID do locatário para o aplicativo.
	- Criar uma chave de autenticação
	- Definir permissões delegadas

	Há instruções sobre como recuperar esses valores disponíveis no link fornecido acima.
- **Atribuir o aplicativo do Azure Active Directory a uma função**. A função pode estar no nível do escopo no qual você deseja conceder permissão ao aplicativo do Azure Active Directory. Por exemplo, você pode atribuir o aplicativo no nível da assinatura ou no nível de um grupo de recursos. Para obter instruções, veja [Atribuir um aplicativo a uma função](../resource-group-create-service-principal-portal.md#assign-application-to-role).
- [cURL](http://curl.haxx.se/). Este artigo usa cURL para demonstrar como fazer chamadas à API REST em uma conta do Repositório Data Lake.

## Como faço para me autenticar usando o Azure Active Directory?

Você pode usar duas abordagens para se autenticar usando o Azure Active Directory.

* **Interativa**, onde o aplicativo solicita o logon do usuário. Para saber mais, veja [Fluxo de concessão de código de autorização](https://msdn.microsoft.com/library/azure/dn645542.aspx).

* **Não interativa**, onde o aplicativo fornece suas próprias credenciais. Para saber mais, veja [Chamadas de serviço para serviço usando credenciais](https://msdn.microsoft.com/library/azure/dn645543.aspx).

Este artigo usa uma abordagem **não interativa**. Para isso, você deve emitir uma solicitação POST como a mostrada abaixo.

	curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

A saída dessa solicitação incluirá um token de autorização (indicado por `access-token` na saída abaixo) que você passará subsequentemente com as chamadas da API REST. Salve esse token de autenticação em um arquivo de texto. Você precisará dele posteriormente neste artigo.

	{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

## Criar uma conta do Repositório Data Lake

Essa operação se baseia na chamada à API REST definida [aqui](https://msdn.microsoft.com/library/mt694078.aspx).

Use o comando cURL a seguir. Substitua **<nomedoseurepositório>** pelo nome do Repositório Data Lake.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@C:\temp\input.json

No comando acima, substitua <`REDACTED`> pelo token de autorização que você recuperou anteriormente. A carga de solicitação para esse comando está contida no arquivo **input.json** fornecido para o parâmetro `-d` acima. O conteúdo do arquivo input.json lembra o seguinte:

	{
	"location": "eastus2",
	"tags": {
		"department": "finance"
		},
	"properties": {}
	}	

## Criar pastas na conta do Repositório Data Lake

Essa operação se baseia na chamada à API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Use o comando cURL a seguir. Substitua **<nomedoseurepositório>** pelo nome do Repositório Data Lake.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

No comando acima, substitua <`REDACTED`> pelo token de autorização que você recuperou anteriormente. Esse comando cria um diretório chamado **mytempdir** na pasta raiz da conta do Repositório Data Lake.

Você deverá ver uma resposta como esta caso a operação seja concluída com êxito:

	{"boolean":true}

## Listar pastas em uma conta do Repositório Data Lake

Essa operação se baseia na chamada à API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Use o comando cURL a seguir. Substitua **<nomedoseurepositório>** pelo nome do Repositório Data Lake.

	curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

No comando acima, substitua <`REDACTED`> pelo token de autorização que você recuperou anteriormente.

Você deverá ver uma resposta como esta caso a operação seja concluída com êxito:

	{
	"FileStatuses": {
		"FileStatus": [{
			"length": 0,
			"pathSuffix": "mytempdir",
			"type": "DIRECTORY",
			"blockSize": 268435456,
			"accessTime": 1458324719512,
			"modificationTime": 1458324719512,
			"replication": 0,
			"permission": "777",
			"owner": "NotSupportYet",
			"group": "NotSupportYet"
		}]
	}
	}

## Carregar dados na conta do Repositório Data Lake

Essa operação se baseia na chamada à API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

O carregamento de dados usando a API REST WebHDFS é um processo de duas etapas, como explicado abaixo.

1. Envie uma solicitação HTTP PUT sem enviar os dados do arquivo a ser carregado. No comando a seguir, substitua **<nomedoseurepositório>** pelo nome do Repositório Data Lake.

		curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

	A saída desse comando conterá uma URL de redirecionamento temporário, como a mostrada abaixo.

		HTTP/1.1 100 Continue

		HTTP/1.1 307 Temporary Redirect
		...
		...
		Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
		...
		...

2. Agora você deve enviar outra solicitação HTTP PUT em relação à URL listada para a propriedade **Local** na resposta. Substitua **<nomedoseurepositório>** pelo nome do Repositório Data Lake.

		curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

	A saída será semelhante ao seguinte:

		HTTP/1.1 100 Continue

		HTTP/1.1 201 Created
		...
		...

## Ler dados de uma conta do Repositório Data Lake

Essa operação se baseia na chamada à API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

A leitura de dados de uma conta do Repositório Data Lake é um processo de duas etapas.

* Primeiro você envia uma solicitação GET para o ponto de extremidade `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Isso retornará um local para o qual será enviada a próxima solicitação GET.
* Então, você enviará a solicitação GET para o ponto de extremidade `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Isso exibirá o conteúdo do arquivo.

No entanto, como não há qualquer diferença nos parâmetros de entrada entre a primeira e a segunda etapa, você poderá usar o parâmetro `-L` para enviar a primeira solicitação. A opção `-L` essencialmente combina duas solicitações em uma e fará com que a cURL refaça a solicitação no novo local. Por fim, a saída de todas as chamadas de solicitação é exibida, conforme mostrado abaixo. Substitua **<nomedoseurepositório>** pelo nome do Repositório Data Lake.

	curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Você deverá ver um resultado semelhante ao seguinte:

	HTTP/1.1 307 Temporary Redirect
	...
	Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
	...
	
	HTTP/1.1 200 OK
	...
	
	Hello, Data Lake Store user!

## Renomear um arquivo em uma conta do Repositório Data Lake

Essa operação se baseia na chamada à API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Use o comando cURL a seguir para renomear um arquivo. Substitua **<nomedoseurepositório>** pelo nome do Repositório Data Lake.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Você deverá ver um resultado semelhante ao seguinte:

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Excluir um arquivo de uma conta do Repositório Data Lake

Essa operação se baseia na chamada à API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Use o comando cURL a seguir para excluir um arquivo. Substitua **<nomedoseurepositório>** pelo nome do Repositório Data Lake.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Você verá algo semelhante ao mostrado a seguir:

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Excluir uma conta do Repositório do Data Lake

Essa operação se baseia na chamada à API REST definida [aqui](https://msdn.microsoft.com/library/mt694075.aspx).

Use o comando cURL a seguir para excluir uma conta do Repositório Data Lake. Substitua **<nomedoseurepositório>** pelo nome do Repositório Data Lake.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Você verá algo semelhante ao mostrado a seguir:

	HTTP/1.1 200 OK
	...
	...

## Consulte também

- [Aplicativos de Big Data de software livre compatíveis com o Repositório Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)
 

<!---HONumber=AcomDC_0413_2016-->