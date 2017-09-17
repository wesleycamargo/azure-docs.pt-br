---
title: "Acesso condicional com gerenciamento de aplicativo móvel no Azure Active Directory | Microsoft Docs"
description: "Saiba como o acesso condicional com gerenciamento de aplicativo móvel no Azure Active Directory funciona."
services: active-directory
keywords: "acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: c6bc39dc151c80ffe1306464da60a029e54cc6b1
ms.contentlocale: pt-br
ms.lasthandoff: 09/05/2017

---
# <a name="conditional-access-with-mobile-app-management-in-azure-active-directory"></a>Acesso condicional com gerenciamento de aplicativo móvel no Azure Active Directory  

O acesso condicional baseado em aplicativo do Azure AD (Azure Active Directory) no Portal do Azure, combinado com as políticas de proteção de aplicativo do Intune, ajudam a restringir o acesso de aplicativos de nuvem a aplicativos móveis que dão suporte à proteção de aplicativo do Intune, por exemplo, restringindo o acesso do aplicativo Outlook ao Exchange Online. Esse suporte permite que os dispositivos que não estão registrados para gerenciamento pelo Intune MDM ainda protejam os dados da empresa.   

O acesso condicional do gerenciamento de aplicativo móvel permite que você combine com outras políticas, tais como políticas de acesso condicional baseadas no dispositivo, para fornecer flexibilidade no modo de proteger os dados, tanto para dispositivos pessoais quanto corporativos. 

##<a name="before-you-begin"></a>Antes de começar

Este tópico pressupõe que você esteja familiarizado com:

- Os conceitos básicos do [acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- Como [configurar uma política de acesso condicional](active-directory-conditional-access-azure-portal-get-started.md).


Além disso, talvez você queira examinar as [Práticas recomendadas para acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md).  



## <a name="prerequisites"></a>Pré-requisitos

1.  Antes de criar uma política de acesso condicional com base no aplicativo, você deve ter uma assinatura Premium do Azure Active Directory ou do Enterprise Mobility + Security, e os usuários devem ser licenciados para o EMS ou Azure AD. 
2.  Antes de criar um novo acesso condicional com a política de gerenciamento de aplicativo móvel, você deve examinar os cenários e as considerações de migração

## <a name="supported-platforms"></a>Plataformas com suporte

-   iOS

-   Android

## <a name="approved-client-applications"></a>Aplicativos do cliente aprovados 

- Microsoft Outlook

- Microsoft SharePoint

- Microsoft OneDrive

- Equipes da Microsoft

- Microsoft Word

- Microsoft Excel

- Microsoft PowerPoint


## <a name="exchange-online-policy"></a>Política do Exchange Online 

Este cenário consiste em um acesso condicional com política de gerenciamento de aplicativo móvel para acesso ao Exchange Online com aplicativos aprovados.


### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:

- Configura o email usando um aplicativo de email nativo no iOS ou Android para se conectar ao Exchange

- Recebe um email que indica que o acesso só está disponível usando o aplicativo Outlook

- Baixa o aplicativo com o link

- Abre o aplicativo Outlook e o assina com as credenciais do Azure AD

- É solicitado a instalar o Authenticor (iOS) ou o Portal da Empresa (Android) para continuar

- Instala o aplicativo e pode retornar ao aplicativo Outlook para continuar

- É solicitado a registrar um dispositivo

- É capaz de acessar o email

Quaisquer eventuais políticas de Proteção de Aplicativo do Intune são ativadas no momento do acesso aos dados corporativos e podem solicitar que o usuário reinicie o aplicativo, que use um PIN adicional, etc. (se configuradas para o aplicativo e a plataforma).

### <a name="configuration"></a>Configuração 

**Etapa 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/01.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**:

    a. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **Aplicativos cliente**, selecione **Aplicativos móveis e aplicativos da área de trabalho**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/05.png)
 

**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/06.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.


3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente**. 

    a. Como **Aplicativos cliente**, selecione **Exchange Active Sync**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/08.png)

    b. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/05.png)


**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**


