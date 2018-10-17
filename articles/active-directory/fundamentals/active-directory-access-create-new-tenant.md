---
title: Início rápido para acessar o Azure Active Directory e criar um novo locatário | Microsoft Docs
description: Início rápido com as etapas para localizar o Azure Active Directory e como criar um novo locatário para a organização.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: lizross
custom: it-pro
ms.openlocfilehash: 8ef68c8afcf61a1a11c341a679443071aece9812
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46362996"
---
# <a name="quickstart-access-azure-active-directory-to-create-a-new-tenant"></a>Início rápido: acessar o Azure Active Directory para criar um novo locatário
É possível realizar todas as tarefas administrativas usando o portal do Azure AD (Azure Active Directory), incluindo a criação de um novo locatário para a organização. 

Neste início rápido, você aprenderá a acessar o portal do Azure e o Azure Active Directory e a criar um locatário básico para a organização.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, você precisará:

- Verificar se a organização tem uma licença válida do Azure AD.

- Verificar se você é um administrador global.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure
Entre no [portal do Azure](https://portal.azure.com/) da organização usando uma conta de administrador global.

![Tela do portal do Azure](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

## <a name="create-a-new-tenant-for-your-organization"></a>Criar um novo locatário para a organização
Após entrar no portal do Azure, é possível criar um novo locatário para a organização. O novo locatário representa a organização e ajuda a gerenciar uma instância específica dos serviços em nuvem da Microsoft para usuários internos e externos.

### <a name="to-create-a-new-tenant"></a>Criar um novo locatário
1. Selecione **Azure Active Directory**, **Criar recursos**, **Identidade** e **Azure Active Directory**.

    A página **Criar diretório** será exibida.

    ![Página "Criar" do Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

2.  Na página **Criar diretório**, insira as seguintes informações:
    
    - Digite _Contoso_ na caixa **Nome da organização**.

    - Digite _Contoso_ na caixa **Nome de domínio inicial**.

    - Mantenha a opção _Estados Unidos_ na caixa **País ou região** box.

3. Selecione **Criar**.

O novo locatário será criado com o domínio contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Limpar recursos
Se você não quiser continuar usando este aplicativo, poderá excluir o locatário seguindo estas etapas:

- Selecione **Azure Active Directory** e na página **Contoso – Visão geral**, selecione **Excluir diretório**.

    O locatário e suas informações associadas serão excluídos.

    ![Criar página do diretório](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Próximas etapas
- Para alterar ou adicionar nomes de domínio, confira [Como adicionar um nome de domínio personalizado ao Azure Active Directory](add-custom-domain.md)

- Para adicionar usuários, confira [Adicionar ou excluir um novo usuário](add-users-azure-active-directory.md)

- Para adicionar grupos e membros, confira [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- Saiba mais sobre o [acesso baseado em função usando o Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md) e o [Acesso condicional](../../role-based-access-control/conditional-access-azure-management.md) para ajudar a gerenciar o acesso a aplicativos e recursos da organização.
