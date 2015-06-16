
<properties 
	pageTitle="Personalizar os identificadores de operação gerados pelo Swashbuckle" 
	description="Aprenda a personalizar identificadores de operação do Swagger que são gerados pelo Swashbuckle para um aplicativo de API no Serviço de Aplicativo do Azure." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/16/2015" 
	ms.author="bradyg"/>

# Personalizar os identificadores de operação gerados pelo Swashbuckle 

## Visão geral

O Swashbuckle gera identificadores de operação do Swagger concatenando o nome do controlador e o nome do método. Esse padrão cria um problema quando você tem várias sobrecargas de um método: o Swashbuckle gera ids de operação duplicadas, o que é um JSON de Swagger inválido.

Por exemplo, o código de controlador a seguir faz com que o Swashbuckle gere três ids de operação Contact_Get.

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

Você pode resolver o problema manualmente fornecendo nomes exclusivos aos métodos, como no seguinte exemplo:

* Obter
* GetById
* GetPage

A alternativa é estender Swashbuckle para fazer com que ele gere automaticamente ids de operação exclusivas. Este artigo mostra como fazer isso.

## Estender o Swashbuckle 

As etapas a seguir mostram como personalizar o Swashbuckle usando o arquivo *SwaggerConfig.cs* incluído no projeto pelo modelo de projeto de Visualização de aplicativos de API do Visual Studio. Você também pode personalizar o Swashbuckle em um projeto de API da Web que você configura para implantação como um aplicativo de API.

1. Criar uma implementação de `IOperationFilter` personalizada 

	A interface `IOperationFilter` fornece um ponto de extensibilidade para usuários do Swashbuckle que desejam personalizar vários aspectos do processo de metadados do Swagger. O código a seguir demonstra um método para alterar o comportamento de geração de id de operação. O código acrescenta nomes de parâmetro ao nome de id de operação.

		using Swashbuckle.Swagger;
		using System.Web.Http.Description;
		
		namespace ContactsList
		{
		    public class MultipleOperationsWithSameVerbFilter : IOperationFilter
		    {
		        public void Apply(
		            Operation operation,
		            SchemaRegistry schemaRegistry,
		            ApiDescription apiDescription)
		        {
		            if (operation.parameters != null)
		            {
		                operation.operationId += "By";
		                foreach (var parm in operation.parameters)
		                {
		                    operation.operationId += string.Format("{0}",parm.name);
		                }
		            }
		        }
		    }
		}

2. No arquivo *App_Start\SwaggerConfig.cs*, chamar o método `OperationFilter` faz com que o Swashbuckle use a nova implementação de `IOperationFilter`.

		c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

	![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

	O arquivo *SwaggerConfig.cs* que é fornecido pelo pacote do Swashbuckle NuGet contém muitos exemplos comentados de pontos de extensibilidade. Os comentários adicionais não são mostrados aqui.

	Depois que você faz essa alteração, a implementação de `IOperationFilter` é usada e faz com que ids de operação exclusivas sejam geradas.
 
	![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

## Próximas etapas

Este artigo mostrou como personalizar o Swashbuckle para fazer com que ele gere ids de operação exclusivas. Para obter mais informações, consulte [Swashbuckle no GitHub](https://github.com/domaindrivendev/Swashbuckle).

<!---HONumber=58--> 