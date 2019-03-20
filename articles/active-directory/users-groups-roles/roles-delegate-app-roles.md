---
title: Delegar funções de administrador do aplicativo – Azure Active Directory | Microsoft Docs
description: Gerenciamento de acesso do aplicativo delegando funções para conceder direitos de permissões no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aba12a2bca882b5f0e6e95d8e4f6014b318a12e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119710"
---
# <a name="delegate-app-administrator-roles-in-azure-active-directory"></a>Delegar funções de administrador do aplicativo no Azure Active Directory

 O Azure AD permite delegar o gerenciamento de acesso do aplicativo a um conjunto de funções administrativas internas. Além de reduzir a sobrecarga do administrador global, o delegar privilégios especializados para gerenciar as tarefas de acesso do aplicativo pode melhorar sua postura de segurança e reduzir o potencial de acesso não autorizado. Problemas de delegação e diretrizes gerais são discutidos em [Delegar a administração no Azure Active Directory](roles-concept-delegation.md).

## <a name="delegate-app-administration"></a>Delegar a administração de aplicativos

As funções a seguir concedem permissões para gerenciar registros de aplicativo, configurações de logon único, atribuições de usuário e de grupo e concorde com as permissões delegadas e permissões do aplicativo (exceto o Microsoft Graph e o Azure AD Graph). A única diferença é que a função de administrador do aplicativo também concede permissões para gerenciar as configurações do Proxy de Aplicativo. Nenhuma dessas funções fornece a capacidade de gerenciar configurações de Acesso Condicional.
> [!IMPORTANT]
> Os usuários a quem essa função é atribuída podem adicionar credenciais a um aplicativo e usar essas credenciais para representar a identidade do aplicativo. Essa representação da identidade do aplicativo pode ser uma elevação de privilégio sobre o que o usuário pode fazer por meio de suas outras atribuições de função no Azure AD. Um usuário a quem essa função é atribuída potencialmente pode criar ou atualizar os usuários ou outros objetos ao representar o aplicativo.

Para conceder a capacidade de gerenciar o acesso de aplicativo no portal do Azure:

1. Entre no [locatário do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta qualificada para a função de Administrador Global no locatário.
2. Quando você tiver permissões suficientes, abra a [página Funções e administradores](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators).
3. Abra uma das funções a seguir para ver as respectivas atribuições de membro:
   * **Administrador de aplicativo**
   * **Administrador de aplicativos de nuvem**
4. Na página **Membros** para a função, selecione **Adicionar membro**.
5. Selecione um ou mais membros aos quais adicionar a função. <!--Members can be users or groups.-->

Você pode exibir a descrição para essas funções em [Funções disponíveis](directory-assign-admin-roles.md#available-roles).

## <a name="delegate-app-registration"></a>Delegar o registro do aplicativo

Por padrão, todos os usuários podem criar registros de aplicativo, mas é possível seletivamente conceder permissão para criar registros de aplicativo ou permissão para dar consentimento à autorização de um aplicativo.

1. Entre no [locatário do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta qualificada para a função de Administrador Global no locatário.
2. Quando você tiver obtido permissões suficientes, defina uma ou ambas das seguintes opções:
   * Na página [Configurações do usuário para o seu locatário](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings), defina **Usuários podem registrar aplicativos** para Não.
   * Em [Configurações do usuário para aplicativos empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/), defina **Usuários podem consentir que os aplicativos acessem dados da empresa em nome deles** como Não.
3. Em seguida, atribua usuários que precisam dessa permissão para serem membros da função de desenvolvedor de aplicativo, conforme necessário.

Quando um usuário registra um aplicativo, ele é automaticamente adicionado como o primeiro proprietário do aplicativo.

## <a name="delegate-app-ownership"></a>Delegar a propriedade de aplicativos

Os proprietários do aplicativo e de registro de aplicativo podem, cada um, gerenciar apenas as aplicações de aplicativo ou registros de aplicativo de que são proprietários. Por exemplo, quando você adiciona um proprietário para o aplicativo Salesforce, esse proprietário pode gerenciar o acesso e a configuração apenas para o Salesforce, mais nenhum outro aplicativo. Um aplicativo pode ter vários proprietários e um usuário pode ser o proprietário de vários aplicativos.

Um proprietário de aplicativo pode:

* Alterar propriedades do aplicativo, tais como o nome e as permissões solicitadas pelo aplicativo
* Gerenciar credenciais
* Configurar logon único
* Atribuir acesso de usuário
* Adicionar ou remover outros proprietários
* Editar o manifesto do aplicativo
* Publicar o aplicativo na galeria de aplicativo

> [!IMPORTANT]
> Os usuários a quem essa função é atribuída podem adicionar credenciais a um aplicativo e usar essas credenciais para representar a identidade do aplicativo. Essa representação da identidade do aplicativo pode ser uma elevação de privilégio sobre o que o usuário pode fazer por meio de suas outras atribuições de função no Azure AD. Um usuário a quem essa função é atribuída potencialmente pode criar ou atualizar os usuários ou outros objetos ao representar o aplicativo.

O proprietário de um registro de aplicativo pode exibir e editar esse registro.

<!-- ### To assign an enterprise app ownership role to a user

1. Sign in to your [Azure AD tenant](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) with an account that is the Global Administrator for the tenant.
2. On the [Roles and administrators page](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators), open one of the following roles to see its member assignments:
  * **Enterprise Application Owner**
  * **Application Registration Owner**
3. On the **Members** page for the role, select **Add member**.
4. Select one or more members to add to the role. -->

### <a name="to-assign-an-owner-to-an-application"></a>Para atribuir um proprietário a um aplicativo

1. Entre no seu [locatário do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta qualificada para o administrador do aplicativo ou o administrador de aplicativos de nuvem do locatário.
2. Na [página de registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) do locatário, selecione um aplicativo para abrir a página **Visão geral** do aplicativo.
3. Selecione **Proprietários** para ver a lista de proprietários do aplicativo.
4. Selecione **Adicionar** para selecionar um ou mais proprietários para adicionar ao aplicativo.

### <a name="to-assign-an-owner-to-an-application-registration"></a>Para atribuir um proprietário a um registro de aplicativo

1. Entre no seu [locatário do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta qualificada para a função de administrador do aplicativo ou administrador de aplicativos de nuvem do locatário.
2. Quando você tiver permissões suficientes, na [página Aplicativos Empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) do locatário, selecione um registro de aplicativo para abri-lo.
3. Escolha a opção **Configurações**.
4. Selecione **Proprietários** na página **Configurações** para ver a lista de proprietários do aplicativo.
5. Selecione **Adicionar proprietário** para selecionar um ou mais proprietários para adicionar ao aplicativo.

## <a name="next-steps"></a>Próximas etapas

* [Referência da função de administrador do Azure AD](directory-assign-admin-roles.md)
