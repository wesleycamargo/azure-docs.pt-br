<properties
   pageTitle="Publish-WebApplicationWebSite (script do Windows PowerShell)"
   description="Saiba como publicar um projeto Web em um site do Azure. Se os recursos necessários não existirem, este script criará tais recursos em sua assinatura do Azure."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/13/2015"
   ms.author="kempb" />

# Publish-WebApplicationWebSite (script do Windows PowerShell)

##Sintaxe

Publica um projeto Web em um site do Azure. Se os recursos necessários não existirem, o script criará tais recursos em sua assinatura do Azure.

	Publish-WebApplicationWebSite
	–Configuration <configuration>
	-SubscriptionName <subscriptionName>
	-WebDeployPackage <packageName>
	-DatabaseServerPassword @{Name = "name"; Password = "password"}
	-SendHostMessagesToOutput
	-Verbose


## Configuração

O caminho para o arquivo de configuração de JSON que descreve os detalhes da implantação.

|Parâmetro|Valor padrão|
|---|---|
|Aliases|nenhum|
|Obrigatório?|verdadeiro|
|Position|nomeado|
|Valor padrão|nenhum|
|Aceitar entrada do Pipeline?|false|
|Aceitar caracteres curinga?|false|

## SubscriptionName

O nome da assinatura do Azure na qual você deseja criar o site.

|Parâmetro|Valor padrão|
|---|---|
|Aliases|nenhum|
|Obrigatório?|false|
|Position|nomeado|
|Valor padrão|nenhum|
|Aceitar entrada do Pipeline?|false|
|Aceitar caracteres curinga?|false|

## WebDeployPackage

O caminho para o pacote de implantação Web a publicar no site. Você pode criar este pacote usando o assistente Publicar Web no Visual Studio. Consulte [Como criar um pacote de implantação da Web no Visual Studio](http://go.microsoft.com/fwlink/p/?LinkID=623089).

|Parâmetro|Valor padrão|
|---|---|
|Aliases|nenhum|
|Obrigatório?|false|
|Position|nomeado|
|Valor padrão|nenhum|
|Aceitar entrada do Pipeline?|false|
|Aceitar caracteres curinga?|false|

## DatabaseServerPassword

O nome do administrador e a senha do Banco de Dados SQL no Azure.

|Parâmetro|Valor padrão|
|---|---|
|Aliases|nenhum|
|Obrigatório?|false|
|Position|nomeado|
|Valor padrão|nenhum|
|Aceitar entrada do Pipeline?|false|
|Aceitar caracteres curinga?|false|

## SendHostMessagesToOutput

Se seu valor for true, imprimir mensagens do script para o fluxo de saída.

|Parâmetro|Valor padrão|
|---|---|
|Aliases|nenhum|
|Obrigatório?|false|
|Position|nomeado|
|Valor padrão|false|
|Aceitar entrada do Pipeline?|false|
|Aceitar caracteres curinga?|false|

## Comentários

Para obter uma explicação completa de como usar o script para criar ambientes de desenvolvimento e teste, consulte [Usando scripts do Windows PowerShell para publicar para ambientes de desenvolvimento e teste](https://msdn.microsoft.com/library/azure/dn642480.aspx).

O arquivo de configuração JSON especifica os detalhes daquilo que está para ser implantado. Ele inclui as informações que você especificou quando criou o projeto, como o nome e também o nome de usuário para o site. Ele também inclui o banco de dados a provisionar, se houver. O código a seguir mostra um exemplo de arquivo de configuração JSON:

	{
	    "environmentSettings": {
	        "webSite": {
	            "name": "WebApplication10554",
	            "location": "West US"
	        },
	        "databases": [
	            {
	                "connectionStringName": "DefaultConnection",
	                "databaseName": "WebApplication10554_db",
	                "serverName": "iss00brc88",
	                "user": "sqluser2",
	                "password": "",
	                "edition": "",
	                "size": "",
	                "collation": "",
	                "location": "West US"
	            }
	        ]
	    }
	}

Você pode editar o arquivo de configuração JSON para alterar o que é implantado. Uma seção de site é obrigatória, mas a seção de banco de dados é opcional.

## Próximas etapas

[Publish-WebApplicationVM (script do Windows PowerShell)](https://msdn.microsoft.com/library/azure/dn689112.aspx)

<!---HONumber=Sept15_HO3-->