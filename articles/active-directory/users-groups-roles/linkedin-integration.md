---
title: Consentimento do administrador para conexões de conta do LinkedIn – Azure Active Directory | Microsoft Docs
description: Explica como habilitar ou desabilitar as conexões de conta de integração do LinkedIn em aplicativos da Microsoft no Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e07c53192ea2c8b792256af944c81c9c909dc55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60469657"
---
# <a name="consent-to-linkedin-account-connections-for-an-azure-active-directory-organization"></a>Consentimento para conexões de conta do LinkedIn para uma organização do Active Directory do Azure

Você pode permitir que os usuários em sua organização para acessar suas conexões do LinkedIn dentro de alguns aplicativos da Microsoft. Nenhum dado é compartilhado até que os usuários dar consentimento para se conectar suas contas. Você pode integrar sua organização no Azure Active Directory (Azure AD) [Centro de administração](https://aad.portal.azure.com).

> [!IMPORTANT]
> As configuração de conexões de conta no LinkedIn atualmente está sendo distribuído para organizações do AD do Azure. Quando ele é distribuído para sua organização, ele é habilitado por padrão.
> 
> Exceções:
> * A configuração não está disponível para clientes que usam o Microsoft Cloud para o governo dos EUA, Microsoft Cloud Alemanha ou Azure e Office 365 operados pela 21Vianet na China.
> * A configuração está desativada por padrão para locatários provisionados na Alemanha. Observe que a configuração não está disponível para clientes que usam o Microsoft Cloud Alemanha.
> * A configuração está desativada por padrão para locatários provisionados na França.
>
> Depois que as conexões de conta do LinkedIn são habilitadas para a sua organização, as conexões de conta funcionar depois que os usuários de consentir que os aplicativos acessem dados da empresa em seu nome. Para obter informações sobre a configuração de consentimento do usuário, consulte [como remover o acesso do usuário a um aplicativo](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="use-the-azure-portal-to-enable-linkedin-account-connections"></a>Usar o portal do Azure para habilitar conexões de conta do LinkedIn

Você pode habilitar conexões de conta do LinkedIn para apenas os usuários que você deseja ter acesso de toda a sua organização, somente a usuários selecionados em sua organização.

1. Entrar para o [Centro de administração do Azure AD](https://aad.portal.azure.com/) com uma conta que seja um administrador global para a organização do AD do Azure.
2. Selecione **Usuários**.
3. Na folha **Usuários**, selecione **Configurações de usuário**.
4. Sob **conexões de conta do LinkedIn**, permitir que os usuários se conectem suas contas para acessar suas conexões do LinkedIn dentro de alguns aplicativos da Microsoft. Nenhum dado é compartilhado até que os usuários dar consentimento para se conectar suas contas.

  * Selecione **Sim** dar consentimento para o serviço para todos os usuários na organização
  * Selecione **selecionados** para dar consentimento para somente a usuários selecionados da organização
  * Selecione **não** para retirar o consentimento para usuários em sua organização

    ![Integrar o LinkedIn conexões de conta da organização](./media/linkedin-integration/linkedin-integration.png)

5. Quando terminar, selecione **salvar** para salvar suas configurações.
     
## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Use a diretiva de grupo para habilitar conexões de conta do LinkedIn

1. Baixe os [arquivos de modelo administrativo do Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extraia os arquivos **ADMX** e copie-os para o repositório central.
3. Gerenciamento de política de grupo aberto.
4. Crie um Objeto de Política de Grupo com a seguinte configuração: **Configuração do Usuário** > **Modelos Administrativos** > **Microsoft Office 2016** > **Diversos** > **Mostrar recursos do LinkedIn em aplicativos do Office**.
5. Selecione **Habilitado** ou **Desabilitado**.
  
   Estado | Efeito
   ------ | ------
   **Habilitado** | A configuração **Mostrar recursos do LinkedIn em aplicativos do Office** nas opções do Office 2016 está habilitada. Os usuários em sua organização podem usar recursos do LinkedIn em seus aplicativos do Office 2016.
   **Desabilitado** | A configuração **Mostrar recursos do LinkedIn em aplicativos do Office** nas opções do Office 2016 está desabilitada e os usuários finais não podem alterar essa configuração. Os usuários em sua organização não podem usar recursos do LinkedIn nos seus aplicativos do Office 2016.

Essa política de grupo afeta somente os aplicativos do Office 2016 para um computador local. Se os usuários desabilitarem o LinkedIn em seus aplicativos do Office 2016, eles ainda podem ver LinkedIn recursos do Office 365.

## <a name="next-steps"></a>Próximos passos

* [Consentimento do usuário e dados de compartilhamento para o LinkedIn](linkedin-user-consent.md)

* [Informações do LinkedIn e recursos em seus aplicativos da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Central de Ajuda do LinkedIn](https://www.linkedin.com/help/linkedin)

* [Exibir sua configuração atual de integração do LinkedIn no portal do Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
