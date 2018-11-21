---
title: Como adicionar blobs a objetos em Gêmeos Digitais do Azure | Microsoft Docs
description: Entendendo como adicionar blobs a objetos no Azure Digital Twins
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 0929a4a63eee35d21723c980887d6b4217898682
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688303"
---
# <a name="how-to-add-blobs-to-objects-in-azure-digital-twins"></a>Como adicionar blobs a objetos em gêmeos do Azure Digital

Blobs são representações não estruturadas de tipos de arquivos comuns (como imagens e logs). Os Blobs rastreiam o tipo de dados que eles representam usando um tipo MIME (por exemplo: "image / jpeg") e metadados (nome, descrição, tipo etc.).

O Azure Digital Twins suporta a conexão de Blobs a dispositivos, espaços e usuários. Blobs podem representar uma imagem de perfil para um usuário, uma foto do dispositivo, um vídeo, um mapa ou um log.

> [!NOTE]
> Este artigo pressupõe que:
> * Sua instância está configurada corretamente para receber solicitações da API de gerenciamento.
> * Sua instância está configurada corretamente para receber solicitações da API de gerenciamento.

## <a name="uploading-blobs-an-overview"></a>Upload de blobs: uma visão geral

Solicitações multipartes são usadas para fazer o upload de Blobs para endpoints específicos e suas respectivas funcionalidades.

> [!IMPORTANT]
> Solicitações multipartes requerem três informações essenciais. Gêmeos Digitais do Azure:
> * Um **Content-Type** cabeçalho:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
> * Um **Content-Disposition**: `form-data; name="metadata"`.
> * O conteúdo do arquivo para upload.
>
> O **Tipo de Conteúdo** e **Conteúdo-Disposição** exatos podem variar dependendo do cenário de uso.

Cada solicitação multipartes é dividida em vários blocos. Solicitações multipartes feitas para as APIs do Gerenciamento de Gêmeos Digitais do Azure são divididas em **duas** (**2**) tais partes:

1. A primeira parte é obrigatório e contém metadados de Blob como um tipo MIME associado pela **Content-Type** e **Content-Disposition** acima.

1. A segunda parte contém o conteúdo real do Blob (o conteúdo não estruturado do arquivo).  

Nenhuma das duas partes é necessária para solicitações de **PATCH**. Ambos são necessários para **POST** ou criam operações.

### <a name="blob-metadata"></a>Metadados de blob

Além de **Content-Type** e **Content-Disposition**, solicitações multipartes também devem especificar o corpo JSON correto. Qual corpo JSON a ser enviado depende do tipo de operação de solicitação HTTP que está sendo executada.

Os esquemas JSON principais quatro usados são:

![Blobs de espaço][1]

Esses esquemas de modelo são descritas em detalhes na documentação do Swagger fornecida.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Aprenda a usar a documentação de referência fornecida lendo [Como usar o Swagger](./how-to-use-swagger.md).

### <a name="examples"></a>Exemplos

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Para fazer uma solicitação POST que carregue um arquivo de texto como um Blob e o associe a um espaço:

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

| Valor de parâmetro | Substitua por |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | Um nome de limite de conteúdo com diversas partes |

Uma implementação .NET do mesmo upload do Blob é fornecida abaixo usando a classe [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metaData in a suitable format
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

Abaixo, um passo a passo de pontos finais fundamentais e suas funcionalidades específicas é fornecido.

### <a name="devices"></a>Dispositivos

BLOBs podem ser anexados aos dispositivos. A imagem abaixo (descrevendo a documentação de referência do Swagger para suas APIs de gerenciamento) especifica os pontos de extremidade da API relacionados ao dispositivo para consumo de Blob e qualquer parâmetro de caminho necessário para passar para eles:

![Blobs de dispositivo][2]

Por exemplo, para atualizar ou criar um Blob e anexar o Blob a um Dispositivo, uma solicitação de PATCH é feita para:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_BLOB_ID* | O ID do Blob desejado |

Solicitações bem-sucedidas retornarão um objeto JSON do DeviceBlob na resposta. Os DeviceBlobs estão em conformidade com o seguinte esquema JSON:

| Atributo | Tipo | DESCRIÇÃO | Exemplos |
| --- | --- | --- | --- |
| **DeviceBlobType** | Cadeia de caracteres | Uma categoria de Blob que pode ser anexada a um dispositivo | `Model` e `Specification` |
| **DeviceBlobSubtype*** | Cadeia de caracteres | Uma subcategoria de Blob que é mais granular que DeviceBlobType | `PhysicalModel`, `LogicalModel`, `KitSpecification`, e `FunctionalSpecification` |

> [!TIP]
> Use a tabela acima para manipular dados de solicitação retornados com sucesso.

### <a name="spaces"></a>Espaços

BLOBs também podem ser anexados aos espaços. A imagem abaixo lista todos os pontos de extremidade da API do Space responsáveis por manipular os Blobs juntamente com os parâmetros de caminho a serem passados para eles:

![Blobs de espaço][3]

Por exemplo, para retornar um Blob conectado a um Space, faça uma solicitação GET para:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_BLOB_ID* | O ID do Blob desejado |

Fazer uma solicitação PATCH para o mesmo endpoint permitirá que você atualize uma descrição de metadados e crie uma nova versão do Blob. A solicitação HTTP é feita usando o método PATCH juntamente com quaisquer dados de formulário meta e multipartes necessários.

Operações bem-sucedidas retornarão um SpaceBlob que esteja de acordo com o esquema a seguir e possa ser usado para consumir os dados retornados:

| Atributo | Tipo | DESCRIÇÃO | Exemplos |
| --- | --- | --- | --- |
| **SpaceBlobType** | Cadeia de caracteres | Uma categoria de Blob que pode ser anexada a um espaço | `Map` e `Image` |
| **SpaceBlobSubtype** | Cadeia de caracteres | Uma subcategoria de Blob que é mais granular que SpaceBlobType | `GenericMap`, `ElectricalMap`, `SatelliteMap`, e `WayfindingMap` |

### <a name="users"></a>Usuários

Os blobs podem ser anexados aos modelos do usuário (para associar uma imagem de perfil). A imagem abaixo descreve pontos de extremidade relevantes da API de usuários e qualquer parâmetro de caminho obrigatório, como um `id`:

![Blobs de usuário][4]

Por exemplo, para buscar um Blob anexado a um usuário, faça uma solicitação GET com todos os dados de formulário necessários para:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_BLOB_ID* | O ID do Blob desejado |

O JSON retornado (UserBlobs) estará em conformidade com os modelos JSON a seguir:

| Atributo | Tipo | DESCRIÇÃO | Exemplos |
| --- | --- | --- | --- |
| **UserBlobType** | Cadeia de caracteres | Uma categoria de Blob que pode ser anexada a um usuário | `Image` e `Video` |
| **UserBlobSubtype** |  Cadeia de caracteres | Uma subcategoria de Blob que é mais granular que UserBlobType | `ProfessionalImage`, `VacationImage`, e `CommercialVideo` |

## <a name="common-errors"></a>Erros comuns

Não incluindo as informações corretas do cabeçalho:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a documentação de referência fornecida do Swagger Gêmeos Digitais do Azure, leia [Como usar o Digital Twins Swagger](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
