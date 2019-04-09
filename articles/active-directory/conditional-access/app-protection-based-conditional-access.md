---
title: Como exigir a política de proteção do aplicativo para acesso de aplicativo de nuvem com acesso condicional no Azure Active Directory | Microsoft Docs
description: Saiba como exigir a política de proteção do aplicativo para acesso de aplicativo de nuvem com acesso condicional no Azure Active Directory.
services: active-directory
keywords: acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 4/4/2019
ms.author: joflore
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: f695d50e251d0104cf9f0d38fe4489a0e66dfe15
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288495"
---
# <a name="how-to-require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Como: Exigir a política de proteção do aplicativo para acesso de aplicativo de nuvem com acesso condicional (visualização)

Os funcionários usam dispositivos móveis para tarefas de pessoais e corporativas. Ao mesmo tempo que garante que seus funcionários sejam produtivos, você também quer impedir a perda de dados. Com acesso condicional do Azure Active Directory (Azure AD), você pode proteger os dados da empresa, restringindo o acesso aos seus aplicativos de nuvem para aplicativos cliente que têm uma política de proteção do aplicativo pela primeira vez.

Este tópico explica como configurar políticas de acesso condicional que podem exigir a política de proteção do aplicativo antes de acesso a dados.

## <a name="overview"></a>Visão geral

Com o [acesso condicional do Azure AD](overview.md), você pode ajustar como os usuários autorizados podem acessar seus recursos. Por exemplo, você pode limitar o acesso aos seus aplicativos de nuvem a dispositivos confiáveis.

