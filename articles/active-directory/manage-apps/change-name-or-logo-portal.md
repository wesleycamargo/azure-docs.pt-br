---
title: Alterar o nome ou logotipo de um aplicativo empresarial no Azure Active Directory | Microsoft Docs
description: Como alterar o nome ou logotipo de um aplicativo empresarial personalizado no Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbebc528227ebb06bd66e31b802dd4fd618d6a99
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718763"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Alterar o nome ou logotipo de um aplicativo empresarial no Azure Active Directory

É fácil alterar o nome ou logotipo de um aplicativo empresarial personalizado no Azure AD (Azure Active Directory). Você deve ter as permissões apropriadas para fazer essas alterações, e você deve ser o criador do aplicativo personalizado.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Como alterar nome ou logotipo de um aplicativo empresarial?

1. Entrar para o [portal do Azure Active Directory](https://aad.portal.azure.com/) com uma conta que seja um administrador global do diretório. O **Centro de administração do Azure Active Directory** página será exibida.
2. No painel esquerdo, selecione **Aplicativos Empresariais**. É exibida a lista de seus aplicativos empresariais.
3. Selecione um aplicativo. A página de visão geral do aplicativo é exibida.
4. No painel de visão geral do aplicativo, sob o **Manage** título, selecione **propriedades**. O **propriedades** página será exibida.
5. Se você quiser alterar o nome, selecione o **nome** caixa, digite o novo nome e pressione **Enter**.
6. Se você quiser alterar o logotipo, localize o **logotipo** campo e, em seguida, selecione o ícone de pasta ao lado a **selecionar um arquivo** caixa, que está abaixo da imagem de logotipo do aplicativo atual.

   ![Seleção do comando de propriedades](./media/change-name-or-logo-portal/change-logo.png)

   Caso contrário, se você não estiver alterando o logotipo, vá para a etapa 8.
7. No seletor de arquivo, selecione o arquivo desejado como o novo logotipo. O nome do arquivo aparece na caixa abaixo da imagem de logotipo atual.

   > [!NOTE]
   > Azure exige que a imagem do logotipo para ser um arquivo PNG e aplica limites no tamanho do arquivo, altura e largura.
8. Clique em **Salvar**. Se você escolher um novo logotipo, o **logotipo** alterações na imagem para refletir o novo arquivo de logotipo do campo.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Exibir grupos e membros de sua organização no Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](assign-user-or-group-access-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
* [Desabilitar as entradas de usuário em um aplicativo empresarial](disable-user-sign-in-portal.md)
