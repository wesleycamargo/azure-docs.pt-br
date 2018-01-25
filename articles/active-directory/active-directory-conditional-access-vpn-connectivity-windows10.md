---
title: "Acesso condicional do Azure Active Directory para conectividade de VPN (versão prévia) | Microsoft Docs"
description: 'Saiba como o acesso condicional do Azure Active Directory para conectividade de VPN funciona. '
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 511df58befe9aed4aa65fc6944cae3a8e2c74c2d
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-conditional-access-for-vpn-connectivity-preview"></a>Acesso condicional do Azure Active Directory para conectividade de VPN (versão prévia)

Com o [acesso condicional do Azure AD (Active Directory)](active-directory-conditional-access-azure-portal.md), você pode ajustar como usuários autorizados acessam seus recursos. Com o acesso condicional do Azure AD para conectividade de VPN (rede virtual privada), você pode ajudar a proteger as conexões VPN.


Para configurar o acesso condicional para conectividade VPN, você precisa concluir as etapas a seguir: 

1.  Configurar o servidor VPN.
2.  Configurar o cliente VPN.
3.  Configurar a política de acesso condicional.


## <a name="before-you-begin"></a>Antes de começar

Este tópico pressupõe que você esteja familiarizado com os tópicos a seguir:

- [Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md)
- [VPN e acesso condicional](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

Para obter informações sobre como a Microsoft implementa esse recurso, consulte [Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile) (Aprimorando o acesso remoto no Windows 10 com um perfil de VPN automático).   


## <a name="prerequisites"></a>pré-requisitos

Para configurar o acesso condicional do Azure Active Directory para conectividade VPN, você precisa ter um servidor VPN configurado. 



## <a name="step-1-configure-your-vpn-server"></a>Etapa 1: Configurar o servidor VPN 

Essa etapa configura os certificados raiz para autenticação de VPN com o Azure AD. Para configurar o acesso condicional para conectividade VPN, você precisa:

1. Criar um certificado VPN no Portal do Azure.
2. Baixar o certificado VPN.
2. Implantar o certificado para o servidor VPN.

O Azure AD usa o certificado VPN para assinar certificados emitidos para os clientes Windows 10 ao autenticar no Azure AD para conectividade VPN. O token solicitado pelo cliente do Windows 10 é um certificado que ele então apresenta ao aplicativo, que nesse caso é o servidor VPN.

![Baixar o certificado para acesso condicional](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

No Portal do Azure, você pode criar dois certificados para gerenciar as transições quando um certificado está prestes a expirar. Quando você cria um certificado, pode escolher se ele é o certificado primário, que é usado durante a autenticação para assinar o certificado para a conexão.

Para criar um certificado de VPN:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador global.

2. No menu à esquerda, clique em **Azure Active Directory**. 

    ![Selecione Azure Active Directory](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. Na página **Azure Active Directory**, na seção **Gerenciar**, clique em **Acesso condicional**.

    ![Selecionar o Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. Na folha **Acesso Condicional**, na seção **Gerenciar**, clique em **Conectividade VPN (versão prévia)**.

    ![Selecionar a Conectividade VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. Na página **Conectividade VPN**, clique em **Novo certificado**.

    ![Selecionar o novo certificado](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. Na página **Novo**, realize as seguintes etapas:

    ![Selecionar duração e primário](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. Para **Selecionar duração**, selecione **1 ano**.

    b. Para **Primário**, selecione **Sim**.

    c. Clique em **Criar**.

7. Na página de conectividade VPN, clique em **Baixar certificado**.


Agora você está pronto para implantar o certificado recém-criado no servidor VPN. No servidor VPN, adicione o certificado baixado como uma *AC raiz confiável para autenticação de VPN*.

Para implantações com base em RRAS do Windows, no servidor NPS, adicione o certificado raiz ao repositório *NTauth corporativo* executando os seguintes comandos:

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2-configure-your-vpn-client"></a>Etapa 2: Configurar o cliente VPN 

Nesta etapa, você configura o perfil de conectividade de cliente VPN, conforme descrito em [VPN e acesso condicional](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access).


## <a name="step-3-configure-your-conditional-access-policy"></a>Etapa 3: Configurar a política de acesso condicional

Essa seção fornece instruções para configurar a política de acesso condicional para conectividade VPN.


1. Na página **Acesso Condicional**, na barra de ferramentas na parte superior, clique em **Adicionar**.

    ![Selecionar Adicionar na página de acesso condicional](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. Na página **Novo**, na caixa **Nome**, digite um nome para a política. Por exemplo, digite **Política de VPN**.

    ![Adicionar nome de política na página de acesso condicional](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. Na seção **Atribuição**, clique em **Usuários e Grupos**.

    ![Selecionar Usuários e grupos](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. Na página **Usuários e grupos**, execute as seguintes etapas:

    ![Selecionar usuário de teste](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. Clique em **Selecionar usuários e grupos**.

    b. Clique em **Selecionar**.

    c. Na página **Selecionar**, selecione seu usuário de teste e clique em **Selecionar**.

    d. Na página **Usuários e grupos**, clique em **Concluído**.

7. Na página **Novo**, realize as seguintes etapas:

    ![Selecionar aplicativos de nuvem](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. Na seção **Atribuições**, clique em **Aplicativos de nuvem**.

    b. Na página **Aplicativos de nuvem**, clique em **Selecionar aplicativos** e, em seguida, clique em **Selecionar**.

    c. Na página **Selecionar**, na caixa **Aplicativos**, digite **vpn**.

    d. Selecione **Servidor VPN**.

    e. Clique em **Selecionar**.


13. Na página **Novo**, para abrir a folha **Conceder**, na seção **Controles**, clique em **Conceder**.

    ![Selecionar Conceder](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. Na página **Conceder**, execute as seguintes etapas:

    ![Selecionar Exigir autenticação multifator](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Selecione **Exigir autenticação multifator**.

    b. Clique em **Selecionar**.

15. Na página **Novo**, em **Habilitar política**, clique em **Ativar**.

    ![Habilitar política](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. Na página **Novo**, clique em **Criar**.



## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como a Microsoft implementa esse recurso, consulte [Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile) (Aprimorando o acesso remoto no Windows 10 com um perfil de VPN automático).    

