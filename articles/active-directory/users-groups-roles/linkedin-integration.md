---
title: Consentimento para serviços do LinkedIn para sua organização - Azure Active Directory | Microsoft Docs
description: Explica como habilitar ou desabilitar a integração do LinkedIn para aplicativos da Microsoft no Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: abcb1696efe44293d01153aa37a9835ba5f43370
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199691"
---
# <a name="consent-to-linkedin-integration-for-your-azure-active-directory-organization"></a>Consentimento para a integração do LinkedIn para sua organização do Active Directory do Azure

Neste artigo, você pode aprender como habilitar ou desabilitar a integração do LinkedIn para sua organização no Centro de administração do Azure Active Directory (Azure AD).

> [!IMPORTANT]
> A configuração de integração do LinkedIn está atualmente sendo distribuída para organizações do AD do Azure. Quando ele é distribuído para sua organização, ele é habilitado por padrão.
> 
> Exceções:
> * A configuração não está disponível para clientes que usam o Microsoft Cloud para o governo dos EUA, Microsoft Cloud Alemanha ou Azure e Office 365 operados pela 21Vianet na China.
> * A configuração está desativada por padrão para locatários provisionados na Alemanha. Observe que a configuração não está disponível para clientes que usam o Microsoft Cloud Alemanha.
> * A configuração está desativada por padrão para locatários provisionados na França.
>
> A integração funciona somente se você tiver habilitado *e* depois que os usuários de consentir que os aplicativos acessem dados da empresa em seu nome. Para obter informações sobre a configuração de consentimento do usuário, consulte [como remover o acesso do usuário a um aplicativo](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-the-azure-portal"></a>Habilitar ou desabilitar a integração do LinkedIn para seus usuários no portal do Azure

Você pode habilitar ou desabilitar a integração do LinkedIn para todo o locatário ou apenas para usuários selecionados no locatário.

1. Entre no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/) com uma conta que seja um administrador global do locatário do Azure AD.
2. Selecione **Usuários**.
3. Na folha **Usuários**, selecione **Configurações de usuário**.
4. Em **Conexões de conta do LinkedIn**:

   * Selecione **Sim** para dar consentimento para todos os usuários na organização para se conectar suas contas para obter acesso aos seus contatos do LinkedIn dentro de alguns aplicativos da Microsoft.
   * Selecione **selecionados** para dar consentimento para somente usuários selecionados da organização para se conectar suas contas para obter acesso aos seus contatos do LinkedIn dentro de alguns aplicativos da Microsoft.
   * Selecione **não** para retirar o consentimento para usuários em sua organização para se conectar suas contas para obter acesso aos seus contatos do LinkedIn dentro de alguns aplicativos da Microsoft.
    ![Habilitando a integração do LinkedIn da organização](./media/linkedin-integration/linkedin-integration.png)
5. Salve as configurações quando terminar selecionando **Salvar**.

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-group-policy"></a>Habilitar ou desabilitar a integração do LinkedIn para seus usuários na Política de Grupo

1. Baixe os [arquivos de modelo administrativo do Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extraia os arquivos **ADMX** e copie-os para o repositório central.
3. Gerenciamento de política de grupo aberto.
4. Crie um Objeto de Política de Grupo com a seguinte configuração: **Configuração do Usuário** > **Modelos Administrativos** > **Microsoft Office 2016** > **Diversos** > **Mostrar recursos do LinkedIn em aplicativos do Office**.
5. Selecione **Habilitado** ou **Desabilitado**.
  
   Estado | Efeito
   ------ | ------
   **Habilitado** | A configuração **Mostrar recursos do LinkedIn em aplicativos do Office** nas opções do Office 2016 está habilitada. Os usuários em sua organização pode usar recursos do LinkedIn nos seus aplicativos do Office.
   **Desabilitado** | A configuração **Mostrar recursos do LinkedIn em aplicativos do Office** nas opções do Office 2016 está desabilitada e os usuários finais não podem alterar essa configuração. Os usuários em sua organização não podem usar recursos do LinkedIn nos seus aplicativos do Office 2016.

Essa política de grupo afeta somente os aplicativos do Office 2016 para um computador local. Os usuários podem ver LinkedIn recursos nos cartões de perfil em todo o Office 365, mesmo se eles desabilitarem o LinkedIn em seus aplicativos do Office 2016.

## <a name="learn-more"></a>Saiba mais

* [Integrar o LinkedIn em sua organização](linkedin-user-consent.md)

* [Informações do LinkedIn e recursos em seus aplicativos da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Central de Ajuda do LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Próximas etapas

Use o seguinte link para ver suas configurações atuais de integração LinkedIn no portal do Azure:

[Exibir sua configuração atual de integração do LinkedIn no portal do Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
