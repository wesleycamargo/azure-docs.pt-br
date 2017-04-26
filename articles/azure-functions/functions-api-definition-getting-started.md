---
title: "Introdução aos metadados do OpenAPI no Azure Functions | Microsoft Docs"
description: "Introdução aos metadados do OpenAPI no Azure Functions"
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
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: e066cc2ee99b14d13c5238266513edf61ecbe3e1
ms.lasthandoff: 04/15/2017


---
# <a name="creating-openapi-20-swagger-metadata-for-a-function-app-preview"></a>Criando metadados de OpenAPI 2.0 (Swagger) para um Aplicativo de funções (versão prévia)

Este documento orienta você pelo processo passo a passo da criação de uma definição de OpenAPI para uma API simples hospedada no Azure Functions.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-swagger"></a>O que é OpenAPI (Swagger)?
[Metadados de swagger](http://swagger.io/) é um arquivo que define a funcionalidade e os modos de operação de sua API e permite que uma função que hospeda uma API REST para serem consumidos por uma ampla variedade de outros softwares. Ofertas da Microsoft, como PowerApps e [aplicativos de API](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier), bem como ferramentas de desenvolvedores terceiros, como [Postman](https://www.getpostman.com/docs/importing_swagger) e [muitos outros pacotes](http://swagger.io/tools/); todos permitem sua API a ser consumida com uma definição de Swagger.

## <a name="prepare-function"></a>Criando uma função com uma API simples
  Para criar uma definição de OpenAPI, primeiro precisamos criar uma função com uma API simples. Se você já tiver uma API hospedada em um Aplicativo de funções, você poderá pular diretamente para a próxima seção
1. Criar um novo Aplicativo de funções.
  1. [Portal do Azure](https://portal.azure.com) > `+ New` > Procure pela "Aplicativo de funções"
1. Cria uma nova função de gatilho HTTP dentro do seu novo Aplicativo de funções
  1. Sua função é preenchida previamente com o código que define uma API REST muito simples.
  1. Qualquer cadeia de caracteres passada para a função como um parâmetro de consulta ou no corpo é retornada como "Olá {entrada}"
1. Vá para a guia `Integrate` da sua nova função de gatilho HTTP
  1. Ativar/desativar `Allowed HTTP methods` para `Selected methods`
  1. Em `Selected HTTP methods` desmarque cada verbo, exceto POST.
    ![Métodos HTTP selecionados](./media/functions-api-definition-getting-started/selectedHTTPmethods.png)
  1. Esta etapa simplificará sua definição de API mais tarde.

## <a name="enable"></a>Habilitando suporte à definição de API
1. Navegue para `your function name` > `API Definition (preview)`
1. Defina `API Definition Source` como `Internal`
  1. Esta etapa permite que um pacote de opções OpenAPI para seu Aplicativo de funções, incluindo um ponto de extremidade para hospedar um arquivo OpenAPI de domínio do seu Aplicativo de funções, uma cópia embutida do [Editor de OpenAPI](http://editor.swagger.io), e um gerador de definição de início rápido.
![Definição habilitada](./media/functions-api-definition-getting-started/enabledefinition.png)

## <a name="create-definition"></a>Criando sua definição de API de um modelo
1. Clique em `Generate API Definition template`
  1. Essa etapa verifica seu Aplicativo de funções para funções de gatilho de HTTP e usa as informações no functions.json para gerar um documento OpenAPI.
2. Adicionar um objeto de operação para `paths: /api/yourfunctionroute post:`
  1. O documento de OpenAPI de início rápido é uma estrutura de tópicos de um documento OpenAPI completo. Ele requer que mais metadados sejam uma definição OpenAPI completa, como objetos de operação e modelos de resposta.
  1. O objeto de operação de exemplo abaixo tem uma seção que produz/consome, um objeto de parâmetro e um objeto de resposta.
  
  ```yaml
      post:
        operationId: /api/yourfunctionroute/post
        consumes: [application/json]
        produces: [application/json]
        parameters:
          - name: name
            in: body
            description: Your name
            x-ms-summary: Your name
            required: true
            schema:
              type: object
              properties:
                name:
                  type: string
        description: >-
          Replace with Operation Object
          #http://swagger.io/specification/#operationObject
        responses:
          '200':
            description: A Greeting
            x-ms-summary: Your name
            schema:
              type: string
        security:
          - apikeyQuery: []
  ```

> [!NOTE]
>  x-ms-summary fornece um nome de exibição em aplicativos lógicos, fluxo e PowerApps.
>
> Confira [personalizar sua definição de Swagger para PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/) para saber mais.

3. Clique em `save` para salvar suas alterações ![Adicionando definição de modelo](./media/functions-api-definition-getting-started/addingtemplate.png)

## <a name="use-definition"></a>Usando sua definição de API
1. Copie o `API definition URL` e cole-o em uma nova guia do navegador para exibir o documento OpenAPI bruto.
1. Você pode importar o documento de OpenAPI para qualquer número de ferramentas para teste e integração usando essa URL.
  1. Muitos recursos do Azure são capazes de importar automaticamente o documento OpenAPI usando a URL de definição de API que é salvo em suas configurações de aplicativo de função. Como parte da `Function` fonte de definição de API, nós atualizamos essa URL para você.


## <a name="test-definition"></a>Usando a interface do usuário do Swagger para testar sua definição de API
Existem ferramentas de teste integradas para o modo de exibição da interface do usuário do editor de definição de API incorporado. Adicione sua chave de API e, em seguida, use o `Try this operation` botão em cada método. A ferramenta usará a definição de API para formatar as solicitações e respostas bem-sucedidas indicará que a definição está correta.

### <a name="steps"></a>Etapas:

1. Copiar a chave de API da função
  1. A chave de API pode ser encontrada na função do gatilho HTTP em `function name` > `Keys` > `Function Keys`
  ![tecla de função](./media/functions-api-definition-getting-started/functionkey.png)
1. Navegue até a página `API Definition (preview)`.
  1. Clique em `Authenticate` e adicione sua chave de API de função ao objeto de segurança na parte superior.
  ![Chave OpenAPI](./media/functions-api-definition-getting-started/definitionTest auth.png)
1. selecione `/api/yourfunctionroute` > `POST`
1. Clique em `Try it out` e digite um nome para testar
1. Você verá um resultado de ÊXITO sob o `Pretty`
![teste de definição de API](./media/functions-api-definition-getting-started/definitionTest.png)

## <a name="next-steps"></a>Próximas etapas
* [Definição de OpenAPI na visão geral de funções](functions-api-definition.md)
  * Leia a documentação completa para obter mais informações sobre o suporte de OpenAPI.
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)  
  * Referência do programador para codificação de funções e definição de gatilhos e de associações.
* [Repositório de GitHub do Azure Functions](https://github.com/Azure/Azure-Functions/)
  * Confira as funções de Github para fornecer comentários sobre a versão prévia de suporte de definição de API! Execute o github para qualquer coisa que você gostaria de ver atualizada.

