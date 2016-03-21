<properties
	pageTitle="Desenvolver a API para o PowerApps Enterprise | Microsoft Azure"
	description="Desenvolver ou compilar APIs personalizadas para o PowerApps"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="rajram"/>

# Desenvolva uma API para o PowerApps

Você pode criar ou desenvolver sua própria API que pode ser usada no PowerApps. As etapas são as seguintes:

- Compile e implante sua API
- Definição de autor da API do [Swagger 2.0](http://swagger.io/) para sua API

Este tópico lista estas etapas.

## Etapa 1: compilar e implantar sua API

Criar e implantar uma API para o PowerApps é como criar qualquer API. Você pode escolher sua linguagem de programação e estrutura favoritas. Você também pode optar por hospedar sua API em qualquer lugar. Recomendamos que você a hospede no ambiente de serviço de aplicativo do PowerApps como [aplicativo de API](https://azure.microsoft.com/services/app-service/api/).

Os artigos a seguir mostram como compilar e implantar uma API .Net, Java ou Node.js no ambiente de serviço de aplicativo:

- [Compile e implante um .NET no serviço de aplicativo do Azure](../app-service-api/app-service-api-dotnet-get-started.md)
- [Criar e implantar um aplicativo de API Java no Serviço de Aplicativo do Azure](../app-service-api/app-service-api-java-api-app.md)
- [Criar e implantar um aplicativo de API do Node.js no serviço de aplicativo do Azure](../app-service-api/app-service-api-nodejs-api-app.md)


## Etapa 2: definição de autor da API do Swagger 2.0 para sua API

Se você seguir um dos artigos referenciados na *Etapa 1*, uma definição da API Swagger 2.0 padrão é gerada automaticamente para sua API. Para otimizá-la para o PowerApps, você pode, como alternativa, personalizar a definição da API Swagger 2.0 gerada usando as seguintes extensões de esquema.

Para saber como personalizar a definição da API Swagger 2.0 em geral, consulte [Personalizar definições de API gerada por Swashbuckle](../app-service-api/app-service-api-dotnet-swashbuckle-customize.md).

### Extensões de esquema
Além do swagger gerado automaticamente pelo Swashbuckle, há algumas extensões swagger extras disponíveis durante a criação de uma API para PowerApps. Esta seção relaciona e descreve essas extensões.

##### x-ms-summary
Uma sequência de caracteres que descreve os nomes de exibição para entidades que não têm um campo de resumo definido nas especificações de Swagger. Os **Nomes de parâmetro** são um exemplo.

##### x-ms-visibility
Esse valor descreve se a entidade é exibida no designer de fluxo de lógica. Os valores a seguir estão disponíveis:

- “none” (padrão)
- “avançado”
- “interno” - o designer de fluxo de lógica não mostra essas operações

Se uma operação for marcada como “importante”, é esperado que o cliente Fluxo de lógica destaque essas operações.

##### x-ms-trigger
Define se essa operação pode ser usada como um disparador no fluxo de lógica. As opções incluem:
	
- nenhum (padrão): a operação não pode ser usada como um disparador.
- único: esta operação também pode ser usada como um disparador.
- em lote: esta operação pode ser usada como um disparador. Além disso, essa operação responde com uma “matriz” de objetos JSON, e o fluxo de lógica aciona um disparador para cada item na matriz.


##### x-ms-dynamic-values
Esta é uma dica para o designer Fluxo de lógica que a API fornece uma lista de valores dinamicamente permitidos para esse parâmetro. O designer Fluxo de lógica pode invocar uma operação conforme definido pelo valor desse campo e extrair os valores possíveis do resultado. O designer Fluxo de lógica pode exibir esses valores como opções ao usuário final.

O valor é um objeto que contém as seguintes propriedades:
	
- operationId: uma cadeia de caracteres que corresponde à operationId para a operação é invocada
- parâmetros: um objeto cujas propriedades definem os parâmetros necessários para a operação
- coleção de valores: uma cadeia de caracteres de caminho que é avaliada como uma matriz de objetos na carga de resposta
- caminho de valor: uma cadeia de caracteres do caminho do objeto dentro de “coleta de valor” que se refere ao valor do parâmetro.
- título do valor: uma cadeia de caracteres no objeto dentro de “coleta de valores” que se refere a uma descrição para o valor.


Exemplo:

```javascript
"/api/tables/{table}/items": {
  "post": {
    "operationId": "TableData_CreateItem",
    "summary": "Create an object in {Salesforce}",
    "parameters": [
      {
        "name": "table",
        "x-ms-summary": "Object Type",
        "x-ms-dynamic-values": {
          "operationId": "TableMetadata_ListTables",      // operation that needs to be invoked
          "parameters": { },                              // parameters for the above operation, if any
          "value-collection": "values",                   // field that contains the collection
          "value-path": "Name",                           // field that contains the value
          "value-title": "DisplayName"                    // field that contains a display name for the value
      }
      // ...
    ]
    // ...
  }
  // ...
}
```

No exemplo acima, o swagger define uma operação chamada _TableData\_CreateItem_ que cria um novo objeto no Salesforce.

O Salesforce tem muitos objetos internos. O objeto _x-ms-dynamic-values_ é usado aqui para ajudar o designer a descobrir a lista de objetos internos do Salesforce. Ela o obtém ao chamar _TableMetadata\_ListTables_.

##### x-ms-dynamic-schema
Esta é uma dica para o designer Fluxo de lógica que o esquema para este parâmetro (ou resposta) é dinâmico por natureza. Ele pode invocar uma operação conforme definido pelo valor desse campo e descobrir o esquema dinamicamente. Ele pode exibir uma interface do usuário apropriada para levar as entradas do usuário ou exibir os campos disponíveis.

Exemplo:

```javascript
{
  "name": "item",
  "in": "body",
  "required": true,
  "x-ms-dynamic-schema": {
    "operationId": "Metadata_GetTableSchema",
    "parameters": {
      "tablename": "{table}"              // the value that the user has selected from the above parameter
    },
    "value-path": "Schema"                // the field that contains the JSON schema
  }
},
```

Isso é útil em cenários onde as entradas para uma operação são dinâmicas. Por exemplo, considere o caso do SQL. O esquema de cada tabela é diferente. Então, quando um usuário seleciona uma tabela específica, o designer Fluxo de lógica precisa entender a estrutura da tabela para que ele possa exibir os nomes de coluna. Nesse contexto, se a definição de swagger tiver _x-ms-dynamic-schema_, ela convoca a operação correspondente para produzir efeito sobre o esquema.

<!---HONumber=AcomDC_0309_2016-->