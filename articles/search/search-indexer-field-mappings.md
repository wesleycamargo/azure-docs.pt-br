---
title: Os mapeamentos de campo do indexador de Pesquisa do Azure fazem uma ponte entre as fontes de dados e os índices de pesquisa
description: Configurar mapeamentos de campo de indexador de Pesquisa do Azure para obter as diferenças nos nomes de campo e representações de dados
services: search
documentationcenter: ''
author: chaosrealm
manager: pablocas
editor: ''

ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/30/2016
ms.author: eugenesh

---
# Os mapeamentos de campo do indexador de Pesquisa do Azure fazem uma ponte entre as fontes de dados e os índices de pesquisa
Ao usar indexadores de Pesquisa do Azure, ocasionalmente você pode se encontrar em situações em que seus dados de entrada não correspondem ao esquema de seu índice de destino. Nesses casos, você pode usar **mapeamentos de campo** para transformar seus dados na forma desejada.

Algumas situações em que os mapeamentos de campo são úteis:

* A fonte de dados tem um campo `_id`, mas a Pesquisa do Azure não permite nomes de campo iniciados com um sublinhado. Um mapeamento de campo permite um campo "rename". 
* Você deseja preencher vários campos no índice com os mesmos dados da fonte de dados, por exemplo, porque você deseja aplicar diferentes analisadores a esses campos. Mapeamentos de campo permitem a “bifurcação” de um campo de fonte de dados.
* Você precisa codificar ou decodificar Base64 seus dados. Mapeamentos de campo dão suporte a diversas **funções de mapeamento**, incluindo funções para codificação e decodificação Base64.   

> [!IMPORTANT]
> Atualmente, essa funcionalidade de mapeamento de campo está em preview. Ela está disponível somente na API REST que usa a versão **2015-02-28-Preview**. Lembre-se de que as APIs de visualização servem para teste e avaliação, e não devem ser usadas em ambientes de produção.
> 
> 

## Configurar mapeamentos de campo
Você pode adicionar mapeamentos de campo ao criar um novo indexador usando a API [Criar Indexador](search-api-indexers-2015-02-28-preview.md#create-indexer). Você pode gerenciar mapeamentos de campo ao criar um novo indexador usando a API [Atualizar Indexador](search-api-indexers-2015-02-28-preview.md#update-indexer).

Um mapeamento de campo consiste em 3 partes:

1. Um `sourceFieldName`, que representa um campo na fonte de dados. Essa propriedade é obrigatória. 
2. Um `targetFieldName` opcional, que representa um campo em seu índice de pesquisa. Se omitido, o mesmo nome que aparece na fonte de dados é usado.
3. Um `mappingFunction` opcional, que pode transformar seus dados usando uma das várias funções predefinidas. A lista completa de funções está [abaixo](#mappingFunctions).

Mapeamentos de campos são adicionados à matriz `fieldMappings` na definição do indexador.

Por exemplo, veja como você pode acomodar as diferenças nos nomes de campo:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
} 
```

Um indexador pode ter vários mapeamentos de campo. Por exemplo, aqui está como você pode "bifurcar" um campo:

```JSON

"fieldMappings" : [ 
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }, 
] 
```

> [!NOTE]
> A Pesquisa do Azure usa a comparação que não diferencia maiúsculas de minúsculas para resolver os nomes de campo e a função em mapeamentos de campo. Isso é conveniente (você não precisa obter todas as maiúsculas e minúsculas corretas), mas isso significa que a fonte de dados ou o índice não pode ter campos que diferem somente maiúsculas e minúsculas.
> 
> 

<a name="mappingFunctions"></a>

## Funções de mapeamento de campo
Essas funções atualmente têm suporte:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### base64Encode
Executa codificação Base64 de *URL segura* da cadeia de caracteres de entrada. Pressupõe-se que a entrada é codificada para UTF-8.

#### Casos de uso de exemplo
Somente caracteres de URL segura podem aparecer em uma chave de documento de pesquisa do Azure (porque os clientes devem ser capazes de resolver o documento usando a API de Pesquisa, por exemplo). Se seus dados contiverem caracteres de URL não seguros e você desejar usá-los para preencher um campo de chave em seu índice de pesquisa, use essa função.

#### Exemplo
```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Path", 
    "targetFieldName" : "UrlSafePath",
    "mappingFunction" : { "name" : "base64Encode" } 
  }] 
```

<a name="base64DecodeFunction"></a>

### base64Decode
Executa a decodificação de Base64 da cadeia de caracteres de entrada. A entrada é considerada uma cadeia de caracteres de codificação Base64 de *URL segura*.

#### Casos de uso de exemplo
Valores de metadados personalizados de blob devem ser codificados em ASCII. Você pode usar a codificação Base64 para representar cadeias de caracteres Unicode arbitrárias em metadados personalizados do blob. No entanto, para que a pesquisa seja significativa, você pode usar essa função para transformar os dados codificados novamente em cadeias de caracteres "normais" ao popular o índice de pesquisa.

#### Exemplo
```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Base64EncodedMetadata", 
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode" } 
  }] 
```

<a name="extractTokenAtPositionFunction"></a>

### extractTokenAtPosition
Divide um campo de cadeia de caracteres usando o delimitador especificado e seleciona o token na posição especificada na divisão resultante.

Por exemplo, se a entrada for `Jane Doe`, o `delimiter` for `" "`(espaço) e o `position` for 0, o resultado será `Jane`; se o `position` for 1, o resultado será `Doe`. Se a posição se refere a um token que não existe, um erro será retornado.

#### Casos de uso de exemplo
Sua fonte de dados contém um campo `PersonName` e você deseja indexá-la como dois campos `FirstName` e `LastName` separados. Você pode usar essa função para dividir a entrada usando o caractere de espaço como o delimitador.

#### Parâmetros
* `delimiter`: uma cadeia de caracteres a ser usado como o separador ao dividir a cadeia de caracteres de entrada.
* `position`: uma posição baseada em zero do número inteiro do token para escolher depois que a cadeia de caracteres de entrada for dividida.    

#### Exemplo
```JSON 

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } } 
  }, 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } } 
  }] 
```

<a name="jsonArrayToStringCollectionFunction"></a>

### jsonArrayToStringCollection
Transforma uma cadeia de caracteres formatada como uma matriz JSON de cadeias de caracteres em uma matriz de cadeia de caracteres que pode ser usada para preencher um campo `Collection(Edm.String)` no índice.

Por exemplo, se a cadeia de caracteres de entrada for `["red", "white", "blue"]`, o campo de destino do tipo `Collection(Edm.String)` será preenchido com os três valores `red`, `white` e `blue`. Para valores de entrada que não podem ser analisados como matrizes de cadeia de caracteres JSON, um erro será retornado.

#### Casos de uso de exemplo
O banco de dados SQL do Azure não tem um tipo de dados interno que é mapeado naturalmente para campos `Collection(Edm.String)` na Pesquisa do Azure. Para preencher os campos da coleção de cadeia de caracteres, formate seus dados de origem como uma matriz de cadeia de caracteres JSON e usar essa função.

#### Exemplo
```JSON

"fieldMappings" : [ 
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
] 
```

## Ajude-nos a aprimorar a Pesquisa do Azure
Se você tiver solicitações de recursos ou ideias para o aperfeiçoamentos, entre em contato conosco pelo [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0504_2016-->