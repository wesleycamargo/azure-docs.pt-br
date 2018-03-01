---
title: Como criar e publicar um produto no Gerenciamento de API do Azure
description: Aprenda a criar e publicar produtos no Gerenciamento de API do Azure.
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
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: b9e3127a6b055a1fe013fa91714676a7c56686c5
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="create-and-publish-a-product"></a>Criar e publicar um produto  

No Gerenciamento de API, um produto contém uma ou mais APIs, bem como uma quota de uso e os termos de uso. Uma vez publicado o produto, os desenvolvedores podem assinar o produto e começar a usar as APIs dele.  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar e publicar um produto
> * Adicionar uma API ao produto

![produto adicionado](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>pré-requisitos

+ Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-and-publish-a-product"></a>Criar e publicar um produto

1. Clique em **Produtos** no menu à esquerda para exibir a página **Produtos**.
2. Clique em **+ Produto**.

    ![produto adicionado](media/api-management-howto-add-products/add-product.png)

    Quando você adicionar um produto, precisará fornecer as seguintes informações: 

    |NOME|DESCRIÇÃO|
    |---|---|
    |Nome de exibição|O nome que você deseja que seja mostrado no **Portal do Desenvolvedor**.|
    |NOME|Um nome descritivo do produto.|
    |DESCRIÇÃO|O campo **Descrição** permite que você forneça informações detalhadas sobre o produto, como sua finalidade, as APIs a que fornece acesso, entre outras informações úteis.|
    |Estado|Pressione **Publicado** se você deseja publicar o produto. Antes que as APIs de um produto possam ser chamadas, o produto precisa ser publicado. Por padrão, novos produtos não são publicados e ficam visíveis somente para o grupo **Administradores** .|
    |Requer aprovação|Marque **Requerer aprovação de assinatura** se desejar que um administrador revise e aceite ou rejeite as tentativas de assinatura para o produto. Se a caixa não estiver marcada, as tentativas de assinatura serão aprovadas automaticamente. |
    |Limite de contagem de assinaturas|Para limitar a contagem de várias assinaturas simultâneas, insira o limite de assinaturas. |
    |Termos legais|Você pode incluir os termos de uso para o produto que os assinantes devem aceitar a fim de usá-lo.|
    |APIs|Os produtos são associações de uma ou mais APIs. Você pode incluir várias APIs e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. <br/> Você pode adicionar uma API existente durante a criação do produto. Você pode adicionar uma API ao produto mais tarde, seja na página **Configurações** dos Produtos ou durante a criação de uma API.|<br/>Os Desenvolvedores devem primeiro se inscrever em um produto para obter acesso à API. Com a assinatura, eles obtêm uma chave de assinatura que funciona para qualquer API no produto.<br/> Se você criou a instância do APIM, já é um administrador e, portanto, está inscrito em cada produto por padrão.|

3. Clique em **Criar** para criar o novo produto.

### <a name="add-more-configurations"></a>Adicionar mais configurações

Você pode continuar a configurar o produto depois de salvá-lo escolhendo a guia **Configurações**. 

Exibir/adicionar assinantes ao produto na guia **Assinaturas**.

Defina a visibilidade de um produto para desenvolvedores ou convidados na guia **Controle de acesso**.

## <a name="add-apis"> </a>Adicionar APIs a um produto

Os produtos são associações de uma ou mais APIs. Você pode incluir várias APIs e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. Você pode adicionar uma API existente durante a criação do produto. Você pode adicionar uma API ao produto mais tarde, seja na página **Configurações** dos Produtos ou durante a criação de uma API.

Os Desenvolvedores devem primeiro se inscrever em um produto para obter acesso à API. Com a assinatura, eles obtêm uma chave de assinatura que funciona para qualquer API no produto. Se você criou a instância do APIM, já é um administrador e, portanto, está inscrito em cada produto por padrão.

### <a name="add-an-api-to-an-existing-product"></a>Adicionar uma API a um produto existente

1. Selecione um produto.
2. Selecione a guia APIs.
3. Clique em **+ API**.
4. Escolha uma API e clique em **Criar**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar e publicar um produto
> * Adicionar uma API ao produto

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Criar API em branco e simular respostas de API](mock-api-responses.md)
