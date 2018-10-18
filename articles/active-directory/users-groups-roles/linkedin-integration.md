---
title: Habilitar a integração de conexões do LinkedIn no Azure Active Directory | Microsoft Docs
description: Explica como habilitar ou desabilitar conexões de conta do LinkedIn para aplicativos da Microsoft no Active Directory do Azure
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 0eaa2656313ecd9b64503051265dc16285f0a1f3
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44492800"
---
# <a name="linkedin-account-connections"></a>Conexões de conta do LinkedIn

Neste artigo, você pode aprender como habilitar ou desabilitar conexões de conta do LinkedIn para o locatário no centro de administração do Microsoft Azure AD (Azure Active Directory).

> [!IMPORTANT]
> A configuração de conexões de conta do LinkedIn está atualmente sendo lançada nos locatários do Azure AD. Quando lançada para o locatário, é habilitada por padrão. 
> 
> Exceções:
> * A configuração não está disponível para clientes que usam o Microsoft Cloud para o governo dos EUA, Microsoft Cloud Alemanha ou Azure e Office 365 operados pela 21Vianet na China.
> * A configuração está desativada por padrão para locatários provisionados na Alemanha. Observe que a configuração não está disponível para clientes que usam o Microsoft Cloud Alemanha.
> * A configuração está desativada por padrão para locatários provisionados na França.

> A integração funcionará somente se você a tiver habilitado *e* se você permitir que os usuários consintam que os aplicativos acessem dados da empresa em nome desses usuários. Para obter informações sobre a configuração de consentimento, veja [Como remover o acesso do usuário a um aplicativo](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-in-the-azure-portal"></a>Habilitar ou desabilitar conexões de conta do LinkedIn para locatário no portal do Azure

Você pode habilitar ou desabilitar as conexões da conta do LinkedIn para todo o locatário ou apenas para usuários selecionados no locatário.

1. Entre no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/) com uma conta que seja um administrador global do locatário do Azure AD.
2. Selecione **Usuários**.
3. Na folha **Usuários**, selecione **Configurações de usuário**.
4. Em **Conexões de conta do LinkedIn**:
  * Selecione **Sim** para habilitar conexões de conta do LinkedIn para todos os usuários no locatário
  * Selecione **Selecionado** para habilitar as conexões de conta do LinkedIn apenas para usuários do locatário selecionados
  * Selecione **Não** para desabilitar as conexões de conta do LinkedIn para todos os usuários ![Habilitar conexões de conta do LinkedIn](./media/linkedin-integration/linkedin-integration.png)
5. Salve as configurações quando terminar selecionando **Salvar**.

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-using-group-policy"></a>Habilitar ou desabilitar conexões de conta do LinkedIn para locatário usando a Política de Grupo

1. Baixe os [arquivos de modelo administrativo do Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extraia os arquivos **ADMX** e copie-os para o repositório central.
3. Gerenciamento de política de grupo aberto.
4. Crie um Objeto de Política de Grupo com a configuração a seguir: **Configuração de Usuário** > **Modelos Administrativos** > **Microsoft Office 2016** > **Diversos** > **Mostrar recursos do LinkedIn em aplicativos do Office**.
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
Use o link a seguir para visualizar a configuração atual de conexões da conta do LinkedIn no Portal do Azure:

[Configurar as conexões de conta do LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 