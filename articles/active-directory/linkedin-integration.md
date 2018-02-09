---
title: "Habilitar ou desabilitar a integração do LinkedIn para aplicativos do Office no Azure Active Directory | Microsoft Docs"
description: "Explica como habilitar ou desabilitar a integração do LinkedIn para aplicativos da Microsoft no Azure Active Directory"
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/30/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 5ebc44d0ef6200baeacf4f1f8c4371e2d1eed9db
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="linkedin-integration-for-office-applications"></a>Integração do LinkedIn para aplicativos do Office
Este artigo mostra como restringir os usuários aos quais a integração do LinkedIn é fornecida no Azure Active Directory (Azure AD). A integração do LinkedIn é habilitada por padrão quando ela é adicionado ao seu locatário, o que permite aos usuários acessar dados públicos do LinkedIn dentro de alguns dos seus aplicativos da Microsoft. Cada usuário pode escolher, de forma independente, conectar a sua conta corporativa ou de estudante à sua conta do LinkedIn.

> [!IMPORTANT]
> A integração do LinkedIn não está sendo implantada em todos os locatários do Azure AD ao mesmo tempo. Depois que ela é implantada no seu locatário do Azure, a integração do LinkedIn é habilitada por padrão. A integração do LinkedIn não está disponível para locatários do governo, soberanos e go-local. 

## <a name="linkedin-integration-from-the-user-perspective"></a>Integração do LinkedIn da perspectiva do usuário
Quando os usuários em sua organização conectam as suas respectivas contas do LinkedIn às suas contas corporativas ou de estudante, [eles permitem que o LinkedIn forneça dados](https://www.linkedin.com/help/linkedin/answer/84077) que serão usados por aplicativos e serviços da Microsoft fornecidos pela sua organização. [Os usuários podem desconectar contas](https://www.linkedin.com/help/linkedin/answer/85097), que remove a permissão para o LinkedIn compartilhar dados com a Microsoft. A integração do LinkedIn utiliza informações publicamente disponíveis do perfil do LinkedIn. [Os usuários podem controlar como o seu próprio perfil do LinkedIn é exibido](https://www.linkedin.com/help/linkedin/answer/83) usando as configurações de privacidade do LinkedIn, incluindo se o seu perfil pode ser exibido em aplicativos da Microsoft.

## <a name="privacy-considerations"></a>Considerações sobre privacidade
Habilitar a integração do LinkedIn no Azure AD permite que aplicativos e serviços da Microsoft possam acessar algumas das informações do LinkedIn dos seus usuários. Leia a [Política de Privacidade da Microsoft](https://privacy.microsoft.com/privacystatement/) para saber mais sobre as considerações de privacidade, ao habilitar a integração do LinkedIn no Azure AD. 

## <a name="manage-linkedin-integration"></a>Gerenciar integração do LinkedIn
A integração do LinkedIn para empresas é habilitada por padrão no Azure AD. A integração do LinkedIn permite que todos os usuários em sua organização utilizem os recursos do LinkedIn nos serviços da Microsoft, como exibir perfis do LinkedIn no Outlook. Desabilitar a integração LinkedIn remove os recursos do LinkedIn dos serviços e aplicativos da Microsoft e interrompe o compartilhamento de dados entre o LinkedIn e a sua organização por meio de serviços da Microsoft.

### <a name="enable-or-disable-linkedin-integration-for-your-organization-in-the-azure-portal"></a>Habilitar ou desabilitar a integração do LinkedIn para sua organização no portal do Azure

1. Entre no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/) com uma conta que seja um administrador global do locatário do Azure AD.
2. Selecione **Usuários e grupos**.
3. Na folha **Usuários e grupos**, selecione **Configurações de usuário**.
4. Em **Integração do LinkedIn**, selecione **Sim** ou **Não** para habilitar ou desabilitar a integração do LinkedIn.
   ![Habilitando a integração do LinkedIn](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="enable-or-disable-linkedin-integration-for-your-organizations-office-2016-apps-using-group-policy"></a>Habilitar ou desabilitar a integração do LinkedIn para aplicativos do Office 2016 da sua organização usando a política de grupo

1. Baixe os [arquivos de modelo administrativo do Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extraia os arquivos **ADMX** arquivos e copie-os para seu **repositório central**.
3. Gerenciamento de política de grupo aberto.
4. Crie um objeto de política de grupo com a seguinte configuração: **Configuração do usuário** > **Modelos administrativos** > **Microsoft Office 2016** > **Diversos** > **Permitir integração do LinkedIn**.
5. Selecione **Habilitado** ou **Desabilitado**.
  * Quando a política for **Habilitado**, a configuração **Mostrar recursos do LinkedIn nos aplicativos do Office** que pode ser encontrada na caixa de diálogo Opções do Office 2016 está habilitada. Isso também significa que os usuários em sua organização podem usar os recursos do LinkedIn em seus aplicativos do Office.
  * Quando a política está **Desabilitado**, a configuração **Mostrar recursos do LinkedIn nos aplicativos do Office** que pode ser encontrada na caixa de diálogo de opções do Office 2016 está definida para o estado desabilitado e os usuários finais não podem alterar essa configuração. Os usuários em sua organização não podem usar recursos do LinkedIn nos seus aplicativos do Office 2016. 

Essa política de grupo afeta somente os aplicativos do Office 2016 para um computador local. Os usuários podem ver LinkedIn recursos nos cartões de perfil em todo o Office 365, mesmo se eles desabilitarem o LinkedIn em seus aplicativos do Office 2016. 

### <a name="learn-more"></a>Saiba mais 
* [Informações do LinkedIn e recursos em seus aplicativos da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Central de Ajuda do LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Próximas etapas
Use o seguinte link para ver suas configurações atuais de integração LinkedIn no portal do Azure:

[Configurar a integração do LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 