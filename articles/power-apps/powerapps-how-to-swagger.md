<properties
	pageTitle="Como personalizar a definição do Swagger para o PowerApps e Fluxos Lógicos | Microsoft Azure"
	description="Exibir as extensões de esquema necessárias para o Swagger funcionar com o PowerApps e Fluxos Lógicos"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/12/2016"
   ms.author="mandia"/>


# Personalizar a definição do Swagger para o PowerApps e Fluxos Lógicos

>[AZURE.IMPORTANT] Este tópico foi movido para powerapps.microsoft.com em [Personalizar sua definição de Swagger para o PowerApps e fluxos](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/). Vá para o PowerApps a fim de obter a versão mais recente. Esse link do Azure está sendo arquivado.


## Definição de autor da API do Swagger 2.0 para sua API

Para saber como adicionar o Swagger à sua API da Web, confira [Swashbuckle][1].

## Extensões de esquema
Além das especificações padrão do Swagger, há algumas extensões adicionais do swagger disponíveis ao criar uma API personalizada para o PowerApps e Fluxos Lógicos. Esta seção relaciona e descreve essas extensões.

##### x-ms-summary
Uma sequência de caracteres que descreve os nomes de exibição para entidades que não tenham o campo `summary` definido nas especificações do Swagger. Os **Nomes de parâmetro** são um exemplo.

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
	
- `operationId`: uma cadeia de caracteres que corresponde à operationId para a operação é invocada
- `parameters`: um objeto cujas propriedades definem os parâmetros necessários para a operação
- `value-collection`: uma cadeia de caracteres de caminho que é avaliada como uma matriz de objetos na carga de resposta
- `value-path`: uma cadeia de caracteres de caminho no objeto dentro de “coleta de valores” que se refere ao valor do parâmetro.
- `value-title`: uma cadeia de caracteres de caminho no objeto dentro de “coleta de valores” que se refere a uma descrição do valor.


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

Neste exemplo, o swagger define a operação `TableData_CreateItem` que cria um novo objeto no Salesforce.

O Salesforce tem muitos objetos internos. O objeto `x-ms-dynamic-values` é usado aqui para ajudar o designer a descobrir a lista de objetos internos do Salesforce. Ele obtém a lista chamando `TableMetadata_ListTables`.

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

Isso é útil em cenários onde as entradas para uma operação são dinâmicas. Por exemplo, considere o caso do SQL. O esquema de cada tabela é diferente. Então, quando um usuário seleciona uma tabela específica, o designer Fluxo de lógica precisa entender a estrutura da tabela para que ele possa exibir os nomes de coluna. Nesse contexto, se a definição do swagger tiver `x-ms-dynamic-schema`, ela convoca a operação correspondente para produzir efeito sobre o esquema.


<!--Reference links in article-->
[1]: https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md

<!---HONumber=AcomDC_0713_2016-->