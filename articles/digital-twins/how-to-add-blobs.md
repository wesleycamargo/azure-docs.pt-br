---
title: Como adicionar blobs a objetos em Gêmeos Digitais do Azure | Microsoft Docs
description: Saiba como adicionar blobs a objetos nos Gêmeos Digitais do Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 604093dcec048b0991bbc9beac3ef998cc47e351
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974498"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Adicionar blobs a objetos nos Gêmeos Digitais do Azure

Os blobs são representações não estruturadas de tipos de arquivos comuns, como imagens e logs. Os blobs rastreiam o tipo de dados que eles representam usando um tipo MIME (por exemplo: "image/jpeg") e metadados (nome, descrição, tipo, etc.).

Os Gêmeos Digitais do Azure oferecem suporte à conexão de blobs a dispositivos, espaços e usuários. Os blobs podem representar uma imagem de perfil para um usuário, uma foto do dispositivo, um vídeo, um mapa ou um log.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-an-overview"></a>Upload de blobs: uma visão geral

Você pode usar solicitações multipartes para fazer o upload de blobs para pontos de extremidade específicos e suas respectivas funcionalidades.

> [!IMPORTANT]
> Os solicitações multipartes requerem três informações:
> * Um **Content-Type** cabeçalho:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Um **Content-Disposition**: `form-data; name="metadata"`
> * O conteúdo do arquivo que será carregado
>
> As informações de **Content-Type** e **Content-Disposition** podem variar dependendo do cenário de uso.

As solicitações multipartes feitas para as APIs do Gerenciamento dos Gêmeos Digitais do Azure são divididas em duas partes:

* metadados de blob como um tipo MIME associado, conforme mostrado nas informações de **Content-Type** e **Content-Disposition**

* conteúdo do blob (o conteúdo não estruturado do arquivo)  

Nenhuma das duas partes é necessária para solicitações de **PATCH**. Ambos são necessários para **POST** ou criam operações.

### <a name="blob-metadata"></a>Metadados de blob

Além de **Content-Type** e **Content-Disposition**, as solicitações multipartes devem especificar o corpo JSON correto. Qual corpo JSON a ser enviado depende do tipo de operação de solicitação HTTP que está sendo executada.

Os quatro esquemas JSON principais são:

![Esquemas JSON][1]

A documentação do Swagger descreve esses esquemas de modelo em detalhes.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Aprenda a usar a documentação de referência lendo [Como usar o Swagger](./how-to-use-swagger.md).

### <a name="examples"></a>Exemplos

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Para fazer uma solicitação **POST** que carregue um arquivo de texto como um blob e o associe a um espaço:

```plaintext
POST YOUR_MANAGEMENT_API_URL/spaces/blobs HTTP/1.1
Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"

--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Valor | Substitua por |
| --- | --- |
| USER_DEFINED_BOUNDARY | Um nome de limite de conteúdo com diversas partes |

O código a seguir é uma implementação .NET do mesmo upload do Blob que usa a classe [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

## <a name="api-endpoints"></a>Pontos de extremidade de API

As seções a seguir descrevem os pontos de extremidade de API relacionados ao blob principal e suas funcionalidades.

### <a name="devices"></a>Dispositivos

Você pode anexar blobs aos dispositivos. A imagem a seguir mostra a documentação de referência do Swagger para suas APIs de Gerenciamento. Ela especifica os pontos de extremidade da API relacionados ao dispositivo para consumo de blob e qualquer parâmetro de caminho necessário para passar para eles.

![Blobs de dispositivo][2]

Por exemplo, para atualizar ou criar um blob e anexar o blob a um dispositivo, faça uma solicitação **PATCH** para:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_BLOB_ID* | A ID do blob desejado |

As solicitações bem-sucedidas retornarão um objeto JSON **DeviceBlob** na resposta. Os objetos **DeviceBlob** estão em conformidade com o seguinte esquema JSON:

| Atributo | Tipo | DESCRIÇÃO | Exemplos |
| --- | --- | --- | --- |
| **DeviceBlobType** | Cadeia de caracteres | Uma categoria de blob que pode ser anexada a um dispositivo | `Model` e `Specification` |
| **DeviceBlobSubtype** | Cadeia de caracteres | Uma subcategoria de blob que é mais específica que **DeviceBlobType** | `PhysicalModel`, `LogicalModel`, `KitSpecification`, e `FunctionalSpecification` |

> [!TIP]
> Use a tabela anterior para manipular dados de solicitação retornados com sucesso.

### <a name="spaces"></a>Espaços

Você também pode anexar blobs aos espaços. A imagem abaixo lista todos os pontos de extremidade da API de espaços responsáveis pela manipulação dos blobs. Lista também os parâmetros de caminho a serem passados para esses pontos de extremidade.

![Blobs de espaço][3]

Por exemplo, para retornar um blob anexado a um espaço, faça uma solicitação **GET** para:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_BLOB_ID* | A ID do blob desejado |

Fazer uma solicitação **PATCH** para o mesmo ponto de extremidade permitirá que você atualize uma descrição de metadados e crie uma nova versão do blob. A solicitação HTTP é feita usando o método **PATCH** juntamente com quaisquer dados de formulário meta e multipartes necessários.

As operações bem-sucedidas retornarão um objeto **SpaceBlob** que esteja de acordo com o esquema a seguir. Você pode usá-lo para consumir os dados retornados.

| Atributo | Tipo | DESCRIÇÃO | Exemplos |
| --- | --- | --- | --- |
| **SpaceBlobType** | Cadeia de caracteres | Uma categoria de Blob que pode ser anexada a um espaço | `Map` e `Image` |
| **SpaceBlobSubtype** | Cadeia de caracteres | Uma subcategoria de blob que é mais específica que **SpaceBlobType** | `GenericMap`, `ElectricalMap`, `SatelliteMap`, e `WayfindingMap` |

### <a name="users"></a>Usuários

Você pode anexar os blobs aos modelos do usuário (por exemplo, para associar uma imagem de perfil). A imagem abaixo exibe os pontos de extremidade relevantes da API de usuários e quaisquer parâmetros de caminho obrigatório, como `id`:

![Blobs de usuário][4]

Por exemplo, para buscar um blob anexado a um usuário, faça uma solicitação **GET** com todos os dados de formulário necessários para:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_BLOB_ID* | A ID do blob desejado |

O JSON retornado (objetos **UserBlob**) estará em conformidade com os modelos JSON a seguir:

| Atributo | Tipo | DESCRIÇÃO | Exemplos |
| --- | --- | --- | --- |
| **UserBlobType** | Cadeia de caracteres | Uma categoria de blob que pode ser anexada a um usuário | `Image` e `Video` |
| **UserBlobSubtype** |  Cadeia de caracteres | Uma subcategoria de blob que é mais específica que **UserBlobType** | `ProfessionalImage`, `VacationImage`, e `CommercialVideo` |

## <a name="common-errors"></a>Erros comuns

Um erro comum é não incluir as informações de cabeçalho corretas:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a documentação de referência do Swagger para Gêmeos Digitais do Azure, leia [Como usar o Swagger dos Gêmeos Digitais do Azure](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
