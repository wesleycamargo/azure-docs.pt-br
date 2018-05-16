---
title: Publicar versões da sua API usando o Gerenciamento de API do Azure | Microsoft Docs
description: Execute as etapas deste tutorial para saber como publicar várias versões no Gerenciamento de API.
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
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 1cbe63184578f7d1e72992577a11c58b9b83a002
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="publish-multiple-versions-of-your-api"></a>Publicar várias versões da sua API 

Algumas vezes, é impraticável fazer com que todos os chamadores de sua API usem exatamente a mesma versão. Quando os chamadores quiserem atualizar para uma versão posterior, eles vão querer ser capazes de fazer isso usando uma abordagem fácil de entender. É possível fazer isso usando **versões** no Gerenciamento de API do Azure. Para obter mais informações, consulte [Versões e revisões](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar uma nova versão a uma API existente
> * Escolher um esquema de versão
> * Adicionar a versão a um produto
> * Procurar a versão no portal do desenvolvedor

![Versão mostrada no portal do desenvolvedor](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>pré-requisitos

* Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
* Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

## <a name="add-a-new-version"></a>Adicionar uma nova versão

![Menu de contexto da API - adicionar versão](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. Selecione **API de Conferência** na lista de APIs.
2. Selecione o menu de contexto (**...**) ao lado dela.
3. Selecione **+ Adicionar Versão**.

    > [!TIP]
    > As versões também podem ser habilitadas quando você cria uma nova API - selecione **Versão desta API?** na tela **Adicionar API**.

## <a name="choose-a-versioning-scheme"></a>Escolher um esquema de controle de versão

O Gerenciamento de API do Azure permite que você escolha a maneira com a qual você permite que chamadores especifiquem qual versão de sua API eles desejam. Especifique qual versão de API usar selecionando um **esquema de controle de versão**. Esse esquema pode ser **caminho, cabeçalho ou cadeia de consulta**. No exemplo a seguir, o caminho é usado para selecionar o esquema de controle de versão.

![Adicionar a tela de versão](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. Deixe **caminho** selecionado como seu **esquema de controle de versão**.
2. Adicione **v1** como seu **identificador de versão**.

    > [!TIP]
    > Se você selecionar **cabeçalho** ou **cadeia de consulta** como um esquema de controle de versão, é necessário fornecer um valor adicional – o nome do cabeçalho ou parâmetro de cadeia de consulta.

3. Forneça uma descrição, se quiser.
4. Selecione **Criar** para configurar sua nova versão.
5. Em **API de Conferência Grande** na Lista de APIs, você pode ver duas APIs distintas - **Original** e **v1**.

    ![Versões listadas em uma API no portal do Azure](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Se você adicionar uma versão a uma API sem controle de versão, uma **Original** será sempre criada para você - respondendo na URL padrão. Isso garante que os chamadores existentes não serão quebrados pelo processo de adição de uma versão. Se você criar uma nova API com versões habilitadas no início, uma Original não será criada.

6. Agora você pode editar e configurar **v1** como uma API separada da **Original**. As alterações em uma versão não afetam a outra.

## <a name="add-the-version-to-a-product"></a>Adicionar a versão a um produto

Para os chamadores verem a nova versão, ela deve ser adicionada a um **produto**.

1. Selecione **Produtos** na página de modelo de implantação clássica.
2. Selecione **Ilimitado**.
3. Selecione **APIs**.
4. Selecione **Adicionar**.
5. Selecione **API de Conferência, Versão v1**.
6. Navegue até a página de gerenciamento de serviço e selecione **APIs**.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Procurar a versão no portal do desenvolvedor

1. Selecione **Portal do Desenvolvedor** no menu superior.
2. Selecione **APIs**, observe que **API de Conferência** mostra as versões **Original** e **v1**.
3. Selecione **v1**.
4. Observe a **URL Se solicitação** da primeira operação na lista. Ela mostra que o caminho da URL da API inclui **v1**.

    ![Menu de contexto da API - adicionar versão](media/api-management-getstarted-publish-versions/developer_portal.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar uma nova versão a uma API existente
> * Escolher um esquema de versão 
> * Adicionar a versão a um produto
> * Procurar a versão no portal do desenvolvedor

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Atualizar e dimensionar](upgrade-and-scale.md)