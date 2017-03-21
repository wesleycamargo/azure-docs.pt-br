---
title: "Criar uma conta de APIs de Serviços Cognitivos no portal do Azure | Microsoft Docs"
description: "Como criar uma conta de APIs de Serviços Cognitivos da Microsoft no portal do Azure."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: garye;gibattag
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: a19f56f0b87d992e445034989bd35a827a72f367
ms.lasthandoff: 03/03/2017


---

# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Criar uma conta de APIs de Serviços Cognitivos no Portal do Azure

Para usar APIs de Serviços Cognitivos da Microsoft, primeiro você precisa criar uma conta no portal do Azure.

1. Entre no [Portal do Azure](http://portal.azure.com).

2. Clique em **+ NOVO**.

3. Selecione **Inteligência + Análise** e **APIs de Serviços Cognitivos (visualização)**.

    ![Selecionar APIs de Serviços Cognitivos](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. Na página **Criar**, forneça as seguintes informações:

    -   **Nome da Conta:** nome da conta. Recomendamos o uso de um nome descritivo, por exemplo *AFaceAPIAccount*.

    -   **Assinatura:** selecione uma das assinaturas do Azure disponíveis nas qual você tem, no mínimo, permissões de Colaborador.

    -   **Tipo de API:** escolha a API de Serviços Cognitivos que você deseja usar. Para obter mais informações sobre as várias APIs de Serviços Cognitivos disponíveis, consulte o site [Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/).

    ![Selecionar tipo de API](media/cognitive-services-apis-create-account/list-of-apis.png)

    -   **Tipo de preço:** o custo da conta dos Serviços Cognitivos depende do uso real e das opções escolhidas. Para obter mais informações sobre o preço de cada API, consulte as [páginas de preços](https://azure.microsoft.com/pricing/details/cognitive-services/).

    -   **Grupo de Recursos:** um grupo de recursos é uma coleção de recursos que compartilham o mesmo ciclo de vida, permissões e políticas. Para saber mais sobre Grupos de Recursos, consulte [Gerenciar recursos do Azure pelo portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    -   **Local do Grupo de Recursos:** isso é necessário apenas se a API selecionada for global (não limitado a um local). No entanto, se a API for global e não estiver limitada a uma localização, será necessário especificar uma localização para o grupo de recursos em que os metadados associados à conta da API de Serviços Cognitivos residirão. Essa localização não afetará a disponibilidade do tempo de execução da conta. Para saber mais sobre grupos de recursos, consulte [Gerenciar recursos do Azure pelo portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    -   **Configuração de API:** por padrão, a criação de conta estará desabilitada até que o [Administrador da Conta do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles) a habilite explicitamente.

        Essa alteração de configuração se aplicará somente ao tipo de API selecionado no momento e à localização ou à localização do Grupo de recursos no painel à esquerda.

        ![Criar conta de APIs de Serviços Cognitivos](media/cognitive-services-apis-create-account/create-account.png)

        > [!NOTE]
        > Se você receber uma notificação informando que a configuração de atualização falhou, isso indica que você não está conectado como um [Administrador da Conta](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator#types-of-azure-admin-accounts). O Administrador da Conta deve seguir as etapas anteriores para habilitar a criação.
        >
        > ![Mensagem “A configuração de atualização falhou”](./media/cognitive-services-apis-create-account/updatefailed.png)
        
        Em alguns casos, o Administrador da Conta pode não ter acesso à assinatura. Nesse caso, solicite que o Administrador de Serviços siga as etapas do documento [Adicionar ou alterar funções de administrador do Azure que gerenciam a assinatura ou o serviço](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).
        
        Para encontrar o Administrador da Conta ou o Administrador de Serviços da assinatura, selecione sua assinatura no [portal do Azure](https://portal.azure.com) e, em seguida, __Propriedades__. As informações do __Administrador da Conta__ e o do __Administrador de Serviços__ são exibidas na parte inferior da folha de propriedades.
        
        ![Propriedades da assinatura](./media/cognitive-services-apis-create-account/subscription-properties.png)
    
    A Microsoft poderá usar os dados enviados por você aos Serviços Cognitivos para melhorar os produtos e serviços Microsoft. Para obter mais informações, consulte a [seção Serviços Cognitivos da Microsoft](http://www.microsoft.com/Licensing/product-licensing/products.aspx)
    nos Termos de Serviços Online.

5. Para fixar a conta no painel do portal do Azure, clique em **Fixar no Painel**.

6. Clique em **Criar** para criar a conta.

Depois que a conta dos Serviços Cognitivos for implantada com êxito, clique no bloco do painel para exibir as informações da conta.

É possível usar a **URL do Ponto de Extremidade** na seção **Visão Geral** e as chaves na seção **Chaves** para começar a fazer chamadas à API nos aplicativos.

![Exibir informações da conta](media/cognitive-services-apis-create-account/display-account.png)

![Exibir chaves da conta](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre todos os Serviços Cognitivos da Microsoft disponíveis, consulte [Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/).

- Para obter guias de início rápido sobre o uso de algumas APIs de Serviços Cognitivos de exemplo, consulte:
    - [Introdução às APIs de Análise de Texto para detectar sentimento, frases-chave, tópicos e idioma](cognitive-services-text-analytics-quick-start.md)
    - [Guia de início rápido da API de Recomendações dos Serviços Cognitivos](cognitive-services-recommendations-quick-start.md)
