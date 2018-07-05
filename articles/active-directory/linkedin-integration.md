---
title: Habilitar conexões do LinkedIn para serviços e aplicativos da Microsoft no Azure Active Directory | Microsoft Docs
description: Explica como habilitar ou desabilitar conexões de conta do LinkedIn para aplicativos da Microsoft no Active Directory do Azure
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/28/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 4b3ff0b2481b42f516d28ac17f2616685730b7d5
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109579"
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>Conexões de conta do LinkedIn para serviços e aplicativos da Microsoft
Neste artigo, você pode aprender como gerenciar as conexões de conta do LinkedIn para o locatário no centro de administração do Microsoft Azure AD (Azure Active Directory). 

> [!IMPORTANT]
> A funcionalidade de conexões de conta do LinkedIn está sendo lançada nos locatários do Microsoft Azure AD. Quando lançada para o locatário, é habilitada por padrão. Não está disponível para clientes governamentais dos Estados Unidos e organizações com caixas de correio do Exchange Online hospedadas na Austrália, Canadá, China, França, Alemanha, Índia, Coreia do Sul, Reino Unido, Japão e África do Sul. O suporte para esses locais de caixa de correio estará disponível em breve.  Para obter uma exibição atualizada das informações de implantação, consulte a página do [Roteiro do Office 365](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc).

## <a name="benefit-to-users"></a>Benefícios aos usuários
Depois que os usuários se conectarem a sua conta do LinkedIn, as informações do LinkedIn são usadas para mostrar informações personalizadas e recursos em vários aplicativos ou serviços da Microsoft. Os usuários podem ver informações sobre as pessoas com quem trabalham com o cartão de perfil da Microsoft, mesmo para as pessoas fora da sua organização. Ao longo do tempo, sua experiência LinkedIn também se tornará mais relevante e personalizada para seu trabalho. Por exemplo, LinkedIn pode sugerir novas conexões com base com quem os usuários trabalham ou informações sobre pessoas em seus calendários naquele dia.

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>Como as conexões de conta do LinkedIn aparecem para o usuário
As conexões de conta do LinkedIn permitem que os usuários visualizem informações públicas do perfil do LinkedIn em alguns de seus aplicativos da Microsoft. Os usuários no locatário podem escolher conectar suas contas corporativas ou de estudante do LinkedIn e da Microsoft para ver informações adicionais do perfil do LinkedIn. Para obter mais informações, consulte [informações e recursos do LinkedIn nos serviços e aplicativos da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740).

Quando os usuários na organização conectam suas contas corporativas ou de estudante do LinkedIn e da Microsoft, eles têm duas opções: 
* Conceder permissão para compartilhar dados entre as duas contas. Isso significa que eles permitem que a conta do LinkedIn compartilhe dados com a conta corporativa ou de estudante da Microsoft, bem como a conta corporativa ou de estudante da Microsoft para compartilhar dados com a conta do LinkedIn. Dados compartilhados com o LinkedIn deixam os serviços online. 
* Conceder permissão para compartilhar dados apenas da conta do LinkedIn para a conta corporativa ou de estudante da Microsoft

Para obter mais informações sobre dados compartilhados entre o LinkedIn dos usuários e as contas corporativas ou de estudante da Microsoft, consulte [LinkedIn em aplicativos da Microsoft no trabalho ou na escola](https://www.linkedin.com/help/linkedin/answer/84077). 
* Os [Usuários podem desconectar as contas](https://www.linkedin.com/help/linkedin/answer/85097) e remover as permissões de compartilhamento de dados a qualquer momento. 
* [Os usuários podem controlar como seu próprio perfil do LinkedIn será exibido](https://www.linkedin.com/help/linkedin/answer/83), incluindo se o perfil dos usuários poderão ser exibidos nos aplicativos da Microsoft.

## <a name="privacy-considerations"></a>Considerações sobre privacidade
Habilitar as conexões da conta do LinkedIn permite que os serviços e aplicativos da Microsoft acessem algumas informações do LinkedIn de seus usuários. Leia a [Declaração de Privacidade da Microsoft](https://privacy.microsoft.com/privacystatement/) para saber mais sobre as considerações de privacidade ao habilitar conexões da conta do LinkedIn no Microsoft Azure AD. 

## <a name="manage-linkedin-account-connections"></a>Gerenciar conexões da conta do LinkedIn
A funcionalidade de conexões da conta do LinkedIn está ativada por padrão para todo o locatário. Você pode escolher habilitar as conexões da conta do LinkedIn para todo o locatário ou habilitar as conexões de conta do LinkedIn para usuários selecionados no locatário. 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>Habilitar ou desabilitar a conexão de conta do LinkedIn para locatário no Portal do Azure

1. Entre no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/) com uma conta que seja um administrador global do locatário do Azure AD.
2. Selecione **Usuários**.
3. Na folha **Usuários**, selecione **Configurações de usuário**.
4. Em **Conexões de conta do LinkedIn**:
  * Selecione **Sim** para habilitar conexões de conta do LinkedIn para todos os usuários no locatário
  * Selecione **Selecionado** para habilitar as conexões de conta do LinkedIn apenas para usuários do locatário selecionados
  * Selecione **Não** para desabilitar as conexões de conta do LinkedIn para todos os usuários ![Habilitar conexões de conta do LinkedIn](./media/linkedin-integration/linkedin-integration.png)
5. Salve as configurações quando terminar selecionando **Salvar**.

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>Habilitar ou desabilitar as conexões de conta do LinkedIn para aplicativos do Office 2016 da organização utilizando a Política de Grupo

1. Baixe os [arquivos de modelo administrativo do Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extraia os arquivos **ADMX** e copie-os para o repositório central.
3. Gerenciamento de política de grupo aberto.
4. Crie um Objeto de Política de Grupo com a configuração a seguir: **Configuração de Usuário** > **Modelos Administrativos** > **Microsoft Office 2016** > **Diversos** > **Mostrar recursos do LinkedIn em aplicativos do Office**.
5. Selecione **Habilitado** ou **Desabilitado**.
  * Quando a política for **Habilitado**, a configuração **Mostrar recursos do LinkedIn nos aplicativos do Office** que pode ser encontrada na caixa de diálogo Opções do Office 2016 está habilitada. Isso também significa que os usuários em sua organização podem usar os recursos do LinkedIn em seus aplicativos do Office.
  * Quando a política está **Desabilitado**, a configuração **Mostrar recursos do LinkedIn nos aplicativos do Office** que pode ser encontrada na caixa de diálogo de opções do Office 2016 está definida para o estado desabilitado e os usuários finais não podem alterar essa configuração. Os usuários em sua organização não podem usar recursos do LinkedIn nos seus aplicativos do Office 2016.

Essa política de grupo afeta somente os aplicativos do Office 2016 para um computador local. Os usuários podem ver LinkedIn recursos nos cartões de perfil em todo o Office 365, mesmo se eles desabilitarem o LinkedIn em seus aplicativos do Office 2016. 

### <a name="learn-more"></a>Saiba mais 
* [Informações do LinkedIn e recursos em seus aplicativos da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Central de Ajuda do LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Próximas etapas
Use o link a seguir para visualizar a configuração atual de conexões da conta do LinkedIn no Portal do Azure:

[Configurar as conexões de conta do LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 