---
title: Importar um aplicativo do Azure Functions como uma API no portal do Azure | Microsoft Docs
description: Este tutorial mostra como usar o Gerenciamento de API do Azure para importar um aplicativo do Azure Functions como uma API.
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
ms.openlocfilehash: 670fa58de7155028b0f72f1f819b9f269e07b9eb
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239045"
---
# <a name="import-an-azure-functions-app-as-an-api"></a>Importar um aplicativo do Azure Functions como uma API

Este artigo mostra como importar um aplicativo do Azure Functions como uma API. O artigo também mostra como testar a API do Gerenciamento de API do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Importar um aplicativo do Functions como uma API
> * Testar a API no Portal do Azure
> * Testar a API no portal do desenvolvedor

## <a name="prerequisites"></a>Pré-requisitos

+ Conclua o início rápido [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Verifique se que você tem um aplicativo do Azure Functions em sua assinatura. Para obter mais informações, consulte [Criar um aplicativo do Functions](../azure-functions/functions-create-first-azure-function.md#create-a-function-app).
+ [Crie definição de OpenAPI](../azure-functions/functions-openapi-definition.md) de seu aplicativo do Azure Functions.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importar e publicar uma API de back-end

1. Selecione **APIs** em **GERENCIAMENTO DE API**.
2. Na lista **Adicionar nova API**, selecione **Aplicativo do Functions**.

    ![Aplicativo do Functions](./media/import-function-app-as-api/function-app-api.png)
3. Pressione **Procurar** para ver a lista de aplicativos do Functions em sua assinatura.
4. Selecione o aplicativo. O Gerenciamento de API localiza, busca e importa o swagger associado ao aplicativo selecionado. 
5. Adicione um sufixo da URL da API. O sufixo é um nome que identifica a API específica messa instância do Gerenciamento de API. O sufixo deve ser exclusivo nessa instância do Gerenciamento de API.
6. Publica a API associando-a a um produto. Nesse caso, o produto **Ilimitado** é usado. Se você deseja que a API seja publicada e fique disponível para desenvolvedores, adicione-a a um produto. Você pode adicionar a API a um produto quando criá-la ou pode adicioná-la mais tarde.

    Os produtos são associações de uma ou mais APIs. Você pode incluir várias APIs e oferecê-las aos desenvolvedores no portal do desenvolvedor. Os Desenvolvedores devem primeiro se inscrever em um produto para obter acesso à API. Quando um desenvolvedor assina, obtém uma chave de assinatura que é boa para qualquer API no produto. Quem cria a instância do Gerenciamento de API se torna um administrador. Por padrão, os administradores assinam todos os produtos.

    Por padrão, cada instância de Gerenciamento de API é fornecida com dois produtos função Web:

    * **Inicial**
    * **Ilimitado**   
7. Selecione **Criar**.

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Preencher as chaves do Azure Functions no Gerenciamento de API do Azure

Se os aplicativos do Azure Functions importados estiverem protegidos por chaves, o Gerenciamento de API criará *valores nomeados* para as chaves automaticamente. O Gerenciamento de API não preenche as entradas com segredos. Conclua as etapas abaixo para cada entrada:  

1. Selecione a guia **Valores nomeados** na instância do Gerenciamento de API.
2. Selecione uma entrada e selecione **Mostrar valor** na barra lateral.

    ![Valores nomeados](./media/import-function-app-as-api/apim-named-values.png)

3. Se o texto exibido na caixa **Valor** é semelhante ao **código do \<nome do Azure Functions\>**, vá para **Aplicativos do Functions**e vá para **Functions**.
4. Selecione **Gerenciar**e copie a chave relevante com base no método de autenticação do aplicativo.

    ![Aplicativo do Functions – Copiar chaves](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. Cole a chave na caixa **Valor** e selecione **Salvar**.

    ![Aplicativo do Functions – Colar valores de chave](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-api-management-api-in-the-azure-portal"></a>Testar a nova API do Gerenciamento de API no portal do Azure

Você pode chamar as operações diretamente do portal do Azure. Usar o portal do Azure é uma maneira conveniente de exibir e testar as operações de uma API.  

1. Selecione a API criada na seção anterior.
2. Selecione a guia **Testar**.
3. Selecione uma operação.

    A página exibe os campos dos parâmetros de consulta e os campos dos cabeçalhos. Um dos cabeçalhos é **Ocp-Apim-Subscription-Key**, para a chave de assinatura do produto que está associada a essa API. Se você criou a instância do Gerenciamento de API, já é um administrador e, portanto, a chave é preenchida automaticamente. 
4. Selecione **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

## <a name="call-operation"></a>Chamar uma operação no portal do desenvolvedor

Você também pode chamar as operações do Portal do desenvolvedor para testar APIs. 

1. Selecione a API que você criou na etapa [Importar e publicar uma API de back-end](#create-api).
2. Selecione **Portal do desenvolvedor**.

    O site do portal do desenvolvedor é aberto.
3. Selecione a **API** que você criou.
4. Selecione a operação que deseja testar.
5. Selecione **Experimentar**.
6. Selecione **Enviar**.
    
    Após invocar uma operação, o portal do desenvolvedor exibe o **Status de resposta**, os **Cabeçalhos de resposta** e o **Conteúdo de resposta**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transformar e proteger uma API publicada](transform-api.md)