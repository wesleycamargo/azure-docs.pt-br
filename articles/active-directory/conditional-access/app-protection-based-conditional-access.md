---
title: Exigir a política de proteção do aplicativo para acesso de aplicativo de nuvem com acesso condicional no Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 2250449c0ef342332945b80cb10cb9a02885b259
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496923"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Exigir a política de proteção do aplicativo para acesso de aplicativo de nuvem com acesso condicional (visualização)

Os funcionários usam dispositivos móveis para tarefas de pessoais e corporativas. Ao mesmo tempo que garante que seus funcionários sejam produtivos, você também quer impedir a perda de dados. Com acesso condicional do Azure Active Directory (Azure AD), você pode proteger os dados da empresa, restringindo o acesso aos seus aplicativos de nuvem. Use aplicativos de cliente com uma política de proteção do aplicativo pela primeira vez.

Este artigo explica como configurar políticas de acesso condicional que podem exigir uma política de proteção do aplicativo antes de conceder acesso a dados.

## <a name="overview"></a>Visão geral

Com o [acesso condicional do Azure AD](overview.md), você pode ajustar como os usuários autorizados podem acessar seus recursos. Por exemplo, você pode limitar o acesso aos seus aplicativos de nuvem a dispositivos confiáveis.

Você pode usar [políticas de proteção do aplicativo Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da empresa. Políticas de proteção de aplicativo do Intune não exigem uma solução MDM (gerenciamento) do dispositivo móvel. Você pode proteger dados da sua empresa com ou sem registro de dispositivos em uma solução de gerenciamento de dispositivo.

Acesso condicional do Azure Active Directory restringe o acesso aos seus aplicativos de nuvem para aplicativos cliente que Intune relatou-se ao Azure AD como o recebimento de uma política de proteção do aplicativo. Por exemplo, você pode restringir o acesso ao Exchange Online para o aplicativo do Outlook que tem uma política de proteção de aplicativo do Intune.

Na terminologia de acesso condicional, esses aplicativos de cliente são conhecidos como política protegida com um *política de proteção de aplicativo*.  

![Acesso condicional](./media/app-protection-based-conditional-access/05.png)

Para obter uma lista de aplicativos cliente protegido por política, consulte [requisito de política de proteção de aplicativo](technical-reference.md#approved-client-app-requirement).

Você pode combinar políticas de acesso condicional baseado em proteção de aplicativo com outras políticas, como [políticas de acesso condicional baseado em dispositivo](require-managed-devices.md). Dessa forma, você pode fornecer flexibilidade na forma de proteger os dados para dispositivos pessoais e corporativos.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Benefícios do requisito de acesso condicional baseado em proteção de aplicativo

Semelhante a conformidade é relatada pelo Intune para iOS e Android para um dispositivo gerenciado, o Intune agora relatórios para o Azure AD se uma política de proteção do aplicativo é aplicada. Acesso condicional pode usar essa política como uma verificação de acesso. Essa nova política de acesso condicional, a política de proteção do aplicativo, aumenta a segurança. Ele protege contra erros do administrador, tais como:

- Usuários que não têm uma licença do Intune.
- Usuários que não podem receber uma política de proteção de aplicativo do Intune.
- Política do Intune app protection aplicativos que não está configuradas para receber uma política.


## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você esteja familiarizado com:

- O [requisito de política de proteção de aplicativo](technical-reference.md#app-protection-policy-requirement) referência técnica.
- A referência técnica do [requisito de aplicativo cliente aprovado](technical-reference.md#approved-client-app-requirement).
- Os conceitos básicos do [acesso condicional no Azure Active Directory](overview.md).
- Como [configurar uma política de acesso condicional](app-based-mfa.md).


## <a name="prerequisites"></a>Pré-requisitos

Para criar uma política de acesso condicional baseado em proteção de aplicativo, faça o seguinte:

- Ter um Enterprise Mobility + Security ou uma assinatura do Azure Active Directory premium + Intune.
- Verifique se os usuários estão licenciados para o Enterprise Mobility + Security ou Azure AD + Intune.
- Verifique se que o aplicativo cliente está configurado no Intune para receber uma política de proteção do aplicativo.
- Verifique se que os usuários estão configurados no Intune para receber uma política de proteção de aplicativo do Intune.

## <a name="app-protection-based-policy-for-exchange-online"></a>Política de proteção com base de aplicativo para o Exchange Online

Este cenário consiste em uma política de acesso condicional baseado em proteção de aplicativo para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:

- Configura o email usando um aplicativo de email nativo no iOS ou Android para se conectar ao Exchange.
- Recebe um email que indica que o acesso está disponível somente usando o aplicativo Outlook.
- Baixa o aplicativo com o link.
- Abre o aplicativo Outlook e entrar com credenciais do Azure AD.
- É solicitado a instalar o Microsoft Authenticator para uso do iOS ou o Portal de empresa do Intune para Android usam continuar.
- Instala o aplicativo e retorna para o aplicativo Outlook para continuar.
- É solicitado a registrar um dispositivo.
- Pode receber uma política de proteção de aplicativo do Intune.
- Pode acessar o email.

Quaisquer políticas de proteção de aplicativo do Intune devem ser no aplicativo para acessar dados corporativos. As políticas podem solicitar que o usuário para reiniciar o aplicativo ou usar um PIN adicional. Esse é o caso se as políticas estão configuradas para o aplicativo e plataforma.

### <a name="configuration"></a>Configuração

**Etapa 1: Configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/01.png)

1. Insira o nome da sua política de acesso condicional.

2. Sob **atribuições**, na **usuários e grupos**, selecione pelo menos um usuário ou grupo para cada política de acesso condicional.

3. Na **aplicativos de nuvem**, selecione **Office 365 Exchange Online**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. Na **condições**, configure **plataformas de dispositivo** e **aplicativos cliente (visualização)**:

     a. Na **plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Na **aplicativos de cliente (visualização)**, selecione **aplicativos móveis e clientes da área de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Sob **controles de acesso**, selecione **exigem a política de proteção de aplicativo (visualização)**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/05.png)
 

**Etapa 2: Configurar uma política de acesso condicional do Azure AD para o Exchange Online com o ActiveSync (EAS)**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/06.png)

1. Insira o nome da sua política de acesso condicional.

2. Sob **atribuições**, na **usuários e grupos**, selecione pelo menos um usuário ou grupo para cada política de acesso condicional.


3. Na **aplicativos de nuvem**, selecione **Office 365 Exchange Online**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. Na **condições**, configure **aplicativos cliente (visualização)**. 

     a. Na **aplicativos de cliente (visualização)**, selecione **aplicativos móveis e clientes da área de trabalho** e **clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/92.png)

    b. Sob **controles de acesso**, selecione **exigem a política de proteção de aplicativo (visualização)**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/05.png)


