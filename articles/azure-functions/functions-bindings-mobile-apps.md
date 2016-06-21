<properties
	pageTitle="Associações de Aplicativos Móveis do Azure Functions | Microsoft Azure"
	description="Entenda como usar associações dos Aplicativos Móveis do Azure no Azure Functions."
	services="functions"
	documentationCenter="na"
	authors="ggailey777"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="06/02/2016"
	ms.author="glenga"/>

# Associações de Aplicativos Móveis do Azure Functions

Este artigo explica como configurar e codificar associações dos Aplicativos Móveis do Azure no Azure Functions.

[AZURE.INCLUDE [introdução](../../includes/functions-bindings-intro.md)]

Os Aplicativos Móveis do Serviço de Aplicativo do Azure permitem que você exponha os dados de ponto de extremidade de tabela para clientes móveis. Esses mesmos dados tabulares podem ser usados em associações de entrada e de saída com o Azure Functions. Como ele dá suporte ao esquema dinâmico, um aplicativo móvel de back-end Node.js é ideal para expor dados tabulares para serem usados com suas funções. O esquema dinâmico está habilitado por padrão e deve ser desabilitado em um aplicativo móvel de produção. Para saber mais sobre pontos de extremidade de tabela em um back-end Node.js, consulte [Visão geral: operações de tabela](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations). Nos Aplicativos Móveis, o back-end do Node.js dá suporte à navegação e edição de tabelas no portal. Para saber mais, veja [edição no portal](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#in-portal-editing) no tópico SDK do Node.js. Ao usar um aplicativo móvel de back-end do .NET com o Azure Functions, você deverá atualizar manualmente o modelo de dados conforme exigido pela sua função. Para saber mais sobre os pontos de extremidade de tabela em um aplicativo móvel de back-end do .NET, veja [Como definir um controlador de tabela](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller) no tópico do SDK de back-end do .NET.

## Criar uma variável de ambiente para a URL de back-end do aplicativo móvel

As associações de aplicativos móveis atualmente exigem que você crie uma variável de ambiente que retorne a URL do back-end do aplicativo móvel em si. Essa URL pode ser encontrada no [Portal do Azure](https://portal.azure.com) localizando seu aplicativo móvel e abrindo a folha.

![Folha de Aplicativos Móveis no Portal do Azure](./media/functions-bindings-mobile-apps/mobile-app-blade.png)

Para definir essa URL como uma variável de ambiente em seu aplicativo de funções:

1. No seu aplicativo de funções no [Portal do Azure Functions](https://functions.azure.com/signin), clique em **Configurações do aplicativo de funções** > **Ir para configurações do Serviço de Aplicativo**. 

	![Folha de configurações do aplicativo de funções](./media/functions-bindings-mobile-apps/functions-app-service-settings.png)

2. Em seu aplicativo de funções, clique em **Todas as configurações**, role para baixo até **Configurações de aplicativo**, em seguida, em **Configurações de aplicativo**, digite um novo **Nome** para a variável de ambiente, cole a URL em **Valor**, certificando-se de usar o esquema HTTPS, clique em **Salvar** e feche a folha do aplicativo de funções para retornar para o portal de Funções.

	![Adicionar uma variável de ambiente de configuração de aplicativo](./media/functions-bindings-mobile-apps/functions-app-add-app-setting.png)

Agora você pode definir essa nova variável de ambiente como o campo *conexão* em suas associações.

## <a id="mobiletablesapikey"></a> Use uma chave de API para proteger o acesso aos seus pontos de extremidade de tabelas dos Aplicativos Móveis.

No Azure Functions, as associações de tabelas móveis permitem especificar uma chave de API, que é um segredo compartilhado que pode ser usado para impedir o acesso indesejado de aplicativos que não fazem parte das suas funções. Os Aplicativos Móveis não possuem suporte interno para a autenticação de chave de API. No entanto, você pode implementar uma chave de API em seu aplicativo móvel de back-end do Node.js seguindo os exemplos em [Azure App Service Mobile Apps backend implementing an API key](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) (Back-end de Aplicativos Móveis do Serviço de Aplicativo do Azure implementando uma chave de API). Da mesma forma, você pode implementar uma chave de API em um [aplicativo móvel de back-end .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key).

>[AZURE.IMPORTANT] Essa chave de API não deve ser distribuída com seus clientes de aplicativo móvel, só deve ser distribuída com segurança aos clientes no lado do serviço, como o Azure Functions.

## <a id="mobiletablesinput"></a> Associação de entrada dos Aplicativos Móveis do Azure

As associações de entrada podem carregar um registro de um ponto de extremidade de tabela móvel e passá-lo diretamente para sua associação. A ID do registro é determinada com base no gatilho que invocou a função. Em uma função do C#, quaisquer alterações feitas no registro são enviadas automaticamente para a tabela quando a função sai com êxito.

#### function.json para associação de entrada dos Aplicativos Móveis

O arquivo *function.json* dá suporte às seguintes propriedades:

- `name`: nome da variável usada no código de função para o novo registro.
- `type`: o tipo de associação deve ser definido como *mobileTable*.
- `tableName`: a tabela na qual o novo registro será criado.
- `id`: a ID do registro a ser recuperado. Essa propriedade dá suporte a associações semelhantes a `{queueTrigger}`, que usarão o valor da cadeia de caracteres da mensagem de fila como a ID do registro.
- `apiKey`: cadeia de caracteres que é a configuração de aplicativo que especifica a chave de API opcional para o aplicativo móvel. Isso será necessário quando seu aplicativo móvel usar uma chave de API para restringir o acesso de cliente.
- `connection`: cadeia de caracteres que é o nome da variável de ambiente nas configurações do aplicativo que especifica a URL do seu back-end do aplicativo móvel.
- `direction`: direção da associação, que deve ser definida como *in*.

Arquivo *function.json* de exemplo:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "id" : "{queueTrigger}",
	      "connection": "My_MobileApp_Url",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Exemplo de código dos Aplicativos Móveis do Azure para um gatilho de fila do C#

Com base no function.json de exemplo acima, a associação de entrada recupera o registro do ponto de extremidade de tabela dos Aplicativos Móveis com a ID que corresponde à cadeia de caracteres de mensagem de fila e a passa para o parâmetro *record*. Quando o registro não for encontrado, o parâmetro será nulo. O registro será então atualizado com o novo valor de *Text* quando a função sair.

	#r "Newtonsoft.Json"	
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, JObject record)
	{
	    if (record != null)
	    {
	        record["Text"] = "This has changed.";
	    }    
	}

#### Exemplo de código dos Aplicativos Móveis do Azure para um gatilho de fila do Node.js

Com base no function.json de exemplo acima, a associação de entrada recupera o registro do ponto de extremidade de tabela dos Aplicativos Móveis com a ID que corresponde à cadeia de caracteres de mensagem de fila e a passa para o parâmetro *record*. Em funções do Node.js, os registros atualizados não são enviados de volta à tabela. Este exemplo de código grava o registro recuperado no log.

	module.exports = function (context, input) {    
	    context.log(context.bindings.record);
	    context.done();
	};


## <a id="mobiletablesoutput"></a>Associação de saída dos Aplicativos Móveis do Azure

Sua função pode gravar um registro para o ponto de extremidade de tabela dos Aplicativos Móveis usando uma associação de saída.

#### function.json para associação de saída dos Aplicativos Móveis

O arquivo function.json dá suporte às seguintes propriedades:

- `name`: nome da variável usada no código de função para o novo registro.
- `type`: o tipo de associação que deve ser definido como *mobileTable*.
- `tableName`: a tabela na qual o novo registro é criado.
- `apiKey`: cadeia de caracteres que é a configuração de aplicativo que especifica a chave de API opcional para o aplicativo móvel. Isso será necessário quando seu aplicativo móvel usar uma chave de API para restringir o acesso de cliente.
- `connection`: cadeia de caracteres que é o nome da variável de ambiente nas configurações do aplicativo que especifica a URL do seu back-end do aplicativo móvel.
- `direction`: direção da associação, que deve ser definida como *out*.

function.json de exemplo:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "connection": "My_MobileApp_Url",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

#### Exemplo de código dos Aplicativos Móveis do Azure para um gatilho de fila do C#

Este exemplo de código C# insere um novo registro em um ponto de extremidade de tabela dos Aplicativos Móveis com uma propriedade *Text* na tabela especificada na associação acima.

	public static void Run(string myQueueItem, out object record)
	{
	    record = new {
	        Text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}

#### Exemplo de código dos Aplicativos Móveis do Azure para um gatilho de fila do Node.js

Este exemplo de código Node.js insere um novo registro em um ponto de extremidade de tabela dos Aplicativos Móveis com uma propriedade *Text* na tabela especificada na associação acima.

	module.exports = function (context, input) {
	
	    context.bindings.record = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	
	    context.done();
	};

## Próximas etapas

[AZURE.INCLUDE [próximas etapas](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0608_2016-->