---
title: Como vincular uma assinatura do Azure ao Azure AD B2C | Microsoft Docs
description: "Guia passo a passo para habilitar a cobrança de locatário do Azure AD B2C em uma assinatura do Azure."
services: active-directory-b2c
documentationcenter: dev-center-name
author: parakhj
manager: mtillman
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2017
ms.author: parja
ms.openlocfilehash: 063c00fe47be25b9359e80d71abfaf453c7a7074
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2017
---
# <a name="linking-an-azure-subscription-to-an-azure-ad-b2c-tenant"></a>Vinculando uma assinatura do Azure a um locatário Azure AD B2C

> [!IMPORTANT]
> Obtenha as informações mais recentes sobre o uso de cobrança e preços para Azure AD B2C na seguinte página: [Preços do Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

Os encargos de uso para o Azure AD B2C são cobrados de uma assinatura do Azure. Quando um locatário do Azure AD B2C é criado, o administrador de inquilinos precisa vincular explicitamente o locatário do Azure AD B2C a uma assinatura do Azure. Este artigo mostra como fazer isso.

> [!NOTE]
> Uma assinatura vinculada a um locatário do Azure AD B2C só pode ser usada para a cobrança do uso do Azure AD B2C. A assinatura não pode ser usada para adicionar outros serviços do Azure ou licenças do Office 365 *ao locatário do Azure AD B2C*.

 O link de assinatura é obtido com a criação de um “recurso” do Azure AD B2C na assinatura de destino do Azure. Vários “recursos” do Azure AD B2C podem ser vinculados a uma única assinatura do Azure juntamente com outros recursos do Azure (por exemplo, VMs, armazenamento de dados, Aplicativos Lógicos). Você pode ver todos os recursos na assinatura, indo para o locatário do Azure AD ao qual a assinatura está associada.

Uma assinatura do Azure válida é necessária para continuar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um locatário do Azure AD B2C

Você deve primeiro [criar um locatário do Azure AD B2C](active-directory-b2c-get-started.md) ao qual deseja vincular uma assinatura. Ignore esta etapa se você já criou um locatário do Azure AD B2C.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Abrir o portal do Azure no locatário do Azure AD que mostra a sua assinatura do Azure

Navegue até o locatário do Azure AD que mostra a sua assinatura do Azure. Abra o [portal do Azure](https://portal.azure.com) e alterne para o locatário do Azure AD que mostra a assinatura do Azure que você deseja usar.

![Alternando para o locatário do Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Localizar Azure AD B2C no Azure Marketplace

Clique no botão **Novo**. No campo **Pesquisar no Marketplace**, insira `B2C`.

![Adicionar o botão realçado e o texto AD B2C no campo Pesquisar o marketplace](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

Na lista de resultados, selecione **Azure AD B2C**.

![Azure AD B2C selecionado na lista de resultados](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Os detalhes sobre o Azure AD B2C são mostrados. Para começar a configurar seu novo locatário do Azure Active Directory B2C, clique no botão **Criar**.

Na tela de criação de recursos, selecione **Vincular um Azure AD B2C existente à minha assinatura do Azure**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Criar um recurso do Azure AD B2C na assinatura do Azure

Na caixa de diálogo de criação de recursos, selecione um locatário do Azure AD B2C na lista suspensa. Aparecerão todos os locatários para os quais você é o administrador global e aqueles que não estão vinculadas a uma assinatura.

O nome do recurso do Azure AD B2C será pré-selecionado para corresponder ao nome de domínio do locatário do Azure AD B2C.

Em Assinatura, selecione uma assinatura ativa do Azure que você administra.

Selecione um Grupo de Recursos e o local do Grupo de Recursos. Aqui, a seleção não tem impacto sobre o local, desempenho ou status de cobrança do locatário do Azure AD B2C.

![Criar recurso do B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Gerenciar seus recursos de locatário do Azure AD B2C

Depois que um recurso do Azure AD B2C é criado com êxito na assinatura do Azure, você verá um novo recurso do tipo "locatário do B2C" adicionado aos seus outros recursos do Azure.

Você pode usar esse recurso para:

- Navegar até a assinatura para revisar as informações de cobrança.
- Ir até seu locatário do Azure AD B2C
- Enviar uma solicitação de suporte
- Mover o recurso de locatário do Azure AD B2C para outra assinatura do Azure ou para outro Grupo de Recursos.

![Configurações de Recursos do B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="csp-subscriptions"></a>Assinaturas de CSP

No momento, um locatário do Azure AD B2C **não pode** ser vinculado a assinaturas de CSP.

### <a name="self-imposed-restrictions"></a>Restrições autoimpostas

Um usuário pode ter estabelecido uma restrição regional para a criação de recursos do Azure. Essa restrição pode impedir a criação de recursos do Azure AD B2C. Para atenuar, reduza essa restrição.

## <a name="next-steps"></a>Próximas etapas

Quando essas etapas forem concluídas para cada um dos seus locatários do Azure AD B2C, sua assinatura do Azure será cobrada de acordo com os detalhes do Azure Direct ou Enterprise Agreement.

Você pode revisar os detalhes de cobrança e uso dentro da assinatura do Azure selecionada. Você também pode rever os relatórios de uso detalhado do dia a dia usando a [API de Relatórios de Uso](active-directory-b2c-reference-usage-reporting-api.md).