**Etapa 3: Configurar a política de proteção de aplicativo do Intune para aplicativos de cliente Android e iOS**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Política de dispositivo compatível ou com base em proteção de aplicativo para o Exchange Online

Este cenário consiste em uma política de acesso condicional de dispositivo compatível ou com base em proteção de aplicativo para acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que:
 
- Um usuário já estiver registrado, com ou sem dispositivos corporativos.
- Os usuários que não são registrados e registrados com o Azure AD com o uso de um aplicativo protegido por aplicativo precisam registrar um dispositivo para acessar os recursos.
- Os usuários registrados que usam o aplicativo protegido não precisam registrar novamente o dispositivo.
- O usuário pode receber uma política de proteção de aplicativo do Intune se não forem registrados.
- O usuário pode acessar o email com o Outlook e uma política de proteção de aplicativo do Intune se não forem registrados.
- O usuário pode acessar o email com o Outlook, se o dispositivo é registrado.


### <a name="configuration"></a>Configuração

**Etapa 1: Configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/62.png)

1. Insira o nome da sua política de acesso condicional.

2. Sob **atribuições**, na **usuários e grupos**, selecione pelo menos um usuário ou grupo para cada política de acesso condicional.

3. Na **aplicativos de nuvem**, selecione **Office 365 Exchange Online**. 

     ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. Na **condições**, configure **plataformas de dispositivo** e **aplicativos cliente (visualização)**. 
 
     a. Na **plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Na **aplicativos de cliente (visualização)**, selecione **aplicativos móveis e clientes da área de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Sob **controles de acesso**, selecione as seguintes opções:

   - **Exigir que o dispositivo seja marcado como em conformidade**

   - **Exigir a política de proteção do aplicativo (versão prévia)**

   - **Exigir um dos controles selecionados**   
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/11.png)



