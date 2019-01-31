---
title: Habilitar a integração do LinkedIn no Azure Active Directory | Microsoft Docs
description: Explica como habilitar ou desabilitar a integração do LinkedIn para aplicativos da Microsoft no Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/22/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: cccbeefd49f5374aeffcae07ac8c888f7a28690f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159991"
---
# <a name="linkedin-integration"></a>Integração do LinkedIn

Neste artigo, você pode aprender como habilitar ou desabilitar a integração do LinkedIn para o locatário no centro de administração do Microsoft Azure AD (Azure Active Directory).

> [!IMPORTANT]
> A configuração de integração do LinkedIn está atualmente sendo distribuída para locatários do Azure AD. Quando lançada para o locatário, é habilitada por padrão.
> 
> Exceções:
> * A configuração não está disponível para clientes que usam o Microsoft Cloud para o governo dos EUA, Microsoft Cloud Alemanha ou Azure e Office 365 operados pela 21Vianet na China.
> * A configuração está desativada por padrão para locatários provisionados na Alemanha. Observe que a configuração não está disponível para clientes que usam o Microsoft Cloud Alemanha.
> * A configuração está desativada por padrão para locatários provisionados na França.

> A integração funcionará somente se você a tiver habilitado *e* se você permitir que os usuários consintam que os aplicativos acessem dados da empresa em nome desses usuários. Para obter informações sobre a configuração de consentimento, veja [Como remover o acesso do usuário a um aplicativo](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-the-azure-portal"></a>Habilitar ou desabilitar a integração do LinkedIn para seus usuários no portal do Azure

Você pode habilitar ou desabilitar a integração do LinkedIn para todo o locatário ou apenas para usuários selecionados no locatário.

1. Entre no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/) com uma conta que seja um administrador global do locatário do Azure AD.
2. Selecione **Usuários**.
3. Na folha **Usuários**, selecione **Configurações de usuário**.
4. Em **Integração do LinkedIn**:
  * Selecione **Sim** para habilitar a integração do LinkedIn para todos os usuários em seu locatário
  * Escolha **Selecionado** para habilitar a integração do LinkedIn somente para usuários do locatário selecionado
  * Selecione  **Não**  para desabilitar a integração do LinkedIn para todos os usuários ![Habilitando a integração do LinkedIn](./media/linkedin-integration/linkedin-integration.png)
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
