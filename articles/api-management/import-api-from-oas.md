---
title: "Importar uma especificação de OpenAPI usando o Portal do Azure | Microsoft Docs"
description: "Saiba como importar uma especificação de OpenAPI com o Gerenciamento de API."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: f0c77c6e959ca99698b3ea704756a6abf36147f3
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="import-an-openapi-specification"></a>Importar uma especificação de OpenAPI

Este artigo mostra como importar uma API de back-end da "Especificação de OpenAPI" que reside em http://conferenceapi.azurewebsites.net?format=json. Esta API de back-end é fornecida pela Microsoft e hospedada no Azure. O artigo também mostra como testar a API do APIM.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Importar uma API de back-end de “Especificação de OpenAPI”
> * Testar a API no Portal do Azure
> * Testar a API no Portal do desenvolvedor

## <a name="prerequisites"></a>Pré-requisitos

Conclua o seguinte guia de início rápido: [Criar uma nova instância do serviço de Gerenciamento de API do Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importar e publicar uma API de back-end

1. Selecione **APIs** em **GERENCIAMENTO DE API**.
2. Selecione **Especificação de OpenAPI** na lista **Adicionar uma nova API**.
    ![Especificação OpenAPI](./media/import-api-from-oas/oas-api.png)
3. Insira as configurações apropriadas. Você pode definir todos os valores da API durante a criação. Como alternativa, você pode definir algumas delas mais tarde acessando a guia **Configurações**. <br/> Se você pressionar **Tab**, alguns campos (ou todos eles) serão preenchidos com as informações do serviço de back-end especificado.

    ![Criar uma API](./media/api-management-get-started/create-api.png)

    |Configuração|Valor|Descrição|
    |---|---|---|
    |**Especificação OpenAPI**|http://conferenceapi.azurewebsites.net?format=json|Referencia o serviço que implementa a API. O gerenciamento de API envia as solicitações para esse endereço.|
    |**Nome de exibição**|*API de Conferência de Demonstração*|Se você pressionar Tab depois de inserir a URL do serviço, o APIM preencherá esse campo com base no que está no json. <br/>Esse nome é exibido no Portal do desenvolvedor.|
    |**Nome**|*demo-conference-api*|Fornece um nome exclusivo para a API. <br/>Se você pressionar Tab depois de inserir a URL do serviço, o APIM preencherá esse campo com base no que está no json.|
    |**Descrição**|Forneça uma descrição opcional da API.|Se você pressionar Tab depois de inserir a URL do serviço, o APIM preencherá esse campo com base no que está no json.|
    |**Sufixo da URL da API**|*conference*|O sufixo é acrescentado à URL base do serviço de gerenciamento de API. O Gerenciamento de API diferencia as APIs pelo sufixo e, portanto, o sufixo deve ser único para cada API para um editor específico.|
    |**Esquema de URL**|*HTTPS*|Determina quais protocolos podem ser usados para acessar a API. |
    |**Produtos**|*Ilimitado*| Publica a API associando-a a um produto. Para adicionar, opcionalmente, essa nova API a um produto, digite o nome do produto. Esta etapa pode ser repetida várias vezes para adicionar a API a vários produtos.<br/>Os produtos são associações de uma ou mais APIs. Você pode incluir várias APIs e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. Primeiro, os desenvolvedores devem assinar um produto para obter acesso à API. Com a assinatura, eles obtêm uma chave de assinatura que funciona para qualquer API no produto. Se você criou a instância do APIM, você já é um administrador e, portanto, está inscrito em todos os produtos por padrão.<br/> Por padrão, cada instância de gerenciamento de API vem com dois produtos de exemplo: **Inicial** e **Ilimitado**. |

4. Selecione **Criar**.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testar a nova API do APIM no portal do Azure

As operações podem ser chamadas diretamente do portal do Azure, o que oferece uma maneira fácil de exibir e testar as operações de uma API.  

1. Selecione a API que você criou na etapa anterior.
2. Pressione a guia **Testar**.

    ![Testar API](./media/api-management-get-started/test-api.png)
1. Clique em **GetSpeakers**.

    A página exibe campos para parâmetros de consulta, mas, nesse caso, não há nenhum. A página também exibe campos para os cabeçalhos. Um dos cabeçalhos é "Ocp-Apim-Subscription-Key", para a chave de assinatura do produto que está associado a essa API. Se você criou a instância do APIM, já é um administrador e, portanto, a chave é preenchida automaticamente. 
4. Pressione **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

## <a name="call-operation"> </a>Chamar uma operação no Portal do desenvolvedor

As operações também podem ser chamadas do **Portal do desenvolvedor** para testar APIs. 

1. Selecione a API que você criou na etapa "Importar e publicar uma API de back-end".
2. Pressione **Portal do desenvolvedor**.

    ![Testar no Portal do desenvolvedor](./media/api-management-get-started/developer-portal.png)

    O site "Portal do desenvolvedor" é aberto.
3. Selecione **API**.
4. Selecione **API de Conferência de Demonstração**.
5. Clique em **GetSpeakers**.
    
    A página exibe campos para parâmetros de consulta, mas, nesse caso, não há nenhum. A página também exibe campos para os cabeçalhos. Um dos cabeçalhos é "Ocp-Apim-Subscription-Key", para a chave de assinatura do produto que está associado a essa API. Se você criou a instância do APIM, já é um administrador e, portanto, a chave é preenchida automaticamente.
6. Pressione **Experimentar**.
7. Pressione **Enviar**.
    
    Após invocar uma operação, o portal do desenvolvedor exibe o **Status de resposta**, os **Cabeçalhos de resposta** e o **Conteúdo de resposta**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transformar e proteger uma API publicada](transform-api.md)