**Etapa 2: Configurar uma política de acesso condicional do Azure AD para o Exchange Online com o ActiveSync**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/06.png)

1. Insira o nome da sua política de acesso condicional.

2. Sob **atribuições**, na **usuários e grupos**, selecione pelo menos um usuário ou grupo para cada política de acesso condicional.

3. Na **aplicativos de nuvem**, selecione **Office 365 Exchange Online**. 

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. Na **condições**, configure **aplicativos cliente (visualização)**. 

    Na **aplicativos de cliente (visualização)**, selecione **aplicativos móveis e clientes da área de trabalho** e **clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/92.png)

5. Sob **controles de acesso**, selecione as seguintes opções:

   - **Exigir que o dispositivo seja marcado como em conformidade**

   - **Exigir a política de proteção do aplicativo (versão prévia)**

   - **Exigir um dos controles selecionados**

     ![Acesso condicional](./media/app-protection-based-conditional-access/11.png)



**Etapa 3: Configurar a política de proteção de aplicativo do Intune para aplicativos de cliente Android e iOS**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Política de dispositivo compatível e com base em proteção de aplicativo para o Exchange Online

Este cenário consiste em uma política de acesso condicional baseado em proteção de aplicativo e em conformidade de dispositivo para acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:
 
-   Configura o email usando um aplicativo de email nativo no iOS ou Android para se conectar ao Exchange.
-   Recebe um email que indica que o acesso requer seus dispositivos sejam registrados.
-   Downloads do Portal da empresa Intune e entra no portal.
-   Verifica o email e é solicitado a usar o aplicativo Outlook.
-   Baixa o aplicativo Outlook.
-   Abre o aplicativo Outlook e insere as credenciais usadas no registro.
-   Pode receber uma política de proteção de aplicativo do Intune.
-   Pode acessar o email com o Outlook e uma política de proteção de aplicativo do Intune.

Quaisquer políticas de proteção de aplicativo do Intune são ativadas antes de conceder acesso a dados corporativos. As políticas podem solicitar que o usuário para reiniciar o aplicativo ou usar um PIN adicional. Esse é o caso se as políticas estão configuradas para o aplicativo e plataforma.


### <a name="configuration"></a>Configuração

**Etapa 1: Configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/01.png)

1. Insira o nome da sua política de acesso condicional.

2. Sob **atribuições**, na **usuários e grupos**, selecione pelo menos um usuário ou grupo para cada política de acesso condicional.

3. Na **aplicativos de nuvem**, selecione **Office 365 Exchange Online**. 

     ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. Na **condições**, configure **plataformas de dispositivo** e **aplicativos cliente (visualização)**. 
 
     a. Na **plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Na **aplicativos de cliente (visualização)**, selecione **aplicativos móveis e clientes da área de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Sob **controles de acesso**, selecione as seguintes opções:

   - **Exigir que o dispositivo seja marcado como em conformidade**

   - **Exigir a política de proteção do aplicativo (versão prévia)**

   - **Exigir todos os controles selecionados**   
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/13.png)



**Etapa 2: Configurar uma política de acesso condicional do Azure AD para o Exchange Online com o ActiveSync**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/06.png)

1. Insira o nome da sua política de acesso condicional.

2. Sob **atribuições**, na **usuários e grupos**, selecione pelo menos um usuário ou grupo para cada política de acesso condicional.

