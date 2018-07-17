---
title: Importar e publicar sua primeira API no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como importar e publicar sua primeira API com o gerenciamento de API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 538977b9057a5699d61d6c2cc44209367e3550e2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38722840"
---
# <a name="import-and-publish-your-first-api"></a>Importar e publicar sua primeira API 

Este tutorial mostra como importar uma API de back-end da “especificação OpenAPI” residindo em http://conferenceapi.azurewebsites.net?format=json. Esta API de back-end é fornecida pela Microsoft e hospedada no Azure. 

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

    Você pode definir os valores da API durante a criação ou mais tarde, acessando a guia **Configurações**. O asterisco vermelho ao lado de um campo indica que ele é obrigatório.

    Use os valores da tabela abaixo para criar sua primeira API.

    |Configuração|Valor|DESCRIÇÃO|
    |---|---|---|
    |**Especificação OpenAPI**|http://conferenceapi.azurewebsites.net?format=json|Referencia o serviço que implementa a API. O gerenciamento de API envia as solicitações para esse endereço.|
    |**Nome de exibição**|*API de Conferência de Demonstração*|Se você pressionar Tab depois de inserir a URL do serviço, o APIM preencherá esse campo com base no que está no json. <br/>Esse nome é exibido no Portal do desenvolvedor.|
    |**Nome**|*demo-conference-api*|Fornece um nome exclusivo para a API. <br/>Se você pressionar Tab depois de inserir a URL do serviço, o APIM preencherá esse campo com base no que está no json.|
    |**Descrição**|Forneça uma descrição opcional da API.|Se você pressionar Tab depois de inserir a URL do serviço, o APIM preencherá esse campo com base no que está no json.|
    |**Esquema de URL**|*HTTPS*|Determina quais protocolos podem ser usados para acessar a API. |
    |**Sufixo da URL da API**|*conference*|O sufixo é acrescentado à URL base do serviço de gerenciamento de API. O Gerenciamento de API diferencia as APIs pelo sufixo e, portanto, o sufixo deve ser único para cada API para um editor específico.|
    |**Produtos**|*Ilimitado*|Os produtos são associações de uma ou mais APIs. É possível incluir várias APIs em um produto e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. <br/>Você publica a API associando-a a um produto (neste exemplo, *Ilimitado*). Para adicionar essa nova API a um produto, digite o nome do produto (você também pode fazer isso posteriormente, na página **Configurações**). Esta etapa pode ser repetida várias vezes para adicionar a API a vários produtos.<br/>Para obter acesso à API, os desenvolvedores devem, primeiro, inscrever-se em um produto. Com a assinatura, eles obtêm uma chave de assinatura que funciona para qualquer API no produto. <br/> Se você criou a instância do APIM, já é um administrador e, portanto, está inscrito em cada produto.<br/> Por padrão, cada instância de gerenciamento de API vem com dois produtos de exemplo: **Inicial** e **Ilimitado**. |
    |Versão desta API?||Para obter mais informações sobre o controle de versão, consulte [Publicar várias versões de sua API](api-management-get-started-publish-versions.md)|
    
    >[!NOTE]
    > Para publicar a API, você deve associá-la a um produto. É possível fazer isso na **página Configurações**.
    
3. Selecione **Criar**.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testar a nova API do APIM no portal do Azure

As operações podem ser chamadas diretamente do portal do Azure, o que oferece uma maneira fácil de exibir e testar as operações de uma API.  
1. Selecione a API que você criou na etapa anterior (na guia **APIs**).
2. Pressione a guia **Testar**.  ![Testar a API](./media/api-management-get-started/test-api.png)
3. Clique em **GetSpeakers**.
    A página exibe campos para parâmetros de consulta, nesse caso, não há nenhum, e cabeçalhos. Um dos cabeçalhos é "Ocp-Apim-Subscription-Key", para a chave de assinatura do produto que está associado a essa API. A chave é preenchida automaticamente.
4. Pressione **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

## <a name="call-operation"> </a>Chamar uma operação no portal do desenvolvedor

As operações também podem ser chamadas do **Portal do desenvolvedor** para testar APIs.

1. Navegue até o **portal do Desenvolvedor**.
![Portal do desenvolvedor](./media/api-management-get-started/developer-portal.png)

2. Selecione **APIS**, clique em **API de Conferência de Demonstração** e então em **GetSpeakers**.
    
    A página exibe campos para parâmetros de consulta, nesse caso, não há nenhum, e cabeçalhos. Um dos cabeçalhos é "Ocp-Apim-Subscription-Key", para a chave de assinatura do produto que está associado a essa API. Se você criou a instância do APIM, já é um administrador e, portanto, a chave é preenchida automaticamente.
3. Pressione **Experimentar**.
4. Pressione **Enviar**.
    
    Depois de uma operação ser invocada, o portal do desenvolvedor mostra as respostas.  

## <a name="next-steps"> </a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Importar sua primeira API
> * Testar a API no Portal do Azure
> * Testar a API no Portal do desenvolvedor

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Criar e publicar um produto](api-management-howto-add-products.md)
