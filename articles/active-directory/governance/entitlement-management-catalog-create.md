---
title: Criar e gerenciar um catálogo no gerenciamento de direitos do AD do Azure (visualização) – Azure Active Directory
description: Saiba como criar um novo contêiner de recursos e pacotes de acesso no gerenciamento de direitos do Active Directory do Azure (visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d9220cd2162b4c8cb77c1e7abd0372052f5454
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541609"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Criar e gerenciar um catálogo no gerenciamento de direitos do AD do Azure (visualização)

> [!IMPORTANT]
> Gerenciamento de direitos do Active Directory (Azure AD) do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Criar um catálogo

Um catálogo é um contêiner de recursos e pacotes de acesso. Quando você deseja agrupar recursos relacionados e acessar os pacotes, você cria um catálogo. Pessoa que cria o catálogo se torna o proprietário do catálogo primeiro. Um proprietário do catálogo pode adicionar os proprietários de catálogo adicionais.

**Função de pré-requisito:** Usuário administrador ou criador de catálogo

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **catálogos**.

    ![Catálogos de gerenciamento de direitos no portal do Azure](./media/entitlement-management-catalog-create/catalogs.png)

1. Clique em **novo catálogo**.

1. Insira um nome exclusivo para o catálogo e forneça uma descrição.

    Os usuários verão essas informações em detalhes do acesso de um pacote.

1. Se você quiser que os pacotes de acesso nesse catálogo disponível para que os usuários solicitem assim que eles são criados, defina **Enabled** à **Sim**.

1. Se você quiser permitir que os usuários em diretórios externos selecionados para ser capaz de solicitar acesso pacotes nesse catálogo, defina **habilitada para usuários externos** à **Sim**.

    ![Novo painel de catálogo](./media/entitlement-management-catalog-create/new-catalog.png)

1. Clique em **criar** para criar o catálogo.

## <a name="add-resources-to-a-catalog"></a>Adicionar recursos a um catálogo

Para incluir recursos em um pacote de acesso, os recursos devem existir em um catálogo. Os tipos de recursos que você pode adicionar são grupos, aplicativos e sites do SharePoint Online.

**Função de pré-requisito:** Usuário administrador ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo que você deseja adicionar recursos ao.

1. No menu à esquerda, clique em **recursos**.

1. Clique em **adicionar recursos**.

1. Clique em um tipo de recurso: **Grupos**, **Applications**, ou **sites do SharePoint**.

    Se você for um criador de catálogo, você pode adicionar qualquer grupo do Office 365 ou o grupo de segurança de AD do Azure, que sua para seu catálogo. Se houver um grupo que você deseja atribuir aos usuários, mas você não possui o grupo, você precisará ter um administrador do usuário inclua esse grupo ao seu catálogo.

    Se você for um criador de catálogo, você pode adicionar qualquer aplicativo do enterprise do Azure AD você possui, incluindo aplicativos SaaS e seus próprios aplicativos federados ao Azure AD, para seu catálogo. Se houver um aplicativo que você deseja atribuir aos usuários, mas não possuem, você precisará ter um administrador de usuário adicionar esse aplicativo para seu catálogo. Depois que o aplicativo faz parte do catálogo, você pode selecionar qualquer uma das funções do aplicativo em um pacote de acesso.

1. Selecione um ou mais recursos do tipo que você deseja adicionar ao catálogo.

1. Quando terminar, clique em **adicionar**.

    Esses recursos podem ser incluídos em pacotes de acesso no catálogo.

## <a name="remove-resources-from-a-catalog"></a>Remover recursos de um catálogo

Você pode remover os recursos de um catálogo. Um recurso só pode ser removido de um catálogo se não estiver sendo usado em qualquer um dos pacotes de acesso do catálogo.

**Função de pré-requisito:** Usuário administrador ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo que você deseja remover os recursos do.

1. No menu à esquerda, clique em **recursos**.

1. Selecione os recursos que você deseja remover.

1. Clique em **remova** (ou clique no botão de reticências (**...** ) e, em seguida, clique em **remover recurso**).

## <a name="add-catalog-owners-or-access-package-managers"></a>Adicionar proprietários de catálogo ou acessar gerenciadores de pacotes

Se você deseja delegar o gerenciamento de catálogo ou os pacotes de acesso no catálogo, você pode adicionar proprietários de catálogo ou acessar gerenciadores de pacotes. Quem cria um catálogo se torna o proprietário do catálogo primeiro.

**Função de pré-requisito:** Usuário administrador ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo que você deseja adicionar os administradores.

1. No menu à esquerda, clique em **funções e os administradores**.

1. Clique em **adicionar proprietários** ou **adicionar gerenciadores de pacotes de acesso** para selecionar os membros para essas funções.

1. Clique em **selecionar** para adicionar esses membros.

## <a name="edit-a-catalog"></a>Editar um catálogo

Você pode editar o nome e descrição para um catálogo. Os usuários veem essas informações em detalhes do acesso de um pacote.

**Função de pré-requisito:** Usuário administrador ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo que você deseja editar.

1. Sobre o catálogo **visão geral** , clique em **editar**.

1. Edite o nome ou a descrição do catálogo.

1. Clique em **Salvar**.

## <a name="delete-a-catalog"></a>Excluir um catálogo

Você pode excluir um catálogo, mas somente se ele não tem quaisquer pacotes de acesso.

**Função de pré-requisito:** Usuário administrador ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo que você deseja excluir.

1. Sobre o catálogo **visão geral**, clique em **excluir**.

1. Na caixa de mensagem que é exibida, clique em **Sim**.

## <a name="next-steps"></a>Próximas etapas

- [Criar e gerenciar um pacote de acesso](entitlement-management-access-package-create.md)
