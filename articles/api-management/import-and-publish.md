---
title: Importar e publicar sua primeira API no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como importar e publicar sua primeira API com o gerenciamento de API.
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: ffe5ee95c66eee7dccd25a1afd2fe639cbc273f5
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="import-and-publish-your-first-api"></a>Importar e publicar sua primeira API 

Este tutorial mostra como importar uma API de back-end da "Especificação OpenAPI" que reside em http://conferenceapi.azurewebsites.net?format=json. Esta API de back-end é fornecida pela Microsoft e hospedada no Azure. 

Depois que a API de back-end é importada para o APIM (gerenciamento de API), a API do APIM torna-se uma fachada para a API de back-end. Quando você importar a API de back-end, a API de origem e a API do APIM serão idênticas. O APIM permite que você personalize a fachada de acordo com suas necessidades sem mexer na API de back-end. Para obter mais informações, consulte [Transformar e proteger sua API](transform-api.md). 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Importar sua primeira API
> * Testar a API no Portal do Azure
> * Testar a API no Portal do desenvolvedor

![Nova API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>pré-requisitos

Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importar e publicar uma API de back-end

Esta seção mostra como importar e publicar uma API de back-end da especificação OpenAPI.
 
1. Selecione **APIs** em **GERENCIAMENTO DE API**.
2. Selecione a **Especificação OpenAPI** na lista.

    ![Criar uma API](./media/api-management-get-started/create-api.png)

    Você pode definir os valores da API durante a criação ou mais tarde, acessando a guia **Configurações**.  

    |Configuração|Valor|DESCRIÇÃO|
    |---|---|---|
    |**Especificação OpenAPI**|http://conferenceapi.azurewebsites.net?format=json|Referencia o serviço que implementa a API. O gerenciamento de API envia as solicitações para esse endereço.|
    |**Nome de exibição**|*API de Conferência de Demonstração*|Se você pressionar Tab depois de inserir a URL do serviço, o APIM preencherá esse campo com base no que está no json. <br/>Esse nome é exibido no Portal do desenvolvedor.|
    |**Nome**|*demo-conference-api*|Fornece um nome exclusivo para a API. <br/>Se você pressionar Tab depois de inserir a URL do serviço, o APIM preencherá esse campo com base no que está no json.|
    |**Descrição**|Forneça uma descrição opcional da API.|Se você pressionar Tab depois de inserir a URL do serviço, o APIM preencherá esse campo com base no que está no json.|
    |**Sufixo da URL da API**|*conference*|O sufixo é acrescentado à URL base do serviço de gerenciamento de API. O Gerenciamento de API diferencia as APIs pelo sufixo e, portanto, o sufixo deve ser único para cada API para um editor específico.|
    |**Esquema de URL**|*HTTPS*|Determina quais protocolos podem ser usados para acessar a API. |
    |**Produtos**|*Ilimitado*| Publica a API associando-a a um produto. Para adicionar, opcionalmente, essa nova API a um produto, digite o nome do produto. Esta etapa pode ser repetida várias vezes para adicionar a API a vários produtos.<br/>Os produtos são associações de uma ou mais APIs. Você pode incluir várias APIs e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. Primeiro, os desenvolvedores devem assinar um produto para obter acesso à API. Com a assinatura, eles obtêm uma chave de assinatura que funciona para qualquer API no produto. Se você criou a instância do APIM, já é um administrador e, portanto, está inscrito em cada produto por padrão.<br/> Por padrão, cada instância de gerenciamento de API vem com dois produtos de exemplo: **Inicial** e **Ilimitado**. |
3. Selecione **Criar**.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testar a nova API do APIM no portal do Azure

As operações podem ser chamadas diretamente do portal do Azure, o que oferece uma maneira fácil de exibir e testar as operações de uma API.  
1. Selecione a API que você criou na etapa anterior.
2. Pressione a guia **Testar**.  ![Testar a API](./media/api-management-get-started/test-api.png)
3. Clique em **GetSpeakers**.
    A página exibe campos para parâmetros de consulta, mas, nesse caso, não há nenhum. A página também exibe campos para os cabeçalhos. Um dos cabeçalhos é "Ocp-Apim-Subscription-Key", para a chave de assinatura do produto que está associado a essa API. A chave é preenchida automaticamente.
4. Pressione **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

## <a name="call-operation"> </a>Chamar uma operação no portal do desenvolvedor

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

## <a name="next-steps"> </a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Importar sua primeira API
> * Testar a API no Portal do Azure
> * Testar a API no Portal do desenvolvedor

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Criar e publicar um produto](api-management-howto-add-products.md)