3. Na **aplicativos de nuvem**, selecione **Office 365 Exchange Online**. 

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. Na **condições**, configure **aplicativos cliente (visualização)**. 

    Na **aplicativos de cliente (visualização)**, selecione **aplicativos móveis e clientes da área de trabalho** e **clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/92.png)

5. Sob **controles de acesso**, selecione as seguintes opções:

   - **Exigir que o dispositivo seja marcado como em conformidade**

   - **Exigir a política de proteção do aplicativo (versão prévia)**

   - **Exigir todos os controles selecionados**   
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/13.png)




**Etapa 3: Configurar a política de proteção de aplicativo do Intune para aplicativos de cliente Android e iOS**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Política baseada em aplicativo ou proteção de aplicativo para o Exchange Online e SharePoint Online

Este cenário consiste em uma política de aplicativos aprovados ou com base em proteção de aplicativo para acessar o Exchange Online e SharePoint Online.


### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:

- Configura os aplicativos cliente que estão na lista de aplicativos que suportam o requisito de política de proteção do aplicativo ou o requisito de aplicativos aprovados.  
- Usa os aplicativos cliente que atendem ao requisito de política de proteção de aplicativo e podem receber uma política de proteção de aplicativo do Intune.
- Usa aplicativos de cliente que atendem ao requisito de política de aplicativos aprovados que dá suporte a política de proteção de aplicativo do Intune.
- Abre o aplicativo para acessar o email ou documentos.
- Abre o aplicativo Outlook e entrar com credenciais do Azure AD.
- É solicitado a instalar o Microsoft Authenticator para uso do iOS ou o Portal de empresa do Intune para Android usam se eles ainda não estiverem instalados.
- Instala o aplicativo e pode retorna para o aplicativo Outlook para continuar.
- É solicitado a registrar um dispositivo.
- Pode receber uma política de proteção de aplicativo do Intune.
- Pode acessar o email com o Outlook e uma política de proteção de aplicativo do Intune.
- Pode acessar sites e documentos com um aplicativo não no requisito de política de proteção do aplicativo, mas listados no requisito de aplicativo aprovado.

Quaisquer políticas de proteção de aplicativo do Intune são necessárias antes de conceder acesso a dados corporativos. As políticas podem solicitar que o usuário para reiniciar o aplicativo ou usar um PIN adicional. Esse é o caso se as políticas estão configuradas para o aplicativo e plataforma.

**Comentários**

- Se você quiser dar suporte a ambas as políticas de acesso condicional baseados em aplicativo e proteção de aplicativo, você pode usar esse cenário.
- Neste *ou* política, aplicativos com um requisito de política de proteção de aplicativo são avaliados para acesso pela primeira vez antes do requisito de aplicativos cliente aprovados.

### <a name="configuration"></a>Configuração

**Etapa 1: Configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/62.png)

1. Insira o nome da sua política de acesso condicional.

2. Sob **atribuições**, na **usuários e grupos**, selecione pelo menos um usuário ou grupo para cada política de acesso condicional.

3. Na **aplicativos de nuvem**, selecione **Office 365 Exchange Online**. 

     ![Acesso condicional](./media/app-protection-based-conditional-access/02.png)

4. Na **condições**, configure **plataformas de dispositivo** e **aplicativos cliente (visualização)**. 
 
     a. Na **plataformas de dispositivo**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Na **aplicativos de cliente (visualização)**, selecione **aplicativos móveis e clientes da área de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Sob **controles de acesso**, selecione as seguintes opções:

   - **Exigir um aplicativo cliente aprovado**

   - **Exigir a política de proteção do aplicativo (versão prévia)**

   - **Exigir um dos controles selecionados**
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/12.png)


**Etapa 2: Configurar a política de proteção de aplicativo do Intune para aplicativos de cliente Android e iOS**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).




## <a name="next-steps"></a>Próximos passos

- Se você quiser saber como configurar uma política de acesso condicional, consulte [Exigir MFA para aplicativos específicos com acesso condicional do Azure Active Directory](app-based-mfa.md).
- Se você estiver pronto para configurar políticas de acesso condicional para o seu ambiente, consulte [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md). 