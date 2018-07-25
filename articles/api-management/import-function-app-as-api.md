---
title: Importar um aplicativo de funções como uma API com o Portal do Azure | Microsoft Docs
description: Este tutorial mostra como usar o APIM (Gerenciamento de API) para importar um Aplicativo de funções como uma API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/15/2018
ms.author: apimpm
ms.openlocfilehash: 0ee83446bb08e66c7f325bdd5585b8cc0484a74e
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090921"
---
# <a name="import-a-function-app-as-an-api"></a>Importar um aplicativo de funções como uma API

Este artigo mostra como importar um aplicativo de funções como uma API. O artigo também mostra como testar a API do APIM.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Importar um aplicativo de funções como uma API
> * Testar a API no Portal do Azure
> * Testar a API no Portal do desenvolvedor

## <a name="prerequisites"></a>Pré-requisitos

+ Conclua o seguinte guia de início rápido: [Criar uma nova instância do serviço de Gerenciamento de API do Azure](get-started-create-service-instance.md)
+ Verifique se há um Aplicativo Azure Function App em sua assinatura. Para obter mais informações, consulte [Criar um aplicativo de funções](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)
+ [Criar definição de OpenAPI](../azure-functions/functions-openapi-definition.md) de seu Azure Function App

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importar e publicar uma API de back-end

1. Selecione **APIs** em **GERENCIAMENTO DE API**.
2. Selecione **Aplicativo de Funções** na lista **Adicionar uma nova API**.

    ![Aplicativo de funções](./media/import-function-app-as-api/function-app-api.png)
3. Pressione **Procurar** para ver a lista de aplicativos de funções em sua assinatura.
4. Selecione o aplicativo. O APIM localiza o swagger associado ao aplicativo selecionado, busca-o e importa-o. 
5. Adicione um sufixo da URL da API. O sufixo é um nome que identifica essa API específica nesta instância do APIM. Ele deve ser exclusivo nesta instância de APIM.
6. Publica a API associando-a a um produto. Nesse caso, o produto "*Ilimitado*" é usado.  Se você deseja que a API seja publicada e fique disponível para os desenvolvedores, adicione-a a um produto. Você pode fazer isso durante a criação da API ou configurá-lo mais tarde.

    Os produtos são associações de uma ou mais APIs. Você pode incluir várias APIs e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. Primeiro, os desenvolvedores devem assinar um produto para obter acesso à API. Com a assinatura, eles obtêm uma chave de assinatura que funciona para qualquer API no produto. Se você criou a instância do APIM, já é um administrador e, portanto, está inscrito em cada produto por padrão.

    Por padrão, cada instância de Gerenciamento de API é fornecida com dois produtos função Web:

    * **Inicial**
    * **Ilimitado**   
7. Selecione **Criar**.

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Preencher as chaves do Azure Functions no Gerenciamento de API do Azure

Se os Azure Functions importados estiverem protegidos por chaves, o Gerenciamento de API do Azure criará automaticamente **valores nomeados** para eles, mas não preencherá as entradas com os segredos. Para cada entrada, você precisará executar as etapas abaixo.  

1. Navegue até a guia **Valores nomeados** na instância do Gerenciamento de API.
2. Clique em uma entrada e pressione **Mostrar valor** na barra lateral.

    ![Valores nomeados](./media/import-function-app-as-api/apim-named-values.png)

3. Se o conteúdo é semelhante ao *código para {nome do Azure Function}*, vá para o Azure Functions App e navegue até seu Azure Function.
4. Acesse a seção **Gerenciar** do Azure Function e copie a chave relevante, com base no método de autenticação do Azure Function.

    ![Aplicativo de funções](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. Cole a chave na caixa de texto de **Valores nomeados** e clique em **Salvar**.

    ![Aplicativo de funções](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testar a nova API do APIM no portal do Azure

As operações podem ser chamadas diretamente do portal do Azure, o que oferece uma maneira fácil de exibir e testar as operações de uma API.  

1. Selecione a API que você criou na etapa anterior.
2. Pressione a guia **Testar**.
3. Selecione alguma operação.

    A página exibe os campos dos parâmetros de consulta e os campos dos cabeçalhos. Um dos cabeçalhos é "Ocp-Apim-Subscription-Key", para a chave de assinatura do produto que está associado a essa API. Se você criou a instância do APIM, já é um administrador e, portanto, a chave é preenchida automaticamente. 
1. Pressione **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

## <a name="call-operation"> </a>Chamar uma operação no portal do desenvolvedor

As operações também podem ser chamadas do **Portal do desenvolvedor** para testar APIs. 

1. Selecione a API que você criou na etapa "Importar e publicar uma API de back-end".
2. Pressione **Portal do desenvolvedor**.

    O site "Portal do desenvolvedor" é aberto.
3. Selecione a **API** que você criou.
4. Clique na operação que deseja testar.
5. Pressione **Experimentar**.
6. Pressione **Enviar**.
    
    Após invocar uma operação, o portal do desenvolvedor exibe o **Status de resposta**, os **Cabeçalhos de resposta** e o **Conteúdo de resposta**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transformar e proteger uma API publicada](transform-api.md)