![Acesso condicional](./media/active-directory-conditional-access-mam/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="exchange-online-and-sharepoint-online-policy"></a>Política do Exchange Online e do SharePoint Online

Este cenário consiste em um acesso condicional com política de gerenciamento de aplicativo móvel para acesso ao Exchange Online e ao SharePoint Online com aplicativos aprovados.

### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:

- Tenta usar o aplicativo SharePoint para se conectar e para exibir os sites corporativos desse usuário

- Tenta entrar com as mesmas credenciais usadas pelo aplicativo Outlook

- Não precisa registrar novamente e pode obter acesso aos recursos


### <a name="configuration"></a>Configuração

**Etapa 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online e o SharePoint Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/71.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.


3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online** e o **Office 365 SharePoint Online**. 

    ![Acesso condicional](./media/active-directory-conditional-access-mam/02.png)

4. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**:

    a. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **Aplicativos cliente**, selecione **Aplicativos móveis e aplicativos da área de trabalho**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/05.png)




**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/06.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. Online 

    ![Acesso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente**:

    a. Como **Aplicativos cliente**, selecione **Exchange Active Sync**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/08.png)

    b. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/05.png)




**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**


![Acesso condicional](./media/active-directory-conditional-access-mam/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="mobile-application-management-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Gerenciamento de aplicativos móveis ou política de dispositivo em conformidade para o Exchange Online e SharePoint Online

Este cenário consiste em um acesso condicional com política de gerenciamento de aplicativo móvel ou política de dispositivo em conformidade para acesso ao Exchange Online com aplicativos aprovados.


### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que:
 
- Alguns usuários já estão registrados (com ou sem dispositivos corporativos)

- Os usuários que não estão registrados com o Azure AD usando um aplicativo protegido por aplicativo precisam registrar um dispositivo para acessar os recursos

- Os usuários registrados usando o aplicativo protegido não precisam registrar novamente o dispositivo


### <a name="configuration"></a>Configuração

**Etapa 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online e o SharePoint Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/62.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online** e o **Office 365 SharePoint Online**. 

     ![Acesso condicional](./media/active-directory-conditional-access-mam/02.png)

4. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**. 
 
    a. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **Aplicativos cliente**, selecione **Aplicativos móveis e aplicativos da área de trabalho**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **Controles de acesso**, você precisa ter o seguinte selecionado:

    - **Exigir que o dispositivo seja marcado como em conformidade**

    - **Exigir um aplicativo cliente aprovado (versão prévia)**

    - **Exigir um dos controles selecionados**   
 
    ![Acesso condicional](./media/active-directory-conditional-access-mam/11.png)



**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/61.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. 

    ![Acesso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente**. 

    Como **Aplicativos cliente*, selecione **Exchange Active Sync**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/08.png)

5. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.
 
    ![Acesso condicional](./media/active-directory-conditional-access-mam/11.png)




**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**


![Acesso condicional](./media/active-directory-conditional-access-mam/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="mobile-application-management-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Gerenciamento de aplicativos móveis e política de dispositivo em conformidade para o Exchange Online e SharePoint Online

Este cenário consiste em um acesso condicional com política de gerenciamento de aplicativo móvel e política de dispositivo em conformidade para acesso ao Exchange Online com aplicativos aprovados.

### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:
 
-   Configura o email usando um aplicativo de email nativo no iOS ou Android para se conectar ao Exchange
-   Recebe um email que indica que o acesso requer que seu dispositivo seja registrado
-   Baixa e entra no Portal da Empresa
-   Verifica o email e é solicitado a usar o aplicativo Outlook
-   Baixa o aplicativo Outlook
-   Abre o aplicativo Outlook e insere as credenciais usadas no registro
-   O usuário é capaz de acessar o email

Quaisquer eventuais políticas de Proteção de Aplicativo do Intune são ativadas no momento do acesso aos dados corporativos e podem solicitar que o usuário reinicie o aplicativo, que use um PIN adicional, etc. (se configuradas para o aplicativo e a plataforma)


### <a name="configuration"></a>Configuração

**Etapa 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online e o SharePoint Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/62.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online** e o **Office 365 SharePoint Online**. 

     ![Acesso condicional](./media/active-directory-conditional-access-mam/02.png)

4. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**. 
 
    a. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **Aplicativos cliente**, selecione **Aplicativos móveis e aplicativos da área de trabalho**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **Controles de acesso**, você precisa ter o seguinte selecionado:

    - **Exigir que o dispositivo seja marcado como em conformidade**

    - **Exigir um aplicativo cliente aprovado (versão prévia)**

    - **Exigir um dos controles selecionados**   
 
    ![Acesso condicional](./media/active-directory-conditional-access-mam/11.png)



**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/61.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. 

    ![Acesso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente**. 

    Como **Aplicativos cliente**, selecione **Exchange Active Sync**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/08.png)

5. Como **Controles de acesso**, você precisa ter o seguinte selecionado:

    - **Exigir que o dispositivo seja marcado como em conformidade**

    - **Exigir um aplicativo cliente aprovado (versão prévia)**

    - **Exigir todos os controles selecionados**   
 
    ![Acesso condicional](./media/active-directory-conditional-access-mam/64.png)




**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**


![Acesso condicional](./media/active-directory-conditional-access-mam/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).



## <a name="migration-considerations"></a>Considerações sobre a migração

Se você tiver políticas configuradas no Portal Clássico do Azure, você deverá migrá-las para o Portal do Azure porque:


- Um usuário que está em uma política do Portal Clássico do Azure e em uma política do Portal do Azure precisa atender aos requisitos em ambas as políticas 

- Se você não migrar as políticas existentes, não será capaz de implementar políticas que estejam concedendo acesso


## <a name="migration-from-the-azure-classic-portal"></a>Migração do Portal Clássico do Azure

Neste cenário: 

- No seu [Portal Clássico do Azure](https://manage.windowsazure.com), você configurou:

    - SharePoint Online

    ![Acesso condicional](./media/active-directory-conditional-access-mam/14.png)

    - Uma política de acesso condicional com base em dispositivo

    ![Acesso condicional](./media/active-directory-conditional-access-mam/15.png)

- Você deseja configurar uma política de acesso condicional de gerenciamento de aplicativos móveis no Portal do Azure 
 

### <a name="configuration"></a>Configuração 

- Examinar as políticas de acesso condicional com base no dispositivo

- Migrá-las para o Portal do Azure 

- Adicionar políticas de acesso condicional de gerenciamento de aplicativos móveis


## <a name="migrating-from-intune"></a>Migrando do Intune 

Neste cenário:

- No [Intune](https://portal.azure.com/#blade/Microsoft_Intune/SummaryBlade ), você tem uma política de acesso condicional de gerenciamento de aplicativos móveis para o Exchange Online ou para o SharePoint Online configurada

    ![Acesso condicional](./media/active-directory-conditional-access-mam/15.png)

- Você deseja migrar para o uso de acesso condicional de gerenciamento de aplicativos móveis no Portal do Azure


### <a name="configuration"></a>Configuração 
 
- Examinar as políticas de acesso condicional com base no dispositivo

- Migrá-las para o Portal do Azure 

- Examinar as políticas de acesso condicional de gerenciamento de aplicativos móveis configuradas para o Exchange Online ou para o SharePoint Online no Intune

- Adicionar o controle para **Exigir aplicativos aprovados** além do controle baseado em dispositivo 
 

## <a name="migrating-from-the-azure-classic-portal-and-intune"></a>Migrando do Portal Clássico do Azure e do Intune

Neste cenário:

- Você tem os seguintes itens configurados:

    - **Portal Clássico do Azure:** condicional com base no dispositivo 

    - **Intune:** políticas de acesso condicional de gerenciamento de aplicativos móveis 
    
- Você deseja migrar ambas as políticas para o uso de políticas de acesso condicional de gerenciamento de aplicativos móveis no Portal do Azure


### <a name="configuration"></a>Configuração

- Examinar as políticas de acesso condicional com base no dispositivo

- Migrá-las para o Portal do Azure 

- Examinar a política de acesso condicional de gerenciamento de aplicativos móveis configurada para o Exchange Online ou para o SharePoint Online no Intune

- Adicionar o controle para **Exigir aplicativos aprovados** além do controle baseado em dispositivo 




## <a name="next-steps"></a>Próximas etapas

Se você quiser saber como configurar uma política de acesso condicional, veja [Introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md). 