Você pode usar [políticas de proteção do aplicativo Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da empresa. As políticas de proteção do aplicativo Intune não exigem a solução de gerenciamento de dispositivo móvel (MDM), que permite que você proteja os dados da sua empresa com ou sem registro de dispositivos em uma solução de gerenciamento de dispositivos.

Acesso condicional do Azure Active Directory restringe o acesso aos seus aplicativos de nuvem para aplicativos cliente que Intune relatou-se ao Azure AD como o recebimento de uma política de proteção do aplicativo. Por exemplo, você pode restringir o acesso ao Exchange Online para o aplicativo do Outlook que tem uma política de proteção de aplicativo do Intune.

Na terminologia de acesso condicional, esses aplicativos de cliente são conhecidos como política protegida com um **política de proteção de aplicativo**.  

![Acesso condicional](./media/app-protection-based-conditional-access/05.png)

Para obter uma lista de política de aplicativos de cliente protegido, consulte [requisito de política de proteção de aplicativo](technical-reference.md#approved-client-app-requirement).

Você pode combinar políticas de acesso condicional baseado em proteção de aplicativo com outras políticas como [políticas de acesso condicional baseado em dispositivo](require-managed-devices.md) para fornecer flexibilidade na forma de proteger os dados para dispositivos pessoais e corporativos.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Benefícios do requisito de acesso condicional baseado em proteção de aplicativo

Semelhante a conformidade está sendo relatado pelo Intune para iOS e Android para o dispositivo gerenciado, agora o Intune relatórios para o Azure AD se a política de proteção de aplicativo é aplicada para que o acesso condicional pode usar isso como uma verificação de acesso. Essa nova política de acesso condicional **política de proteção de aplicativo** aumenta a segurança, protegendo contra erros do administrador, como:

- usuários que não têm uma licença do Intune
- usuários que não podem receber uma política de proteção de aplicativo do Intune
- Política do Intune app protection aplicativos que não foram configuradas para receber uma política


## <a name="before-you-begin"></a>Antes de começar

Este tópico pressupõe que você esteja familiarizado com:

- O [requisito de política de proteção de aplicativo](technical-reference.md#app-protection-policy-requirement) referência técnica.

- A referência técnica do [requisito de aplicativo cliente aprovado](technical-reference.md#approved-client-app-requirement).

- Os conceitos básicos do [acesso condicional no Azure Active Directory](overview.md).

- Como [configurar uma política de acesso condicional](app-based-mfa.md).


## <a name="prerequisites"></a>Pré-requisitos

Para criar uma política de acesso condicional baseado em proteção de aplicativo, você deve:
- Ter um Enterprise Mobility + Security ou uma assinatura do Azure Active Directory premium + Intune
- Certifique-se de que os usuários estão licenciados para o EMS ou Azure AD + Intune
- Verifique se que o aplicativo cliente foi configurado no Intune para receber uma política de proteção de aplicativo
- Verifique se que os usuários estão configurados no Intune para receber uma política de proteção de aplicativo do Intune

## <a name="app-protection-based-policy-for-exchange-online"></a>Política de proteção com base de aplicativo para o Exchange Online

Este cenário consiste em uma política de acesso condicional baseado em proteção de aplicativo para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:

- Configura o email usando um aplicativo de email nativo no iOS ou Android para se conectar ao Exchange

- Recebe um email que indica que o acesso só está disponível usando o aplicativo Outlook

- Baixa o aplicativo com o link

- Abre o aplicativo Outlook e o assina com as credenciais do Azure AD

- É solicitado a instalar o Authenticator (iOS) ou o Portal da Empresa (Android) para continuar

- Instala o aplicativo e pode retornar ao aplicativo Outlook para continuar

- É solicitado a registrar um dispositivo

- É capaz de receber uma política de proteção de aplicativo do Intune

- É capaz de acessar o email

Quaisquer políticas de proteção de aplicativo do Intune devem ser no aplicativo para acesso a dados corporativos e podem solicitar que o usuário para reiniciar o aplicativo, use um etc adicionais do PIN (se configurado para o aplicativo e plataforma).

### <a name="configuration"></a>Configuração

**Etapa 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/01.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**:

     a. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Como **Aplicativos cliente (versão prévia)**, selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Como **controles de acesso**, você precisa ter **exigem a política de proteção de aplicativo (visualização)** selecionado.

    ![Acesso condicional](./media/app-protection-based-conditional-access/05.png)
 

**Etapa 2: configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/06.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.


3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente (versão prévia)**. 

     a. Como **Aplicativos cliente (versão prévia)**, selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/92.png)

    b. Como **controles de acesso**, você precisa ter **exigem a política de proteção de aplicativo (visualização)** selecionado.

    ![Acesso condicional](./media/app-protection-based-conditional-access/05.png)


**Etapa 3: configurar a política de proteção de aplicativo do Intune para aplicativos de cliente Android e iOS**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Política de dispositivo compatível ou com base em proteção de aplicativo para o Exchange Online

Este cenário consiste em uma política de acesso condicional de dispositivo compatível ou com base em proteção de aplicativo para acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que:
 
- Algum usuário já está registrado (com ou sem dispositivos corporativos)

- Os usuários que não estão registrados com o Azure AD usando um aplicativo protegido por aplicativo precisam registrar um dispositivo para acessar os recursos

- Os usuários registrados usando o aplicativo protegido não precisam registrar novamente o dispositivo

- Usuário pode receber uma política de proteção de aplicativo do Intune se não for registrado

- Usuário pode acessar o email com o Outlook e uma política de proteção de aplicativo do Intune se não for registrado

- Usuário pode acessar o email com o Outlook se o dispositivo está registrado


### <a name="configuration"></a>Configuração

**Etapa 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/62.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. 

     ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**. 
 
     a. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Como **Aplicativos cliente (versão prévia)**, selecione **Aplicativos móveis e clientes da área de trabalho** e **Clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Como **Controles de acesso**, você precisa ter o seguinte selecionado:

   - **Exigir que o dispositivo seja marcado como em conformidade**

   - **Exigir a política de proteção do aplicativo (versão prévia)**

   - **Exigir um dos controles selecionados**   
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/11.png)



**Etapa 2: configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/06.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. 

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. **Condições:** Como **Condições**, você precisa configurar **Aplicativos cliente**. 

    Como **Aplicativos cliente (versão prévia)**, selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/92.png)

5. Como **Controles de acesso**, você precisa ter o seguinte selecionado:

   - **Exigir que o dispositivo seja marcado como em conformidade**

   - **Exigir a política de proteção do aplicativo (versão prévia)**

   - **Exigir um dos controles selecionados**   
    ![Acesso condicional](./media/app-protection-based-conditional-access/11.png)



**Etapa 3: configurar a política de proteção de aplicativo do Intune para aplicativos de cliente Android e iOS**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Política de dispositivo compatível e com base em proteção de aplicativo para o Exchange Online

Este cenário consiste em uma política de acesso condicional baseado em proteção de aplicativo e em conformidade de dispositivo para acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:
 
-   Configura o email usando um aplicativo de email nativo no iOS ou Android para se conectar ao Exchange
-   Recebe um email que indica que o acesso requer que seu dispositivo seja registrado
-   Baixa e entra no Portal da Empresa
-   Verifica o email e é solicitado a usar o aplicativo Outlook
-   Baixa o aplicativo Outlook
-   Abre o aplicativo Outlook e insere as credenciais usadas no registro
-   É capaz de receber uma política de proteção de aplicativo do Intune
-   É capaz de acessar o email com o Outlook e uma política de proteção de aplicativo do Intune

Quaisquer políticas de proteção de aplicativo do Intune estão ativadas antes de acesso aos dados corporativos e podem solicitar que o usuário para reiniciar o aplicativo, use um etc adicionais do PIN (se configurado para o aplicativo e plataforma)


### <a name="configuration"></a>Configuração

**Etapa 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/01.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. 

     ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**. 
 
     a. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Como **Aplicativos cliente (versão prévia)**, selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Como **Controles de acesso**, você precisa ter o seguinte selecionado:

   - **Exigir que o dispositivo seja marcado como em conformidade**

   - **Exigir a política de proteção do aplicativo (versão prévia)**

   - **Exigir todos os controles selecionados**   
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/13.png)



