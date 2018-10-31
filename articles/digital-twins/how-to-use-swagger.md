---
title: Noções básicas sobre como usar o Swagger de Gêmeos Digitais do Azure | Microsoft Docs
description: Como usar o Swagger dos Gêmeos Digitais do Azure
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: adgera
ms.openlocfilehash: dbadc90a206937d4c9f1d7b75a872d93b1a8a587
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323680"
---
# <a name="how-to-use-azure-digital-twins-swagger"></a>Como usar o Swagger dos Gêmeos Digitais do Azure

Cada instância de Gêmeos Digitais do Azure provisionada inclui sua própria documentação de referência do Swagger gerada automaticamente.

[Swagger](https://swagger.io/) (ou [OpenAPI](https://www.openapis.org/)) reúne informações complexas de API em um recurso de referência interativo e independente de linguagem. Especificamente, o Swagger fornece material de referência essencial sobre quais payloads JSON, métodos HTTP e pontos de extremidade específicos usar para executar operações em relação a uma API.

## <a name="swagger-summary"></a>Resumo do Swagger

O Swagger fornece um resumo interativo da sua API, incluindo:

* Informações de API e Modelo de Objeto.
* Pontos de extremidade da API REST especificando os payloads de solicitação, cabeçalhos, parâmetros, caminhos de contexto e métodos HTTP.
* Teste das funcionalidades de API.
* Informações de resposta de exemplo para validar e confirmar as respostas HTTP.
* Informações de código de erro.

Portanto, o Swagger é uma ferramenta conveniente para ajudá-lo com desenvolvimento e testes de chamadas feitas para a API de Gerenciamento.

> [!TIP]
> Para referência, uma versão prévia do Swagger é fornecida para demonstrar o conjunto de recursos da API.
> Ele está hospedado no [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Você pode acessar sua própria documentação do Swagger da API de gerenciamento gerada em:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Nome do Atributo Personalizado | Substitua por |
| --- | --- |
| `yourInstanceName` | O nome da sua instância de Gêmeos Digitais do Azure |
| `yourLocation` | Em qual região do servidor de sua instância está hospedada |

## <a name="reference-material"></a>Material de referência

Material de referência gerado automaticamente explica conceitos importantes e Modelos de Objeto.

Um resumo conciso descreve a API:

![Parte superior do Swagger][1]

Modelos de Objeto de API de núcleo também são listados:

![Modelos do Swagger][2]

Você pode clicar em cada Modelo de Objeto listado para um resumo mais detalhado dos principais atributos:

![Modelo do Swagger][3]

Os modelos de objeto do Swagger gerados são convenientes para ver todos os [Objetos e APIs](./concepts-objectmodel-spatialgraph.md) dos Gêmeos Digitais do Azure disponíveis. É um excelente recurso para os desenvolvedores usarem ao criar soluções em Gêmeos Digitais do Azure.

## <a name="endpoint-summary"></a>Resumo de ponto de extremidade

O Swagger também fornece uma visão completa de todos os pontos de extremidade que compõem a API.

Cada ponto de extremidade listado também inclui as informações de solicitação necessárias, como:

* Parâmetros obrigatórios.
* Tipos de dados de parâmetro necessários.
* O método HTTP para acessar o recurso.

![Pontos de extremidade do Swagger][4]

É possível clicar em cada recurso para ver uma visão mais detalhada.

## <a name="using-swagger-to-test-endpoints"></a>Uso do Swagger para testar os pontos de extremidade

Uma das funcionalidades poderosas que o Swagger oferece é a capacidade de **Experimentar** ou testar um ponto de extremidade de API diretamente por meio de documentação da interface do usuário.

Depois de clicar em um ponto de extremidade específico, você verá um botão **Experimentar**:

![Experimentar o Swagger][5]

Expandir essa seção exibe os campos de entrada para cada parâmetro obrigatório e opcional. Insira os valores de acordo e clique em **Executar**:

![Swagger experimentado][6]

Depois de executar o teste, você pode validar os dados de resposta.

## <a name="swagger-response-data"></a>Dados de resposta do Swagger

Cada ponto de extremidade listado também inclui dados de corpo de resposta para validar seu desenvolvimento e testes. Esses exemplos incluem os códigos de status desejados e o JSON para solicitações HTTP bem-sucedidas.

![Resposta do Swagger][7]

Os exemplos também incluem os códigos de erro para ajudar a depurar ou melhorar os testes com falha.

## <a name="swagger-oauth-20-authorization"></a>Autorização OAuth 2.0 do Swagger

Para testar interativamente as solicitações com base nos recursos de API protegidos por OAuth 2.0, confira a [documentação oficial](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Próximas etapas

Para ler mais sobre modelos de objeto de Gêmeos Digitais do Azure e o grafo de inteligência espacial, leia [este artigo](./concepts-objectmodel-spatialgraph.md).

Para saber como autenticar-se com a API de gerenciamento, leia [Autenticar com APIs](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.png
[2]: media/how-to-use-swagger/swagger_management_models.png
[3]: media/how-to-use-swagger/swagger_management_model.png
[4]: media/how-to-use-swagger/swagger_management_endpoints.png
[5]: media/how-to-use-swagger/swagger_management_try.png
[6]: media/how-to-use-swagger/swagger_management_tried.png
[7]: media/how-to-use-swagger/swagger_management_response.png
