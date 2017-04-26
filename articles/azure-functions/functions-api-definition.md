---
title: Metadados de OpenAPI no Azure Functions | Microsoft Docs
description: "Visão geral do suporte OpenAPI em Azure Functions"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 08063960d3956ca65dd32d9fe726c366799a2087
ms.lasthandoff: 04/18/2017


---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Suporte aos metadados de OpenAPI 2.0 no Azure Functions (versão prévia)
Esse recurso de versão prévia permite que você escreva uma definição de OpenAPI 2.0 (anteriormente conhecido como Swagger) dentro de um Aplicativo de funções e o hospedar esse arquivo usando o Aplicativo de funções.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-metadata"></a>O que são metadados de OpenAPI?
[Metadados de OpenAPI](http://swagger.io/) permite que uma função que hospeda uma REST API seja consumida por uma ampla variedade de outros softwares. De ofertas próprias como o PowerApps e [aplicativos de API](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier), à ferramentas de desenvolveres terceiros como [Postman](https://www.getpostman.com/docs/importing_swagger), e [muitos outros pacotes](http://swagger.io/tools/).

>[!TIP]
>É recomendável iniciar com o [tutorial de introdução](./functions-api-definition-getting-started.md) e, em seguida, retornar a este documento para saber mais sobre recursos específicos.

## <a name="enable"></a>Habilitando suporte à definição de OpenAPI
* Todas as configurações de OpenAPI podem ser configuradas na página `API Definition (preview)`, nas configurações do Aplicativo de funções.
* Definir `API defintion source` para `Function` para habilitar uma definição de OpenAPI hospedada e uma geração de definição de inicialização rápida.
  * `External URL` permite que sua função use uma definição de OpenAPI que é hospedada em outro lugar.

## <a name="generate-defintion"></a>Gerar um esqueleto de Swagger de seus metadados de função
Um modelo é uma maneira excelente de começar a escrever sua primeira definição de OpenAPI. O recurso de modelo de definição cria uma definição de OpenAPI esparsa usando todos os metadados de function.json para cada uma de suas funções de gatilho de HTTP. **Você precisará preencher mais informações sobre sua API na [especificação de OpenAPI](http://swagger.io/specification/), como modelos de solicitação e resposta.**

[Confira este guia de Introdução para obter instruções passo a passo](./functions-api-definition-getting-started.md)

### <a name="templates"></a>Modelos disponíveis

|Nome| Descrição |
|:-----|:-----|
|Definição gerada|Uma definição de OpenAPI com a quantidade máxima de informações que podem ser inferidas de metadados existentes da função|

### <a name="quickstart-details"></a>Metadados incluídos na definição gerada

A tabela a seguir representa as configurações do portal e dados correspondentes no function.json conforme ele são mapeados para o Swagger gerado.

|Swagger.json|Interface do usuário do portal|Function.json|
|:----|:-----|:-----|
|[Host](http://swagger.io/specification/#fixed-fields-15)|`Function app settings` > `Go to App Service Settings` > `Overview` > `URL`|*não presente*
|[Caminhos](http://swagger.io/specification/#paths-object-29)|`Integrate` > `Selected HTTP methods`|Associações: rota
|[Item de caminho](http://swagger.io/specification/#path-item-object-32)|`Integrate` > `Route template`|Associações: métodos
|[Segurança](http://swagger.io/specification/#security-scheme-object-112)|simétricas|*não presente*|
|operationID*|Rota + verbos permitidos|Rota + verbos permitidos|

\*ID da operação só é necessário para a integração com PowerApps + fluxo
> [!NOTE]
>  x-ms-summary fornece um nome de exibição em aplicativos lógicos, fluxo e PowerApps.
>
> Confira [personalizar sua definição de Swagger para PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/) para saber mais.

## <a name="CICD"></a>Usando o CI/CD para definir uma definição de API

 Você deve habilitar a definição de API de hospedagem no portal antes de habilitar o controle do código-fonte para modificar a definição da API do controle do código-fonte. Siga as instruções abaixo.

1. Navegue até `API Definition (preview)` nas configurações do Aplicativo de funções.
  1. Defina `API definition source` como `Function`
  1. Clique em `Generate API definition template`, em seguida, em `Save` para criar uma definição de modelo para modificar posteriormente.
  1. Observe seu `API definition URL` e `key`
1. [Configurar CI/CD](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements)
2. Modificar seu `swagger.json` no controle do código-fonte em `\site\wwwroot\.azurefunctions\swagger\swagger.json`

Agora as alterações para `swagger.json` no repositório são hospedadas por seu Aplicativo de funções em `API definition URL` e `key`, anotado na etapa 1.3

## <a name="next-steps"></a>Próximas etapas
* [Tutorial de introdução](functions-api-definition-getting-started.md)
  * Experimente o nosso passo a passo para exibir uma definição de OpenAPI em ação!
* [Repositório de GitHub do Azure Functions](https://github.com/Azure/Azure-Functions/)
  * Confira as funções de Github para fornecer comentários sobre a versão prévia de suporte de definição de API! Execute o github para qualquer coisa que você gostaria de ver atualizada.
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)  
  * Referência do programador para codificação de funções e definição de gatilhos e de associações.

