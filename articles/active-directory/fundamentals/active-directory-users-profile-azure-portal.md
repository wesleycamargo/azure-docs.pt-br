---
title: Adicionar ou atualizar as informações do perfil de um usuário no Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar informações a um perfil de usuário no Azure Active Directory, incluindo detalhes de uma imagem e o trabalho.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.openlocfilehash: df0f4fac471192c7e8745c07423857ea3c3d906b
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450840"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Como adicionar ou atualizar as informações do perfil de um usuário usando o Azure Active Directory
Adicione informações de perfil de usuário, incluindo uma imagem do perfil, informações específicas do trabalho e algumas configurações usando o Azure AD (Azure Active Directory). Para obter mais informações sobre como adicionar novos usuários, consulte [Como adicionar ou excluir usuários no Azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Adicionar ou alterar informações do perfil
Como você verá, há mais informações disponíveis no perfil de um usuário do que você pode adicionar durante a criação do usuário. Todas essas informações adicionais são opcionais e podem ser adicionadas conforme a necessidade da sua organização.

## <a name="to-add-or-change-profile-information"></a>Para adicionar ou as alterar informações do perfil
1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador global ou administrador de usuários para o diretório.

2. Selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, selecione um usuário. Por exemplo, _Alain Charon_.

    A página **Alain Charon - Perfil**  é exibida.

    ![Página do perfil do usuário, incluindo informações editáveis](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Selecione **Editar** para adicionar ou atualizar as informações incluídas em cada uma das seções disponíveis.

    ![Página de perfil do usuário, mostrando as áreas editáveis](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Imagem do perfil.** Selecione uma imagem em miniatura para a conta do usuário. Esta imagem aparece no Azure Active Directory e nas páginas pessoais do usuário, como a página myapps.microsoft.com.

    - **Identidade.** Adicione qualquer informação relacionada à conta, como um sobrenome ou um nome de usuário alterado. 

    - **Informações do trabalho.** Adicione qualquer informação relacionada ao trabalho como cargo, departamento ou gerente do usuário.

    - **Configurações.** Decida se o usuário pode entrar no locatário do Azure Active Directory. Você também pode especificar a localização global do usuário.

    - **Informações de contato.** Adicione quaisquer informações de contato relevantes para o usuário. Por exemplo, um endereço ou um número de celular.

    - **Informações de contato de autenticação.** Verifique essas informações para certificar-se de que há um número de telefone e um endereço de email ativos para o usuário. Essas informações são usadas pelo Azure Active Directory para garantir que o usuário seja realmente o usuário durante a entrada. As informações de contato de autenticação somente podem ser atualizadas por um administrador global.

4. Clique em **Salvar**.

    Todas as alterações são salvas para o usuário.

    >[!Note]
    >Você deve usar o Windows Server Active Directory para atualizar a identidade, as informações de contato ou as informações do trabalho para os usuários cuja fonte de autoridade é o Windows Server Active Directory. Depois de concluir sua atualização, você deve aguardar a conclusão do próximo ciclo de sincronização antes de ver as alterações.

## <a name="next-steps"></a>Próximas etapas
Após atualizar os perfis dos usuários, você poderá executar os seguintes processos básicos:

- [Adicionar ou excluir usuários](add-users-azure-active-directory.md)

- [Atribuir funções a usuários](active-directory-users-assign-role-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

Ou você pode executar outras tarefas de gerenciamento de usuários como atribuir delegados, usar políticas e compartilhar contas de usuários. Para obter mais informações sobre outras ações disponíveis, consulte [documentação de gerenciamento de usuário do Azure Active Directory](../users-groups-roles/index.yml).
