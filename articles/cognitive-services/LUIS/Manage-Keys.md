---
title: Gerenciar suas chaves no LUIS | Microsoft Docs
description: Use o LUIS (Reconhecimento vocal) para gerenciar sua API programática, ponto de extremidade e chaves externas.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: 8e6e363649a0bdab5525de7b8e7abe9a53d14573
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266015"
---
# <a name="manage-your-luis-keys"></a>Gerenciar suas chaves LUIS
Uma chave permite criar e publicar seu aplicativo LUIS ou consultar seu ponto de extremidade. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>Principais conceitos
Confira [Keys in LUIS](luis-concept-keys.md) (Chaves no LUIS) para entender os conceitos de autoria e de chave do ponto de extremidade LUIS.

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>Atribuir chave do ponto de extremidade
Na página **Publicar aplicativo**, já há uma chave na tabela **Recursos e chaves**. Essa é a chave de criação (início). 

1. Crie uma chave LUIS no [portal do Azure](https://portal.azure.com). Para obter mais instruções, confira [Creating a subscription key using Azure](luis-how-to-azure-subscription.md) (Criando uma chave de assinatura usando o Azure).
 
2. Para adicionar a chave LUIS criada na etapa anterior, clique no botão **Adicionar chave** para abrir a caixa de diálogo **Atribuir uma chave ao seu aplicativo**. 

    ![Atribuir uma chave ao seu aplicativo](./media/luis-manage-keys/assign-key.png)
3. Selecione um Locatário na caixa de diálogo. 
 
    > [!Note]
    > No Azure, um locatário representa a ID do Azure Active Directory do cliente ou da organização associada a um serviço. Se você já se inscreveu anteriormente para uma assinatura do Azure com sua conta Microsoft individual, você já tem um locatário! Quando você faz logon no [portal do Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant), é automaticamente conectado ao seu locatário padrão. Você é livre para usar esse locatário, mas você talvez queira criar uma conta de administrador organizacional.

4. Escolha a assinatura do Azure associada à chave do Azure LUIS que você deseja adicionar.

5. Selecione a conta do Azure LUIS. A região da conta é exibida em parênteses. 

    ![Pressione a tecla](./media/luis-manage-keys/assign-key-filled-out.png)

6. Após atribuir essa chave do ponto de extremidade, use-a em todas as consultas de ponto de extremidade. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>Regiões de publicação
Saiba mais sobre [regiões](luis-reference-regions.md) de publicação, incluindo a publicação na [Europa](luis-reference-regions.md#publishing-to-europe) e na [Austrália](luis-reference-regions.md#publishing-to-australia). Regiões de publicação são diferentes de regiões de criação. Certifique-se de criar um aplicativo na região de criação correspondente à região de publicação desejada.

## <a name="unassign-key"></a>Cancelar atribuição da chave

* Na **lista Recursos e chaves**, clique no ícone de lixeira ao lado da entidade cuja atribuição você deseja cancelar. Em seguida, clique em **OK** na mensagem de confirmação para confirmar a exclusão.
 
    ![Cancelar a atribuição da entidade](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> Cancelar a atribuição da chave LUIS não a exclui da sua assinatura do Azure.

## <a name="next-steps"></a>Próximas etapas

Use a chave para publicar seu aplicativo na página **Publicar aplicativo**. Para obter instruções sobre a publicação, confira [Publicar aplicativo](PublishApp.md).

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website