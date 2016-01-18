
<properties
	pageTitle="Aprimore seu aplicativo de API para Aplicativos Lógicos| Microsoft Azure"
	description="Como decorar seu aplicativo de API para funcionar bem com Aplicativos Lógicos"
	services="app-service\logic"
	documentationCenter=".net"
	authors="sameerch"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="sameerch"/>

# Aprimore seu aplicativo de API para Aplicativos Lógicos #

Neste artigo, você aprenderá como estabelecer a definição de API do seu aplicativo de API para que ele funcione bem com Aplicativos Lógicos. Isso vai melhorar a experiência do usuário final para o seu aplicativo API quando ele é usado no designer de Aplicativos Lógicos.

## Pré-requisitos

Se você não tem experiência com [aplicativos de API](app-service-api-apps-why-best-platform.md) no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md), é recomendável ler a série composta de várias partes em [criação de aplicativos da API](app-service-dotnet-create-api-app.md)


## Adicionar Nomes de Exibição ##
O designer de Aplicativos Lógicos exibe os nomes das operações, campos e parâmetros, os quais às vezes podem ser difíceis de ler já que são gerados por meio de programação. Para melhorar a legibilidade, o designer de Aplicativos Lógicos pode, onde ele estiver disponível, exibir um valor de texto mais legível - conhecido como um *nome de exibição* - em vez dos nomes padrão de operação, campo e parâmetro. Para fazer isso, o designer de Aplicativos Lógicos verifica a presença de certas propriedades nos metadados swagger fornecidos pelo seu aplicativo de API. As propriedades a seguir são usadas como nomes de exibição:

* Operações (Ação e Gatilhos) O valor da propriedade **summary**, se houver; caso contrário, o valor da propriedade **operationId**. Observe que a especificação Swagger 2.0 permite até 120 caracteres para a propriedade **summary**.

* Parâmetros (Entradas) O valor da propriedade de extensão **x-ms-summary**, se houver; caso contrário, o valor da propriedade **name**. A propriedade de extensão **x-ms-summary** deve ser definida dinamicamente no código. Esse processo é descrito na seção "Usando atributos personalizados para anotar as propriedades de extensão" deste tópico. A propriedade **name** pode ser definida usando comentários ///. Esse processo é descrito na seção "Usando comentários XML na geração da Definição de API" deste tópico.

* Campos de Esquema (Respostas de Saída) O valor da propriedade de extensão **x-ms-summary**, se houver; caso contrário, o valor da propriedade **name**. A propriedade de extensão **x-ms-summary** deve ser definida dinamicamente no código. Esse processo é descrito na seção "Usando atributos personalizados para anotar as propriedades de extensão" deste tópico. A propriedade **name** pode ser definida usando comentários ///. Esse processo é descrito na seção "Usando comentários XML na geração da Definição de API" deste tópico.

**Observação:** é recomendável manter seus nomes de exibição com extensão de até 30 caracteres.

### Usando comentários XML na geração da Definição de API