**Etapa 2: configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/06.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. 

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente (versão prévia)**. 

    Como **Aplicativos cliente (versão prévia)**, selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/92.png)

5. Como **Controles de acesso**, você precisa ter o seguinte selecionado:

   - **Exigir que o dispositivo seja marcado como em conformidade**

   - **Exigir aplicativo cliente aprovado (versão prévia)**

   - **Exigir todos os controles selecionados**   
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/13.png)




**Etapa 3: configurar a política de proteção de aplicativo do Intune para aplicativos de cliente Android e iOS**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Política baseada em aplicativo ou proteção de aplicativo para o Exchange Online e SharePoint Online

Este cenário consiste em uma política de aplicativos aprovados ou com base em proteção de aplicativo para acessar o Exchange Online e SharePoint Online.


### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:

- Configura a qualquer um dos aplicativos cliente que estão na lista de aplicativos que suportam o requisito de política de proteção do aplicativo ou o requisito de aplicativos aprovados.  
- Aplicativos de cliente do usuário usa que atendem ao requisito de política de proteção de aplicativo podem receber uma política de proteção de aplicativo do Intune
- Usuário utiliza aplicativos cliente que atendem ao requisito de política de aplicativos aprovados que dá suporte a política de proteção de aplicativo do Intune
- Abre o aplicativo para acessar o email ou documentos
- Abre o aplicativo Outlook e o assina com as credenciais do Azure AD
- É solicitado a instalar o Authenticator (iOS) ou o Portal da empresa (Android) para continuar (se ainda não instalado)
- Instala o aplicativo e pode retornar ao aplicativo Outlook para continuar
- É solicitado a registrar um dispositivo
- É capaz de receber uma política de proteção de aplicativo do Intune
- É capaz de acessar o email com o Outlook e uma política de proteção de aplicativo do Intune
- É capaz de acessar sites/documentos com um aplicativo não no requisito de política de proteção do aplicativo, mas listadas no requisito de aplicativo aprovado.

Quaisquer políticas de proteção de aplicativo do Intune são necessárias antes de acesso aos dados corporativos e podem solicitar que o usuário para reiniciar o aplicativo, use um etc adicionais do PIN (se configurado para o aplicativo e plataforma)

**Comentários**

- Se você quiser dar suporte a ambas as políticas de acesso condicional baseados em aplicativo e proteção de aplicativo, você pode usar esse cenário.

- Neste *ou* política, aplicativos com **política de proteção de aplicativo** requisito são avaliados para acesso antes **aplicativos cliente aprovados** requisito.

### <a name="configuration"></a>Configuração

**Etapa 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/62.png)

1. O **Nome** da política de acesso condicional.

2. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.

3. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. 

     ![Acesso condicional](./media/app-protection-based-conditional-access/02.png)

4. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**. 
 
     a. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Como **Aplicativos cliente (versão prévia)**, selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Como **Controles de acesso**, você precisa ter o seguinte selecionado:

   - **Exigir um aplicativo cliente aprovado**

   - **Exigir a política de proteção do aplicativo (versão prévia)**

   - **Exigir um dos controles selecionados**   
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/12.png)


**Etapa 2: configurar a política de proteção de aplicativo do Intune para aplicativos de cliente Android e iOS**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).




## <a name="next-steps"></a>Próximas etapas

Se você quiser saber como configurar uma política de acesso condicional, consulte [Exigir MFA para aplicativos específicos com acesso condicional do Azure Active Directory](app-based-mfa.md).

Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](best-practices.md). 