Para o desenvolvimento usando o Visual Studio, é uma prática comum anotar os controladores de API usando [comentários XML](https://msdn.microsoft.com/library/b2s063f7.aspx). Quando compilado com [/doc](https://msdn.microsoft.com/library/3260k4x7.aspx), o compilador criará um arquivo de documentação XML. O conjunto de ferramentas Swashbuckle incluído no SDK do aplicativo de API pode incorporar esses comentários ao gerar os metadados de API, e você pode configurar seu projeto de API para fazer isso seguindo estas etapas:

1. Abra o projeto no Visual Studio.

2. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Propriedades**.

	![Propriedades do Projeto](./media/app-service-api-optimize-for-logic-apps/project-properties.png)

3. Quando as páginas de propriedades do projeto forem exibidas, execute as seguintes etapas:

	- Selecione a **Configuração** para a qual as definições serão aplicadas. Normalmente você selecionará “Todas as configurações”, para que as definições especificadas apliquem-se tanto à compilação de Depuração quanto à compilação de Versão.
	
	- Selecione a guia **Compilar** à esquerda
	
	- Confirme que a opção **Arquivo de documentação XML** está marcada. O Visual Studio fornecerá um nome de arquivo padrão com base no nome do seu projeto. Você pode definir seu valor como o que requer que seja requerido pela convenção de nomenclatura utilizada ou deixá-lo como está.

	![Definir a propriedade de documento XML](./media/app-service-api-optimize-for-logic-apps/xml-documentation-file-property.png)

4. Abra o arquivo *SwaggerConfig.cs* (localizado na pasta **App\_Start** do projeto).

5. Adicione diretivas **using** à parte superior do arquivo *SwaggerConfig.cs* para os namespaces **System** e **System.Globalization**.

		using System;
		using System.Globalization;
 
6. Pesquise o arquivo *SwaggerConfig.cs* buscando uma chamada para **GetXmlCommentsPath**, remova a marca de comentário da linha para que ela seja executada. Já que você ainda não implementou esse método, o Visual Studio sublinhará a chamada para **GetXmlCommentsPath** e indicará que ela não está definida no contexto atual. Não há problema nisso. Você o implementará na próxima etapa.

7. Adicione a implementação a seguir do método **GetXmlCommentsPath** para a classe **SwaggerConfig** (definida no arquivo *SwaggerConfig.cs*). Esse método simplesmente retornará o arquivo de documentação XML especificado anteriormente nas configurações do projeto.

        public static string GetXmlCommentsPath()
        {
            return String.Format(CultureInfo.InvariantCulture, 
								 @"{0}\bin\ContactsList.xml", 
								 AppDomain.CurrentDomain.BaseDirectory);
        }

8. Por fim, especifique os comentários XML para os métodos de controlador. Para fazer isso, abra um dos arquivos do controlador do seu aplicativo de API e digite /// em uma linha vazia que preceda um método de controlador que você deseje documentar. O Visual Studio vai inserir automaticamente uma seção comentada dentro da qual você pode especificar um resumo do método, bem como informações de valor retornado e de parâmetro.

Agora, quando você criar e publicar seu aplicativo de API, você verá que o arquivo de documentação também faz parte da carga e é carregado com o restante do seu aplicativo de API.

## Categorizar Propriedades e Operações Avançadas

O designer de Aplicativos Lógicos limitou o espaço na tela para mostrar as operações, parâmetros e propriedades. Além disso, um aplicativo de API pode definir um amplo conjunto de operações e propriedades. O resultado de tantas informações serem exibidas em uma pequena área pode tornar o uso do designer difícil para o usuário final.

Para atenuar essa confusão, o designer de Aplicativos Lógicos permite que você agrupe operações e propriedades do aplicativo de API em categorias definidas pelo usuário. Usando uma categorização adequada das operações e propriedades, um aplicativo de API pode melhorar a experiência do usuário, apresentando as mais básicas e úteis operações e propriedades por primeiro.

Para fornecer essa capacidade, o designer de Aplicativos Lógicos procura a presença de uma propriedade de extensão de fornecedor personalizada específica na definição de API do swagger de sua API de aplicativo. Essa propriedade é denominada **x-ms-visibility** e pode ter os seguintes valores:

* vazio ou "nenhum" Essas operações e propriedades são prontamente visíveis pelo usuário.

* "avançado" Já que essas operações e propriedades são avançadas, elas ficam ocultas por padrão. No entanto, o usuário pode acessá-los facilmente se necessário.

* "internas" Essas operações e propriedades são tratadas como propriedades de sistema ou internas, e não devem ser usadas diretamente pelo usuário. Como resultado, elas são ocultas pelo designer e disponíveis apenas no modo de Exibição de Código. Para essas propriedades, você também pode especificar a propriedade de extensão **x-ms-scheduler-recommendation** para definir o valor por meio do designer de Aplicativos Lógicos. Para obter um exemplo, consulte o artigo sobre [adição de gatilhos a um aplicativo de API](app-service-api-dotnet-triggers.md).


## Usando atributos personalizados para anotar as propriedades de extensão

Conforme mencionado acima, propriedades de extensão do fornecedor personalizadas são usadas para anotar os metadados de API para fornecer informações mais detalhadas do que aquelas que o designer de Aplicativos Lógicos pode usar. Se você usar metadados estáticos para descrever seu aplicativo de API, você pode editar diretamente o */metadata/apiDefinition.swagger.json* em seu projeto para adicionar manualmente as propriedades de extensão necessárias.

Para aplicativos de API que usam metadados dinâmicos, você pode fazer uso de atributos personalizados para anotar o código. Você pode definir um filtro de operação no arquivo *SwaggerConfig.cs* para localizar os atributos personalizados e adicionar a extensão de fornecedor necessária. Essa abordagem é descrita em detalhes abaixo, para gerar dinamicamente a propriedade de extensão **x-ms-summary**.

1. Defina uma classe de atributo chamada **CustomSummaryAttribute**, que será usada para fazer anotações em seu código.

	    [AttributeUsage(AttributeTargets.All)]
	    public class CustomSummaryAttribute : Attribute
	    {
	        public string SummaryText { get; internal set; }

	        public CustomSummaryAttribute(string summaryText)
	        {
	            this.SummaryText = summaryText;
	        }
	    }

2. Defina um filtro de operação chamado **AddCustomSummaryFilter**, que vai procurar esse atributo personalizado nos parâmetros da operação.


	    using Swashbuckle.Swagger;

		...

		public class AddCustomSummaryFilter : IOperationFilter
	    {
	        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
	        {
	            if (operation.parameters == null)
	            {
	                // no parameter
	                return;
	            }

	            foreach (var param in operation.parameters)
	            {
	                var summaryAttributes = apiDescription.ParameterDescriptions.First(x => x.Name.Equals(param.name))
	                                        .ParameterDescriptor.GetCustomAttributes<CustomSummaryAttribute>();

	                if (summaryAttributes != null && summaryAttributes.Count > 0)
	                {
	                    // add x-ms-summary extension
	                    if (param.vendorExtensions == null)
	                    {
	                        param.vendorExtensions = new Dictionary<string, object>();
	                    }
	                    param.vendorExtensions.Add("x-ms-summary", summaryAttributes[0].SummaryText);
	                }
	            }
	        }
	    }

3. Editar o *SwaggerConfig.cs* arquivo e adicione a classe de filtro definida acima.


            GlobalConfiguration.Configuration
                .EnableSwagger(c =>
                    {
                        ...
                        c.OperationFilter<AddCustomSummaryFilter>();
                        ...
                    }

4. Use a classe **CustomSummaryAttribute** para anotar o código, conforme mostrado no trecho de código a seguir.

        /// <summary>
        /// Send Message
        /// </summary>
        /// <param name="queueName">The name of the Storage Queue</param>
        /// <param name="messageText">The message text to be sent</param>
        public void SendMessage(
            [CustomSummary("Queue Name")] string queueName,
            [CustomSummary("Message Text")] string messageText)
        {
             ...
        }

	Quando você compila o aplicativo API acima, isso gera os seguintes metadados de API:


			...
            "post": {
                ...
                "parameters": [
                    {
                        "name": "queueName",
                        "in": "query",
                        "description": "The name of the Storage Queue",
                        "required": true,
                        "x-ms-summary": "Queue Name",
                        "type": "string"
                    },
                    {
                        "name": "messageText",
                        "in": "query",
                        "description": "The message text to be sent",
                        "required": true,
                        "x-ms-summary": "Message Text",
                        "type": "string"
                    }
                ],
                ...

5. De modo similar, você pode definir o filtro de esquema **AddCustomSummarySchemaFilter** para anotar automaticamente a propriedade de extensão **x-ms-summary** para os modelos de esquema, como no exemplo a seguir.

	    public class AddCustomSummarySchemaFilter: ISchemaFilter
	    {
	        public void Apply(Schema schema, SchemaRegistry schemaRegistry, Type type)
	        {
	            SetCustomSummary(schema, type.GetCustomAttribute<CustomSummaryAttribute>());

	            if (schema.properties != null)
	            {
	                foreach (var property in schema.properties)
	                {
	                    var summaryAttribute = type.GetProperty(property.Key).GetCustomAttribute<CustomSummaryAttribute>();
	                    SetCustomSummary(property.Value, summaryAttribute);
	                }
	            }
	        }

	        private static void SetCustomSummary(Schema schema, CustomSummaryAttribute summaryAttribute)
	        {
	            if (summaryAttribute != null)
	            {
	                if (schema.vendorExtensions == null)
	                {
	                    schema.vendorExtensions = new Dictionary<string, object>();
	                }
	                schema.vendorExtensions.Add("x-ms-summary", summaryAttribute.SummaryText);
	            }
	        }
	    }

## Resumo

Neste artigo, você viu como aprimorar a experiência do usuário do seu aplicativo API quando ele é usado no designer de Aplicativos Lógicos. Como melhor prática, é recomendável que você forneça nomes amigáveis adequados para todas as operações (ações e gatilhos), parâmetros e propriedades. Também é recomendável que você forneça no máximo 5 operações básicas. Para parâmetros de entrada, a recomendação é restringir o número de propriedades básicas para não mais do que 4 e, para propriedades, a recomendação é de 5 ou menos. O restante das suas operações e propriedades devem ser marcadas como avançadas.
 

<!---HONumber=AcomDC_0107_2016-->